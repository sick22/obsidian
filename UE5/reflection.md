[◀ UE5 C++ 개발 대시보드로 돌아가기](./UE5.md)

# Unreal Engine 5 리플렉션 및 헤더 매크로 가이드

언리얼 엔진의 가비지 컬렉션, 직렬화, 에디터 및 블루프린트 연동을 담당하는 핵심 매크로 시스템인 `UPROPERTY()`와 `UFUNCTION()`의 세부 지정자를 다룹니다.

---

## 1. UPROPERTY()
C++ 클래스의 멤버 변수를 언리얼 엔진의 리플렉션(Reflection) 시스템에 등록하여 가비지 컬렉션(Garbage Collection), 직렬화(Serialization), 에디터 노출 및 블루프린트 연동 등의 기능을 수행할 수 있도록 지원하는 매크로입니다.

### 핵심 목적
- 언리얼 엔진 가비지 컬렉터(Garbage Collector)의 수명 주기에 C++ 멤버 변수를 참조 대상으로 관리하기 위함.
- 언리얼 에디터의 디테일(Details) 패널에 멤버 변수를 노출 및 제어하기 위함.
- 블루프린트(Blueprint) 그래프 내에서 변수의 상태를 조회하거나 수정할 수 있도록 노출하기 위함.

### 파라미터 상세 (지정자 - Specifier)
- `EditAnywhere`: 에디터의 클래스 디폴트(아키타입)와 레벨에 배치된 인스턴스 디테일 창 모두에서 편집 가능하게 설정합니다.
- `VisibleAnywhere`: 에디터의 클래스 디폴트와 레벨에 배치된 인스턴스 디테일 창 모두에서 보이기만 하고 편집은 불가능하도록 설정합니다.
- `EditDefaultsOnly`: 블루프린트 클래스 디폴트(아키타입) 창에서만 편집할 수 있으며, 레벨에 배치된 개별 인스턴스 창에서는 편집할 수 없도록 제한합니다.
- `VisibleDefaultsOnly`: 블루프린트 클래스 디폴트 창에서만 값을 확인할 수 있으며, 레벨에 배치된 개별 인스턴스 창에서는 표시되지 않도록 제한합니다. (공통 속성의 확인에 유용)
- `EditInstanceOnly`: 레벨에 배치된 개별 인스턴스 창에서만 편집할 수 있으며, 블루프린트 클래스 디폴트 창에서는 편집할 수 없도록 제한합니다.
- `VisibleInstanceOnly`: 레벨에 배치된 개별 인스턴스 창에서만 값을 확인할 수 있으며, 블루프린트 클래스 디폴트 창에서는 표시되지 않도록 제한합니다. (실시간 변동값 모니터링에 유용)
- `BlueprintReadOnly`: 블루프린트 그래프 내에서 Get 노드로 값을 읽을 수만 있도록 노출합니다.
- `BlueprintReadWrite`: 블루프린트 그래프 내에서 Get 및 Set 노드를 모두 사용하여 편집 가능하도록 노출합니다.
- `Category = "카테고리명"`: 에디터 디테일 패널 내에서 변수가 표시될 그룹의 경로를 지정합니다.

### 반환 값
- 매크로 지시어(Macro Directive)이므로 반환 값은 존재하지 않습니다. 컴파일 전 언리얼 헤더 툴(Unreal Header Tool, UHT)이 메타데이터 분석용 소스코드를 자동 생성하는 식별자로 사용됩니다.

### 기술적 팁 (Technical Tips)
- **가비지 컬렉션(GC):** `UObject` 기반 클래스의 포인터 멤버 변수에 `UPROPERTY()` 매크로를 선언하지 않으면, 가비지 컬렉터가 참조 여부를 감지할 수 없어 해당 객체가 도중에 파괴되어 댕글링 포인터(Dangling Pointer)가 될 위험이 큽니다.
- **성능 고려:** 모든 변수에 불필요하게 `UPROPERTY()`를 명시하면 컴파일 시간 및 메타데이터 적재 메모리가 늘어날 수 있습니다. 리플렉션이 필요하지 않은 단순 프리미티브 타입은 명시를 지양해야 합니다.
- **포인터 보호:** 포인터를 해제할 때 `UPROPERTY()`로 등록되어 있다면 해당 소멸 지점에서 가비지 컬렉터가 스마트 포인터 메커니즘을 통해 참조 유효성 검사 및 정리를 지원합니다.

### 코드 예시
```cpp
UCLASS()
class MYPROJECT_API AMyCharacter : public ACharacter
{
    GENERATED_BODY()

public:
    // 에디터에서 자유롭게 편집 가능하며 블루프린트에서도 읽기/쓰기가 가능한 정수 변수
    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Character Stats")
    int32 Health;

    // 에디터 및 블루프린트에서 컴포넌트 정보 확인만 가능한 컴포넌트 포인터
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Components")
    class USpringArmComponent* CameraBoom;
};
```

---

## 2. UPROPERTY 메타데이터 지정자 (meta)
`UPROPERTY()` 매크로 내부에 `meta = (Key = Value)` 또는 `meta = (Key)` 형태로 추가적인 설정 메타데이터를 선언하여, 언리얼 에디터의 UI 표기 방식, 블루프린트 연동 규칙, 변수 값의 유효 범위 제약 등을 제어하는 속성 지정자입니다.

### 핵심 목적
- 변수의 C++ 이름과 독립적인 에디터 노출 형태를 제공하기 위함 (디스플레이 명칭 변경, 에디터 상 3D 위젯 제공 등).
- C++ 접근 제어자(private 등) 규칙과 언리얼 에디터/블루프린트 간의 노출 타협점을 마련하기 위함.
- 에디터에서 프로퍼티 입력 시 수치 범위의 강제화(Clamp) 및 특정 조건(EditCondition)에 따른 활성화 방식을 지정하기 위함.

### 파라미터 상세 (자주 사용되는 메타데이터 키)
- `AllowPrivateAccess = "true"`: `private` 멤버 변수를 블루프린트 자식 클래스 및 그래프에서 읽고 쓸 수 있도록 허용합니다. (C++의 캡슐화 원칙과 블루프린트의 유연성을 동시에 만족시킬 때 필수적으로 사용됩니다.)
- `ClampMin / ClampMax`: 에디터 디테일 패널에서 수치형 변수를 직접 타이핑하거나 드래그하여 입력할 수 있는 최솟값과 최댓값을 강제 제한합니다.
- `UIMin / UIMax`: 에디터 디테일 패널의 슬라이더 바를 조작하여 입력할 수 있는 최소/최대 범위를 규정합니다. (슬라이더 범위 밖의 값은 직접 텍스트로 타이핑하여 강제 입력할 수 있으나, Clamp 제한에 최종 종속됩니다.)
- `EditCondition = "PropertyName"`: 지정된 bool형 프로퍼티가 `true` 상태일 때만 해당 변수가 디테일 패널에서 활성화(편집 가능 상태)되도록 설정합니다.
- `DisplayName = "Name"`: 에디터 디테일 창에서 변수 이름 대신 노출할 문자열을 임의로 결정합니다.
- `MakeEditWidget = "true"`: `FVector` 또는 `FTransform` 멤버 변수를 대상으로 하며, 레벨 뷰포트 내에 3D 조작 기즈모(Gizmo) 위젯을 표시하여 시각적으로 좌표를 수정할 수 있게 합니다.

### 반환 값
- 매크로 내부의 특수 식별자 옵션군이므로 함수와 같은 반환 값은 존재하지 않습니다. 컴파일 전 언리얼 헤더 툴(UHT)에 의해 파싱되어 메타데이터 레지스트리에 등록됩니다.

### 기술적 팁 (Technical Tips)
- **컴파일 경고 부재:** C++ 컴파일러(MSVC, Clang 등)는 `meta` 내부의 오탈자나 미지원 키값 오류를 감지하지 못합니다. 단지 언리얼 헤더 툴(UHT) 파싱 단계를 지나쳐 컴파일 후 언리얼 에디터 실행 시 해당 기능이 오작동(또는 무시)하므로 구문 오탈자에 각별한 주의가 필요합니다.
- **캡슐화 보존:** private 변수를 단순 블루프린트 에디터 수정 목적으로 public으로 전환하여 내부 설계 구조를 깨뜨리지 말고, `AllowPrivateAccess = "true"`를 활용하여 C++ 상의 캡슐화를 엄밀하게 보존할 것을 권장합니다.

### 코드 예시
```cpp
UCLASS()
class MYPROJECT_API AMyWeapon : public AActor
{
    GENERATED_BODY()

private:
    // private이지만 블루프린트 자식 및 인스턴스 디테일 패널에서 읽기 및 3D 배치가 가능함
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Weapon Stat", meta = (AllowPrivateAccess = "true"))
    int32 BaseDamage;

public:
    // 무기 발사 속도를 0~1000 사이로 강제 제한하며 슬라이더 가이드는 0~500으로 제한
    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Weapon Stat", meta = (ClampMin = "0.0", ClampMax = "1000.0", UIMin = "0.0", UIMax = "500.0"))
    float FireRate;

    // 특정 옵션 설정 여부
    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Weapon Mod")
    bool bEnableCustomRange;

    // bEnableCustomRange가 true일 때만 에디터에서 편집이 가능함
    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Weapon Mod", meta = (EditCondition = "bEnableCustomRange"))
    float CustomMaxRange;
};
```

---

## 3. UFUNCTION()
C++ 멤버 함수를 언리얼 엔진의 리플렉션(Reflection) 시스템에 등록하여 블루프린트 호출/구현 연동, RPC(Remote Procedure Call) 네트워크 복제, 타이머 및 델리게이트 바인딩 등을 가능하게 하는 매크로입니다.

### 핵심 목적
- C++에서 작성된 로직을 블루프린트 그래프 상에서 이벤트나 노드 형태로 호출할 수 있게 연동하기 위함.
- 네트워크 멀티플레이어 환경에서 서버/클라이언트 간에 호출을 원격으로 실행하는 RPC 구조를 수립하기 위함.
- 언리얼 엔진의 타이머 시스템(`FTimerManager`)이나 다이내믹 델리게이트 바인딩 시 리플렉션 시스템을 경유해 안전하게 콜백을 등록하기 위함.

### 파라미터 상세 (자주 사용되는 지정자)
- `BlueprintCallable`: 블루프린트 이벤트 그래프 내에서 호출할 수 있는 일반 실행 노드로 노출합니다.
- `BlueprintImplementableEvent`: C++에서는 선언만 정의하고, 실제 실행 본문(Body)은 블루프린트 자식 클래스에서 이벤트 노드로 구현하도록 설정합니다. (C++ 파일에 해당 함수의 바디 구현부를 절대 작성하지 않아야 컴파일 에러를 막을 수 있습니다.)
- `BlueprintNativeEvent`: C++에서 기본 동작을 구현하고, 블루프린트 자식 클래스에서 오버라이드하여 추가 로직을 확장할 수 있게 설정합니다. C++의 실제 본문은 접미사 `_Implementation`을 붙인 형태로 작성해야 합니다.
- `BlueprintPure`: 실행 핀(Exec Pin)이 생기지 않는 순수 데이터 연산 노드로 노출합니다. 대상의 상태를 변형시키지 않고 값을 단순 연산 및 반환(Getter)하는 용도로 쓰이며, 주로 `const` 멤버 함수에 권장됩니다.
- `Server / Client / NetMulticast`: 네트워크 RPC 함수로 선언합니다. 각각 서버 권한(Server), 소유 클라이언트(Client), 또는 전체 클라이언트(NetMulticast) 방향으로 복제 실행됩니다. (`Reliable` 혹은 `Unreliable` 지시어와 함께 지정되어야 합니다.)
- `meta = (AllowPrivateAccess = "true")`: `private` 함수를 블루프린트에 이벤트나 노드 형태로 노출시킬 수 있게 권한 접근을 완화합니다.

### 반환 값
- 매크로 지시어이므로 함수와 같은 반환 값은 존재하지 않습니다. 컴파일 이전 언리얼 헤더 툴(UHT)에 의해 해당 함수를 실행 및 바인딩하기 위한 리플렉션 래퍼 코드가 자동 생성됩니다.

### 기술적 팁 (Technical Tips)
- **바인딩 무결성:** 언리얼 엔진의 `GetWorldTimerManager().SetTimer` 또는 Dynamic Delegate의 `BindUFunction` 등을 활용해 콜백 함수를 지정할 때, 대상 함수에 `UFUNCTION()` 매크로를 빠뜨리면 런타임 바인딩 실패 경고가 발생하거나 콜백이 정상적으로 유발되지 않습니다.
- **가상화 결합:** `BlueprintNativeEvent` 함수는 내부적으로 다형성 메커니즘을 흉내 내므로 상속 관계에서 오버라이딩 시 `virtual` 키워드는 실제 `함수명`이 아닌 `함수명_Implementation` 선언부에 붙여야 안정적으로 동작합니다.
- **수행 오버헤드:** 리플렉션을 거치는 함수 호출 과정은 일반 C++ 함수 직접 호출에 비해 성능 오버헤드가 수반되므로, Tick 내부 등 매 프레임 수천 번 이상 지속적으로 호출되는 임계 경로(Hot Path)에는 블루프린트 연동 오버헤드를 낮추는 설계가 필수적입니다.

### 코드 예시
```cpp
UCLASS()
class MYPROJECT_API AMyCharacter : public ACharacter
{
    GENERATED_BODY()

public:
    // C++에서 본문을 구현하며, 블루프린트에서 일반 노드로 실행함
    UFUNCTION(BlueprintCallable, Category = "Character | Combat")
    void FireWeapon();

    // C++ 파일에 별도 구현을 두지 않고, 블루프린트에서 기능을 정의하여 실행하는 이벤트
    UFUNCTION(BlueprintImplementableEvent, Category = "Character | Combat")
    void OnReceiveDamage(float DamageAmount);

    // C++ 기본 기능을 가지고 있으며, 블루프린트에서 기능을 오버라이드할 수 있음
    UFUNCTION(BlueprintNativeEvent, BlueprintCallable, Category = "Character | Movement")
    bool CanSprint();
    virtual bool CanSprint_Implementation(); // C++ 본문 구현용 선언
};
```
