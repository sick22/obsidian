
residual connection -  해당 layer의 입력 값과 해당 layer의 출력 값을 더해서 기울기 소실을 예방하는 과정

인코더와 디코더로 이뤄져있는 seq2seq 구조이다. RNN 구조의 사용을 완전히 배제하여 오로지 attention 매커니즘만으로 시퀀스를 처리하며, 이는 기존의 seq2seq 모델과의 차이점이다. 병렬 계산에 강점을 가진다.

자연어 처리(NLP)에 강점을 가지며 
object detection에 대한 부분도 연구가 이뤄지고 있다.(DETR, ViT)

transformer는 토큰화된 임베딩 벡터 값을 이용해 학습하므로 토큰화가 중요하다. 
attention을 이용한 구조로 이뤄져있다. 
토큰화된 백터는 가중치 행렬을 통과하여 각각 query, keys, values로 변환된다. 이때 세가지 구성요소가 같은 벡터값에서 이뤄진다면 그건 self attention에 해당한다. 
query 값과 key 값의 곱을 통해 value 값과의 가충치합을 구하게 된다. 이 과정을 통해 각각의 토큰이 나머지 토큰들과 어떤 연관성을 가지는지에 대한 attention score가 나온다 이 값에 softmax 함수를 적용용하여 값이 1인 attention weights가 나온다.

그 값을 residual connection과 normalization을 거쳐서 최종 출력을 구한다. 

이과정에서 모든 쿼리에 대해서 병렬로 한 번에 진행하므로 multihead self-attention이라고 한다. 출력 값들은 decoder의 key와 value 값으로 들어간다. 그 다음 인코더와 같은 과정을 거친다.


