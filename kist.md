 물체 인식 역사(YOLO)
컴퓨터 비전 - 컴퓨터가 사람과 같이 인식하도록
방식 | 이진수의 패턴을 찾고 학습해서 새로운 이미지의 패턴을 분석하여 이미지를 인식
목표 | 이미지에서 우리가 원하는 물체를 끌어오는것

1. 어떤 물체를 끌어올 것인가?
2. 어떻게 끌어올건가
3. 모델은?
4. 계산은?

## 물체 인식
	영상 분류 classification(어떤 사진인지), 물체 감지 object recognition (위치까지), object segementation(픽셀 위치까지)  

identification - 개별 물체에 대해서 식별 (상위)
categorization - 물체의 종류를 인식 (하위)

### 고려할 점
- 각도 (viewpoint)
- 크기 (scale)
- 조명 (illumination)
- 모양의 변형 (deformation)
- 일부가 가려졌을 때(Occlusion)
- 계산량 과다(background Clutter) - Region Of interest ROI(필요한 부분만)
- 보호색 , 헷갈리는 경우(camouflage)

template matching - 대조해서 스코어를 매김, 가비지 값들이 많음
template matching의 한계점을 딥러닝이 해결

global features - 스케일이 다른 경우, 각도가 다른 경우 한계가 발생 -> local로 측정


local features 의 중요한 목록들
- repeatabillity 
- distinctiveness 
- compactness and efficent
- loaclity 주변과 연관 가능한 특징

### **harris corner detector**
	 물체를 약간 움직이며 변화 값을 구하여 코너를 찾는 것

- flat
- edge
- corner

강점
-  rotation
-  밝기
약점
- scale

--- 

-> scale에 대해서 새로운 feature를 설계


###  **SIFT**
	 position 뿐만 아니라 scale까지 변수 2차 미분(1차 미분 값의 차)
방향성을 갖게 되어 비교할 때의 성능 향상

### **SURF**
	 SIFT보다 조금 빠른 feature 2차 미분

색이 없는 물체의 경우 feature가 뽑아 나오지 않음( 인식불가)

---

-> 다시 코너에 대해서 분석
Fast corner detector 
원을 그려서 주변 함수들을 분석하여 중심 화소가 코너인지 아닌지 분석

코너가 존재하지 않을 경우(원형)에 대해 한계

-> 경계를 찾아서 샘플링

sampled points from contour fragments



---

spectral matching
각 사진에서 나온 feature 들을 매칭해야함

k-d tree 를 통해 필요없는 값을 버리고 통해 값이 비슷한 feature를 찾음

spectral theory
	eigenvector 와 eigenvalue를 이용하는 방법에 대한 이론
	축을 찾아서 축에서 벗어난 데이터들은 버림

매칭에 대한 정확도 
True positive, True negative, False negative, False positive 를 통해 정확도를 찾음
 **단**, 물체가 있는 영상과 없는 영상을 비슷하게 비교해야함

  -> 맞는 영상을 통해 정확도를 구하자 (F1 score)
  recall, precision (물체가 있는 영상에 있다고 했는지 에 대한 정확도)

---

하지만 딥러닝이 시작되면서 feature들을 구분하는 방식에 대한 허들이 낮아짐

|     | machine learning | deep learning                                                                                               |
| --- | ---------------- | ----------------------------------------------------------------------------------------------------------- |
| 특징  | top-down         | bottom-up \ 전체적인 모듈의 성능이 좋아지도록 만들어야 함. (End-to-End learning) \ No-hand-craft(데이터만 있어도 만들 수 있음) 네트워크가 알아서 분석 |

 bottom-up 의 특징 때문에 하드웨어의 사양이 올라가면서 deep learning의 성능이 증가

---

**Convolutional Neural Network (CNN)**

End-to-End
convolutional filtering 
	필터를 통해 윈도우 사이즈 만큼의 주변의 데이터의 가중치를 통해 관계를 알아냄

### **ImageNet Challenges**
**LeNet-5**
7 layer 1M parameters

**ImageNet Challenge**
딥러닝 대회에서 deep learning을 통해 deep learning 열풍

**AlexNet**
60M parameter

**ZFNet**

-> 레이어가 많아질 수록 역전파 시에 앞부분의 학습 성능이 떨어짐

**GoogLeNet**

**VGGNet**
138M parameter
높은 계산량 요구

**ResNet**
skip connetions (크기가 줄지 않고 역전파를 시켜 약점을 강화)

과적합 (한가지 분야에 너무 학습 되어 있다면 다른 변동성을 가진 데이터에 대해 큰 오차 발생)



