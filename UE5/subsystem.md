[◀ UE5 C++ 개발 대시보드로 돌아가기](./UE5.md)

# Unreal Engine 서브시스템(Subsystem) 아키텍처 가이드

언리얼 엔진의 서브시스템(Subsystem)은 특정 수명 주기(Life Cycle)에 연결되어 자동 생성 및 소멸하는 영속적 모듈러 싱글톤(Singleton) 구조체입니다. 

---

## 1. USubsystem (서브시스템 기본 클래스)

### 핵심 목적
- 개발자가 메모리 생성/소멸 코드를 수동으로 작성하지 않고, 특정 수명 주기에 맞춰 동작하는 전역 관리자(Manager) 혹은 대행 매니저 객체를 안전하게 구현하기 위함.
- 전통적인 싱글톤 패턴이 유발하는 수명 주기 제어 난해성과 메모리 누수를 완전히 방지하고, 에디터 및 블루프린트와의 강결합 연동 인터페이스를 일원화하기 위함.

### 주요 서브시스템 수명 주기(Life Cycle) 및 분류
서브시스템은 정의하는 기본 클래스 상속 유형에 따라 엔진 내부 수명 주기와 완전 동기화됩니다.

1. **`UEngineSubsystem` (엔진 서브시스템):**
   - **수명:** 엔진 프로세스 실행 시 생성되어 프로세스가 완전히 종료될 때까지 상주합니다.
   - **용도:** 모듈별 공통 에디터 유틸리티, 전역 플랫폼 API 매니저 등 게임 외부의 모듈 제어.
2. **`UEditorSubsystem` (에디터 서브시스템):**
   - **수명:** 언리얼 에디터의 시작과 끝을 함께합니다.
   - **용도:** 개발 단계 에디터 툴, 위젯 확장 플러그인 연동. (패키징 빌드 시에는 완전 제외됨)
3. **`UGameInstanceSubsystem` (게임 인스턴스 서브시스템):**
   - **수명:** 게임 애플리케이션 가동 시 생성되어 프로그램 종료 시점까지 유지됩니다.
   - **용도:** 사용자 프로필 세이브 데이터 관리자, 사운드 볼륨 및 기본 설정 매니저, 서버 세션 호스팅 제어 관리자.
4. **`UWorldSubsystem` (월드 서브시스템):**
   - **수명:** 특정 레벨 월드(World)가 메모리에 로드되어 생성될 때 같이 스폰되고, 해당 레벨이 언로드될 때 자동으로 해제 소멸합니다.
   - **용도:** 특정 스테이지 전용 몬스터 스폰 매니저, 인게임 날씨/환경 제어 시스템.
5. **`ULocalPlayerSubsystem` (로컬 플레이어 서브시스템):**
   - **수명:** 로컬 플레이어 인스턴스가 접속(Login)하여 뷰포트를 확보할 때 함께 구성되고, 플레이어가 연결을 끊고 나갈 때 소멸합니다.
   - **용도:** `UEnhancedInputLocalPlayerSubsystem`과 같이 화면과 입력 채널을 쥐고 있는 기기 종속적 입력 관리자.

---

### API 취득 상세 (GetSubsystem)
원하는 컨텍스트(World, GameInstance, LocalPlayer 등)의 싱글톤 컬렉션으로부터 서브시스템 주소를 조회할 때 사용하는 언리얼 템플릿 게터 API입니다.

- **파라미터 상세:**
  - `GetSubsystem<T>()` [템플릿]:
    - `T`: 조회하여 가져올 서브시스템 자식 클래스 타입입니다 (예: `UMyScoreManagerSubsystem`).
  - 함수의 소유 주체가 되는 인스턴스(컨텍스트)에 맞춰 호출합니다.
    - `GetWorld()->GetSubsystem<UWorldSubsystem 자식>()`
    - `GetGameInstance()->GetSubsystem<UGameInstanceSubsystem 자식>()`
- **반환 값:**
  - 해당 생명 주기에 정상 등록되어 작동하고 있는 `<T>` 클래스 인스턴스의 주소 포인터(`T*`)를 안전 반환합니다.
  - 해당 컨텍스트가 소멸 중이거나 유효하지 않은 경우 `nullptr`을 안전하게 반환합니다.

---

### 기술적 팁 (Technical Tips)
- **가비지 컬렉션(GC) 자동 연동:**
  - `USubsystem`은 엔진 최하위 UObject를 상속받은 C++ 클래스이므로, 레벨 전환 또는 게임 종료 시 언리얼 가비지 컬렉터가 서브시스템 내부의 스마트 포인터 및 UObject 포인터들의 참조 카운트를 분석하여 메모리를 자동 회수하므로 `delete` 문을 사용할 필요가 없습니다.
- **블루프린트 완벽 노출:**
  - 클래스 선언 상단에 `UCLASS(BlueprintType)` 매크로 지정자만 설정하면, 별도의 중계 노드 작성 없이 블루프린트 그래프 상에서 즉시 `Get Subsystem` 노드를 통해 C++ 서브시스템 멤버 변수 및 기능 함수에 직접 접근할 수 있습니다.
- **멀티플레이어 환경 제약 (Replication 미지원):**
  - 서브시스템은 월드에 상주하는 `AActor`가 아닌 메모리 싱글톤 UObject 형태이기 때문에, 서버와 클라이언트 간의 네트워크 복제(Replication) 메커니즘을 지원하지 않습니다. 
  - 서버 조율이 필요한 공통 상태 데이터를 동기화하려면 서브시스템 내부 데이터를 `AGameState` 또는 플레이어의 `APlayerState` 액터를 경유하여 중계 처리해야 합니다.

---

### C++ 구현 예제 코드

#### `MyGameScoreSubsystem.h` (헤더 정의)
```cpp
#pragma once

#include "CoreMinimal.h"
#include "Subsystems/GameInstanceSubsystem.h" // 부모 수명 주기 헤더
#include "MyGameScoreSubsystem.generated.h"

// BlueprintType 지정을 통해 블루프린트 에디터에서 즉시 검색 및 쿼리 가능하도록 노출
UCLASS(BlueprintType, Blueprintable)
class MYPROJECT_API UMyGameScoreSubsystem : public UGameInstanceSubsystem
{
    GENERATED_BODY()

public:
    // 서브시스템 라이프사이클에 맞물려 엔진이 자동 가동시키는 가상 이벤트 함수들
    virtual void Initialize(FSubsystemCollectionBase& Collection) override;
    virtual void Deinitialize() override;

    // 인게임 데이터를 제어할 예시 인터페이스
    UFUNCTION(BlueprintCallable, Category = "Score")
    void AddScore(int32 Value);

    UFUNCTION(BlueprintPure, Category = "Score")
    int32 GetCurrentScore() const { return CurrentScore; }

private:
    // 가비지 컬렉션의 추적 범위에 포함시켜 메모리 보호
    UPROPERTY(VisibleAnywhere, Category = "Score")
    int32 CurrentScore = 0;
};
```

#### `MyGameScoreSubsystem.cpp` (구현 파일)
```cpp
#include "MyGameScoreSubsystem.h"

void UMyGameScoreSubsystem::Initialize(FSubsystemCollectionBase& Collection)
{
    Super::Initialize(Collection);

    // 게임 인스턴스가 기동될 때 최초 1회 자동 실행되는 전처리 구역
    CurrentScore = 0;
    UE_LOG(LogTemp, Warning, TEXT("MyGameScoreSubsystem: 인스턴스 초기화 완료"));
}

void UMyGameScoreSubsystem::Deinitialize()
{
    // 게임 프로세스가 종료되거나 소멸하기 직전 자동 실행되는 정리 구역
    UE_LOG(LogTemp, Warning, TEXT("MyGameScoreSubsystem: 인스턴스 해제 완료"));

    Super::Deinitialize();
}

void UMyGameScoreSubsystem::AddScore(int32 Value)
{
    CurrentScore += Value;
    UE_LOG(LogTemp, Log, TEXT("현재 누적 스코어: %d"), CurrentScore);
}
```

#### 타 C++ 액터에서 서브시스템 취득 및 가동 예시
```cpp
void AMyBaseCharacter::OnTargetKilled()
{
    // 게임 인스턴스 싱글톤 주소를 쿼리하여 그 아래에 속한 커스텀 서브시스템 검색
    if (UGameInstance* GI = GetGameInstance())
    {
        if (UMyGameScoreSubsystem* ScoreSubsystem = GI->GetSubsystem<UMyGameScoreSubsystem>())
        {
            // 서브시스템 멤버 함수 실행
            ScoreSubsystem->AddScore(100);
        }
    }
}
```
