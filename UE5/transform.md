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
