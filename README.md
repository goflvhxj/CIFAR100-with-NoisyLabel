# CIFAR100-with-NoisyLabel
Mission : [kaggle](https://www.kaggle.com/c/cifar100-image-classification-with-noisy-labels/data) : CIFAR100 image classification with Noisy Labels<br/>

진행 시기 : 2023.04
<br/> 
<br/>

## Summary
크게 3가지의 과정으로 진행<br/> 
1. Model tuning
2. Anomaly Detection
3. Robust Modeling
<br/>

## Process
### **1. Model tuning**
**ResNet 50 Pretrained Model**
- NoisyLabel로 인해 test acc 70% 부근에서 overfit 발생
- Pretrained Model로 ImageNet에서 test data가 대부분 학습된 것으로 추정
- 모델을 직접 구현하기로 결정
<br/>

**ResNet 18 직접 구현**
- label smoothing, dropout 추가, normalize 등 tuning을 진행하였지만 test acc 50% 부근에서 overfit 발생
- noise label data를 미리 제거하는 것으로 결정
<br/> 

### **2. Anomaly Detection**
**k-means**
- 목적 : clustering 통해 outlier 제거
- 각 데이터와 중심점 사이의 거리가 먼 상위 5% 이미지 제거
- 이미지 제거 후 ResNet18 구현 모델 학습 시 여전히 test acc 50%에서 overfit 발생

**cleanlab**
- cleanlab은 데이터셋에서 이상치를 판단하여 이상치 인덱스를 반환해주는 라이브러리
- 이상치를 탐지하는 modeling 만드는 과정에서 MLP Model은 인식을 하지만 CNN 모델은 인식을 하지 못함
- MLP Model로만 진행하였을 경우 너무 많은 이상치가 탐지되어 cleanlab library 사용하기 어렵다고 판단
- 이상치를 탐지하여 제거하는 것 보다 이상치에 강한 robust model을 구축하기로 결정
<br/>

### **3. Robust Modeling**
["Probabilistic End-to-end Noise Correction for Learning with Noisy Labels"](https://arxiv.org/abs/1903.07788), [github](https://github.com/yikun2019/PENCIL)
<br/>
<br/>

**The framework of PENCIL**
<p align="left">
  <img src="https://github.com/yikun2019/PENCIL/raw/master/framework.png" width="500" height="400">
</p>
<br/>

**Result**<br/>
params : epoch : 320, alpha : 0.01, beta : 0.1, gamma : 500, lr : 0.01, batch_size : 128, k = 100, model : resnet56<br/> 
<p align="left">
  <img src="https://user-images.githubusercontent.com/103241965/232204979-33aec909-4901-46e8-9253-1dbfd0196191.png" width="500" height="400">
</p>
- test acc가 약 70% 정도로 
