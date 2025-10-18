# RULE 12: PROJECT_FILE_LOCATIONS_RULE

**우선순위**: ⭐⭐ (중요 - 자주 참조)

**목적**: 프로젝트의 주요 파일 위치를 한눈에 파악하여 작업 효율성을 높이고, 길 가면서 주석을 읽으며 프로젝트를 이해할 수 있도록 지원

---

## 📂 핵심 파일 위치 (빠른 참조)

### 🎯 Behavior Tree 파일들

```
Assets/Units/Worker/Worker BT.asset
```

**설명**: Worker 유닛의 Behavior Tree 정의 파일
- Unity Behavior 패키지 사용
- Worker와 WorkerThreeRobot이 공통으로 사용
- 주요 행동: Move, Gather, ReturnSupplies, BuildBuilding, Stop

**주석으로 배우기**:
- BT 파일은 Unity Editor에서 시각적으로 편집 가능
- 코드로 보려면 Unity MCP를 통해 접근 필요

---

### 💻 Worker 관련 스크립트

#### 1. Worker.cs
```
Assets/Scripts/Units/Worker.cs
```

**역할**:
- AbstractUnit 상속
- IGatherer, IBuildingBuilder 인터페이스 구현
- 자원 채취(Gather), 건물 건설(Build) 기능
- Event Bus를 통한 공급 이벤트 발행

**주요 메서드**:
- `Gather(GatherableSupply supply)`: 자원 채취 명령
- `ReturnSupplies(GameObject commandPost)`: 자원 반납
- `Build(BuildingSO building, Vector3 targetLocation)`: 건물 건설
- `HandleGatherSupplies(...)`: 이벤트 핸들러

**주석 읽기 포인트**:
- Line 12-23: HasSupplies 프로퍼티 (Blackboard Variable 사용법)
- Line 28-31: Event Channel 구독 패턴
- Line 68-71: Event Bus 발행 (Bus<SuppliesEvent>.Raise)

---

#### 2. WorkerThreeRobot.cs
```
Assets/Scripts/Units/WorkerThreeRobot.cs
```

**역할**:
- Worker 상속 (Gather 기능 상속)
- Nova 1492 스타일 3-파트 로봇 시스템
- Head, Body, Legs 각각 다른 속도로 회전
- NavMeshAgent 통합

**주요 특징**:
- Head: 180도/초 (빠름)
- Body: 90도/초 (중간)
- Legs: 45도/초 (느림)

**주석 읽기 포인트**:
- Line 6-10: 클래스 설명 (3-파트 시스템 개요)
- Line 14-21: 각 파트의 역할과 회전 속도 설명
- Line 55-78: InitializePartRotation() - RobotPart 초기화 과정
- Line 80-97: RotateParts() - 각 파트가 다른 속도로 회전하는 로직
- Line 99-116: ValidateRobotParts() - 로봇 파츠 검증 (한글 주석)
- Line 118-138: SetRobotPart() - 런타임에 파츠 교체 (한글 주석)

---

#### 3. AbstractUnit.cs
```
Assets/Scripts/Units/AbstractUnit.cs
```

**역할**:
- 모든 유닛의 기본 클래스
- NavMeshAgent, BehaviorGraphAgent 필수 컴포넌트
- IMoveable 인터페이스 구현
- UnitSpawnEvent 발행

**주요 메서드**:
- `MoveTo(Vector3 position)`: 유닛 이동
- `Stop()`: 유닛 정지

**주석 읽기 포인트**:
- Line 9: RequireComponent 속성 (필수 컴포넌트 명시)
- Line 26: UnitSpawnEvent 발행 (Event Bus 사용 예시)

---

#### 4. RobotPart.cs
```
Assets/Scripts/Units/RobotPart.cs
```

**역할**:
- 개별 로봇 파트의 독립적인 회전 처리
- Nova 1492 스타일: 각 파트가 다른 속도로 회전
- 상대 회전(Relative Rotation) 시스템

**주요 메서드**:
- `RotateTowards(Vector3 targetDirection, float rootRotationSpeed)`: 목표 방향으로 회전
- `SetRotationSpeed(float speed)`: 회전 속도 설정

**주석 읽기 포인트**:
- Line 5-8: RobotPart 클래스 설명
- Line 24-53: RotateTowards() - 상대 회전 로직 상세 설명
- Line 74-83: RobotPartType enum 정의

---

### 🎮 프리팹 파일들

#### Worker 프리팹
```
Assets/Units/Worker/Worker.prefab
Assets/Units/Worker/WorkerThreeRobot.prefab
```

**설명**:
- Worker.prefab: 기본 Worker 프리팹
- WorkerThreeRobot.prefab: 3-파트 로봇 Worker 프리팹 (Nova 1492 스타일)

**컴포넌트 구성**:
- NavMeshAgent (이동)
- BehaviorGraphAgent (행동 트리)
- Worker 또는 WorkerThreeRobot 스크립트
- DecalProjector (선택 표시)

---

### 📦 ScriptableObject 에셋

#### UnitSO
```
Assets/Units/Worker/Worker.asset
Assets/Units/Worker/WorkerThreeRobot1.asset
```

**역할**: 유닛 데이터 정의 (이름, 설명, 아이콘 등)

#### BuildingSO Commands
```
Assets/Units/Buildings/Commands/Build Worker.asset
Assets/Units/Buildings/Commands/Build WorkerThreeRobot.asset
```

**역할**: 건물에서 유닛을 생산하는 명령어 정의

---

### 🗂️ 프로젝트 폴더 구조

#### 스크립트 폴더
```
Assets/Scripts/
├── Behavior/          # Behavior Tree 커스텀 노드
├── Commands/          # 명령 시스템 (Move, Gather, Build 등)
├── Editor/            # Unity Editor 확장 (ThreePartRobotBuilder 등)
├── Environment/       # 환경 오브젝트 (GatherableSupply, BaseBuilding 등)
├── EventBus/          # Event Bus 시스템 (Bus.cs, IEvent.cs)
├── Events/            # 이벤트 정의 (UnitSelectedEvent, SupplyEvent 등)
├── Player/            # 플레이어 입력 (PlayerInput.cs)
├── UI/                # UI 시스템
├── Units/             # 유닛 스크립트 (Worker, AbstractUnit 등)
└── Utilities/         # 유틸리티 스크립트
```

#### 에셋 폴더
```
Assets/Units/Worker/   # Worker 관련 프리팹, BT, ScriptableObject
Assets/Textures/       # 텍스처 파일들
Assets/Materials/      # 머티리얼 파일들
Assets/Prefabs/        # 기타 프리팹들
```

---

## 📚 강의 노트 및 문서

### 강의 노트 (HTML)
```
lecture/lecture-notes/complete-lecture-notes-4.html
```

**최신 강의 노트**: 모든 강의 내용이 Toss 스타일로 정리됨
- Lecture 1~15 포함
- 코드 예제에 상세한 한글 주석
- 각 강의별 "왜 필요한가?" 섹션

### 아키텍처 문서
```
lecture/urts-course/ARCHITECTURE_KR.html
```

**프로젝트 아키텍처 문서**: PlantUML 다이어그램 포함
- 클래스 구조 및 관계도
- Event Bus 시스템 설명
- Behavior Tree 통합

---

## 🔍 인터페이스 및 주요 클래스

### 인터페이스 위치

#### IGatherer.cs
```
Assets/Scripts/Units/IGatherer.cs
```
**설명**: 자원 채취 인터페이스 (Worker, WorkerThreeRobot 구현)

#### IBuildingBuilder.cs
```
Assets/Scripts/Units/IBuildingBuilder.cs
```
**설명**: 건물 건설 인터페이스 (Worker 구현)

#### IMoveable.cs
```
Assets/Scripts/Units/IMoveable.cs
```
**설명**: 이동 가능한 유닛 인터페이스 (AbstractUnit 구현)

---

### Event Bus 시스템

#### Bus.cs
```
Assets/Scripts/EventBus/Bus.cs
```

**역할**: Generic Event Bus 구현
- Line 3-9: 전체 코드가 10줄 미만으로 매우 간결
- Generic Type과 Delegate 활용
- Null-conditional 연산자 (?.) 사용

**주석 읽기 포인트**:
- 코드 자체가 매우 짧고 명확하여 주석 없음
- Lecture 14 강의 노트에서 자세한 설명 확인

#### IEvent.cs
```
Assets/Scripts/EventBus/IEvent.cs
```

**역할**: 모든 이벤트가 구현해야 하는 마커 인터페이스

---

### 이벤트 정의들

```
Assets/Scripts/Events/
├── UnitSelectedEvent.cs        # 유닛 선택 이벤트
├── UnitDeselectedEvent.cs      # 유닛 선택 해제 이벤트
├── UnitSpawnEvent.cs           # 유닛 생성 이벤트
├── SupplyEvent.cs              # 자원 이벤트
└── (기타 이벤트들...)
```

**주석 읽기 포인트**:
- 각 이벤트는 struct로 정의 (값 타입)
- IEvent 인터페이스 구현
- 필요한 데이터만 포함 (경량화)

---

## 🛠️ Unity Editor 도구

### ThreePartRobotBuilder
```
Assets/Scripts/Editor/ThreePartRobotBuilder.cs
```

**역할**: 3-파트 로봇 자동 생성 도구
- Menu: "RTS/Build Three Part Robot Prefab"
- SerializedObject 사용하여 모든 필드 자동 할당
- 프리팹 생성 후 즉시 사용 가능

**관련 룰**: RULE_8_THREE_PART_ROBOT_BUILDER.md

---

## 📖 주석으로 배우는 법 (길 가면서 읽기)

### 추천 읽기 순서:

1. **Event Bus 시스템 이해** (5분)
   - `Bus.cs` (10줄) - 가장 짧고 핵심적
   - `IEvent.cs` (5줄) - 마커 인터페이스
   - `UnitSelectedEvent.cs` (10줄) - 실제 이벤트 예시

2. **유닛 구조 이해** (10분)
   - `AbstractUnit.cs` - 모든 유닛의 기본
   - `Worker.cs` - 구체적인 유닛 구현
   - `IGatherer.cs`, `IBuildingBuilder.cs` - 인터페이스

3. **3-파트 로봇 시스템 이해** (15분)
   - `RobotPart.cs` - 개별 파트 회전 로직
   - `WorkerThreeRobot.cs` - 3-파트 통합 시스템

4. **강의 노트에서 상세 설명 읽기** (30분)
   - `complete-lecture-notes-4.html`
   - Lecture 14: Event Bus
   - Lecture 15: UnitDeselectedEvent

---

## 💡 팁

### 파일 빠르게 찾기 (Unity Editor)

1. **스크립트**: `Ctrl+P` (Project 검색) → 파일명 입력
2. **프리팹**: `Assets/Units/Worker/` 폴더 북마크
3. **BT 편집**: Worker BT.asset 더블클릭 → Behavior Graph 창 열림

### 파일 빠르게 찾기 (VS Code / IDE)

1. **Cmd+P** (Quick Open) → 파일명 입력
2. **Cmd+Shift+F** (전역 검색) → 클래스명, 메서드명 검색

### Unity MCP로 BT 읽기

```bash
mcp__UnityMCP__read_resource
uri: unity://Assets/Units/Worker/Worker BT.asset
```

---

## 🔄 업데이트 이력

- **2025-10-18**: Rule 12 생성 - Worker BT 및 주요 파일 위치 문서화
- **목적**: 길 가면서 주석 읽으며 프로젝트 이해할 수 있도록 지원

---

**관련 룰**:
- Rule 9: DETAILED_CODE_COMMENTS_RULE (주석 작성 규칙)
- Rule 8: THREE_PART_ROBOT_BUILDER_RULE (로봇 빌더 도구)
- Rule 6: LECTURE_COMMAND_PROCESSING_RULE (강의 노트 작성)
