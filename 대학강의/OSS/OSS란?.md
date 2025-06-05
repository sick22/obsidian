# 3.13
(ppt 2장~3장)

## Open Source Software

- 유사어: libre software, free software, FOSS, FLOSS  
- 반대어: Proprietary software, Closed software  

### 용어 정리

- **COTS (Commercial Off-the-Shelf)**  
  완제품을 판매, 대여 또는 권한을 부여할 수 있는 것

- **NDI (Network & Digital Infrastructure)**  
  CCTV, 셋톱박스 등. 기기값을 지불하면 부가적으로 따라옴

### 정부가 OSS를 사용해야 하는가?

- 상세하게 평가 가능 → 보안 등 요구사항 확인 가능  
- 대규모 개별 리뷰를 통해 품질과 보안 향상  
- 기록물의 지속성 보존, 정책 투명성 보장  
- 아무런 대가 없이 반복적으로 복사 가능 (단, 지원은 개별 부담)  
- 개발 비용을 사용자와 공유  
- 특정 요구에 맞게 수정하거나 반례 확인 가능  
- 특정 소수 집단의 Vendor lock-in에서 자유로움  

예시: Digital.gov / Data.go.kr

## Open Source Software 개발 시작하기

### 기초작업
- 사용자 및 개발자 모으기
- Principle of Scaled Presentations

### OSS 개발 모델
1. 개발 시작  
2. 유저로부터 버그 리포트 수집  
3. 유저를 개발자로 전환 (User as Developer)  
- 개발불가 대신 "개발 예정"이라 표현

### 보유한 역량과 함께 시작하기

### 좋은 이름 선택하기
- 프로젝트의 목적을 유추할 수 있어야 함  
- 기억하기 쉬워야 함  
- 재치 있는 이름 (Pun) 가능  
- 상표권/기존 이름 중복 여부 확인  
  - www.uspto.gov  
  - www.kipris.or.kr  
- 도메인 및 소셜 계정 확보  
- 기여자들이 찾기 쉽게 네임스페이스 확보

### 명확한 Mission Statement 선정
- Quick Description 또는 Mission Statement  
- 5문장 이하  
  - 적절한 기술 수준 설명  
  - 최소한의 핵심 정보 포함 (예: Cluster, High-Availability)

### 프로젝트가 Free임을 언급
- 첫 페이지에 반드시 open-source 키워드 삽입

### 다운로드
- LTS (Long Term Support)  
- PGP (Pretty Good Privacy)

### 버전 관리 & 버그 트래커
- VCS (Version Control System) 예: GitHub, Bitbucket

### 문서화
- 편집하기 쉽고 간단해야 함  
- FAQ: 예상 가능한 질문만, 편리성 중심

### 데모, 스크린샷, 비디오
- 비디오는 4분 이내  
- 예: "Watch our 3-minute video"

### 라이선스

- **GPL (General Public License)**  

### Copyright
- 사용자가 정한 범위 내에서 사용 가능  
- 버그 책임은 제작자  
- 재라이선싱/상업적 사용 여부는 제작자가 결정

### Copyleft
- 자유롭게 사용 가능 (제작자 명시, 소스코드 공개 필요)  
- 독점 소프트웨어로 변경하여 배포 불가  
- 상업적 사용 가능

### Permissive
- 제작자 명시 필수  
- 오픈소스 공개 X, 소스코드 공개 X  
- 파생 작업을 독점 소프트웨어로 변경 가능  
- 상업적 사용 가능

### Creative Commons
- 자유 사용 가능  
- 제작자 명시  
- 모든 사용 가능 (재배포, 상업 등)

### 커뮤니티 어조

1. 사적인 토의 피하기  
   - 개방형 토의의 한계  
     - 이메일 지연  
     - 합의 형성에 시간 소요  
     - 신규 멤버 혼란  
     - 왜 X를 해결하는지 모름

2. 무례한 행동 무관용  
   - Zero-tolerance 정책  
     - 무례하거나 모욕적인 행동은 제재  
     - 기술 내용과 코멘트를 분리  
   - Meta-discussion 생성 금지  
     - 응답 안하거나 직접 사과  
     - 주장이나 가르침 X  
     - 사과를 강요하지 않음

3. 행동 강령  
   - 개인과 조직의 규범, 책임, 프랙티스 제시

4. 실용적이고 명확한 코드 리뷰  
   - 타인의 코드를 검토  
   - 코드 품질 개선 목적

5. 처음부터 열려 있는 마음  
   - 가능한 빨리 소스 오픈  
   - 오픈 전 체크리스트:  
     - 비밀번호 포함 여부  
     - 비밀 정보, 샘플 데이터  
     - 민감한 버그 정보  
     - 개인 코멘트  
     - 라이선스 문제  
     - 비표준 문서 포맷 (예: .hwp)  
     - non-portable 빌드 의존성

### 폐쇄적인 프로젝트 재오픈

1. 체크리스트 점검  
2. 사회적/관리적 이슈 고려  
3. 불안감은 무시, 발전적으로 생각  
4. 외부 성공 사례의 개발자 참여 유도

프로젝트 발표 시 참고:  
- 커뮤니케이션 네트워크 공식: C = n(n-1)/2

## 기술적 인프라

- 오픈소스는 협력 기술 중심  
- 디지털 표현 도구와 캡처 지원 도구  
- 도구 숙련도 → 다른 참여자 설득력 증가 → 성공 확률 ↑

### 브룩스 법칙
- 지체된 프로젝트에 인력 추가는 해가 된다  
- 소수의 뛰어난 개발자가 핵심

### 온라인 커뮤니케이션 특징
- 모든 커뮤니케이션은 글로 진행됨  
- 데이터에 대한 라벨링, 루팅  
- 반복 최소화  
- 오류 수정  
- 정보 간 연결

### 버전 컨트롤
- pull, commit message, clone, diff 숙지 필수

---

### VCS 사용하기 (Version Control System)

### Version Everything
- 코드가 아니더라도 가능한 한 버전화하자  
- 단, generated files는 제외  
  - 예: 시스템에 따라 생성되는 환경 의존 파일

### Browsability
- 누구든 코드나 변경 내역을 쉽게 탐색할 수 있어야 함

### Use Branches to Avoid Bottlenecks
- 병목을 방지하기 위해 브랜치 적극 활용

### Singularity of Information
- 정보는 하나의 출처만 유지되도록 관리

### Authorization
- 개발자의 활동 영역을 설정  
  - 예: Commit Access가 있는 사람만 특정 영역 변경 가능  
- Area 기반 권한 관리가 유용  
- 기술적 강제로 통제하려 하기보다, 효율을 고려

---

### 버그 트래킹 (Bug Tracking)

- 버그 트래킹의 정의: 시스템 결함이나 기능 요청 등의 이슈를 관리
- 다양한 용어:  
  - Issue, Ticket, Defect, Artifact, Request
- Ticket은 트래커 데이터베이스 내 하나의 항목을 의미

---

## pytest

- Fail이 발생하면 다음 테스트 실행 안됨
- test_*.py 또는 *_test.py 형식의 파일만 테스트로 인식

### 중요한 포인트
- Marker를 활용한 테스트 그룹 관리
- Fixture의 동작 원리 이해

> 테스트 하나하나가 중요함  
> (옵션1 관련 시험은 출제되지 않음)

---

## 패키지 릴리징 (Release)

### Stabilization
- 릴리즈 브랜치를 릴리즈 가능한 안정된 상태로 만드는 과정
---
### Feature-Based Release
- 마감 직전 기능 몰아넣기(last-minute feature rush)는 위험
- 변화가 많을수록 안정성 낮아지고 버그가 많이 생김
---
### Time-Based Release
- 정기적인 릴리즈 주기 유지 (예: 6개월마다)
- 버그나 기능 상태와 무관하게 릴리즈


docker 가상화 분리된 파트 시험
이미지 핸들링에 대한 커맨드 시험
- ``docker images``
- ``docker rmi <image>``

docker run 
-e 파트는 시험 X
-d 중

contaoner 와의 interaction
``docker exec <container><command>``

``docker diff <container>``
``doocker commit <container> <imagename>``