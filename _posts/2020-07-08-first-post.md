---
title: Naver Clova Paper Review - Semi-supervised Feature Level Attribute Manipulation for Fashion Image Retrieval
categories:
    - DL tech
date: 2020-07-08 01:12:58 +0900
tags: 
    - Image Retrieval
    - Deep Learning
    - Naver Search & Clova    
comments: true
toc: False
---

- 네이버 쇼핑 렌즈는 사용자가 관심있는 패션 아이템 사진을 촬영하면, 이를 입력으로 받아서 이와 동일한 제품을 찾아주는 네이버의 서비스.

- 이 스마트 렌즈 서비스에는 AI Image Retrieval 기술이 사용된다. 

- 네이버 Search&Clova에서 패션 이미지 검색을 위한 연구를 진행하면서, BMVC라는 Computer Vision Top Conference에 작년(2019)에 낸 논문이 있다. 

	> "Semi-supervised Feature Level Attribute Manipulation for Fashion Image Retrieval"

	> 이에 대해 알아보자.

 

논문 리뷰 
==============

Fashion Image Retrieval
------------------------

- 이미지 내에서 패션 아이템을 찾고 해당 BB에 대한 Retrieval을 하여 동일한 이미지를 찾는 것이 Fashion Image Retrieval(FIR)이다.
- 동일한 이미지를 찾는 것(FIR) 뿐만 아니라, 사용자들은 내가 가진 패션 아이템에서 특정 속성만 변화시킨 아이템도 찾고 싶어한다. 따라서 이를 위해 패션 이미지에서 패션 아이템의 색상, 패턴 등만 바꾸는 Fashion Attribute Manipulation(FAM)기술이 요구된다. 
- 기존의 방법들은 FIR만을 위한 방법, FAM을 위한 방법으로 각각을 따 수행하는 방법들이었으나, 이 논문에서는 FIR + FAM을 합하여, 패션이미지의 특정 속성을 변경한 이미지를 검색할 수 있는 방법론을 제시하였음.
- FAM방법들은 attribute기반 retrieval을 위해, attribute label에 의한 pair를 만들어야 하는데, positive pair일 것이라고 묶어논 것들이 positive pair가 아닐 가능성이 있어서, Attribute manipulation없이 pure한 패션 이미지 검색을 하는 것에 비해, 약간의 성능 저하가 발생함. 
- 그러면, 성능 감소가 일어나지 않게 하기위하여, FAM과 FIR모델을 따로 구성하는 건 어떨까? 수백만장의 이미지를 다루는 real world problem에서 두 개의 모델을 구성하는 것은 매우 낭비.
- 이 논문의 contributions은 다음과 같다.
    1. feature level에서 query를 manipulating함에 의해서 패션 아이템의 특성을 변경함과 동시에 변경한 이미지를 검색하는 성능의 저하가 일어나지 않도록 하였음 
    2. attribute 학습을 위한 dense annotation을 필요로 하지 않음
    3. attribute manipulation을 semi supervised방식, generative model로 학습을 할 수 있음을 보였음

방법론 설명
------------
0. 사전 작업: Fashion Image Retrieval(FIR)을 우수하게 수행하는 네트워크를 사용하여 이미지 origin feature를 추출함.
  
1. Embedding stage
    - Attribute-specific embedder(E): 이미지의 origin feature에서 attribute를 추출하는 네트워크
        > attribute dictionary: attribute에 따른 feature dictionary 준비
                
        > feature space상에서 이미지의 embedding과 이 이미지와 같은 색상에 대한 dictionary embedding사이의 간격을 학습함.
        
2. Manipulating stage: real feature의 distribution을 따르도록 manipulated feature를 학습하는 것이 목적임.
    -  Generator와 Discriminator가 adversarial loss로 학습
    -  Generator(G): 이미지의 origin feature 와 이 이미지의 attribute embedding들과 변경하길 원하는 attribute(=manipulated attribute)의 embedding을 사용하여, fake feature를 만드는 generator를 학습함.
        > origin feature와 manipulated attribute를 concatenate하여 generator의 입력으로 넣음. 
        
        > 간단하게 manipulated feature의 distribution과 real data의 distribution을 matching함에 의해서 manipulated feature를 합성함?
    - Discriminator(D):
        > color특성에 대해, 앞서 학습한 E 네트워크는 고정한 상태로, 이미지의 origin feature와 origin feature에서 color를 변경한 fake feature가 주어질 때, 이에 대한 E의 embedding을 획득하고, 이에 대한 D의 출력 embedding을 획득하여 D가 이 둘의 차를 학습하는 작업과 주어진 feature가 fake/real인지 학습하는 작업을 수행하도록 함. 
3. 이렇게 학습한 후, 패션 아이템의 attribute를 바꾸어 이미지 검색을 하고자 할때, 예를들어 색상을 빨->초로 변경하려고 하면, 초록색에 대한 dictionary feature의 attribute와 origin feature를 generator의 입력으로 주어, manipulated feature를 획득한 후, FIR을 수행하여, 유사 패션 아이템을 찾는다. 

