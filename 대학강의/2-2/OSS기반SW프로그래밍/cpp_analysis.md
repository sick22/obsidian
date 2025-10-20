# C++ & OpenCV 학습 자료


## 0.환경설정
 헤더파일은 include 파일 안에 존재
 bin 파일은 프로그램 실행시 필요한 동적라이브러리와 유틸리티 프로그램이 생성
 lib 파일은  OpenCV DLL 파일이 생성될 때 함께 만들어지는 가져오기 라이브러리파일 (import ilbrary)
C++ / 일반 \include
링크 / 일반 \bin
링크 추가 종속 lib 파일
실행할 때는 dll 파일

## 1. 기본 C++ 문법

### `#include`
- **설명**: 전처리기 지시문으로, 다른 파일의 내용을 현재 파일에 포함시킬 때 사용합니다. 라이브러리 헤더 파일을 포함하여 해당 라이브러리의 함수와 클래스를 사용할 수 있게 합니다.
- **예시**:
  ```cpp
  // iostream 라이브러리는 콘솔 입출력(cout, cerr)을 위해 필요합니다.
  #include <iostream>

  // opencv.hpp 헤더 파일은 OpenCV의 핵심 기능을 포함합니다.
  #include "opencv2/opencv.hpp"
  ```

### `namespace`
- **설명**: 이름 충돌을 방지하기 위해 코드의 요소를 그룹화하는 데 사용됩니다. `using namespace std;`는 `std` 네임스페이스의 모든 이름을 현재 범위로 가져와 `std::` 접두사 없이 사용할 수 있게 합니다.
- **예시**:
  ```cpp
  // std와 cv 네임스페이스를 사용하겠다고 선언
  using namespace std;
  using namespace cv;

  // 네임스페이스를 명시적으로 사용하는 경우
  std::cout << "Hello";
  cv::Mat image;
  ```

### `main` 함수
- **설명**: C++ 프로그램의 시작점입니다. 프로그램이 실행될 때 `main` 함수가 가장 먼저 호출됩니다.
- **예시**:
  ```cpp
  int main() {
      // 프로그램 코드가 여기에 들어갑니다.
      return 0; // 0을 반환하면 프로그램이 성공적으로 종료되었음을 의미합니다.
  }
  ```

### 변수 선언 및 초기화
- **설명**: 데이터를 저장하기 위한 메모리 공간을 할당하고 이름을 부여하는 과정입니다.
- **예시**:
  ```cpp
  // int 타입의 변수 선언
  int number;

  // float 타입의 배열 선언 및 초기화
  float data[] = { 1, 2, 3, 4, 5, 6 };

  // OpenCV Mat 클래스 객체 선언
  cv::Mat image;
  ```

### `if` 조건문
- **설명**: 주어진 조건이 참(true)인지 거짓(false)인지에 따라 다른 코드를 실행합니다. `empty()`와 같은 함수와 함께 사용하여 예외 처리를 합니다.
- **예시**:
  ```cpp
  cv::Mat img = cv::imread("image.jpg");
  if (img.empty()) {
      std::cerr << "Image load failed!" << std::endl;
      return -1; // 프로그램 비정상 종료
  }
  ```

### `for` 반복문
- **설명**: 특정 조건을 만족하는 동안 코드 블록을 반복적으로 실행합니다.
- **예시**:
  ```cpp
  // 0부터 2까지 3번 반복
  for (int i = 0; i < 3; i++) {
      // 반복할 코드
      std::cout << i << std::endl;
  }
  ```

### 입출력 (`cout`, `cerr`)
- **설명**: `<iostream>` 라이브러리에 포함된 표준 출력 스트림입니다. `cout`은 일반적인 출력을 위해, `cerr`은 에러 메시지를 출력하기 위해 사용됩니다. `endl`은 줄바꿈을 의미합니다.
- **예시**:
  ```cpp
  cout << "Hello, world!" << endl;
  cerr << "Error: Something went wrong." << endl;
  ```

### 문자열 (`String`, `format`)
- **설명**: OpenCV의 `String` 클래스는 문자열을 쉽게 다룰 수 있게 해줍니다. `format` 함수는 C-스타일 `printf`처럼 형식화된 문자열을 만드는 데 사용됩니다.
- **예시**:
  ```cpp
  using namespace cv;
  String str1 = "Hello";
  String str2 = "world";
  String str3 = str1 + " " + str2; // "Hello world"

  // 숫자를 포함하는 파일 이름 생성
  int i = 1;
  String filename = format("image%02d.png", i); // "image01.png"
  ```

---

## 2. OpenCV 핵심 클래스 및 함수

### `cv::Mat`
- **설명**: Matrix(행렬)의 약자로, OpenCV에서 이미지를 비롯한 n-차원 배열 데이터를 저장하는 핵심 클래스입니다. 이미지 데이터, 픽셀 값 등을 모두 `Mat` 객체를 통해 다룹니다.
- **생성 예시**:
  ```cpp
  // 비어있는 Mat 객체 생성
  Mat img;

  // 특정 크기와 타입으로 Mat 생성 (480x640, 1채널 흑백)
  Mat img2(480, 640, CV_8UC1);

  // 특정 크기와 타입, 초기값으로 Mat 생성 (모든 픽셀을 128(회색)로)
  Mat img5(480, 640, CV_8UC1, Scalar(128));

  // 0으로 채워진 행렬 생성
  Mat mat1 = Mat::zeros(3, 3, CV_32SC1);
  ```

### `cv::imread`
- **설명**: 파일로부터 이미지를 읽어 `Mat` 객체로 반환하는 함수입니다.
- **예시**:
  ```cpp
  Mat img = imread("lenna.bmp");
  ```

### `cv::imshow`
- **설명**: `namedWindow`로 생성된 창에 이미지를 표시하는 함수입니다.
- **예시**:
  ```cpp
  Mat img = imread("lenna.bmp");
  namedWindow("Display window");
  imshow("Display window", img);
  ```

### `cv::waitKey`
- **설명**: 지정된 시간(ms) 동안 키보드 입력을 기다립니다. `0`을 인자로 주면 키 입력이 있을 때까지 무한정 기다립니다. 창이 화면에 표시되려면 이 함수가 꼭 필요합니다.
- **예시**:
  ```cpp
  waitKey(0); // 키를 누를 때까지 대기
  ```

### `cv::destroyWindow` / `cv::destroyAllWindows`
- **설명**: 지정된 이름의 창 또는 모든 창을 닫습니다.
- **예시**:
  ```cpp
  destroyWindow("Display window"); // 특정 창 닫기
  destroyAllWindows(); // 모든 OpenCV 창 닫기
  ```

### `cv::imwrite`
- **설명**: `Mat` 객체에 저장된 이미지 데이터를 파일로 저장하는 함수입니다. 파일 확장자에 따라 형식이 결정됩니다.
- **예시**:
  ```cpp
  Mat img = imread("lenna.bmp");
  // JPEG 형식으로 품질 95%로 저장
  std::vector<int> params;
  params.push_back(cv::IMWRITE_JPEG_QUALITY);
  params.push_back(95);
  imwrite("lenna.jpg", img, params);
  ```

### `Mat::clone()`
- **설명**: 행렬의 깊은 복사(deep copy)를 수행합니다. 원본과 완전히 독립된 새로운 `Mat` 객체를 만듭니다.
- **예시**:
  ```cpp
  Mat img1 = imread("image.bmp");
  Mat img2 = img1; // 얕은 복사 (데이터 공유)
  Mat img3 = img1.clone(); // 깊은 복사 (데이터 독립)
  ```

### `Mat::empty()`
- **설명**: `Mat` 객체가 데이터를 가지고 있는지 확인합니다. 이미지를 성공적으로 불러왔는지 검사할 때 유용합니다.
- **예시**:
  ```cpp
  Mat img = imread("non_existent_image.bmp");
  if (img.empty()) {
      cerr << "Image load failed!" << endl;
  }
  ```

### `cv::Rect`
- **설명**: 사각형 영역을 정의하는 클래스입니다. `x`, `y` 좌표와 `width`, `height`를 가집니다. 이미지의 특정 부분(ROI, Region of Interest)을 잘라낼 때 사용됩니다.
- **예시**:
  ```cpp
  Mat img1 = imread("cat.bmp");
  // (220, 120)에서 시작하는 340x240 크기의 사각형 영역을 잘라냄
  Mat img2 = img1(Rect(220, 120, 340, 240));
  ```

### `Mat::ptr<T>(j)`
- **설명**: 행렬의 특정 행(row)의 시작 주소를 가리키는 포인터를 반환합니다. 픽셀에 빠르게 접근할 때 사용됩니다. `T`는 픽셀의 데이터 타입입니다.
- **예시**:
  ```cpp
  Mat mat = Mat::zeros(10, 10, CV_8UC1);
  // 5번째 행의 첫번째 픽셀 주소를 가져옴
  uchar* p = mat.ptr<uchar>(5);
  // 5번째 행, 3번째 열의 픽셀 값을 255로 변경
  p[3] = 255;
  ```

### 사용된 문자열 리터럴
- `"Hello OpenCV"`
- `" Image Load failed!"`
- `"lennaJJang"`
- `"lenna.bmp"`
- `"test%02d.bmp"`
- `"img%02d.png"`
- `"dog.bmp"`
- `"cat.bmp"`
- `"lenna.jpg"`
