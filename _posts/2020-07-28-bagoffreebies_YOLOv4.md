---
title: Object Detection 성능향상을 위한 Bag of Freebies 기법 - YOLOv4
categories:
    - DL tech
date: 2020-07-28 21:00:00 +0900
tags: [Object Detection, Deep Learning]    
comments: true
toc: False
---
안녕하세요. 오늘은 State-of-the-art Object Detection 논문인 **"YOLO v4: Optimal Speed and Accuracy of Object Detection"** 논문에 나오는 Bag of Freebies 용어와 이 기법들에 대해 알아보려고 합니다. 

제가 Object Detection 관련 프로젝트를 수행하면서, 학습이 끝난 모델을 평가 할 때, 모델이 예측한 박스가 A 클래스일 확률(P<sub>a</sub>)에 대해 thresholding하는 threshold값에 따라서 모델의 정확도(=예측 결과를 confusion matrix로 나타낸 후, 계산한 옳게 예측한 수 대비 전체 모수의 비가 조금씩 달라집니다. 
대체적으로 threshold가 작을 수록 물체를 못찾는 비율(미검율)이 줄어들고, 물체를 과도하게 검출하는 경향을 보입니다. 따라서 학습이 잘된 모델일 수록 모델이 A 물체를 1.0에 가까운 확률로 A물체라고 예측하게 되므로, threshold값에 따라서 모델의 정확도에 변화가 크지 않습니다.

그러나 제가 학습했던 ResNet50 + Feature Pyramid Network 기반의 RetinaNet모델이 이 score threshold값에 따라서 모델 정확도 차이가 크게는 7%정도 발생하였습니다. 그래서 저는 모델을 보다 안정적으로 학습하게 할 방법이 무엇이 있을지에 대해 찾아보다가 이 YOLO v4논문에서 모델 구조 변경없이 
모델 학습 방법, 데이터 Generation방법에 약간의 변화를 주는 것으로 모델 정확도를 높일 수 있는 Bag of Freebies기법들을 알게 되었고 이를 RetinaNet에 직접 적용하여 실험도 해보았기에 이에 대해 소개하려 합니다.
 
 
Bag of Freebies 란
=========================================================================================
Bag of Freebies는 YOLO v4 논문에 나온 용어인데, Object Detection 에서 네트워크 구조를 바꾸어서 학습 시간을 증가시키는 것 외에,
 손실함수 변경, Data Augmentation등의 Data Generation방법이나 학습 방법을 변경하여 모델의 정확도를 올릴 수 있는 기법들을 의미합니다. 
 
 YOLO v4 논문에서는 다양한 Bag of Freebies에 대한 성능 실험을 하였고 실험을 통해 보편적인 데이터셋에서도 우수한 성능을 내는 것으로 증명된 몇가지를 YOLO v4 모델의 구성에 포함하였습니다. 아래 그림은 논문에 나온 YOLO v4에 사용된 BoF기법입니다.
 <center><img src="/assets/images/bagoffreebies.PNG" width="300" height="350"></center> 
 
 이중에서 제가 Object Detection 관련 프로젝트를 하면서 RetinaNet의 정확도를 높이기 위해 실험해보았던 것들과 실험 결과에 대해 소개하도록 하겠습니다.     
   
첫번째는 Image Object Detection시 이미지에 물체보다 배경영역이 더 많아서 Anchor로 Network Feature 를 Sliding하여 학습시,
물체보다 배경영역이 손실함수에 더 큰 영향을 미치는 문제가 발생하기에 이러한 imbalance를 해결하기위해 제안된 modified cross entropy loss인 focal loss입니다. 이는 Retinanet논문에서 제안된 손실함수입니다.

두번째는 물체 라벨링에 variation을 주어서 모델의 Robustness를 강화하는 Label Smoothing기법입니다. (0, 0, 1) 로 라벨링되어있는 물체를 (0.1, 0.1, 0.8) 로 라벨링하는 것인데, 이렇게 하면 모델이 학습을 할때 Overconfident하지 않도록 하여 Regularization과 같은 효과를 낸다고 합니다.
저는 패널 이미지에서 불량을 검출하기 위해 개발한 RetinaNet에서, 기존 모델이 학습데이터에 Overfitting된 경향이 있어서 이 라벨 스무딩을 적용해보았습니다. 
Smoothing Factor를 0.01, 0.05, 0.1로 두고 실험을 해보았으나, smoothing factor가 커질수록 모델 정확도가 떨어졌고, 스무딩을 적용하지 않은 모델이 정확도가 더 높았습니다..
     
- Label Smoothing : one hot encoded label vector에 uniform distribution을 결합하여 smoothed label을 만듦.        
- y_ls  = (1 - smooth_factor) * y_label + smooth_factor / num_class
- smooth_factor로 label smoothing강도 조절
- 모델이 overconfident 하는 것을 완화하므로서 calibration 과 regulazation 효과가 있음.
- Python Implementation
 <script src="https://gist.github.com/HyunjiEllenPak/dc45cc37fcc28eaac6288fd4b3c019e2.js"></script> 

세번째는 Bounding Box Regression손실함수를 기존의 L1기반의 손실함수에서 IOU기반의 손실함수로 변경하는 것입니다. Object Detection학습의 Box Regression학습에서 기존의 L1손실함수를 사용하면 Box Scale에 Variant한 특성이 있습니다. 
예를 들어, 아래의 Fig.1을 보면 모델이 각기 다른 크기의 물체(Bounding Box)를 예측하였을 때, 박스 영역의 관점에서 (b)보다 (a)의 경우가 더 정확한 예측을 하였지만 L1 손실함수를 사용할 경우 (a)와 (b)가 손실함수에 유사한 기여를 하게 되어 모든 박스에 대해 균등하지 않게 학습이 이루어집니다. 
<center><img src="/assets/images/mse_problem.jpg" width="500" height="250"></center>

이러한 문제를 보완하기 위해 제안된 손실함수가 IOU기반의 손실함수들입니다. IOU 손실함수는 모델이 예측한 BB와 실제 물체의 BB가 겹치는 정도(IOU)와 반비례한 손실값을 사용하기에 다른 크기의 박스에 대해서도 공정한 손실값을 계산하게 됩니다. 
처음 IOU Loss가 제안되고 이를 발전시킨 Generalized-IoU(GIOU) Loss, Complete-IoU(CIOU) loss, Distance-IoU(DIOU) loss 가 제안되었습니다(자세한 내용은 아래 논문참고). 
 
저는 RetinaNet에 DIOU손실함수를 적용하여 실험해보았습니다. ResNet50, ResNet101,ResNet152의 3가지 backbone에 대해 실험을 해보았는데, 모두 기존의 L1손실함수로 학습한 모델보다 DIOU Loss로 학습한 모델의 정확도가 높았습니다. 
 ResNet50 backbone을 사용한 RetinaNet모델에 대해, 모델 평가시 최적의 threshold에 대한 모델 정확도는 약1% 증가하였고, mAP도 0.01 증가하였습니다. 
 특히 score threshold에 따른 모델의 정확도 변화가 줄어듦을 확인하여 모델이 L1손실함수를 사용하였을 때보다 A 불량에 대해 예측할 때, A불량이라고 더 확신을 갖고 예측한다는 것을 확인할 수 있었습니다. 이는 DIOU Loss함수가 Scale invariant하기에 다양한 크기의 물체에 대해 균일한 학습이 이루어질 수 있어서 모델의 정확도가 향상되었음을 의미합니다.         

 아래는 제가 RetinaNet에 DIOU Loss와 Label Smoothing을 적용해본 코드 링크입니다.
> [코드 링크](https://github.com/HyunjiClairePak/keras-retinanet)

결론 
==============
이번 포스팅에서는 YOLO v4에서 사용된 BoF 중 주요 몇가지에 대해 알아보고, 이를 RetinaNet에 적용하여 실험해본 결과에 대해 소개하였습니다. 
RetinaNet에서 실험한 결과, DIOU Loss는 모델의 학습 안정성을 높여 정확도를 향상시킴을 확인할 수 있었고 Label Smoothing은 그 효과를 발휘하지 못하였습니다. 

제 개인적인 의견으로는 DIOU Loss는 손실함수 식을 통해서 box scale invariance를 개선한다는 것을 증명할 수 있지만, 
Label smoothing기법은 regularization효과가 수식적으로 증명되는 것은 아니기에, DIOU Loss는 거의 모든 데이터셋에 대해 성능을 개선하는 효과가 있지만 Label Smoothing은 데이터셋에 따라서 효과가 없는 경우도 있는 것 같습니다.  
      
    
 
 참고 자료
-----------------------------------------------------------------------
 - [Bag of Freebies 관련 논문](https://arxiv.org/pdf/2004.10934.pdf)
 - [Label smoothing 관련 논문](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Szegedy_Rethinking_the_Inception_CVPR_2016_paper.pdf)
 - [Distance IoU loss 관련 논문](https://arxiv.org/pdf/1911.08287.pdf)