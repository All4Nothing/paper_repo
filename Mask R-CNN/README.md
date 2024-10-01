# Mask R-CNN
- ***Link :*** https://arxiv.org/abs/1703.06870

### Object Detection vs Semantic Segmentation vs Instance Segmentation

![1](https://github.com/user-attachments/assets/c75f1a28-9d4f-4af4-983c-de4287b4f751)

![2](https://github.com/user-attachments/assets/711972c5-9ee4-4db9-a5bb-b78761350f92)


**Object Detection**

- B(Bounding)Box + Classification : can separate, cannot segment
- Object Detection은 object가 있는 곳을 BBox(Boundar Box)로 표시하며, 각 객체를 구별한다. 대표적인 모델로는 Faster R-CNN이 있다.

**Semantic Segmentation**

- Segmentation + Classification : cannot separate, can segment
- Semantic segmentation은 모든 픽셀에 대해 카테고리를 정한다. 단, 이때 개별 객체는 구분하지 않는다. 예를 들어 고양이 두마리가 있는 사진에서 두 고양이의 픽셀은 모두 ‘고양이’라는 카테고리로 분류된다.

**Object Detection + Semantic Segmentation → Object Segmentation**

- FCN on BBOX!
- Object Segmentation은 Object Detection에서 가능한 separation과 Semantic Segmentation에서 가능한 segmentation을 합친 것으로, 픽셀단위로 객체를 구분하며, 개별 객체 또한 구분한다.

## FCN

![3](https://github.com/user-attachments/assets/be9b3236-c0ed-4ae1-831f-9b49423dc191)
![4](https://github.com/user-attachments/assets/1f548c99-ac4e-483c-bd23-b20f0be071fa)
![5](https://github.com/user-attachments/assets/fca8978b-a031-4e43-bd77-3869b7a22802)


- Fully Convolutional Networks, FCN은 semantic segmentation을 위해 제안된 모델로 크게 세가지 특징이 있다.
- 첫 번째, FCN은 semantic segmentation을 위해 고안된 첫번째 end-to-end 모델이다. End-to-end 구조는 모델의 input layer부터 output layer까지가 모두 학습가능한 nn으로 이뤄져있음을 의미한다. 기존 fully connected layer는 convolutional layer에서 출력된 feature map을 flattening하여 input으로 사용했기에 이미지의 공간 정보를 고려하지 않았다. FCN에서는 1x1 convolution 연산을 통해 각 픽셀 위치마다 채널축으로 flattening하여 각 위치에 해당하는 벡터를 각각 구한다. 이를 통해, 각 필터들이 하나의 weight column과 같이 동작하며 공간 정보를 고려할 수 있고, 채널 수 만큼의 feature map을 얻을 수 있다.
- 두번째, FCN은 또한 upsampling layer를 통해, 기존에 넓은 receptive field를 확보하기 위해 pooling을 진행하여 저해상도의 output을 얻게 되는 문제를 해결했다.
- 마지막으로, FCN은 skip-connection을 사용한다. FCN의 높은 레벨의 레이어의 경우 디테일한 부분들에 대한 특징, fine-grained한 특징에 대한 정보를 가지고 있고, 낮은 레벨의 레이어는 높은 레벨의 레이어보다 더 coarse한 레벨, 전반적이고 의미론적인 특징에 대한 정보를 담고 있다. Semantic segmentation을 위해서는 이 두 가지의 정보가모두 필요하다. 따라서 FCN은 skip-connection을 사용하여 낮은 레벨의 레이어에서의 feature map을 직접적으로 고려할 수 있도록 설계했다.

## R-CNN Family

![6](https://github.com/user-attachments/assets/ee991361-52ff-424f-bad7-0095845a04e8)


R-CNN 계열의 모델들은 Object Detection을 위해 고안된 모델들이다.

**R-CNN**

![7](https://github.com/user-attachments/assets/8d686f9b-0e46-4fce-94e0-a20d6a62a67e)


- R-CNN은 CNN을 object detection에 도입시킨 모델이다. R-CNN은 selective search를 사용하여 region proposal을 구하고, 이렇게 얻은 region들을 CNN을 통해 feature를 추출하고, SVM으로 classification하여 region에 대한 classification을 수행한다. 하지만, R-CNN은 region proposal 하나하나 마다 classification을 수행해줘야 하기 때문에 속도가 매우 느리다는 단점이 있고, end-to-end 구조가 아니기 때문에 학습을 통한 성능 향상에 한계가 있다.

**Fast R-CNN**

![8](https://github.com/user-attachments/assets/c0d046f2-f075-4dbc-9acf-4fffb771e9f8)


- Fast R-CNN은 R-CNN의 느린 속도를 개선하고자, 이미지 전체에 대한 feature를 한번에 추출하고, 이를 재활용하여 여러 object들을 탐지할 수 있도록 하였다. Fast R-CNN은 convolution layer를 통해 이미지 전체의 feature map을 추출하고, ROI(Region Of Interest) Pooling 기법을 사용해, feature map에서 ROI에 해당하는 부분만 추출한다. 이를 기반으로 FC layer를 거쳐 region에 대한 classification을 수행하고, bounding box regression을 수행해 더 정확한 bounding box를 얻는다. 그 결과 R-CNN보다 약 18배 빠른 속도를 달성할 수 있었지만, 여전히 region proposal을 위해 huristic한 방법을 사용하기에 성능을 크게 향상시킬 수는 없었다.

**Faster R-CNN**

![9](https://github.com/user-attachments/assets/d010e688-2fb2-40c2-be89-db848790082b)


- Faster R-CNN에서는 region proposal까지 neural network 기반의 방법을 활용하는 최초의 end-to-end object detection 구조를 제안했다. Faster R-CNN에서는 기존의 time-consuming selective search 방법이 아닌 Region Proposal Network(RPN)을 통해 region proposal을 수행한다. RPN은 sliding window 방식으로 각 픽셀의 위치마다 k개의 anchor box를 고려한다. Anchor box는 각 픽셀 위치에서 발생할 확률이 높은 bounding box들을 사전에 정의해둔 일종의 후보군이라 볼 수 있다. 각 픽셀 위치에서 256차원의 feature 벡터를 추출하고, 이 벡터를 입력으로 classification layer를 거쳐 object의 여부를 판별하는 2k개의 classification 점수를 출력하고, regresion layer를 거쳐 4k개의 좌표값을 출력한다.

## Mask R-CNN

![10](https://github.com/user-attachments/assets/13f0ae67-c85e-48ff-b668-387dfa85a3de)

- Mask R-CNN은 Instance Segmentation을 위해 고안된 모델로, 이름에서 알 수 있듯이 Faster R-CNN과 유사한 구조를 가지지만 몇가지 개선점을 가진다. Faster R-CNN의 경우 RPN의 region proposal 기반으로 ROI pooling 기법을 사용하였기 때문에 정수 좌표만 다뤘으나, Mask R-CNN에서는 ROIAlign이라는 새로운 pooling layer를 제안하여 interpolation을 기반으로 소수점 픽셀 수준에서의 pooling을 지원할 수 있게 되었다.
- 또한, Mask R-CNN에서는 기존의 Faster R-CNN의 classification, box-regression head와 더불어 별도의 mask branch를 추가하여, 하나의 bounding box에 대해 모든 클래스에 대한 binary mask를 생성하고, classification head의 예측 결과를 통해 어떤 mask를 사용할 것인지 결정한다.

### Mask Branch

![11](https://github.com/user-attachments/assets/1b626680-0acf-4289-b74f-ea87da3704fe)


- RoI가 input으로 들어오게 되면 FCN을 적용한 convolutional network를 통과하게 된다. 이때 마스크는 각 class마다 생성된다.
- 일반적인 FCN은 각 pixel에 해당하는 class label 값을 output으로 내는 반면, mask branch에 적용된 FCN은 물체가 존재하는지 여부에 대한 binary 값을 output으로 낸다. 따라서 기존 FCN과 다른 loss function을 사용한다.
    - Normal FCN: per-pixel softmax, multinomial cross-entropy loss
    - Mask branch FCN: per-pixel sigmoid, binary cross-entropy loss

**Mask R-CNN Loss**

![12](https://github.com/user-attachments/assets/0acb8ac4-e184-411a-9f68-c9ed47956b15)


- mask branch에서는 물체의 class에 따라가 아닌, 물체의 존재 여부에 따라 mask를 생성한다.

### RoIAlign

**RoI, Region of Interest**

**Feature Extraction**

![13](https://github.com/user-attachments/assets/af04ff4c-1106-4ab3-b456-ea34b548a12a)


- RoI를 찾기 위해 Fast R-CNN에서는 feature map을 추출한다. feature map의 사이즈는 input 사이즈를 32로 나눠, 이미지 정보를 압축한다. 위의 그림의 예시에서는 512x512x3을 → 16x16x512로 압축한다.

**Get RoIs from the Feature Map**

![14](https://github.com/user-attachments/assets/aae175b6-cd7a-4651-ac56-5c93a32bd5fe)


- 모든 RoI는 좌표와 사이즈로 이루어져 있다.

**Quantization of coordinates on the feature map**

- Quantization : process of constraining an input from a large set of values(like real numbers) to a discrete set(like integers)

![15](https://github.com/user-attachments/assets/0da11d67-5c55-46ed-a8a8-ca96fdec2904)


- 사진과 같은 예시를 보면 bbox의 사이즈는 145x200이며, top-left 좌표는 (192,226)이다. 우리의 feature map은 16x16인데, 위의 200, 145와 같은 숫자는 32로 나눠떨어지지 않는다.

![16](https://github.com/user-attachments/assets/a4307cbe-afd9-4985-a2d6-a771780ef0f9)


- 그래서 소수점은 버려, 결과적으로 사진의 파란색 부분의 정보를 잃게 되고, 초록색 부분에서의 새로운 정보를 얻게되며 이로인해 원래의 RoI를 사용하지 못한다.

**RoI Pooling**

![17](https://github.com/user-attachments/assets/19c3930d-fca8-46ca-bd8a-4209ac7c8007)
![19](https://github.com/user-attachments/assets/bee8f51f-277d-4e19-9e3a-2b87d0ab5c6f)


- 사이즈가 다른 각 RoI들의 크기를 고정된 사이즈로 맞춰주기 위해 pooling을 진행한다. Pooling을 진행하며 그림의 마지막 행과 같이 또 한번 정보를 잃게 된다.

**RoI Align**

![18](https://github.com/user-attachments/assets/91378e06-065d-4705-881b-4b644233d2b7)

![20](https://github.com/user-attachments/assets/e5709e05-d9ce-4cce-8a78-f248643e912b)


- RoI Align은 기존 RoI, RoIPooling의 정보 손실 문제를 해결하기 위한 새로운 방법론이다. Mask R-CNN은 Instance Segmentation task를 수행하기에 pixel간의 관계가 중요하다. 따라서 정보의 손실이 없어야한다. RoI Align은 quantization을 하지 않고, RoI 값을 그대로 사용한다. RoI의 범위를 3x3 사이즈에 맞춰 width와 height를 3등분하고, 각 등분된 구역을 4등분하여 bilinear interpolation을 계산해 각 지점의 값을 계산할 수 있다. 이제 4등분된 구역의 값을 pooling하여 3x3 feature map을 생성할 수 있다. 결과적으로 RoI Align은 quantization없이 pooling을 진행하여 정보의 손실없이 pooling을 진행할 수 있다.
