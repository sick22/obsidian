     **상수**
	상수는 선언시 값을 입력.
	상수는 모두 대문자로 표기.

`#include` (소스복사)
	기본 헤더파일은 <> , 유저 생성 헤더파일은 ""

헤더파일을 생성할 때 `namespace`를 사용해줄 것.

`unsigned`를 정수형 앞에 입력한다면 음수형을 표시하지 않기 때문에 42억자리까지 표기할 수 있다

실수형 연산 / 시 , 나누는 값이나 나눠지는 값을 하나라도 **실수형**으로 해줘야한다.

`#include <typeinfo>` ->`type(ex).name()` 한다면 자료형 확인
### ✅ `iomanip` (입출력 포맷 조정)

| 함수                         | 설명                                   |
| -------------------------- | ------------------------------------ |
| **`std::setw(n)`**         | 출력 필드 폭 지정 (공백 포함해서 n칸 확보)           |
| `std::setfill(c)`          | 빈 공간을 채울 문자 지정 (`setw`랑 같이 써야 효과 있음) |
| **`std::setprecision(n)`** | 소수점 아래 자릿수 조절 (부동소수점 출력에 사용)         |
| `std::fixed`               | 소수점 고정 형식으로 출력                       |
| **`std::scientific`**      | 과학적 표기법으로 출력                         |
| `std::left`, `std::right`  | 출력 정렬 방향 지정 (왼쪽, 오른쪽 정렬)             |

---

### ✅ `cstdlib` (표준 유틸리티 함수들)

| 함수                    | 설명                              |
| --------------------- | ------------------------------- |
| `std::atoi(str)`      | 문자열을 `int`로 변환                  |
| `std::atof(str)`      | 문자열을 `double`로 변환               |
| **`std::rand()`**     | 0~`RAND_MAX` 사이의 난수 생성          |
| `std::srand(seed)`    | 난수 시드 설정 (같은 값 넣으면 같은 난수 순서 나옴) |
| `std::abs(x)`         | 정수의 절댓값 반환                      |
| **`std::exit(code)`** | 프로그램 강제 종료                      |
|                       |                                 |

---

### ✅ `limits` (자료형의 한계값)

| 멤버                                  | 설명                           |
| ----------------------------------- | ---------------------------- |
| `std::numeric_limits<T>::max()`     | T형 타입의 최댓값                   |
| `std::numeric_limits<T>::min()`     | T형 타입의 최소 양수값 (`int`는 음수 포함) |
| `std::numeric_limits<T>::lowest()`  | 가장 작은 값 (음수 포함)              |
| `std::numeric_limits<T>::epsilon()` | 부동소수점에서 1과 구별되는 최소값          |

---

### ✅ `cmath` (수학 관련 함수들)

| 함수                                          | 설명           |
| ------------------------------------------- | ------------ |
| `std::sqrt(x)`                              | 제곱근          |
| `std::pow(x, y)`                            | x^y          |
| `std::sin(x)`, `std::cos(x)`, `std::tan(x)` | 삼각함수         |
| `std::log(x)`                               | 자연로그 (밑이 e)  |
| `std::log10(x)`                             | 상용로그 (밑이 10) |
| `std::ceil(x)`                              | 올림           |
| `std::floor(x)`                             | 내림           |
| `std::round(x)`                             | 반올림          |
| `std::fabs(x)`                              | 실수의 절댓값      |

---

### ✅ `cctype` (문자 판별 함수)

| 함수                | 설명                        |
| ----------------- | ------------------------- |
| `std::isalpha(c)` | 알파벳인지 확인                  |
| `std::isdigit(c)` | 숫자인지 확인                   |
| `std::isalnum(c)` | 알파벳 또는 숫자인지 확인            |
| `std::isspace(c)` | 공백문자인지 확인 (띄어쓰기, 개행, 탭 등) |
| `std::toupper(c)` | 소문자를 대문자로 변환              |
| `std::tolower(c)` | 대문자를 소문자로 변환              |
`static_cast<int>(a)`
```cpp
cout << fixed
cout.precision(7)
cout << setprecision(7)
cout.unsetf(ios::fixed)
```


# Class
캡슐화
```cpp
class Circle{
 private:
  double radius;
 public:
  double getRadius() const; //함수 원형이다. 
  /*const는 멤버 변수의 값을 변경하지 않음. 멤버 함수 선언할 때 const를 붙였으면 ㅁ멤버함수 정의에도 const를 붙여야함 */
  double getArea() const;
  double getPerimeter() const;
  void setRadius(double value);
  ```
  멤버 함수 정의
```cpp
 double Circle::getRadius() const { //Circle:: 을 통해 멤버함수를 정의
  return radius; // radius가 private이지만 class 안이기에 접근 가능하다
  }
 double Circle::getArea() const {
  const double PI = 3.141592; 
  return (PI * radius * radius);
  }
 double Circle::getPerimeter() const {
  const double PI = 3.141592; 
  return (2 * PI * radius);
  }
 void Circle::setRadius(double value) {
  radius = value;
  }
};
```
애플리케이션 부분(클래스를 인스턴트화 -> 객체를 만듦)
```cpp
int main()
{
    cout << "Circle 1" << endl;
    Circle circle1;
    circle1.setRadius(10.0);
    cout << "반지름: " << circle1.getRadius() << endl;
    cout << "넓이: " << circle1.getArea() << endl;
    cout << "둘레: " << circle1.getPerimeter() << endl;

    cout << "Circle 2" << endl;
    Circle circle2;
    circle2.setRadius(20.0);
    cout << "반지름: " << circle2.getRadius() << endl;
    cout << "넓이: " << circle2.getArea() << endl;
    cout << "둘레: " << circle2.getPerimeter();

    return 0;
}
```


### **🏗 생성자 (Constructor)**

**정의:**

➔ 객체(Object)가 **태어날 때** 자동으로 호출되는 함수

**목적:**

➔ **객체를 초기화(Initialize)** 하는 역할

(= 변수를 기본값으로 세팅하거나, 메모리를 연결하거나)


```cpp
class Circle
{
    ...
public:
    Circle(double radius);         // Parameter Constructor
    Circle();                      // Default Constructor
    Circle(const Circle& circle);  // Copy Constructor
    ...
};
```
생성자 오버로딩
```cpp
Circle::Circle(double rds)
    : radius(rds) // Initialization list
{
    // Any other statements
}

Circle::Circle() // 기본 생성자 (정해진 값으로 초기화)
    : radius(0.0) // Initialization list
{
    // Any other statements
}

Circle::Circle(const Circle& cr) //복사 생성자
    : radius(cr.radius) // Initialization list
{
    // Any other statements
}
```

### **🛠 소멸자 (Destructor)**

**정의:**

➔ 객체(Object)가 **죽을 때**(메모리에서 사라질 때) 자동으로 호출되는 함수

**목적:**

➔ **객체가 사용했던 리소스를 정리**해주는 역할

(= 동적 메모리 해제, 파일 닫기, 네트워크 끊기 등)

| 항목     | 생성자 (Constructor) | 소멸자 (Destructor)  |
| :----- | :---------------- | :---------------- |
| 호출 시점  | 객체가 **생성**될 때     | 객체가 **소멸**될 때     |
| 이름     | 클래스 이름과 **똑같음**   | `~클래스이름`          |
| 역할     | 객체 **초기화**        | 객체 **정리(리소스 해제)** |
| 직접 호출? | 직접 호출 안 함         | 직접 호출 안 함         |
| 리턴 타입  | 없음                | 없음                |

```cpp
class Circle
{
    ...
public:
    ...
    ~Circle();  // Destructor
};

...

Circle::~Circle()
{
    // Any statements as needed
}
```



```cpp
/* 멤버 함수 정의 (생성자, 소멸자) */
Circle::Circle(double rds) : radius(rds)
{
    cout << "파라미터 생성자가 호출되었습니다." << endl;
    assert(rds >= 0); // 디버깅 용도. false이면 프로그램 종료
}

Circle::Circle() : radius(0.0)
{
    cout << "기본 생성자가 호출되었습니다." << endl;
}

Circle::Circle(const Circle& circle) : radius(circle.radius)
{
    cout << "복사 생성자가 호출되었습니다." << endl;
}

Circle::~Circle()
{
    cout << "소멸자가 호출되었습니다." << endl;
}
```



#### 분할 컴파일
	소스를 소분
	각 파일을 소분하여 컴파일 한 뒤 컴파일 완료된 파일들을 링커를 통해 하나로 합침

--- 
- 재사용률 감소
- 중복 컴파일 감소

| 전처리문      | 의미             | 역할 및 용도                              |
| --------- | -------------- | ------------------------------------ |
| `#ifndef` | if not defined | 해당 이름(매크로)이 **정의되지 않았을 때만** 아래 코드 실행 |
| `#define` | define         | 매크로 이름을 **정의**함 (한 번만 포함되도록 설정)      |
| `#endif`  | end if         | 조건부 전처리 블록의 **끝을 나타냄**               |

###### 7장 #퀴즈
| 번호  | 정답 또는 설명                                                                                                |
| --- | ------------------------------------------------------------------------------------------------------- |
| 1   | 메모리에 할당되는 순간                                                                                            |
| 2   | 3번 (오버로딩 가능하다)                                                                                          |
| 3   | `~Class`                                                                                                |
| 4   | 1. 같은 이름의 함수를 파라미터의 개수에 따라 다르게 호출하여 사용<br>2. 소멸자는 오버로딩이 불가능하다                                           |
| 6   | Constructor → Constructor → Destructor → End → Destructor<br>(※ 구역을 정해주지 않으면 소멸자는 `return` 시 작동함)       |
| 7   | CCDD                                                                                                    |
| 10  | 1. O<br>2. X (컴파일러가 판단하여)<br>3. X<br>4. O                                                               |
| 11  | 짧은 함수를 여러번 호출해야할 때                                                                                      |
| 12  |                                                                                                         |
| 13  |                                                                                                         |
| 14  |                                                                                                         |
| 15  | - 생성자는 this가 있어야 하는데<br>    <br>- static 함수에는 this가 없으므로<br>    <br>- static 생성자는 존재할 수 없음 → **컴파일 에러** |
| 16  | 불가능<br>`static → static` ✅<br>`instance → instance` ✅<br>`static → instance` ❌<br>`instance → static` ✅ |
| 17  | a (static) → 1회<br>b (instance) → 호출하는 만큼                                                               |
| 18  | static 변수 초기화: `int Example::val = 7;`                                                                  |
| 19  | 3                                                                                                       |
| 20  | a                                                                                                       |