- ***Link :***  https://arxiv.org/abs/1411.1784

# Conditional Generative Adversarial Nets

## Abstract

이 연구에서는 조건부 GAN(CGAN)을 소개한다. CGAN은 generator와 discriminator에 조건부 정보 $y$를 입력으로 제공하여 구성된다.

## Introduction

기존의 GAN은 생성할 데이터를 통제할 수 없지만, 이 논문에서는 Conditional GAN을 통해 조건에 맞는 데이터를 생성하는 즉, data generation process를 통제하는 방법을 소개한다. 여기서 말하는 조건은 class label이나, 복원할 데이터의 일부 또는 다른 형식의 데이터(different modality)가 될 수 있다.

## 3 Conditional Adversarial Nets

### 3.1 Generative Adversarial Nets

GAN은 생성 모델을 학습하는 참신한 방법이다. GAN은 두 개의 적대적(adversarial) 모델인 generator $G$와 discriminator $D$로 구성되어 있다.

$G$와 $D$는 동시에 학습이 진행되며, $G$는 $log \ (1-D(G(z)))$를 최소화하도록, $D$는 $log \ D(X)$를 최소화하도록 학습된다. 

$\underset{G}{min}\ \underset{D}{max}V(D,G) = E_{x\sim p_{data(x)}}[logD(x)]+E_{z\sim p_{z(z)}}[log(1-D(G(z)))]$

GAN에 대한 자세한 설명은 [**Generative Adversarial Nets**]([https://github.com/All4Nothing/papers-repo/tree/main/Generative Adversarial Nets](https://github.com/All4Nothing/papers-repo/tree/main/Generative%20Adversarial%20Nets))

### 3.2 Conditional Adversarial Nets
![1](https://github.com/user-attachments/assets/491e2786-e846-49a6-86ba-d41c0a3689cb)


GAN은 generator와 discriminator가 모두 추가 정보인 $y$에 대해 conditioned on하여 condtional model로 확장할 수 있다. $y$는 class labels나 다른 형식의 데이터(other modalities)와 같은 보조적인 정보가 될 수 있고, 이를 discriminator와 generator에 추가의 input layer로 넣어줌으로써 조건을 부여할 수 있다.

Generator에서는 사전 입력 노이즈 $p_z(z)$와 $y$가 hidden representation으로 결합하고, discriminator에서는 $x$와 $y$가 입력과 discriminative function에 들어간다.

$\underset{G}{min}\ \underset{D}{max}V(D,G)=E_{x\sim p_{data}(x)}[log \ D(x|y)] + E_{z\sim p_z(z)}[log \ (1-D(G(z|y)))]$

## Experimental Results
![2](https://github.com/user-attachments/assets/3cf8c3e8-0170-4de2-bd72-59ee9f994d07)


그림을 보면 각 행마다 MNIST digits에 맞게 이미지가 생성되었음을 볼 수 있다.
