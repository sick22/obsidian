# Faster R-CNN: Region Proposal 네트워크와 전체 구조 설명

---

## Step 1: 전체 개요 - 왜 Region Proposal이 필요한가

딥러닝 기반 객체 탐지(Object Detection)에서 모델은 이미지 안에 **무엇이 있는지**(분류)와 **어디에 있는지**(위치)를 동시에 판단해야 함.

이를 위해 필요한 요소는 다음 두 가지임:

1. **분류(Classification)**: 어떤 물체인지  
2. **위치(Localization)**: 물체가 어디 있는지 → 이게 **Region Proposal**임

---

## Step 2: Region Proposal이란?

**Region Proposal**은 입력 이미지에서 **물체가 있을 법한 후보 영역(Bounding Box)**들을 자동으로 찾아내는 과정임.

기존 방식 (Sliding Window, Selective Search 등)은 느리고 비효율적이었음.  
→ 그래서 **딥러닝 기반 RPN (Region Proposal Network)**이 등장함.

---

## Step 3: RPN (Region Proposal Network)이란?

RPN은 CNN 기반으로 Region Proposal을 자동으로 예측하는 네트워크임.

**특징:**

- CNN 구조 안에 포함되어 있음  
- feature map을 기반으로 anchor 위치에서 proposal 생성  
- Faster R-CNN의 핵심 구성 요소임

---

## Step 4: RPN의 동작 과정

### 1. Backbone CNN

- 입력 이미지 → CNN (예: VGG16, ResNet) → feature map 생성  
- 예시:  
  - 입력 이미지: 600×800  
  - feature map 크기: 38×50  

### 2. Sliding Window

- feature map 위에서 고정된 크기 (3×3)의 윈도우를 슬라이딩하며 탐색  
- 각 위치에서 물체가 있을지 예측함

### 3. Anchor Boxes

- 각 위치마다 다양한 크기와 비율의 anchor box 설정  
  - 예: 3개의 스케일 × 3개의 비율 = 총 9개 anchor  
- 각 위치마다 9개의 bounding box 후보 생성됨

### 4. Conv Layer 분기

3×3 Conv 이후, 1×1 Conv로 두 가지 분기 출력 생성:

1. **Objectness Score (Classification)**  
   - 각 anchor가 물체일 확률 예측  
   - 출력: 2k (k는 anchor 개수)

2. **BBox Regression (회귀)**  
   - anchor를 GT에 맞게 보정하는 값 (tx, ty, tw, th)  
   - 출력: 4k

---

## Step 5: RPN Conv Layer 구조 요약

| Layer 이름          | 크기               | 역할                                      |
|--------------------|------------------|-----------------------------------------|
| 3×3 Conv           | (3×3, 512 filters) | feature map 위에서 지역 특징 추출           |
| 1×1 Conv (cls)     | (1×1, 2k filters)  | 각 anchor에 대해 물체 여부 예측            |
| 1×1 Conv (reg)     | (1×1, 4k filters)  | 각 anchor에 대해 bounding box 보정 예측    |

---

## Step 6: Post-processing - NMS

- anchor 중 겹치는 proposal 제거  
- 점수 높은 것만 남기고 나머지는 제거함  
- 결과적으로 최종 Region Proposal만 남음

---

## Step 7: RPN의 출력 요약

RPN은 각 anchor에 대해 다음을 출력함:

1. **Objectness Score**  
   - 물체일 확률 (Foreground vs Background)

2. **BBox Regression 값**  
   - anchor 위치를 얼마나 보정할지 (tx, ty, tw, th)

3. **Proposal Boxes**  
   - 보정 적용된 실제 박스 좌표  
   - 보통 2,000개 정도 proposal 생성됨 (NMS 이후)

→ 이 proposal들이 **ROI Head**로 넘어감

---

## Step 8: ROI Head가 필요한 이유

RPN은 단지 “물체가 있을 법한 곳”만 알려줌.

→ 이걸 바탕으로 더 정확하게:

1. 어떤 클래스인지 분류  
2. 박스 위치를 더 정밀하게 조정  

→ 이 두 작업을 하는 게 **ROI Head**임

---

## Step 9: ROI Pooling이 필요한 이유

Proposal마다 크기가 다 다름.  
하지만 FC Layer나 Classification Head는 **고정된 크기 입력**만 받음.

→ 그래서 proposal을 고정 크기 (예: 7×7)로 변환해야 함 → 이게 **ROI Pooling**임

---

## Step 10: ROI Pooling 동작 방식

1. Feature map 상에서 proposal 좌표 영역 crop  
2. 해당 영역을 고정 크기 (예: 7×7)로 분할 후, max pooling 수행  
3. 결과적으로 각 proposal은 7×7×C 형태의 텐서로 변환됨

※ ROI Align은 더 정밀한 방식이지만 원리는 유사함

---

## Step 11: ROI Head 구조 및 역할

ROI Pooling 결과를 바탕으로 다음 작업 수행:

| 구성 요소             | 역할                           |
|---------------------|--------------------------------|
| ROI Pooling         | proposal 영역을 7×7로 변환       |
| FC Layers           | 특징 추출                        |
| Classification Head | 클래스 분류 (Softmax)            |
| BBox Regression Head| 위치 보정 (x, y, w, h)           |

---

## Step 12: 예시로 이해하기

- 이미지에 고양이, 강아지, 의자가 있다고 가정  
- RPN이 각각에 대해 여러 proposal 생성  
- ROI Pooling으로 proposal을 고정 크기로 변환  
- ROI Head가 각 proposal에 대해 다음 판단 수행:  
  - 이건 강아지이고 박스는 여기  
  - 이건 배경일 확률 높음  
  - 이건 의자고 박스 위치 보정 필요

---

## Step 13: 전체 학습 과정 요약 (End-to-End Flow)

1. 입력 이미지 → CNN → feature map  
2. RPN: anchor 기반으로 예측 + RPN Loss 계산  
3. Proposal 선택 후 → ROI Pooling  
4. ROI Head: class + bbox 예측 → ROI Loss 계산  
5. 전체 Loss = RPN Loss + ROI Loss  
6. 전체 네트워크에 대해 Backpropagation 수행

---

## Step 14: 학습 시 유의사항

- GT와 IoU가 높은 proposal만 사용 (보통 IoU > 0.7)  
- 너무 많이 혹은 적게 겹치는 anchor는 제외  
- mini-batch 구성 시 foreground : background = 1 : 3 비율 유지

---

## Step 15: 학습 최적화 전략

논문에서는 다음과 같은 2단계 학습 방식 제안함:

1. RPN 먼저 학습  
2. ROI Head 학습  
3. RPN + ROI Head 결합 후 joint fine-tuning

→ 하지만 실제 구현에서는 대부분 **end-to-end joint training** 사용함