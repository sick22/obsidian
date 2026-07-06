# Unreal Engine 5 C++ 핵심 개발 대시보드

이 문서는 Unreal Engine 5.7 C++ 개발 과정에서 수집된 주요 API, 매크로 시스템, 프레임워크 설계 패턴 및 최적화 기법에 대한 통합 인덱스 가이드입니다. 각 대분류 항목을 클릭하여 세부 기술 지침과 소스 코드 예시를 확인할 수 있습니다.

---

## 1. 개발 및 최적화 분류 인덱스

### 📂 [디버깅 및 로깅 (Debugging & Logging)](./debugging.md)
- `UE_LOG` 매크로 설정과 로그 중요도(Verbosity) 수준 관리.
- `GEngine->AddOnScreenDebugMessage`를 활용한 실시간 화면 피드백.
- `DrawDebug` 함수 군(`DrawDebugSphere`, `DrawDebugLine`, `DrawDebugPoint`)을 이용한 월드 내 충돌체 및 궤적 시각화.

### 📂 [액터 트랜스폼 및 이동 (Actor Transform & Movement)](./transform.md)
- `SetActorLocation`을 통한 액터 충돌 스윕(Sweep) 처리 및 물리 이동.
- `SetActorRotation`과 짐벌 락(Gimbal Lock) 방지를 위한 `FQuat` 연동 기법.
- `AddActorWorldRotation`을 활용한 월드 좌표 기반 회전 누적 공식.

### 📂 [리플렉션 및 헤더 매크로 (Reflection & Macros)](./reflection.md)
- `UPROPERTY()` 변수 지정자 (`EditAnywhere`, `VisibleDefaultsOnly`, `BlueprintReadOnly` 등) 세부 속성 제어.
- `meta = (Key = Value)` 메타데이터 지시어를 통한 private 변수의 제한적 허용(`AllowPrivateAccess`) 및 값 제한(`ClampMin/Max`).
- `UFUNCTION()` 매크로에 연동되는 `BlueprintCallable`, `BlueprintImplementableEvent`, `BlueprintNativeEvent` 및 가상 함수 본문(`_Implementation`) 작성 규칙.

### 📂 [컴포넌트 아키텍처 및 계층 구조 (Component Architecture & Creation)](./COMPONENTS.md)
- `UActorComponent`, `USceneComponent`, `UPrimitiveComponent` 간 상속 메커니즘과 메모리 오버헤드 통제.
- `CreateDefaultSubobject`를 이용한 생성자 단계 컴포넌트 스폰과 CDO(클래스 기본 객체) 템플릿 적재.
- `SetupAttachment()` 및 런타임 `AttachToComponent()` 구분을 통한 행렬 갱신 누출 버그 방지.
- `DefaultSceneRoot`에서 `MeshComponent`로 이어지는 트리 구조 앵커 연관성 및 소켓 부착(Socket Attachment) 수식 구조.

### 📂 [게임플레이 프레임워크 및 입력 (Framework & Input)](./framework.md)
- `APawn` 에이전트 클래스 기본 스펙과 입력 컴포넌트(`SetupPlayerInputComponent`) 캡슐화 바인딩.
- 플레이어 컨트롤러 빙의(Possession) 제어 콜백(`PossessedBy`, `UnPossessed`) 활용 구조.
- `EAutoReceiveInput::Player0` 지시어 지정을 활용한 비빙의 액터의 사용자 디바이스 입력 다이렉트 가로채기(Lever, Door 등 상호작용).

### 📂 [헤더 포함 최적화 및 전방 선언 (Include Optimization)](./include_optimization.md)
- 헤더 파일(`.h`)에서의 **전방 선언(Forward Declaration)** 적용을 통한 전처리 파일 팽창 차단.
- 소스 파일(`.cpp`) 구체 물리 include 구성을 통한 도미노 컴파일 방지 및 빌드 시간 단축 기법.
