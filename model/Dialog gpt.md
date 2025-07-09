
## **1 | DialoGPT란 무엇인가?**

- **출신** : Microsoft Research가 2019년에 GPT-2를 대화용으로 특화시켜 공개한 오픈-소스 LM.
    
- **학습 데이터** : 2005–2017 Reddit 다중 턴 대화 147 백만 건으로 사전학습해 “사람 같은” 단일턴 응답 품질을 달성함  .
    
- **모델 크기** :
    
    - _small_ 117 M
        
    - _medium_ 345 M
        
    - _large_ 762 M 
        
        (다운로드, fine-tune, 로컬 추론이 모두 가능).



- **라이선스** : MIT—상업 게임에도 포함 가능(표시 의무만 지키면 됨)  .
 _2005년부터 2017년까지 Reddit 댓글 체인에서 추출한 1억 4,700만 건의 대화 유사 대화를 기반으로 학습된 DialoGPT는 Hugging Face PyTorch 변환기를 확장하여 단일 턴 대화 환경에서 자동 및 인간 평가 측면에서 인간에 가까운 성능을 달성합니다. DialoGPT를 활용하는 대화 시스템은 강력한 기준 시스템보다 더욱 관련성 있고, 내용이 풍부하며, 맥락에 일관된 반응을 생성함을 보여줍니다. 사전 학습된 모델과 학습 파이프라인은 신경 반응 생성 연구 및 더욱 지능적인 오픈 도메인 대화 시스템 개발을 지원하기 위해 공개됩니다._

- **자기 회귀 모델**이란?

자기 회귀 모델은 시퀀스의 이전 입력에서 측정값을 가져와 시퀀스의 다음 성분을 자동으로 예측하는 [기계 학습(ML)](https://aws.amazon.com/what-is/machine-learning/) 모델의 클래스입니다. 자기 회귀는 시계열 분석에 사용되는 통계 기법으로, 시계열의 현재 값이 과거 값의 함수라고 가정합니다. 자기 회귀 모델은 유사한 수학적 기법을 사용하여 시퀀스에 있는 요소 간의 확률적 상관관계를 판단합니다. 그런 다음 도출된 정보를 사용하여 알려지지 않은 시퀀스의 다음 요소를 추측합니다. 예를 들어 자기 회귀 모델이 훈련 중에 여러 영어 문장을 처리하여 단어 ‘is’가 항상 ‘there’라는 단어 뒤에 오는 것을 파악합니다. 그런 다음 ‘there is’가 함께 있는 새 시퀀스를 생성합니다.

v
라이브러리 버전확인
sentence-transformers 4.1.0
torch 2.6.0+cu124
transformers 4.52.4
huggingface-hub 0.33.0
datasets 2.14.4
pandas 2.2.2



정확도 향상

|**카테고리**|**파라미터(새 값 예)**|**왜 도움이 되나**|
|---|---|---|
|**학습률‧스케줄**|learning_rate=1e-5 ★warmup_ratio=0.1 또는 warmup_steps=int(total_steps*0.1) ★lr_scheduler_type="cosine"|미세 튜닝 시 **낮은 lr + warm-up**이 수렴 안정↑; cosine decay가 종점 성능↑|
|**배치 크기**|gradient_accumulation_steps=8 ★ (→ 전역 2×8=16)|손실 노이즈↓, BLEU·PPL 개선 보고 빈번|
|**규제**|weight_decay=0.01 ★gradient_clipping=1.0|과적합·폭발적 그래드 방지|
|**메모리 최적화**|gradient_checkpointing=True|컨텍스트 길이↑ 또는 더 큰 GA 가능|
|**평가 빈도**|evaluation_strategy="steps" ★eval_steps=500 (or 5 % of steps)|가장 높은 BLEU 시점 캡처 용이|
|**베스트 모델 기준**|metric_for_best_model="eval_bleu" ★greater_is_better=True|“낮은 loss≠높은 대화 품질” 문제 해결|
|**Epoch 수**|num_train_epochs=5 (시작 후 조기 종료)|데이터 크기에 따라 under-fit 시 늘리기|
|**혼합 정밀도**|bf16=True (A100/H100)|fp16보다 안정적·속도↑ (가능한 GPU면)|
|**콜백**|callbacks=[EarlyStoppingCallback(early_stopping_patience=3)]|BLEU 정체 시 자동 종료→과적합 방지|


```cpp
import json

from transformers import AutoTokenizer, AutoModelForCausalLM

import torch

import evaluate

  

model_path = "./my_saved_model"

tokenizer = AutoTokenizer.from_pretrained(model_path)

model = AutoModelForCausalLM.from_pretrained(model_path).to("cuda")

model.eval() # 모델 추론 상태로 전환

  

bleu = evaluate.load("bleu")

  

test_data=[]

with open("/content/dialogpt_test_prompt.jsonl","r",encoding="utf-8") as f:

  for line in f:

      test_data.append(json.loads(line))

  

predictions,references = [],[]

  

for item in test_data:

  encoded = tokenizer(

      item["prompt"] + tokenizer.eos_token,

      return_tensors="pt", # 모델에 바로 넣을 수 있는 입력을 만들기 위해 텐서 형태로 변환

      truncation=True, # 입력 테스트가 너무 늘 경우 자름

      max_length=128,

      padding=True

  ).to(model.device)

  

  # 응답 생성

  output_ids = model.generate(

      input_ids=encoded["input_ids"],

      attention_mask=encoded["attention_mask"], # 어떤 부분 주의 계산에 포함 시킬지

      max_length=300,

      pad_token_id=tokenizer.eos_token_id,

      do_sample=True, # 텍스트 생성 시 확률 기반 샘플링 활성화

      # 다음 토큰을 생성할 때 가장 확률이 높은 하나만 고르지 않고 확률 분포에 따라 무작위 샘플

      top_k=50,

      top_p=0.95

  )

  generated = tokenizer.decode(output_ids[0],skip_special_tokens=True)

  

  predictions.append(generated.split())

  references.append([item["completion"].split()])

  # 디버깅용: predictions, references 길이 및 문제 항목 확인

  for i, (pred, ref) in enumerate(zip(predictions, references)):

      if pred is None or ref is None:

          print(f"[❌ None 발견] index: {i}")

      elif not pred or not ref:

          print(f"[⚠️ 비어있음] index: {i}, pred: {pred}, ref: {ref}")

  

filtered_preds, filtered_refs = [], []

  

for pred, ref in zip(predictions, references):

    if pred and ref and pred is not None and ref is not None:

        filtered_preds.append(pred)

        filtered_refs.append(ref)

  

score = bleu.compute(predictions=filtered_preds, references=filtered_refs)

print("BLEU score:", score["bleu"])

/*
--------------------------------------------------------------------------

ValueError                                Traceback (most recent call last)

/tmp/ipython-input-31-3211763545.py in <cell line: 0>()

     57 

     58 

---> 59 score = bleu.compute(predictions=filtered_preds, references=filtered_refs)

     60 print("BLEU score:", score["bleu"])

  

3 frames

/usr/local/lib/python3.11/dist-packages/evaluate/module.py in _infer_feature_from_example(self, example)

    614             f"Input references: {summarize_if_long_list(example['references'])}"

    615         )

--> 616         raise ValueError(error_msg) from None

    617 

    618     def _feature_names(self):

  

ValueError: Predictions and/or references don't match the expected format.

Expected format:

Feature option 0: {'predictions': Value(dtype='string', id='sequence'), 'references': Sequence(feature=Value(dtype='string', id='sequence'), length=-1, id='references')}

Feature option 1: {'predictions': Value(dtype='string', id='sequence'), 'references': Value(dtype='string', id='sequence')},

Input predictions: ['Name:', 'Naina', 'Mathur', ..., 'conflicts?', 'Answer:', 'Answer:'],

Input references: [['Ensuring', 'every', 'student', 'receives', 'the', 'individual', 'attention', 'they', 'need', 'to', 'succeed.']]

이게 오류
*/
```


| **evaluate BLEU가 기대**                                                                                       | **넘긴 값**                                                                           |
| ----------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| **predictions** → list[str] (각 항목이 “완전한 문장”)**references** → list[list[str]] (다참조) **또는** list[str] (단일 참조) | predictions → list[list[str]] (토큰 목록)references → list[list[list[str]]] (토큰 2중 중첩) |
generated.split() 때문에 **문장을 토큰 단위 리스트로** 바꿔 버려서 evaluate가 “형식이 다르다”고 중단한 것입니다.


 BLEU score: 0.004275198748195849

정확도가 낮은 이유 - dialoGPT 모델의 한계로 지속적이 대화가 불가능

사진 사진 오류 오류 

llama