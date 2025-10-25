# RULE 12: PROJECT_FILE_LOCATIONS_RULE

**우선순위**: ⭐⭐ (중요 - 자주 참조)

**목적**: 프로젝트 주요 파일 위치 빠른 참조

---

## 📂 핵심 파일 위치

### Behavior Tree
```
Assets/Units/Worker/Worker BT.asset
```

### 스크립트
```
Assets/Scripts/Units/Worker.cs
Assets/Scripts/Units/WorkerThreeRobot.cs
Assets/Scripts/Units/AbstractUnit.cs
Assets/Scripts/Units/RobotPart.cs
Assets/Scripts/EventBus/Bus.cs
Assets/Scripts/EventBus/IEvent.cs
```

### 프리팹
```
Assets/Units/Worker/Worker.prefab
Assets/Units/Worker/WorkerThreeRobot.prefab
```

### 강의 노트
```
lecture/lecture-notes/complete-lecture-notes-4.html
lecture/lecture-notes/lecture-26-building-events.html
lecture/lecture-notes/lecture-sensing-nearby-units.html
lecture/lecture-notes/eventchannel-invoke-raise-explained.html
lecture/urts-course/ARCHITECTURE_KR.html
```

### TODO 파일
```
TODO/index.html
```

---

## 🗂️ 프로젝트 폴더 구조

```
Assets/Scripts/
├── Behavior/          # Behavior Tree 커스텀 노드
├── Commands/          # 명령 시스템
├── Editor/            # Unity Editor 확장
├── Environment/       # 환경 오브젝트
├── EventBus/          # Event Bus 시스템
├── Events/            # 이벤트 정의
├── Player/            # 플레이어 입력
├── UI/                # UI 시스템
├── Units/             # 유닛 스크립트
└── Utilities/         # 유틸리티
```

---

## 🔍 인터페이스 위치

```
Assets/Scripts/Units/IGatherer.cs          # 자원 채취 인터페이스
Assets/Scripts/Units/IBuildingBuilder.cs   # 건물 건설 인터페이스
Assets/Scripts/Units/IMoveable.cs          # 이동 가능 인터페이스
```

---

## 💡 빠른 검색

**Unity Editor**: `Ctrl+P` → 파일명 입력
**VS Code**: `Cmd+P` → 파일명 입력
**Unity MCP BT**: `mcp__UnityMCP__read_resource` → `unity://Assets/Units/Worker/Worker BT.asset`

---

**업데이트**: 2025-10-25 (EventChannel/Invoke/Raise 설명 노트 추가)
