```python
import cv2
import numpy as np

# 读取所有80张图片并提取SIFT特征
sift = cv2.xfeatures2d.SIFT_create()
des_list = []
for img_path in img_paths:
    img = cv2.imread(img_path)
    kp, des = sift.detectAndCompute(img, None)
    des_list.append(des)

# 建立特征描述子的FLANN索引
index_params = {'algorithm': 1, 'trees': 5} 
search_params = {'checks': 50}
flann = cv2.FlannBasedMatcher(index_params, search_params)
index = flann.build_index(des_list, algorithm ='flann')
 
# 截屏并提取区域的SIFT特征    
screenshot = ...
region_kps, region_dess = [], []
for region in screenshot_regions:
    region_kps, region_dess = sift.detectAndCompute(region, None)
    region_dess.append(des)

# 对每个区域使用FLANN索引找出最匹配的图片     
match_results = [] 
for des in region_dess:
    matches = flann.nnSearch(index, des, 10) 
    match_results.append(matches[0][0][0])

# 对匹配结果进行模板匹配验证 
for i, match_img in enumerate(match_results):
    rect = screenshot_regions[i]
    match_template = cv2.imread(img_paths[match_img]) 
    res = cv2.matchTemplate(rect, match_template, cv2.TM_CCOEFF_NORMED)
    (_, max_val, _, max_loc) = cv2.minMaxLoc(res)
# 如果验证成功,则确认识别结果
```
