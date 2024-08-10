- ***Link :*** https://arxiv.org/abs/1703.10593

# **Unpaired Image-to-Image Translation using Cycle-Consistent Adversarial Networks**

## Abstract

Image-to-Image translation 과제의 목표는 pair-image 데이터셋을 가지고 input image와 output image 사이의 mapping을 학습하는 것이다.

하지만, 많은 task에서 쌍을 이루는 데이터를 구하기는 쉽지 않다. 

따라서, 이 논문에서는 pair가 없는 이미지 샘플에 대해 source domain $X$에서 target domain $Y$로 이미지를 translate하는 모델을 학습하는 방법을 제시한다.

연구의 목표는 adversarial loss를 이용하여 $G$가 생성한 이미지 $G(X)$의 분포와 $Y$의 이미지가 구분되지 않도록, mapping $G:X\rightarrow Y$를 학습하는 것이다.

하지만 adversarial loss만을 이용한 mapping 학습은 제약이 부족하다(under-constrained). 따라서, cycle consistency loss를 도입하여 mapping $G$와 inverse mapping $F : Y \rightarrow X$가 서로 상호 보완적으로 작동하도록 즉, $F(G(x))\approx x \ (and \ vice\ versa)$되도록 한다. 

> $F(G(x))\approx x$의 의미는 뒤에 자세히 설명하겟다.
> 

그 결과 paired training data가 존재하지 않아도, collection style transfer, object transfiguration(객체 변환), season transfer, phto enhancement 등과 같은 문제에서 qualitative한 결과를 보여줬다.

## 1. Introduction

다음과 같은 사진이 있다고 해보자.
![1](https://github.com/user-attachments/assets/3fb9a261-1755-4349-ade4-da52badf7e0e)


‘반 고흐가 이 사진과 같은 풍경을  보고 그린다면 어떨까?’

반 고흐가 직접 이 풍경을 그린 그림은 없더라도, 우리가 아는 반 고흐의 그림 스타일들을 생각해볼 때 아마도 다음과 같은 그림이 나올거라 생각해볼 수 있다. 꽤 그럴사하다.

![2](https://github.com/user-attachments/assets/b2912d40-529c-4178-a324-183df2017761)

이 연구에서도 같은 접근 방식을 제안한다. 우리가 반 고흐가 그린 그림들을 보고 그 스타일을 생각해보는 것처럼, 한 이미지 컬렉션의 특성(characteristics)을 포착하고, 어떠한 pair-image도 없는 다른 이미지 컬렉션에 변환시키는 것이다.

지금껏 Image-to-image translation 문제는 pair-image가 존재하는 supervised 환경에서 강력한 translate을 자랑했다. 하지만, pair-image 데이터셋을 준비하는 건 꽤나 어렵다.
![3](https://github.com/user-attachments/assets/2dca3258-a43c-4a86-b8a6-99839ea0cfd4)


위 사진처럼 살제로 존재하는 이미지 쌍을 찾기는 어려울 것이고, 심지어, 같은 풍경 사진을 Monet나 Van Goah 스타일로 그려진 그림을 찾는다는 것은 불가능할 것이다. Monet와 Van Goah가 그린 그림들의 원래 풍경 사진들을 그대로 찾아내지 않는한..

그래서 이 연구에서는 paired input-output이 아닌, domain $X$와 domain $Y$에 대해, mapping $G  : X \rightarrow Y$를 학습시킨다. $G$는 $\hat{y}$와 $y$를 구분하는 adversary train을 통해 output $\hat{y} = G(x), x \in X$가 $y \in Y$의 이미지와 구분이 되지 않도록 학습한다.

하지만, input $x$와 output $y$가 의미있게 paired되지 않을 수 있다. 왜냐하면 $X$를 $Y$에 매핑하는 함수$G$가 매우 다양하게 존재할 수 있기 때문이다. 각각의 변환 함수는 서로 다른 방식으로 $X$를 이미지 $Y$로 변환할 수 있지만, 최종적으로 생성된 이미지들의 분포는 같을 수 있다. 

게다가 mode collapse 문제를 일으켜, 모든 이미지가 같은 output image로 매핑되어, 즉 같은 이미지만을 출력하는, 최적화에 실패하는 문제가 발생할 수 있다.

이 논문에서는 translation이 *“cycle consistent”* 만족해야함을 알아냈다. Cycle consistent란 예를들어, 영어로 된 문장을 프랑스어로 번역하고, 번역한 문장(French)을 다시 영어로 번역했을 때 원래의 영어 문장으로 돌아와야 한다는 것을 의미한다. 수학적으로 표현하자면, translator $G : X\rightarrow Y$와 translator $F:Y\rightarrow X$가 있다면, $G$와 $F$는 역의 관계이며, 둘의 mappings는  일대일 대응(bijections)이어야 한다. 이 방식을 mapping $G$와 $F$를 훈련하는데 적용하고, *cycle consistency loss*를 추가한다. cycle consistency loss는 $F(G(x)) \approx x$와 $G(F(y)) \approx y$가 되도록 조정하는 loss 함수이다.

- $F(G(x)) \approx x$ : input $x$에 대해, $G(x)$는 $\hat{y}$를 만들고, $F(G(x))$는 $\hat{y}$를 input으로 하여 $\hat{x}$를 만든다. 이렇게 최종적으로 만들어진 output이 초기 input $x$와 유사해지도록 하는 것이 목표이다.

이 cycle consistency loss와 adversarial losses를 결합하여 unpaired image-to-image translation의 전체 목표를 정의한다.

## 2. Related work

### Generative Adversarial Networks (GANs)

GANs이 성공할 수 있었던 핵심적인 이유는, 생성된 이미지가 실제 사진과 구분되지 않도록 하는 adversarial loss 아이디어 덕분이다.

### Neural Style Transfer

Neural Style Transfer는 image-to-image translation을 수행하는 또 한가지 방법이다. 다만, Neural style transfer는 ‘두 이미지 사이’의 변환을 수행한다면, 이 연구에서 하고자 하는 것은 ‘두 이미지 컬렉션 사이’의 대응 구조를 파악하여 mapping을 학습하여 이미지 변환을 수행한다.

Neural style transfer는 주로 한 이미지의 스타일을 다른 이미지에 합성하는 작업을 수행하지만, CycleGAN은 두 이미지 컬렉션 사이의 mapping을 학습하는 특성으로 인해 neural style transfer과 같은 single sample transfer methods로는 수행하기 어려운, ‘painting을 photo로 바꾸는 작업’이나, ‘객체 변형(object transfiguration)’과 같은 작업을 수행할 수 있다.

## 3. Formulation
![4](https://github.com/user-attachments/assets/bc8fe030-2257-4fa6-bdea-02b5322bceca)


모델의 궁극적인 학습 목표는 domain $X$와 $Y$사이의 mapping functions을 학습하는 것이다.

모델은 mapping 함수 ‘$G$’와 ‘$F$’를 포함하고 있는데, $G$는 $x_i \in X$를 $Y$ 도메인에 매핑하고, $F$는 $y\in y$를 $X$ 도메인에 매핑한다. 

또한, 기존 GANs과 마찬가지로 discriminator가 존재하는데, 여기서는 실제 이미지 $x$와 $F$가 만들어낸 이미지 $F(y)$를 구분하는 discriminator ‘$D_X$’와 이미지 $y$와 $G(x)$를 구분하는 ‘$D_Y$’ 두 개가 존재한다.

그리고 이 모델을 학습시키기 위한 목적 함수(objective)로 loss function ‘Adversarial Loss’와 ‘Cycle Consistency Loss’가 있다.

### 3.1 Adversarial Loss

Introduction에서도 말했듯, 우리가 생각하는대로 매핑이 학습 되지 않을 수 있다. 그래서 adversarial losses 만으로는 우리가 원하는 결과를 얻을 수 없기에, cycle consistency loss를 도입한다. Cycle consistency는 $x\rightarrow G(x) \rightarrow F(G(x)) \approx x$가 성립하는 즉, input data $x$를 image translation한 결과를 다시 반대로 translation하였을 때 원래의 $x$로 돌아와야 한다.

마찬가지로 $F$에 대해 $y \rightarrow F(y) \rightarrow G(F(y)) \approx y$가 성립해야 한다.

따라서 이를 위해 cycle consistency loss를 다음과 같이 표현한다.

$L_{cyc}(G,F)=E_{x\sim p_{data(x)}}[||F(G(x))-x||_1] + E_{y\sim p_{data(y)}}[||G(F(y))-y||_1]$ 

다음 사진을 보면 cycle consistency가 잘 학습된 모습을 볼 수 있다.

![5](https://github.com/user-attachments/assets/9286d002-4a7e-4f86-be7a-7999b062a355)

### 3.3 Full Objective

Adversarial loss와 cycle consistency loss를 합친 full objective는 다음과 같다.

$L(G,F,D_X,D_Y)=L_{GAN}(G,D_Y,X,Y)+L_{GAN}(F,D_X,Y,X)+\lambda L_{cyc}(G,F)$

여기서 $\lambda$는 adversarial loss와 cycle consistency loss 사이의 상대적 중요도에 따라 정해진다.

이 연구에서는 $\lambda = 10$으로 진행하였다.

이 모델의 학습 목표는 $G^*,F^*=arg \ min_{G,F}\ max_{D_X,D_Y} \ L(G,F,D_X,D_Y)$로 표현할 수 있다.

### 6. Limitations and Discussion
![6](https://github.com/user-attachments/assets/6cb28246-3a76-44e2-a98e-ab62b14db691)


이 모델은 Color나 texture의 변화는 잘 수행하지만, 위 사진처럼 강아지를 고양이로 바꾸는 geometric한 변화를 수행하기는 한계가 있다. 말과 얼룩말은 서로 비슷하여 변환하기 쉽지만, 개와 고양이는 생긴 형태가 크게 달라 변환하기 어렵다.

또한, 학습 데이터셋의 특성에 의해 변화에 제약을 받는다. 예를 들어 야생의 말 데이터셋(말이 단독으로 있는 이미지)과 얼룩말 데이터셋을 가지고 학습하면, 말에 사람이 타있는 사진에서 horse → zebra task를 수행하는데에는 어려움을 겪는다.

## 7. Appendix

이 논문의 section 7에는 각각의 데이터셋을 학습할 때의 디테일들이 잘 나와있다. 모델을 구현해볼 때 참고하면 좋을 것 같다.

이 논문에서 제시한 모델 구현 코드이다.

https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix

https://github.com/junyanz/CycleGAN

Cycle GAN의 결과물들을 감상하며 글을 마친다.
![7](https://github.com/user-attachments/assets/775756b8-b1c9-4ee5-a823-8e5a066c1487)
![8](https://github.com/user-attachments/assets/b2222829-f09c-496d-8b70-f9cb170d4f35)
![9](https://github.com/user-attachments/assets/139c83aa-4e1c-4535-8b32-035baf710f04)
![10](https://github.com/user-attachments/assets/7ce02f94-9e38-47a9-8915-c468a06d079c)
![11](https://github.com/user-attachments/assets/37ad196c-2dc4-4e88-9f79-f4c47917d223)
![12](https://github.com/user-attachments/assets/e6f84b11-bcc5-4240-b322-d5e9beb70e35)
![13](https://github.com/user-attachments/assets/57fc1f59-a6ee-4b51-9c0d-5cfb9576e0ae)
