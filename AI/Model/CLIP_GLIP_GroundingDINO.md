### 1. CLIP (Contrastive Language-Image Pre-training)

*   **개념:**
    CLIP은 OpenAI에서 개발한 모델로, **이미지와 텍스트 간의 관계를 학습하여 두 모달리티(양식)를 하나의 공유된 임베딩 공간에 매핑**하는 것을 목표로 합니다. 이를 통해 이미지와 텍스트 간의 의미론적 유사성을 측정할 수 있게 되며, 학습 시 보지 못했던 새로운 카테고리에 대해서도 분류(Zero-shot classification)가 가능해지는 강력한 일반화 능력을 보여줍니다.

*   **핵심 메서드:**
    CLIP은 크게 두 가지 독립적인 인코더로 구성됩니다:
    1.  **이미지 인코더 (Image Encoder):** ResNet 또는 Vision Transformer (ViT)와 같은 아키텍처를 사용하여 이미지를 고차원 벡터(임베딩)로 변환합니다.
    2.  **텍스트 인코더 (Text Encoder):** Transformer 기반의 아키텍처를 사용하여 텍스트(캡션)를 고차원 벡터(임베딩)로 변환합니다.

    학습 과정은 다음과 같습니다:
    *   **대규모 데이터셋:** 웹에서 수집한 방대한 양의 (이미지, 텍스트 캡션) 쌍 데이터셋을 사용합니다.
    *   **대조 학습 (Contrastive Learning):** 배치(batch) 내에서 올바른 (이미지, 텍스트) 쌍의 임베딩 유사도는 최대화하고, 잘못된 (이미지, 텍스트) 쌍(즉, 다른 이미지의 캡션이나 다른 캡션의 이미지)의 임베딩 유사도는 최소화하도록 학습합니다.
    *   **공유 임베딩 공간:** 학습이 완료되면 이미지와 텍스트는 동일한 벡터 공간에 존재하게 되며, 의미적으로 유사한 이미지와 텍스트는 이 공간에서 서로 가깝게 위치하게 됩니다.

*   **손실 함수:**
    CLIP은 **대조 손실 (Contrastive Loss)**을 사용합니다. 구체적으로는 **InfoNCE (Information Noise-Contrastive Estimation) 손실**의 변형을 사용합니다.
    *   주어진 배치에서 $N$개의 (이미지, 텍스트) 쌍이 있을 때, $N$개의 올바른 쌍과 $N 	imes (N-1)$개의 잘못된 쌍이 존재합니다.
    *   손실 함수는 올바른 이미지-텍스트 쌍의 코사인 유사도(cosine similarity)를 높이고, 모든 잘못된 쌍의 유사도를 낮추도록 설계됩니다.
    *   이는 이미지 임베딩과 텍스트 임베딩 간의 유사도 행렬을 만들고, 이 행렬의 대각선 요소(올바른 쌍)에 대해서는 Cross-Entropy 손실을 최소화하고, 비대각선 요소(잘못된 쌍)에 대해서는 Cross-Entropy 손실을 최대화하는 방식으로 작동합니다.

### 2. GLIP (Grounded Language-Image Pre-training)

*   **개념:**
    GLIP은 Microsoft에서 개발한 모델로, **객체 탐지(Object Detection)와 언어 이해(Language Understanding)를 통합**하여 **"Grounded Language-Image Pre-training"**을 수행합니다. 즉, 텍스트 표현(예: "a red car")이 이미지 내의 특정 시각적 영역(예: 빨간색 자동차의 바운딩 박스)과 어떻게 연결되는지(grounding)를 학습합니다. 이를 통해 텍스트 쿼리를 기반으로 객체를 탐지하거나, 탐지된 객체에 대한 설명을 생성하는 등 다양한 시각-언어 작업을 수행할 수 있습니다.

*   **핵심 메서드:**
    GLIP은 기존 객체 탐지 모델(예: Faster R-CNN)과 언어 모델을 결합한 구조를 가집니다.
    1.  **통합된 학습:** GLIP은 두 가지 유형의 데이터를 사용하여 학습됩니다:
        *   **객체 탐지 데이터셋:** 이미지와 바운딩 박스, 클래스 레이블이 있는 데이터 (예: COCO).
        *   **이미지-텍스트 쌍 데이터셋:** 이미지와 전체 이미지 캡션만 있는 데이터 (예: Conceptual Captions).
    2.  **Phrase Grounding as Object Detection:** GLIP의 핵심 아이디어는 **구문 접지(Phrase Grounding) 작업을 객체 탐지 문제로 재구성**하는 것입니다. 예를 들어, "a red car"라는 텍스트가 주어지면, 모델은 "red car"라는 클래스를 가진 객체를 탐지하는 것처럼 학습합니다.
    3.  **Cross-modal Encoder:** 이미지 특징과 텍스트 특징을 함께 처리하여 시각적 영역과 텍스트 구문 간의 관계를 학습하는 인코더를 포함합니다.
    4.  **Pseudo-labeling:** 캡션만 있는 데이터셋에 대해서는, 모델이 텍스트 캡션에서 명사를 추출하고 이를 이미지 내의 잠재적인 객체 영역과 연결하여 "가짜 레이블(pseudo-label)"을 생성합니다. 이 가짜 레이블을 사용하여 객체 탐지 학습을 진행합니다.

*   **손실 함수:**
    GLIP은 기본적으로 **객체 탐지 손실**과 **접지 손실(Grounding Loss)**을 결합하여 사용합니다.
    *   **분류 손실 (Classification Loss):** 탐지된 객체가 어떤 클래스(또는 텍스트 구문)에 속하는지 예측하는 손실 (예: Focal Loss 또는 Cross-Entropy).
    *   **바운딩 박스 회귀 손실 (Bounding Box Regression Loss):** 탐지된 객체의 바운딩 박스 좌표를 정확하게 예측하는 손실 (예: L1 Loss, Generalized IoU (GIoU) Loss).
    *   **접지 손실 (Grounding Loss):** 텍스트 구문과 이미지 내의 해당 시각적 영역 간의 정렬을 강화하는 손실입니다. 이는 종종 CLIP과 유사한 **대조 학습 기반의 손실**을 포함하여, 올바른 텍스트-영역 쌍의 유사도를 높이고 잘못된 쌍의 유사도를 낮추도록 합니다.

### 3. Grounding DINO

*   **개념:**
    Grounding DINO는 GLIP의 후속작으로 볼 수 있으며, **"Open-set Object Detection"** 과 **"Phrase Grounding"** 을 더욱 강력하게 수행하는 모델입니다. 사용자가 텍스트 프롬프트(예: "a dog", "a person riding a bicycle")를 입력하면, 모델은 학습 시 보지 못했던 임의의 객체도 이미지 내에서 정확하게 탐지하고 접지할 수 있습니다. 이는 "Detecting Objects with Text"라는 목표를 달성하는 데 중요한 진전을 이루었습니다.

*   **핵심 메서드:**
    Grounding DINO는 DETR (DEtection TRansformer) 계열의 강력한 객체 탐지 모델인 **DINO** 아키텍처를 기반으로 하며, 여기에 언어 모델을 통합합니다.
    1.  **DINO 기반 아키텍처:** DINO는 Transformer 기반의 인코더-디코더 구조를 사용하여 객체 쿼리(object queries)를 통해 객체를 탐지합니다. Grounding DINO는 이 DINO의 강력한 탐지 능력을 활용합니다.
    2.  **언어-가이드 쿼리 생성 (Language-guided Query Generation):** Grounding DINO의 핵심은 텍스트 프롬프트에서 추출된 언어 특징을 사용하여 객체 쿼리를 "접지(grounding)"하는 것입니다. 즉, 텍스트 정보가 객체 탐지를 위한 쿼리 생성 과정에 직접적으로 영향을 미쳐, 텍스트에 명시된 객체를 찾도록 유도합니다.
    3.  **퓨전 인코더 (Fusion Encoder):** 이미지 특징과 텍스트 특징을 효과적으로 융합하는 인코더를 사용하여 두 모달리티 간의 상호작용을 모델링합니다.
    4.  **대조 학습 기반 접지:** GLIP과 유사하게, 시각적 영역과 텍스트 구문 간의 정렬을 위해 대조 학습 메커니즘을 사용합니다. 이를 통해 텍스트 프롬프트에 해당하는 시각적 영역을 정확하게 찾아낼 수 있습니다.
    5.  **Open-set Detection:** 텍스트 프롬프트가 주어지면, 모델은 해당 텍스트에 해당하는 객체를 탐지하려고 시도하므로, 미리 정의된 클래스 목록에 얽매이지 않고 임의의 객체를 탐지할 수 있습니다.

*   **손실 함수:**
    Grounding DINO는 DINO의 손실 함수와 접지 손실을 결합합니다.
    *   **분류 손실 (Classification Loss):** 탐지된 객체가 텍스트 프롬프트와 얼마나 잘 일치하는지 예측하는 손실 (예: Focal Loss).
    *   **바운딩 박스 회귀 손실 (Bounding Box Regression Loss):** 탐지된 객체의 바운딩 박스 좌표를 예측하는 손실 (예: L1 Loss, GIoU Loss).
    *   **대조 접지 손실 (Contrastive Grounding Loss):** 텍스트 임베딩과 이미지 내의 해당 바운딩 박스 영역의 시각적 임베딩 간의 유사도를 최대화하고, 관련 없는 쌍의 유사도를 최소화하는 손실입니다. 이는 텍스트와 시각적 영역 간의 의미론적 정렬을 강화합니다.