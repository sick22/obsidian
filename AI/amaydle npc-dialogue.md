
| 항목                | 내용                                                                                        |
| ----------------- | ----------------------------------------------------------------------------------------- |
| **총 행(샘플) 수**     | **1 912 rows** (train 1 720, test 192)                                                    |
| **열(칼럼) 구성**      | `Name`, `Biography`, `Query`, `Response`, `Emotion` — 총 5개                                |
| **텍스트 길이(문자 기준)** | Biography 45 – 552 · Query 12 – 268 · Response 4 – 367                                    |
| **평균 BPE 토큰 수**   | 약 **220 ± 90 tokens** (전체 열 합산, 추정치)                                                      |
| **포맷 & 인코딩**      | Parquet 파일(UTF-8) · Hugging Face Datasets로 바로 로드 가능                                       |
| **모달리티 & 크기 범주**  | 텍스트 전용 · “1K – 10K” 범주(≈ 200 KB)                                                          |
| **주요 특징**         | - 단일 Q&A 턴 구조 <br> - 유명 IP 캐릭터 혼재 <br> - 같은 NPC 다중 샘플(중복 가능) <br> - Emotion 정성 레이블 20종 내외 |

**RNN** _Recurrent Neural Network_
	
lstm
	**장단기 메모리**(Long Short-Term Memory, LSTM))는 순환 신경망(RNN) 기법의 하나로 셀, 입력 게이트, 출력 게이트, 망각 게이트를 이용해 기존 [순환 신경망](https://ko.wikipedia.org/wiki/%EC%88%9C%ED%99%98_%EC%8B%A0%EA%B2%BD%EB%A7%9D "순환 신경망")(RNN)의 문제인 [기울기 소멸 문제](https://ko.wikipedia.org/wiki/%EA%B8%B0%EC%9A%B8%EA%B8%B0_%EC%86%8C%EB%A9%B8_%EB%AC%B8%EC%A0%9C "기울기 소멸 문제")(vanishing gradient problem)를 방지하도록 개발되었다.

- **GPT** 
	_인코딩 토큰화  -> 벡터화 임베딩 -> 가중치 ->디코딩 -> 생성_

**워드 임베딩**(Word embedding)은 [자연어 처리](https://ko.wikipedia.org/wiki/%EC%9E%90%EC%97%B0%EC%96%B4_%EC%B2%98%EB%A6%AC "자연어 처리")(NLP)에서 단어를 표현하는 것이다. 임베딩은 텍스트 분석에 사용된다. 일반적으로 표현은 벡터 공간에서 더 가까운 단어의 의미가 유사할 것으로 예상되는 방식으로 단어의 의미를 인코딩하는 실수 값 벡터이다. 워드 임베딩은 어휘의 단어나 구문이 실수 벡터에 매핑되는 [언어 모델링](https://ko.wikipedia.org/wiki/%EC%96%B8%EC%96%B4_%EB%AA%A8%EB%8D%B8 "언어 모델") 및 [특징 학습](https://ko.wikipedia.org/wiki/%ED%8A%B9%EC%A7%95_%ED%95%99%EC%8A%B5 "특징 학습") 기술을 사용하여 얻을 수 있다.




로컬 연결이 되지 않는다면 wget을 통해서 서버로 불러오기 그것도 안된다면 최후의 방법으로 직접 수동 업로드