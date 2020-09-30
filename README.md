# DBPN-D-DBPN : https://arxiv.org/abs/1803.02735

#### <I studied while referring to various sites, but it is not enough. Thanks anytime for feedback.>
### <heejo5@naver.com>

Deep Back-Projection Networks For Super-Resolution
--------------------------------------------------
Iterative up and down sampling
------------------------------
  * 본 논문에서 제안하는 SR network
  * 다른 깊이와 분포에서 SR 특징의 sampling rate 올림
  * 각 단계마다 reconstruction error를 계산
  * 깊은 특징을 생성하여 HR 구성요소 보존


DBPN & D-DBPN Architecture
--------------------------
![image](https://user-images.githubusercontent.com/61686244/94676265-04f26f80-0356-11eb-9da7-c2cfa0818c35.png)
* DBPN의 Network 구조는 초기에 특징 추출 후 반복전인 Up sample, Down sample layer가 나오는 구조

Up-Projection Unit & Down-Projection Unit
-----------------------------------------
![image](https://user-images.githubusercontent.com/61686244/94676491-5f8bcb80-0356-11eb-9dcd-4eac02ef9d36.png)


* 앞에서 추출된 특징이 입력으로 Up projection unit에 들어와 Deconv를 통해 특징을 확장한 후에 Scale residual up과 특징을 다시 축소하기 위한 Conv로 들어가고 Conv를 통해 나온 L0t와 입력으로 들어온 Lt-1이 residual로 차를 이용하여 계산한 뒤 다시 특징을 확장하기 위해 Deconv를 통해서 나오게 됨
* 이렇게 나온 특징과 앞에서 저장한 scale residual up을 더해 최종 up projection unit의 outpu을 구해 down projection unit에 들어가게됨
* Down projection unit의 진행 과정 또한 up projection unit과 유사하게 진행이 되고, 각 unit마다 구해진 projection error를 feed-back하게됨
* Projection unit은 큰 사이즈 필터로 8x8 또는 12x12를 사용했는데 보통 SR Network 같은 경우에 큰 사이즈의 필터를 사용하게 되면 수렴 속도가 현저하게 감소가 되어 잘 사용하지 않았지만 이와 같이 반복적으로 projection Unit을 통해 error을 계속해서 feed back하기 때문에 사용이 가능했고 큰 scale factor에 대해서 가능

Dense-Projection Unit
---------------------
![image](https://user-images.githubusercontent.com/61686244/94677101-52231100-0357-11eb-803c-0cd7f507613c.png)
![image](https://user-images.githubusercontent.com/61686244/94676500-6581ac80-0356-11eb-8173-3e244d1af328.png)
* Up sample / Donw sample layer에 HR, LR img가 연결돼서 들어가게됨
* 계산량이 많아지는 것을 억제하기 위해 Bottleneck layer에서 사용했던 것과 동일하게 1x1 conv를 사용하여 필터의 크기를 유지하며 차원을 축소하여 사용 했다고 함
* Up sample에 들어가는 input이 이전의 Down sample의 output하나가 아닌 그 전에 있던 모든 Down sample의 output을 concat하여 사용해 기존의 DBPN보다 성능을 올림

DBPN & D-DBPN 공통
-----------------
* Initial extraction stage ( 2 layer)
* Up projection unit (T layer)
* Down projection unit (T-1 layer)
* Reconstruction (1 layer)
* 2x / 6x6 Conv / 2 stride / 2 padding
* 4x / 8x8 Conv / 4 stride / 2 padding
* 8x / 12x12 Conv / 8 stride / 2 padding

Depth analysis of DBPN, x4
--------------------------
![image](https://user-images.githubusercontent.com/61686244/94677644-38ce9480-0358-11eb-8aa4-7458a7f8971c.png)
* DBPN Network의 깊이에 따른 결과를 다른 network와 4x scale에서 비교 실험한 결과 
* 기존의 DBPN의 projection unit의 수를 각각 2, 4, 6으로 설정한 S M L Network
* S network 는 VDSR, DRCN, LapSRN보다 더 적은 12conv 를 사용하여 좋은 성능을 만들었음
* M network는 24개의 conv layer를 사용하여 VDSR, DRCN< LapSRN, DRRN보다 더 좋은 성능을 만들었고 추가적으로 LapSRN도 24 conv layer를 사용했고 DRRN은 52 conv layer를 사용 
* 3개의 Network중 가장 좋은 성능을 보인 L모델 같은 경우 36 conv layer를 사용했고 31.86 까지 성능을 향상

Depth analysis of DBPN, x8
--------------------------
![image](https://user-images.githubusercontent.com/61686244/94678075-e346b780-0358-11eb-9e4a-a9013c4b6e81.png)
* 결과는 8x scale에서 평가한 것으로 S M L 3가지 모델 모두 기존  Network 보다 성능이 잘 나왔지만 M과 L 모델은 0.04정도 밖에 차이가 나지않음

Number of Parameters
--------------------
![image](https://user-images.githubusercontent.com/61686244/94678104-f35e9700-0358-11eb-8855-69fad4023c79.png)

Up-prjojection units in D-DBPN
------------------------------
![image](https://user-images.githubusercontent.com/61686244/94678298-36206f00-0359-11eb-9cab-2accb18793ea.png)

Conclusions
-----------
![image](https://user-images.githubusercontent.com/61686244/94678373-4f292000-0359-11eb-884c-d4fd03ce345c.png)
* Error Feedback : up/down projection(sampling)의 error을 통해 reconstruction 결과를 좋게 만듦
* Mutually connected up/down sampling stages : 가능한 HR 특징을 다양하게 만들어 사용하고 다시 LR공간으로 projection
* Deep concatenation : up sampling 단계마다 생성되는 output을 연결하여 최종 HR img 복원
* Improvement with dense connection : dense connection을 추가하여 성능 향상(D-DBPN)




