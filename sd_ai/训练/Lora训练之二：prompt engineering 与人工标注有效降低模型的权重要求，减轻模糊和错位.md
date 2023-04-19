接原帖：[[https://bbs.nga.cn/read.php?tid=35774713](https://bbs.nga.cn/read.php?tid=35774713)]  
  
通过 prompt engineering 提升lora模型效率，降低权重需求，精准控制元素。  
  
核心：不再使用 deepbooru 自动打标  
  
  
1，原理  
  
  
接原帖关于Lora与模型的运行原理，prompt起到的是类似于“变量名”的作用。而我们输入到模型训练中的tag应与我们在生成图片时使用的prompt一一对应，才能实现从模型中挖掘并利用tag对应元素的作用。  
  
以往大多采用webui里面提供的deep booru来自动打标，这看起来很简单(哪怕几百张训练图，也是自动化完成了)，但实际上问题很多。  
  
如果AI能识别里面的元素，自动打出tag来，那也不再需要打标了——训练的时候自然会认得什么是什么。而机器没能打出的tag或打错的tag，才是降低模型质量的原因。  
  
我们常说的监督学习，就是由人来监督机器，打标就是数据标注，现在AI里面成本最高的就是数据标注。你可能会说SD只是一种“游戏”，是娱乐，但是哪怕玩游戏也是要花时间的。经过理论和实际研究，经过人工调整的标注，要远远好过无标注或机器打的标签。  
  
而且打标签也要按照规则，即：你怎么输入prompt的，就怎么生成标签，形成一一对应的关系，才能更好的利用tag。  
  
2，prompt规则  
  
  
prompt规则是靠前的权重大、权重逐步减小。  
  
prompt实际的“权重”应该是相对的，也就是说，用更多的prompt，每一个的权重会降低。因此有必要减少不必要的prompt使用。这个在原理里说过  
  
prompt之间、lora之间、lora与底模之间会互相冲突(损失函数要求同时最小，是不可能的)，不存在同时满足各项条件的可能，只能选择“相对同时满足各项条件”的折中解。  
  
因此我在参考一些外国帖子后，总结了一些方法：  
  
生图时的prompt应遵守与tag一致的一定顺序，一种可参考的顺序是：  
  
主题：1boy, 1girl, mywaife(自定义的人物标签)  
  
  
风格、视角、质量: anime, drawing, photorealistic，best quality，masterpiece, ultra high res， full body shot, from side，  
  
动作: sitting, standing, looking at viewer...  
  
主要属性(服装、头发等): short brown hair, long hair, pale pink dress with dark edges, brown slippers。。。  
  
次要属性: 光效这些  
  
背景、其他杂物: brown couch, red patterned fabric on couch, wooden floor, white water-stained paint on walls, refrigerator in background, coffee machine sitting on a countertop, table in front of couch, bananas and coffee pot on table, white board on wall, clock on wall, stuffed animal chicken on floor  
  
  
3，tag规则  
  
  
tag应该按照同样的顺序进行标注。这里使用tag管理器(boorutagmanager)，可以先用机器打一遍标，然后手工改。  
  
按照上面的规范，把标签也整理成这个顺序。某项没有的就略过，直接顺序写下一个。  
  
关键在于：相同的元素要用相同的标签，把机器形成的错误的、重复的标签删除，添加你需要的标签。机器肯定会有很多错误的，重复的标签，都要剔除，再加上你认为有意义的标签。tag管理器有自动补全功能，还可以复制某一个标签到所有图片里。  
  
关于自定义tag和人脸  
  
其实机器是可以识别人脸的，不需要添加 eyes, nose, mouth 。 最多机器会给你有关发型或发色的tag，你是否要删除这些tag，取决于你对人物的需求。  
  
如果你需要人物不改变发型或发色，就要去掉这些标注，让机器把这个发型或发色当作人脸的一部分。如果认为以后需要更换发型等，就不要删除tag，甚至如果机器没有给你标注，你还应该自己标注出来。  
  
自定义tag我认为是一种补救措施，如果因为什么原因无法调用lora中的元素了，才需要使用自定义tag。  
  
关于自定义tag与1girl。  
  
这个问题我在前面帖子里面说过了。  
  
你输入1girl，机器优先找lora里面的1girl，如果有，就按照lora里描述的1girl来绘制1girl，如果没有，就到底模(Chillout之类的)里面找1girl来绘制。  
  
如果你要做一个人物的lora，第一个tag是1girl，就意味着你使用它生图时候输入1girl就可以了。  
  
如果你去掉了1girl，用了一个人物名称代替(比如mywaife)，那么你生图的时候第一个prompt就要输入 mywaife。  
  
  
4，实例  
  
  
因为人脸制作比较麻烦。。。我先用一个衣服lora来做试验。实验结果是手工修正的tag组合比自动打标的降低了四分之一的权重要求，且降低了错误率。最低无错呈现的权重从0.4降低到了0.3，并且可以用预设的tag来调整内容，且与底模中的其他元素融合很好。  
  

− 案例 ...

  
服装素材：  
  
服装为上半身，目的是测试与下半身底模中的素材结合程度。  
  
训练集已经裁剪掉除了衣服之外的所有部分。  
  
  
![](https://img.nga.178.com/attachments/mon_202304/02/-9lddQ8sk0-71yzK1qT1kS9q-el.jpg)  
  
* 注意中间有个黑色蝴蝶结装饰，生图的时候可以用来衡量还原度  
  
  
原图：  
  
![](https://img.nga.178.com/attachments/mon_202304/02/-9lddQ8sk0-e3otK2lT1kSe8-lc.jpg)  
  
  
首先机器打标后，用tag管理器打开训练集，对标签进行整理。由于是服装，就不需要1girl了，第一个tag位置留给自定义tag  
  
  
![](https://img.nga.178.com/attachments/mon_202304/02/-9lddQ8sk0-i6uuK2sT3cSsg-ep.jpg)  
  
首先根据原图(即reg里面的原图，而不是切下来这一小块)的姿势，补全这张图的构图、动作、姿势tag，以便正确的构图。  
  
这里我补充了 full body、 standing、 looking at viewer 。虽然这个原图里面的人并没有looking at viewer，但是不要紧，反正也不学她的全身，要的只是构图。  
  
关于这件衣服，机器给了很多乱七八糟的tag，通过查询数据库，最后确定最接近的tag为 lace up top 和 lace trimmed，由于SD一定程度上支持自然语言(你可以认为和GPT类似)，所以前面还可以加一个颜色 white。  
  
为了统一tag，训练集里所有的图都复制了这两个tag。  
  
下面是背景，indoors。  
  
最后机器识别了一个项链，我把它放到最后一个(杂物)里面了。并且删除任何不需要的标志。  
  
  
  
以此类推，处理所有训练集的tag。  
  
这里我对reg集没有做任何tag处理，但是因为图片较少(只有20张)，我让reg和训练集的repeat次数一样。都是10次，10epoch。  
  
同时作为对照组，另一组模型全部使用机器打标，不做任何修改，直接训练。  
  
测试组(人工打标、修标) 对照组(机器打标，直接训练) 使用同样的参数生图，并且要求与底模、同样的脸部Lora配合  
  
1girl, best quality, masterpiece, ultra high res, (photorealistic:1.4), solo, full body, standing, looking at viewer, bangs, white lace-up top, black skirt, black pantyhose, high heels, indoors  
  
使用相同的脸部lora，权重0.68。  
  
由于这只是一件上衣，因此裙子、黑丝、高跟都用底模(Chilloutmix)里的，使用了 black skirt, black pantyhose, high heels 三个关键词。  
  
  
![](https://img.nga.178.com/attachments/mon_202304/02/-9lddQ4ojq-6xo1ZlT3cSrb-sg.jpg)  
  
无论是测试组还是对照组，底模中的元素表现都没有出错，这说明衣服确实只作用在了“衣服”该在的位置。  
  
但是人工打标的权重在0.31就正确绘制了蝴蝶结形状的装饰物，而0.31左右机器打标的不仅衣服细节模糊不清，而且人脸也受到了较大影响。  
  
实测中到了0.36以上，人工打标的图基本就问题不大，后续只需要提高CFG，而机器打标要到0.4以上才行。  
  
虽然说0.4以上出图也不是不行，但是lora之间是互相影响的，这个lora权重高了，意味着那个lora权重相对就低了。要用0.4以上的权重才能绘制一件衣服的话，假设还要用到其他元素，权重就会不够用，结果就是图片模糊或者脸部效果不好。  
  
再出一版，更说明问题的：  
  
又测试了几版，这里挑了一版有问题的：这一版无法还原“white”属性，衣服无论如何出的都是黑色的。  
  
  
![](https://img.nga.178.com/attachments/mon_202304/02/-9lddQ1ema-5h33ZhT3cSij-sg.jpg)  
  
在这一版中，机器打标的很可能因为缺乏构图信息(比如full body)直接出现了构图错误。而人工标注的在0.28的权重下，就能基本正确的绘制出人物了(虽然颜色是错的)  
  
另外，人工标注的衣服吊带绘制错误了。  
  
为了解决这个问题，我们分析之前设置的tag: 绘制的吊带在肩部到胸部之间的区域，那么之前tag里面有一个杂物tag“项链”。如果应用“项链”这个tag，就会使这个区域被重绘，进而可能纠正这个问题。  
  
接下来的一版里面，除了继续提升lora的权重外，最后面加入“项链”这个关键词。  
  
  
![](https://img.nga.178.com/attachments/mon_202304/02/-9lddQpnvb-6sckZhT3cSij-sg.jpg)  
  
可以看出吊带的错误被项链纠正了。而左侧机器打标的lora模型直到权重0.39才纠正了构图错误。  
  
继续提升lora的权重：  
  
  
  
![](https://img.nga.178.com/attachments/mon_202304/02/-9lddQ4olu-cs9cZgT3cSin-sg.jpg)  
  
可以看到机器打标的模型已经出现了过拟合，使底模中的黑丝等元素被冲突掉了。而右侧人工打标的模型则正常，权重到0.45以后正确还原了衣服细节，除了颜色仍然不对。  
  
但这样已经是一副合格的作品了，接下来只要提升CFG精修即可。  
  
  
  

  
  
  
经过实际测试，我认为由于机器识别tag本身就存在问题(它能识别的，也不再需要你来标注，它识别不了的，才需要你来标注)，以及缺乏构图信息(full body，sitting，standing)，还是应该用机器标注后人工修改，添加为好。  
  
而且实际上还可以使用负面prompt来控制。假设图片中存在一些你不想要的或者没用的东西(比如很多原图手里有手机之类的)，如果你标注出来了，可以放在负面里面过滤掉。  
  
参考资料：  
  
[[https://www.reddit.com/r/StableDiffusion/comments/118spz6/captioning_datasets_for_training_purposes/?rdt=54669](https://www.reddit.com/r/StableDiffusion/comments/118spz6/captioning_datasets_for_training_purposes/?rdt=54669)]  
(占楼继续)  
  
https://bbs.nga.cn/read.php?&tid=35850123

5，有效降低构图错误  
  
  
  
使用刚才4中的案例，如果我们要变更姿势：把standing 改成 sitting  
  
1girl, best quality, masterpiece, ultra high res, (photorealistic:1.4), solo, full body, sitting, looking at viewer, bangs, white lace-up top, black skirt, black pantyhose, high heels, indoors  
  
就很容易出现问题，我一次出4张图，竟然没有一张是对的。  
  
  
![](https://img.nga.178.com/attachments/mon_202304/02/-9lddQpnw3-epqcZmT3cSlc-sg.jpg)  
  
经过研究发现，sitting必然和full body冲突，这里就要求去掉full body。 但是去掉full body后，出来的图片效果还是不理想。  
  
这时候发现，经过人工分组tag的模型具有了重大的优点：  
  
在上面4中的测试模型中，每一张图片都分配了姿势和角度(sitting，standing)、(full body，half body, cowboy shot )  
  
那么坐姿面对镜头，一定是cowboy shot 或者更近的视角。  
  
修改prompt，full body改成 cowboy shot，姿势 sitting，再出一组图，就没有构图错误了：  
  
  
![](https://img.nga.178.com/attachments/mon_202304/02/-9lddQ4oy0-2yr2ZlT3cSlc-sg.jpg)  
  
接下来就是大量出图，找到自己喜欢的微调。  
  
由于人工干预每张训练图，使得每张训练图都被分配了合适的视角、姿势，那么在生图过程中遇到构图问题就可以针对性的调整。  

#### 附件

  
  
https://bbs.nga.cn/read.php?&tid=35850123&pid=681808124&to=1