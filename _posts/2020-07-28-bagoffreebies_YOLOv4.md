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
=========================================================================================
- Object Detection 에서 네트워크 구조를 바꾸는 등의 학습 시간에 영향을 주는 것 말고,
 training strategy 나 training method 변경을 통해 정확도를 올릴 수 있는 기법들.
   
1. Data imbalance 를 해결하기 위한 기법들
    - Focal Loss 사용 : 이미지에서 foreground에 비해 background 영역이 많기에, 학습시 로스함수가 background에 의해 압도되는 현상을 방지하기 위해 foreground에 가중치를 주도록 설계한 modified cross entropy loss.
    
2. 모델의 categories 구분에 variation을 주어서 모델의 robustness를 강화하는 기법 
    - Label Smoothing : one hot encoded label vector에 uniform distribution을 결합하여 smoothed label을 만듦.        
        - y_ls  = (1 - smooth_factor) * y_label + smooth_factor / num_class
        - smooth_factor로 label smoothing강도 조절
        - 모델이 overconfident 하는 것을 완화하므로서 calibration 과 regulazation 효과가 있음.
        - Python Implementation
        
         <script src="https://gist.github.com/HyunjiEllenPak/dc45cc37fcc28eaac6288fd4b3c019e2.js"></script> 
    
        
3. Bounding Box regression 손실 함수 변경
    - 기존에는 BB regression에서는 {x_center, y_center, w, h}값들에 대해  Mean Square Error(MSE) 또는 L1 손실함수를 사용했는데, 이 손실함수는 box scale variant 하여 모델 학습의 robustness를 떨어뜨린다. 
    - 예를 들어, 아래의 Fig.1을 보면 모델이 각기 다른 크기의 물체(Bounding Box)를 예측하였을 때, 박스 영역의 관점에서 (b)보다 (a)의 경우가 더 정확한 예측을 하였지만 L1 손실함수를 사용할 경우 (a)와 (b)가 손실함수에 유사한 기여를 하게 되어 잘못된 학습이 이루어질 수 있다. 
      <center><img src="/assets/images/mse_problem.jpg" width="500" height="250"></center>
    - IoU loss가 제안됨. 
    - 이후에 IoU Loss를 개선한 Generalized IoU Loss, Complete IoU loss, Distance IoU loss 가 제안됨.
  
    

 
 
 참고 자료
-----------------------------------------------------------------------
 - [Bag of Freebies 관련 논문](https://arxiv.org/pdf/2004.10934.pdf)
 - [Label smoothing 관련 논문](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Szegedy_Rethinking_the_Inception_CVPR_2016_paper.pdf)
 - [Distance IoU loss 관련 논문](https://arxiv.org/pdf/1911.08287.pdf)