[◀ UE5 C++ 개발 대시보드로 돌아가기](./UE5.md)

# Unreal Engine 5 액터 트랜스폼 및 이동 가이드

월드 및 로컬 공간 내에서 액터(Actor)의 위치, 회전값을 변경하고 제어하는 물리 및 기하학적 핵심 API를 다룹니다.

---

## 1. SetActorLocation
월드 내에서 액터의 위치를 특정 좌표(`FVector`)로 즉시 이동시킵니다.

### 기본 구문
```cpp
bool SetActorLocation(FVector NewLocation, bool bSweep, FHitResult* OutSweepHitResult, ETeleportType Teleport);
```

### 파라미터 상세
- `FVector NewLocation`: 이동할 새로운 월드 좌표입니다.
- `bool bSweep`: `true`인 경우 이동 경로상의 충돌을 검사합니다.
- `FHitResult* OutSweepHitResult`: 충돌 정보를 담을 구조체 포인터입니다.
- `ETeleportType Teleport`: 물리 시뮬레이션 처리 방식 (`None` 또는 `TeleportPhysics`).

---

## 2. SetActorRotation
월드 내에서 액터의 회전값(Orientation)을 특정 회전 정보(`FRotator` 또는 `FQuat`)로 즉시 변경합니다.

### 기본 구문
```cpp
bool SetActorRotation(FRotator NewRotation, ETeleportType Teleport);
bool SetActorRotation(const FQuat& NewRotation, ETeleportType Teleport);
```

### 파라미터 상세
- `FRotator NewRotation`: 액터에게 부여할 새로운 회전값입니다.
- `FQuat NewRotation`: 쿼터니언을 사용한 회전값 지정입니다.
- `ETeleportType Teleport`: 물리 시뮬레이션 처리 방식 (`None` 또는 `TeleportPhysics`).

### 기술적 팁 (Technical Tips)
- **짐벌 락(Gimbal Lock):** `FRotator` 사용 시 특정 각도에서 발생하는 계산 오류를 피하려면 오일러 각이 아닌 쿼터니언 `FQuat` 사용을 권장합니다.
- **카메라 회전:** 컨트롤러가 제어하는 폰의 경우 `bUseControllerRotationYaw` 등의 옵션과 충돌할 수 있으니 주의하십시오.
- **UE 5.7 관련:** 물리 엔진 가속화와 관련하여 `TeleportPhysics` 플래그의 안정성이 더욱 향상되었습니다.

### 예시 코드
```cpp
FRotator NewRot = FRotator(0.0f, 90.0f, 0.0f);
bool bSuccess = SetActorRotation(NewRot, ETeleportType::TeleportPhysics);
```

---

## 3. AddActorWorldRotation
액터의 현재 월드 회전값에 지정된 델타 회전값(`FRotator` 또는 `FQuat`)을 추가합니다.

### 기본 구문
```cpp
void AddActorWorldRotation(FRotator DeltaRotation, bool bSweep = false, FHitResult* OutSweepHitResult = nullptr, ETeleportType Teleport = ETeleportType::None);
void AddActorWorldRotation(const FQuat& DeltaRotation, bool bSweep = false, FHitResult* OutSweepHitResult = nullptr, ETeleportType Teleport = ETeleportType::None);
```

### 파라미터 상세
- `FRotator/FQuat DeltaRotation`: 현재 회전에 추가할 회전량입니다.
- `bool bSweep`: `true`일 경우 회전 중 충돌을 검사합니다.
- `FHitResult* OutSweepHitResult`: 충돌 발생 시 상세 정보를 저장할 포인터입니다.
- `ETeleportType Teleport`: 물리 시뮬레이션 본체 유지 여부를 결정합니다.

### 기술적 팁 (Technical Tips)
- **공간 기준:** 월드 좌표계 기준이므로 액터의 로컬 방향과 관계없이 일정하게 회전합니다. 로컬 기준 회전은 `AddActorLocalRotation`을 사용하십시오.
- **짐벌 락 방지:** 복잡한 회전 연산이 포함될 경우 `FQuat`를 사용하는 것이 안전합니다.
- **물리 엔진:** `TeleportPhysics` 옵션은 물리 시뮬레이션 중인 오브젝트가 회전할 때 주변 물체에 비정상적인 충격력을 전달하지 않도록 방지합니다.

### 예시 코드
```cpp
// 매 틱마다 월드 위쪽 방향을 축으로 회전
FRotator RotationStep(0.f, 10.f * DeltaSeconds, 0.f);
AddActorWorldRotation(RotationStep, false, nullptr, ETeleportType::None);
```


## 4. GetControlRotation을 이용한 화면 방향 이동 구현

### [AController::GetControlRotation]
- **핵심 목적:** 컨트롤러가 지향하고 있는 현재 회전값(시선 및 카메라 조준 각도)을 쿼리하여 게임플레이 입력 기준 좌표계를 설정하는 API입니다.
- **파라미터 상세:**
  - 없음 (인수 없이 호출).
- **반환 값:**
  - `FRotator`: 컨트롤러가 지향하는 Pitch(상하), Yaw(좌우), Roll(기울기)의 회전 각도 구조체입니다.
- **기술적 팁 (Technical Tips):**
  - **피치/롤 값 소거 필요성:** 캐릭터가 3차원 지면을 이족 보행할 때, 카메라의 시선 각도(Pitch)가 하늘이나 땅을 향해 꺾여 있을 경우 전방 벡터 계산 시 상하 벡터 성분이 섞이게 됩니다. 이 성분을 그대로 이동에 인가하면 캐릭터가 공중으로 떠오르거나 땅 밑으로 파고들려 하는 물리 충돌 왜곡 현상이 발생하므로, 반드시 Yaw(좌우) 값만 필터링하여 평면(2D) 기준 회전으로 억제해야 합니다.
  - **빙의 여부 확인:** `APawn::GetControlRotation()` 호출 시 Pawn에 컨트롤러가 연결되어 있지 않다면 `FRotator::ZeroRotator`가 반환되므로 사전 예외 처리가 필요합니다.
- **코드 예시:**
  ```cpp
  if (Controller != nullptr)
  { 
      // 1. 컨트롤러 시선 각도 획득
      const FRotator Rotation = Controller->GetControlRotation();
      
      // 2. 오직 좌우 회전각(Yaw) 성분만 분리 추출
      const FRotator YawRotation(0.f, Rotation.Yaw, 0.f);

      // 3. 추출된 Yaw 방향을 기준으로 월드 공간 상의 전방 및 우측 단위 방향 벡터 유도
      const FVector ForwardDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::X);
      const FVector RightDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::Y);

      // 4. 입력 세기 값과 조합하여 최종 이동 명령 인가
      AddMovementInput(ForwardDirection, Value.Get<FVector2D>().Y);
      AddMovementInput(RightDirection, Value.Get<FVector2D>().X);
  }
  ```

### 기하학적 이동 방향 추출 메커니즘
화면(카메라)이 지향하는 방향으로 캐릭터가 움직이게 하려면 회전 성분으로부터 방향 벡터를 추출하는 행렬 대수 연산이 내재됩니다.
1. **Yaw 회전 행렬 구조:**
   Yaw 단독 회전 $\theta$에 대한 3D 회전 행렬 $R_{\text{yaw}}(\theta)$는 다음과 같이 월드 Z축을 기준으로 평면 성분을 정사영시킵니다.
   $$
   R_{\text{yaw}}(\theta) = \begin{bmatrix} \cos\theta & -\sin\theta & 0 \\ \sin\theta & \cos\theta & 0 \\ 0 & 0 & 1 \end{bmatrix}
   $$
2. **단위 방향 벡터 유도:**
   - **X축 성분 (전방 벡터):** 회전 행렬의 첫 번째 열(Column)에 대응하며, 이는 월드 공간에서 캐릭터가 바라봐야 할 전방 1단위 크기의 기하학적 방향 벡터(`EAxis::X`)와 완벽히 일치합니다.
   - **Y축 성분 (우측 벡터):** 회전 행렬의 두 번째 열에 대응하며, 캐릭터 시선 기준에서 직교하는 우측 방향 벡터(`EAxis::Y`)를 매칭합니다.



## 5. bUseControllerRotation과 bOrientRotationToMovement 회전 설정

### [bUseControllerRotation (Yaw / Pitch / Roll)]
- **핵심 목적:** 폰(Pawn)의 월드 회전을 플레이어 컨트롤러의 조준 각도(Control Rotation)에 동적으로 직접 동기화시킬 것인지 제어하는 설정 변수입니다.
- **변수 상세 및 기능:**
  - `bUseControllerRotationYaw`: `true`인 경우 컨트롤러 좌우 각도 회전과 캐릭터 본체의 Yaw 각도를 완전히 일치시킵니다.
  - `bUseControllerRotationPitch`: `true`인 경우 컨트롤러 상하 각도에 맞춰 캐릭터 본체를 상하 회전시킵니다 (일반 이족보행 캐릭터는 전복을 방지하기 위해 `false`로 고정합니다).
  - `bUseControllerRotationRoll`: `true`인 경우 컨트롤러 롤 각도에 따라 캐릭터 본체를 기울입니다.
- **동작 제어 패턴:**
  - `true`: 캐릭터가 상시 컨트롤러 정면 방향(보통 카메라 방향)을 1대1로 응시하며 움직입니다 (예: FPS 게임, TPS 백뷰).
  - `false`: 시선 각도와 관계없이 캐릭터 본체는 자유로운 회전 상태를 유지합니다.
- **기술적 팁 (Technical Tips):**
  - 이 변수들을 `false`로 지정하고, 스프링 암 컴포넌트(`USpringArmComponent`)의 `bUsePawnControlRotation = true` 설정을 조절하면 카메라만 폰 주변을 공회전하는 3인칭 프리 카메라 구조를 구현할 수 있습니다.
- **코드 예시:**
  ```cpp
  bUseControllerRotationYaw = true;
  bUseControllerRotationPitch = false;
  bUseControllerRotationRoll = false;
  ```

### [UCharacterMovementComponent::bOrientRotationToMovement]
- **핵심 목적:** 캐릭터가 현재 조이스틱이나 키보드로 가속(이동 입력)하려는 지향 방향으로 본체 회전을 부드럽게 정렬시켜 주는 움직임 컴포넌트의 기능 옵션입니다.
- **변수 상세 및 기능:**
  - `bOrientRotationToMovement`: 캐릭터 무브먼트 컴포넌트 소속의 불리언 변수입니다.
- **동작 제어 패턴:**
  - `true`: 플레이어의 키 입력 벡터(예: W/S/A/D 입력 결과물인 가속 벡터 방향)로 몸체의 정면이 자동 회전 정렬됩니다. 회전하는 물리적 지연 속도는 무브먼트 컴포넌트의 `RotationRate` 설정값을 추종합니다.
  - `false`: 이동 입력 방향과 폰 회전 방향이 기하학적으로 연동되지 않습니다.
- **기술적 팁 (Technical Tips):**
  - **상충 현상 방지:** 이 속성이 활성화되려면 반드시 부모 액터의 `bUseControllerRotationYaw`가 `false` 상태여야만 합니다. 액터가 컨트롤러 방향을 강제 추종하면 가속도 방향 회전 연산이 덮어쓰여져 무효화되기 때문입니다.
  - **회전 속도 설정:** 부드럽고 시각적으로 자연스러운 턴 애니메이션을 연출하려면 `RotationRate`(초당 각도 제한) 조정을 병행해 주어야 합니다.
- **코드 예시:**
  ```cpp
  #include "GameFramework/CharacterMovementComponent.h"

  AMyCharacter::AMyCharacter()
  { 
      // 1. 컨트롤러 회전 추종을 차단하여 몸 회전 자율권 획득
      bUseControllerRotationYaw = false;

      // 2. 가속 입력 방향으로 몸 정면 자동 회전 활성화
      GetCharacterMovement()->bOrientRotationToMovement = true;

      // 3. 회전 감도 조율 (Yaw 기준 초당 540도 회전)
      GetCharacterMovement()->RotationRate = FRotator(0.f, 540.f, 0.f);
  }
  ```
