https://github.com/Elldreth/loopback_scaler

![[Pasted image 20230415010332.png]]

功能介绍：主要用于图生图补充低分辨率的缺少细节的图

官方推荐图生图设置：
缩放模式：裁剪
采样迭代部署：30左右
提示词相关性（CFG）：6-8
重绘幅度：0.2-0.4

脚本参数：
loops迭代次数：脚本在多个循环中处理输入图像，每个循环提高分辨率并优化图像质量。然后，一个循环的图像结果作为下一个循环的输入图像插入，该循环不断基于已创建的内容构建。
Denoise Change**降噪变化**：可以针对每个循环调整去噪强度，使用户能够在保留细节和减少伪影之间取得平衡。
Adaptive Change**自适应更改**：脚本根据输入图像的平均强度调整每个循环的分辨率增加量。这有助于产生更自然的效果。
Maximum Image Width/Maximum Image Height 最大图像宽/高
Add Detail/Add Blur/Smoothing 增加细节/模糊/顺滑 三种滤镜
Sharpness/Brightness/Color/Contrast 锐化/亮度/颜色/对比度 