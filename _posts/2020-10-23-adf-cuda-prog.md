---
title: Cuda Programming 소개 - Image Noise Reduction Filter(ADF)구현 예제
<<<<<<< HEAD
=======
categories:
    - swdev
date: 2020-10-23 00:00:00 +0900
tags: [Medical Imaging, Image Processing]
    
comments: true
toc: False
---

안녕하세요. 오늘은 c/c++개발에서 코드를 GPU에서 실행하도록 하여, 코드의 수행속도를 가속화 할 수 있는 GPU programming에 대해 소개하려고 합니다.
먼저 GPU 프로그래밍에 대해 소개하고 이전에 제가 개발했던 코드를 통해 GPU 프로그래밍 방법에 대해 설명하겠습니다. 
제가 이전에 이미지 노이즈 제거 필터를 개발할 때 필터 수행속도를 가속화하기 위하여 GPU 프로그래밍을 했었습니다 ^^.
   

GPU Programming
=============================================
C/C++ 코드의 주요 연산을 GPU 연산장치에서 실행하기 위해 GPU 프로그래밍을 합니다. 
 C/C++에 GPU 프로그래밍 프레임워크(OpenGL, CUDA 등)를 사용하여 GPU 에서 수행될 수 있는 형태로 코드를 구현하면 됩니다. 
 가장 많이 사용되는 프레임워크는 CUDA와 OpenGL가 있는데, cuda는 nvidia에서, opengl은 MS에서 개발하였습니다. 
 저는 오늘 CUDA 프레임워크를 사용한 GPU프로그래밍을 설명하겠습니다.
 
C/C++ with CUDA Programming
======================================
CUDA 프로그래밍 방법을 살펴보기 전, 관련된 용어를 알아야 합니다. 
 
 용어 설명)
 - Host: CPU단을 지칭
 - Device: GPU단을 지칭
 
GPU 프로그래밍은 다음과 같은 순서로 구현되어야 합니다. 
 
    0) GPU가 여러개일 수 있으므로 코드를 실행할 Device 지정해줍니다. - GPU 하나인 경우 0으로 지정합니다.
        cudaSetDevice(0);
        
    1) 코드를 수행에 필요한 변수(destination)에 GPU 메모리 할당합니다.
        cudaMalloc(destinatino, number_of_bytes);
        
    2) GPU프로그래밍 대상인, destination에 host메모리에 있던 값(source)을 copy합니다.
        > cudaMemcpy함수로 host to device와 device to host 모두를 수행할 수 있으므로, 둘 중에 하나를 알려주는 키워드를 마지막 인자로 입력합니다.                
        
        cudaMemcpy(destination, source, number_of_bytes, cudaMemcpyDeviceToDevice);
        
    3) 디바이스 코드를 수행합니다. 
        host를 통해서 __global__ 키워드가 붙은 함수가 호출되어 device단에서 수행됩니다.
        __global__ void kernel_func(int *img, int w, int h){
            '''
        } 
        이때 __global__코드는 여러 개의 blocks, block내의 여러개의 thread에 의해 병렬적으로 수행되어야 하므로
        block수, thread수를 지정해주어야 합니다.
        
        kernel_func << N_blocks, N_thread>> (img, w, h);
        
    4) 디바이스 코드를 수행한 결과를 cpu메모리로 옮겨줍니다.
        cudaMemcpy(destination, source, number_of_bytes, cudaMemcpyDeviceToHost);
    
이미지 노이즈 제거 필터 구현 with CUDA programming
==============================================================
그러면 이제 제가 작성했던 CUDA기반 GPU프로그래밍 코드를 소개하겠습니다. 
CT 이미지를 분석하기 전, 노이즈 제거를 위한 Anisotropic Diffusion Filter를 구현한 코드입니다.
> 608 x 608 이미지 한장에 대해 ADF필터링을 20회 반복하였을 때 10 ms가 소요되었습니다. 

<script src="https://gist.github.com/HyunjiEllenPak/c5d7f724ffda2ea538526b9335d0fa4a.js"></script>
<script src="https://gist.github.com/HyunjiEllenPak/03e86e3b5035ab6f97f050f8f87438a9.js"></script>

> ADF의 동작 원리 및 효과에 대한 설명은 아래 자료를 참고하시길 바랍니다.
> - [Anisotropic Diffusion에 관한 논문](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=56205&casa_token=lGMbZUwyg_QAAAAA:wet0FAPlNt9xRuL_x0MvFr7C1amNUYUO1mJRa903go2muPZLHoxmtRnmpr3HVWBO6N8pZGfSC0Q&tag=1)
> - [ADF 파라미터에 관한 논문](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.449.948&rep=rep1&type=pdf)
> - [ADF 필터의 이미지 노이즈 제거 효과에 대한 논문](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=6008370&casa_token=iVcIZeAqTaMAAAAA:50L4ICOMYNkwSSYtdrv5daDC4rUA9OQj6fdN6hT87Bkqhw6i4tOFgi2JzwX7khGM24ql8Mg_WnI)

    
        
         
        
        
        
   
       
 
 
 
  
 
 
  