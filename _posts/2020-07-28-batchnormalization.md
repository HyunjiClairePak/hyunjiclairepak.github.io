---
title: Object Detection 성능향상을 위한 Bag of Freebies 기법 - YOLOv4
categories:
    - DL tech
date: 2020-07-28 21:00:00 +0900
tags: 
    - Object Detection
    - Deep Learning    
comments: true
toc: False
---

Bag of Freebies 란
========================
- YOLO v4 논문에 등장한 용어로
- Object Detection 에서 네트워크 구조를 바꾸는 등의 학습 시간에 영향을 주는 것 말고,
 training strategy 나 training method 변경을 통해 정확도를 올릴 수 있는 기법들.
   
1. data imbalance 를 해결하기 위한 기법들
    - Focal loss 사용 : 이미지에서 foreground에 비해 background영역이 많기에, 학습시 로스함수가 background에 의해 압도되는 현상을 방지하기 위해 foreground에 가중치를 주도록 설계한 modified cross entropy loss.
2. Different categories 간에 variation을 주어서 모델의 robustness를 강화하는 기법
    - label smoothing : hard label을 soft label로 변형하여 학습에 사용하는 것.
    - 예를 들어서 기존에 강아지[0, 1], 고양이[1, 0] 라벨이 있을 때, 이를 [0.2, 0.8], [0.7, 0.3]로 라벨링하여 학습에 사용하는 것. 
3. Bounding Box regression 방식 변경
    - 기존에는 BB regression에서 Mean Square Error(MSE) 손실함수 계산을 위해 {x_center, y_center, w, h}값들을 사용했었는데, 이 값들은 BB의 직접적인 좌표를 나타내는 값들이 아니고 서로 연관성을 지녀서 학습 robustness가 떨어진다. 
    - IoU loss가 제안됨. 
    - 최근엔, IoU Loss를 개선한 GIoU Loss, CIoU Loss가 제안됨

 
 
 참고 자료
 ______________________________________________________________________________
 - [YOLOv4 논문](https://arxiv.org/pdf/2004.10934.pdf)
 - [Label smoothing 관련 논문](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Szegedy_Rethinking_the_Inception_CVPR_2016_paper.pdf)