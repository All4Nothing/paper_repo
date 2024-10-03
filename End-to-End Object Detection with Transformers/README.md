# **End-to-End Object Detection with Transformers**

- ***Link :*** https://arxiv.org/abs/2005.12872

![1](https://github.com/user-attachments/assets/66d45eeb-4b7f-4de6-8fb7-a396262559c3)

### 💡 이 논문의 장점은?

- 기존의 객체 탐지(Object detection) 기술과 비교했을 때 매우 간단하며 또한 경쟁력 있는 성능을 보인다.

### 💡 이 논문이 제안한 것은?

- DEtection TRansformer, DETR은 Bipartite matching loss function(이분 매칭 손실 함수)를 제시했고, Transformer를 이용한 object detection task를 수행한다.

## Overall training procedure

1. Extract feature map by CNN backbone
    - CNN을 backbone network로 사용하여 이미지의 features를 추출한다.
2. Add Positional Encoding
    - 이미지에서 상대적인 위치 정보를 담기 위해 positional encoding을 추가하여 encoder로 들어간다.
3. Generate Object queires
4. Output encoder memory by Transformer encoder
5. Output embedding by Transformer decoder
6. Class prediction by Class head
7. Bounding box prediction by Bounding box head
8. Match prediction with ground truth by Hungarian Matcher
9. Compute losses

## Background

- 기존 object detection은 주로 pre-defined anchor을 사용한다. 이는 이미지 내 고정된 지점마다 다양한 scale, aspect ratio를 가진 anchor를 생성하고, anchor 기반으로 생성한 예측 bounding box와 ground truth를 매칭한다. 이때 ground truth와의 IoU값이 특정 threshold 이상일 경우 positive sample로 간주하며, positive sample에 대해서만 bounding box regression을 수행한다. 이처럼 하나의 ground truth에 대해 다수의 bounding box가 매칭되는, 예측 bounding box와 ground truth 간의 many-to-one 관계가 성립한다.

## Bipartite Matching

![2](https://github.com/user-attachments/assets/a009ae87-2bdb-4f2c-b466-808f0cea4ace)  

- 기존 object detection 방법들은 너무 복잡하며 다양한 라이브러리를 활용한다. 또한, bounding box의 형태, bounding box가 겹칠 때의 처리 방법과 같은 prior knowledge(사전 지식)가 요구된다. 예를 들어, 탐지하고자 하는 object가 기차와 같이 긴 물체일 경우 bounding box를 길게 설정한다던가 하는 것이다.
- 또한, 하나의 ground truth를 예측하는 다수의 bounding box가 존재하기 때문에 이러한 near-duplicate한 예측, redundant한 예측을 제거하기 위해 NMS(Non Maximum Supppresion)과 같은 post-processing 과정이 반드시 필요하다.
- 이 논문에서는 이러한 문제를 bipartite matching(이분 매칭)을 통해 set prediction problem을 직접적으로 해결한다. 여기서 set은 수학에서 말하는 집합이다. 집합은 중복되는 원소가 없고, 원소의 순서 또한 상관이 없다.
- 이미지에서 탐지할 object의 개수를 고정해두면, 이분 매칭을 수행할 수 있다.
![3](https://github.com/user-attachments/assets/42b3e57b-f8cf-47cb-a591-1f6932d16555)  
- 이때, Hungarian algorithm을 사용하여 loss가 가장 작게 나오도록,  ground-truth와 prediction사이의 이분 매칭한다.

### Generalized Intersection over Union, GIoU

- GIoU loss는 두 box 사이의 IoU값을 활용한 loss로 scale-invariant(척도 불변)하다는 특징이 있다.
- GIoU를 구하기 위해서는 predicted box $b_{\sigma(i)}$와 ground truth box $\hat{b_i}$를 둘러싸는 가장 작은 box $B(b_{\sigma(i)},\hat{b})$를 구한다. 이때, predicted box와 ground truth box가 많이 겹칠수록 $B(b_{\sigma(i)},\hat{b})$가 작아지며, 두 box가 멀어질수록 $B(b_{\sigma(i)},\hat{b})$가 커진다.
- $IoU(b_{\sigma(i)},\hat{b})$는 두 box 사이의 IoU를 의미하며, $\frac{|B(b_{\sigma(i)},\hat{b}| \setminus b_{\sigma(i)}\cup \hat{b_i} }{|B(b_{\sigma(i)},\hat{b}|}$는 $B(b_{\sigma(i)},\hat{b})$에서 predicted box와 ground truth box를 합한 영역을 뺀 영역에 해당한다. GIoU는 -1에서 1 사이의 값을 가지며, GIoU loss를 사용할 때는 1-GIoU 형태로 사용한다.
- $L_{box}(b_{\sigma(i)},\hat{b})=\lambda_{iou}L_{iou}(b_{\sigma(i)},\hat{b})+\lambda_{L1}||b_{\sigma(i)}-\hat{b}||_1$, $\lambda_{iou},\lambda_{L1}$은 두 term 사이를 조정하는 scalar hyperparameter

## Transformer
![4](https://github.com/user-attachments/assets/76d6afe9-1baf-4ba5-b8b7-b371896d713c)  

DETR에서 사용하는 Transformer와 NLP task에서 사용하는 Transformer는 차이점이 있다.

1. Transformer는 encoder에서 문장에 대한 embedding을 입력받는 반면, DETR은 이미지 feature map을 받는다.
2. Transformer는 decoder에 target embedding을 입력하는 반면, DETR은 object queries를 입력한다.
3. Transformer는 decoder에서 첫 번째 attention 연산 시 masked multi-head attention을 수행하는 반면, DETR은 multi-head self-attention을 수행한다.
4. Transformer는 decoder 이후 하나의 head를 가지는 반면, DETR은 두 개의 head를 가진다.


### Encoder

![5](https://github.com/user-attachments/assets/3c9c86af-36bb-4320-8653-ab85e576b72b)  

- Encoder는 $d \times HW$크기의 연속성을 띠는 feature map을 입력으로 받는다. 이때 $d$는 image featuref를 의미하고 $HW$는 각각의 픽셀 위치 정보를 담고 있다.
- Encoder의 self-attention map을 시각화해보면 개별 인스턴스를 적절히 분리하는 것을 확인할 수 있다.

### Decoder

![6](https://github.com/user-attachments/assets/3be1186b-11d2-47d4-b53a-e3f70031e9cb)  

- Decoder는 $N$개의 object query(학습된 위치 임베딩)를 초기 입력으로 이용한다. 인코더가 global attention을 통해 인스턴스를 분리한 뒤에 디코더는 각 인스턴스의 클래스와 경계선을 추출한다.
