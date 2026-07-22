[◀ UE5 C++ 개발 대시보드로 돌아가기](./UE5.md)

# Unreal Engine 5 디버깅 및 로그 가이드

언리얼 엔진 5에서 개발할 때 가장 기본적이면서도 중요한 디버깅 도구인 `UE_LOG`, `GEngine`, 그리고 `DrawDebug` 함수 계열에 대해 정리합니다.

---

## 1. UE_LOG
`UE_LOG`는 언리얼 엔진의 출력 로그(Output Log) 창에 메시지를 기록하는 매크로입니다. 코드의 흐름을 파악하거나 변수 값을 확인할 때 필수적으로 사용됩니다.

### 기본 구문
```cpp
UE_LOG(LogTemp, Warning, TEXT("Hello Unreal! Value: %d"), MyInt);
```

### 파라미터 상세
1. **Category (로그 카테고리)**:
   - 로그를 분류하는 기준입니다.
   - `LogTemp`: 임시로 사용할 때 주로 쓰며, 사용자 정의 카테고리를 만들 수도 있습니다.
2. **Verbosity (로그 수준)**:
   - 로그의 중요도를 나타내며 색상이 다르게 표시됩니다.
   - `Fatal`: 치명적 에러. 프로그램이 중단됩니다.
   - `Error`: 에러 (빨간색).
   - `Warning`: 경고 (노란색).
   - `Display`: 일반 정보 (흰색, 콘솔에 표시).
   - `Log`: 일반 정보 (로그 파일에 기록).
   - `Verbose / VeryVerbose`: 상세한 디버깅 정보.
3. **Format (문자열 형식)**:
   - `TEXT()` 매크로를 반드시 사용해야 합니다.
   - 표준 `printf`와 유사한 형식 지정자를 사용합니다 (예: `%d`, `%f`, `%s`).
   - `FString`을 출력할 때는 `%s`와 함께 `*` 연산자를 붙여야 합니다: `*MyFString`.

### 예시 코드
```cpp
FString PlayerName = "YongSeog";
int32 Score = 100;

UE_LOG(LogTemp, Log, TEXT("Player %s has a score of %d"), *PlayerName, Score);
```

---

## 2. GEngine
`GEngine`은 언리얼 엔진의 핵심 기능을 담고 있는 전역 포인터(Global Engine Pointer)입니다. 엔진 레벨의 기능에 접근할 때 사용하며, 특히 화면에 직접 메시지를 띄울 때 유용합니다.

### 주요 용도: AddOnScreenDebugMessage
로그 창을 보지 않고 게임 화면상에서 바로 메시지를 확인할 수 있게 해줍니다.

### 기본 구문
```cpp
if (GEngine)
{
    GEngine->AddOnScreenDebugMessage(-1, 5.f, FColor::Red, TEXT("This is a screen message!"));
}
```

### 파라미터 상세
1. **Key (키)**:
   - `-1`: 메시지가 계속 쌓입니다.
   - 특정 숫자(예: 0): 동일한 키를 가진 메시지는 새로운 내용으로 갱신됩니다.
2. **TimeToDisplay (출력 시간)**:
   - 메시지가 화면에 머무르는 시간(초 단위)입니다.
3. **DisplayColor (색상)**:
   - `FColor::Red`, `FColor::Green` 등으로 텍스트 색상을 지정합니다.
4. **DebugMessage (메시지)**:
   - 출력할 문자열입니다.

---

## 요약: UE_LOG vs GEngine
| 비교 항목 | UE_LOG | GEngine (AddOnScreenDebugMessage) |
| :--- | :--- | :--- |
| **출력 위치** | 출력 로그 창 (Output Log) | 게임 화면 (Viewport) |
| **주요 목적** | 상세한 로직 추적, 영구적 기록 | 빠른 실시간 상태 확인 |
| **속도** | 비교적 무거울 수 있음 | 매우 가볍고 직관적임 |
| **형식** | printf 스타일 포맷 지원 | 단순 문자열 출력 |

---

## 3. DrawDebugSphere
게임 월드 내 특정 위치에 디버그용 구체(Sphere)를 시각화하여 로직의 정확성, 충돌 범위, 또는 특정 지점을 실시간으로 확인하는 데 사용됩니다.

### 기본 구문
```cpp
DrawDebugSphere(GetWorld(), Center, Radius, Segments, Color, bPersistentLines, LifeTime, DepthPriority, Thickness);
```

### 파라미터 상세
- `const UWorld* InWorld`: 구체를 렌더링할 월드 컨텍스트입니다. (예: `GetWorld()`)
- `FVector const& Center`: 구체의 중심 좌표입니다.
- `float Radius`: 구체의 반지름입니다.
- `int32 Segments`: 구체를 구성하는 선의 개수입니다.
- `FColor const& Color`: 구체의 색상입니다.
- `bool bPersistentLines`: 영구 표시 여부입니다.
- `float LifeTime`: 화면 유지 시간입니다.
- `uint8 DepthPriority`: 렌더링 우선순위입니다.
- `float Thickness`: 선의 두께입니다.

---

## 4. DrawDebugLine
게임 월드 내의 두 지점(시작점과 끝점)을 잇는 선을 시각화하여 벡터의 방향, 레이캐스트(Raycast) 결과, 또는 오브젝트 간의 연결 상태를 확인하는 데 사용됩니다.

### 기본 구문
```cpp
DrawDebugLine(GetWorld(), LineStart, LineEnd, Color, bPersistentLines, LifeTime, DepthPriority, Thickness);
```

### 파라미터 상세
- `const UWorld* InWorld`: 선을 렌더링할 월드 컨텍스트입니다.
- `FVector const& LineStart`: 선의 시작 지점 좌표입니다.
- `FVector const& LineEnd`: 선의 끝 지점 좌표입니다.
- `FColor const& Color`: 선의 색상입니다.
- `bool bPersistentLines`: 영구 표시 여부입니다.
- `float LifeTime`: 화면 유지 시간입니다.
- `uint8 DepthPriority`: 렌더링 우선순위입니다.
- `float Thickness`: 선의 두께입니다.

---

## 5. DrawDebugPoint
게임 월드 내의 특정 좌표(FVector)에 점을 찍어 위치를 표시합니다. 충돌 지점이나 경로 노드의 정확한 위치를 시각적으로 확인하는 데 유용합니다.

### 기본 구문
```cpp
DrawDebugPoint(GetWorld(), Position, Size, Color, bPersistentLines, LifeTime, DepthPriority);
```

### 파라미터 상세
- `const UWorld* InWorld`: 점을 렌더링할 월드 컨텍스트입니다.
- `FVector const& Position`: 점을 표시할 월드 좌표입니다.
- `float Size`: 점의 크기 (픽셀 단위).
- `FColor const& Color`: 점의 색상입니다.
- `bool bPersistentLines`: 영구 표시 여부입니다.
- `float LifeTime`: 화면 유지 시간입니다.
- `uint8 DepthPriority`: 렌더링 우선순위입니다.


## 5. 게임플레이 오디오 연출 API

### [UGameplayStatics::PlaySoundAtLocation]
- **핵심 목적:** 월드 공간 상의 지정된 3차원 위치 좌표를 기준으로 단발성(One-shot) 3D 입체 음향(`USoundBase`)을 스폰하여 즉시 재생하는 정적 유틸리티 함수입니다.
- **파라미터 상세:**
  - `const UObject* WorldContextObject`: 월드 컨텍스트 쿼리를 제공하는 주체 인스턴스 포인터(일반적으로 `this` 또는 `GetWorld()`)입니다.
  - `USoundBase* Sound`: 재생할 사운드 웨이브(Sound Wave) 혹은 사운드 큐(Sound Cue) 에셋 포인터입니다.
  - `FVector Location`: 입체 음향이 스폰되어 방출될 월드 3D 좌표 벡터입니다.
  - `FRotator Rotation`: 사운드 스폰 시점의 회전값입니다 (`FRotator::ZeroRotator` 기본값).
  - `float VolumeMultiplier`: 기본 사운드 음량에 곱연산 적용할 가중 팩터입니다 (`1.f` 기본값).
  - `float PitchMultiplier`: 기본 사운드 피치(음높이)에 곱연산 적용할 가중 팩터입니다 (`1.f` 기본값).
  - `float StartTime`: 사운드 트랙 재생을 개시할 시간(초) 오프셋 값입니다.
  - `USoundAttenuation* AttenuationSettings`: 거리에 따른 사운드 감쇄 법칙을 정의한 에셋 포인터입니다.
  - `USoundConcurrency* ConcurrencySettings`: 오디오 채널 동시 재생 한도를 제어하는 에셋 포인터입니다.
- **반환 값:**
  - 없음 (`void`)
- **기술적 팁 (Technical Tips):**
  - **수명 주기 자동 해제:** 가동된 사운드는 내부 임시 오디오 컴포넌트를 통해 재생된 뒤 플레이가 종료되면 메모리에서 자동 릴리즈됩니다. 따라서 무기나 장비처럼 액터와 함께 움직이며 실시간으로 소리가 밀착되어야 한다면 본 함수 대신 `PlaySoundAttached()`를 활용해야 합니다.
  - **헤더 지정:** C++ 컴파일을 위해 `#include "Kismet/GameplayStatics.h"` 헤더 추가가 필수적입니다.
- **코드 예시:**
  ```cpp
  #include "Kismet/GameplayStatics.h"

  void AMyCharacter::PlayHitFeedbackSound(FVector ImpactPoint)
  { 
      if (ImpactSound)
      { 
          UGameplayStatics::PlaySoundAtLocation(this, ImpactSound, ImpactPoint);
      }
  }
  ```
