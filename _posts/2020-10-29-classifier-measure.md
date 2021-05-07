---
title: Classification Model 성능 평가지표 - Precision, Recall, F-score, Accuracy, ROC curve, AUC, Lift Curve,...
categories:
    - DL tech
date: 2020-11-04 21:00:00 +0900
tags: [Machine Learning]    
comments: true
use_math: true
toc: False
---
안녕하세요. 오늘은 classification Model의 평가지표에 관하여 알아보고자 합니다. 
classification model을 평가하기 위한 지표에는
Accuracy, Precision, Recall, F1-score, ROC-curve, AUC 가 있습니다. 

쉬운 설명을 위해, 제가 지금 병원의 연간 암 검진 데이터로 암환자를 분류하는 분류모델을 만든다고 가정을 하겠습니다.  
  
1. Accuracy  
    - 모든 데이터 중에서 모델이 옳게 예측한 것의 비율을 의미합니다. 
    -$ \frac{TP+TN}{TP+FP+TN+FN}$
 
2. Precision
    - 모델이 암 환자라고 예측한 것들 중에서 실제로 암 환자인 사람의 비율을 의미합니다.   
    - $\frac{TP}{TP+FP}$

3. Recall
    - 실제 암 환자 중에서 모델이 암 환자라고 예측한 사람의 비율을 의미합니다. 
    - $\frac{TP}{TP+FN}$

4. F1 score
    - $F1-score = \frac{2}{\frac{1}{precision}+\frac{1}{recall}}$
    - precision과 recall의 조화평균(역수의 평균의 역수)입니다. 그 의미는 precision과 recall의 평균을 계산하는데, 둘 중에서 더 작은 값의 영향을 더 많이 받은 평균값입니다.   

5. ROC curve
    - 확률값에 대한 threshold값 변화에 따라 그려진 true positive rate(sensitivity) - false positive rate(specificity)그래프를 말하며 커브가 좌상단에 붙어있을수록 성능이 좋은 모델임을 의미합니다
    - 여기서 말하는 threhold는 예를들어, 강아지 이진분류기에서 모델이 예측한 확률값에 대해 보통 0.5를 넘으면 강아지다라고 하는데, 이 값을 나타냅니다.
    - Sensitivity= Recall
    - Specifity = \frac{TN}{TN+FP}
    
6. AUC (Area Under Curve)
    - precision-recall(PR)그래프나 ROC 그래프에서 curve아래의 면적을 의미합니다. 최대값은 1이고 좋은 모델일 수록 1에 가까운 값을 나타냅니다.
    - PR그래프에서 AUC는 AP값을 나타냅니다.
  
7. Lift Curve(향상도 커브) 
    - Lift = $ \frac{P(B|A)}{P(B)} $ - 맨땅에 B일 확률 분에 A에도 해당되면서 B에도 해당될 확률의 비 
    > 위의 암환자 예시에서, Lift 값은 $\frac{모델이 환자라고 분류한 것 중에서 실제 암 환자의 비}{전체 데이터 중에서 암 환자의 비}$를
  의미합니다.  
    - Lift Curve는 샘플 데이터 수를 늘려가면서 계산한 Lift값을 그린 그래프입니다.- ?.

8. Matthews correlation coefficient(MCC)
    - $\frac{TN x TP - FP x FN}{\sqrt{(TN + FN)(FP + TP)(TN + FP)(FN + TP)}}$
    - TP, TN뿐만 아니라 FP, FN도 모두 포함한 지표이므로 Positive와 Negative모두 잘 예측해야 높은 수치를 나타냅니다.
     
9. Logarithmic Loss
    - Cross entropy Loss 와 같이 실제 확률과 예측 확률 분포의 차이에 따라서 패널티를 주는 로스함수입니다. 
    