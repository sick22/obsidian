##### linked list


##### 벡터는 동적으로 크기가 변한다.
`vetor<int> v1()` 형식으로 선언 
`v.push_back()` 을 통해 마지막 요소 추가
`v.pop_back()` 을 통해 마지막 요소 삭제
`sort()` 통해 정렬

#### string
`stoi(string)` 문자열을 정수로
`stod(string)` 문자열을 실수로
`find()` 함수를 통해 string 안에 있는 단어를 찾을 수 있다.
`string::npos`은 find에서 반환된 값을 없을 때이다.
`replace()`함수를 이용하여 string 내부의 값을 바꿀 수 있다.
`to_stirng(n)`을 이용해서 int형을 스트링으로 변경 가능
#####    메소드
`S.find(찾을 것)`
`S.replace(시작 위치, 길이, 변환할 값)`
`S.length()`: 문자열의 길이를 변환
`S.substr(start)`: 문자열의 `start` 인덱스부터 마지막 인덱스까지의 부분 문자열을 반환
`S.substr(start, length)`: 문자열의 `start` 인덱스부터 `length` 길이만큼의 부분 문자열을 반환
#### begin

`.begin()` 은 컨테이너의 첫 번째 요소를 가리킴
`.end()`는 마지막 요소의 다음 위치 
`(.end()-1)`를 이용하여 마지막 요소를 불러냄 

#### 조건문

조건문의 중간 지점부터 역순으로 하고 싶다면
조건만 역으로 하라 
`for(int i = 1; i <= n; i++)`
`for(int i = n-1; i > 0; i--)`
if문에서 continue를 사용시 조건에 맞으면 바로 반환하여 다음 조건 실행.