---
title: ROI Pooling과 ROIAlign기법의 차이 -  Mask R-CNN
categories:
    - DL tech
date: 2021-05-10 00:00:00 +0900
tags: [Object Detection, Deep Learning]    
comments: true
toc: False
---

RoIAlign기법은 Faster RCNN(아래 그림 참고)에서 사용되었던 ROI Polling의 단점을 보완한 기법입니다.  
Roi Pooling은 Faster RCNN에서 Region Proposal Network로부터 선별된 Box들을 Box Classifer와 Regressor의 입력으로 사용하기 전에,
각기 다른 Size의 Box들을 Fixed-Length Feature Vector로 Resize하기 위해 사용되었습니다.
<center><img src="/assets/images/fasterrcnn.PNG" width="300" height="500"></center>


ROI Pooling의 단점
----------------------
그러나 ROI Pooling은 ROI Feature들을 Fixed Length Feature Vector로 Resize할 때, ROI Box의 각 위치에 대해 Rounding Operation(=Nearest Interpolation)을 한 후에 Max Pooling하므로
 Rounding operation으로 인해 물체의 위치에 대한 정확도가 떨어지게 됩니다. 특히 Small Object를 검출하려는 경우에는 Interpolation에 의해 발생하는 작은 오차가 검출 정확도에 큰 영향을 미치게 됩니다.

ROIAlign 설명
----------------
이에 Mask RCNN에서 ROI Pooling대신에 ROIAlign을 제안하였습니다. ROIAlign은 ROI내의 각 위치에 대한 값을 Bilinear Interpolation으로 정확하게 구한 후에, Max pooling을 수행합니다. 
ROI Pooling과 ROIAlign에 대해 이해가 쉽도록 Fig 1에 ROI Pooling과 ROIAlign을 나타내보았습니다.
 
Fig1에서 (b)와 (c)는 4x4 ROI를 1X1 Vector로 만들기 위해 ROI Pooling과 ROIAlign을 수행하는 것입니다.
<center><img src="/assets/images/roipooling.png" width="500" height="280"></center>


 
 References.
 -----------
 - [Fast RCNN논문](https://www.cv-foundation.org/openaccess/content_iccv_2015/papers/Girshick_Fast_R-CNN_ICCV_2015_paper.pdf)
 - [Faster RCNN 논문](https://proceedings.neurips.cc/paper/2015/file/14bfa6bb14875e45bba028a21ed38046-Paper.pdf)
 - [Mask RCNN 논문](https://openaccess.thecvf.com/content_ICCV_2017/papers/He_Mask_R-CNN_ICCV_2017_paper.pdf)