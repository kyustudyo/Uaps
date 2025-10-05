# Claude Code 세션 인계 문서

## 📋 프로젝트 개요

**프로젝트명**: 노바1492 스타일 로봇 대전 게임 (Unity RTS)
**작업 위치**: `/Users/hankyulee/Desktop/Uaps`
**서브모듈**: `lecture/urts-course` (Unity 프로젝트)

## 🎯 프로젝트 목표

- 노바1492처럼 **3개 파츠로 조립 가능한 로봇** 시스템
- **4:4 팀전** 로봇 대전 게임
- Unity RTS 강의를 따라가며 **강의 노트 작성** (Toss 스타일 HTML)

---

## 📚 현재까지 작업 완료 사항

### 1. Git 구조
- **Uaps**: 메인 저장소
- **lecture/urts-course**: Git 서브모듈 (https://gitlab.com/GameDevTV/unity-real-time-strategy/urts-course.git)
- 작업 흐름:
  1. `urts-course`에서 커밋
  2. `Uaps`에서 서브모듈 참조 업데이트 커밋

### 2. 핵심 구현 완료
- ✅ **WorkerThreeRobot**: 3파츠 로봇 (head/body/legs 독립 회전)
- ✅ **ISelectable**: 유닛 선택 인터페이스
- ✅ **IMovable**: 유닛 이동 인터페이스
- ✅ **Decal Projector**: 선택 시각화 (바닥에만 렌더링)
- ✅ **Rendering Layers**: Floor 레이어로 Decal 제한
- ✅ **Physics Layers**: Units(6), Buildings(7), Floor(8)
- ✅ **LayerMask 시스템**: selectableUnitsLayers, floorLayers

### 3. 강의 노트 작성 완료
- 위치: `lecture/lecture-notes/complete-lecture-notes.html`
- 스타일: **Toss 스타일** (토스 기술 블로그 형식)
- 완료된 강의: **총 12개 강의** (8-9시간 분량)
  - 강의 1-9: 카메라, NavMesh, Scene 설정 등
  - 강의 10: Unit Selection (유닛 선택)
  - 강의 11: Unit & Decal Rendering Layers
  - 강의 12: Unit Movement (우클릭 이동)

---

## 🔧 중요한 기술 구현 상세

### WorkerThreeRobot.cs (3파츠 로봇)
```csharp
// 위치: Assets/Scripts/Units/WorkerThreeRobot.cs
public class WorkerThreeRobot : MonoBehaviour, ISelectable, IMovable
{
    [SerializeField] private GameObject headPart;   // Engineer_1
    [SerializeField] private GameObject bodyPart;   // Logistics_4
    [SerializeField] private GameObject legsPart;   // Engineer_3

    [SerializeField] private float headRotationSpeed = 180f;
    [SerializeField] private float bodyRotationSpeed = 90f;
    [SerializeField] private float legsRotationSpeed = 45f;

    [SerializeField] private DecalProjector decalProjector;

    // ISelectable 구현
    public void Select() { decalProjector.gameObject.SetActive(true); }
    public void Deselect() { decalProjector.gameObject.SetActive(false); }

    // IMovable 구현
    public void MoveTo(Vector3 position) { agent.SetDestination(position); }
}
```

### Layer 시스템
```
Physics Layers:
- 0: Default
- 6: Units (WorkerThreeRobot, Worker)
- 7: Buildings
- 8: Floor (Terrain, Water)

Rendering Layers:
- 0: Default
- 1: Floor (Decal 투영 대상)

Layer Collision Matrix:
- Units ↔ World Bounds: ❌ 충돌 해제
- Buildings ↔ World Bounds: ❌ 충돌 해제
- Floor ↔ World Bounds: ❌ 충돌 해제
```

### Prefab 설정
**Worker.prefab & WorkerThreeRobot.prefab:**
- Layer: 6 (Units)
- Decal Projector 자식 객체:
  - m_DecalLayerMask: 2 (Floor rendering layer)
  - m_IsActive: 0 (기본 비활성)
- CapsuleCollider (Raycast 감지용)
- NavMeshAgent (이동용)

---

## 📝 작업 규칙

**필수 읽기:**
- `lecture/urts-course/.claude/rules-summary.md` ⭐ (핵심 요약, 50줄)
- `lecture/urts-course/.claude/code-rules.md` (상세 룰, 597줄)

**핵심 룰 7개:**
- **Rule 7**: 서버 통합 대비 → 모듈화, Event Bus, 정확한 네이밍
- **Rule 6**: Worker → WorkerThreeRobot 적용 시 충돌 체크
- **Rule 5**: 커밋 메시지 영어+한글
- **Rule 4**: 강의 노트 HTML 추가 (Toss 스타일)
- **Rule 3**: 50+ 유닛 성능 최적화
- **Rule 2**: 적극적 코드 개선 제안
- **Rule 1**: 애매한 요청은 질문 먼저

---

## 🔄 작업 흐름

### WorkerThreeRobot 기능 동기화
Worker에 새 기능이 추가되면 WorkerThreeRobot에도 동일하게 적용:
- **Rule 6 준수**: 전체 파일 읽고 Update() 충돌 체크
- 기존 로직과 충돌 확인 후 적용

### 최근 완료한 강의
**강의 11 (59ae39e)**: Unit & Decal Rendering Layers
- Decal이 Floor만 렌더링되도록
- Units/Buildings Physics Layer 추가
- LayerMask 직렬화 필드 사용

**강의 12 (cedb275)**: Unit Movement
- IMovable 인터페이스
- 우클릭 이동 (HandleRightClick)
- Pattern Matching (is not IMovable)
- Floor 레이어로 이동 제한

**강의 13 (5c60460)**: Drag Selection UI
- RectTransform으로 UI 동적 크기 조정
- 드래그 선택 3단계 상태 관리
- Canvas Scaler Constant Pixel Size
- Mathf.Abs()로 음수 방향 드래그 대응

---

## 🚨 주의사항

### Unity Prefab 작업
- **Prefab 생성 후 반드시 Scene에도 배치** (PrefabInstance 추가)
- 위치: `.claude/unity-prefab-creation-process.md` 참고

### Vector3 임포트 주의
- ❌ `System.Numerics.Vector3`
- ✅ `UnityEngine.Vector3`
- VS Code가 자동 임포트 시 잘못된 네임스페이스 선택 가능

### Layer 변경 후 확인
- 새 Physics Layer 추가 시 **Layer Collision Matrix 반드시 확인**
- 기본적으로 모든 레이어끼리 충돌 설정됨

### 서브모듈 커밋 순서
1. `urts-course`에서 변경 후 커밋
2. `Uaps`로 이동
3. `git add lecture/urts-course` (서브모듈 참조 업데이트)
4. 커밋 (chore: Update urts-course submodule ...)

---

## 📂 주요 파일 위치

### 코드
```
Assets/Scripts/Units/
├── ISelectable.cs          # 선택 가능 인터페이스
├── IMovable.cs             # 이동 가능 인터페이스
├── Worker.cs               # 기본 Worker (1파츠)
├── WorkerThreeRobot.cs     # 3파츠 로봇
└── RobotPart.cs            # 파츠별 회전 컴포넌트

Assets/Scripts/Player/
├── PlayerInput.cs          # 입력 처리 (선택, 이동)
└── CameraConfig.cs         # 카메라 설정 (MousePanSpeed 등)
```

### Prefabs
```
Assets/Units/Worker/
├── Worker.prefab           # 기본 Worker
└── WorkerThreeRobot.prefab # 3파츠 로봇
```

### 설정
```
ProjectSettings/
├── TagManager.asset        # Layer 정의
└── DynamicsManager.asset   # Collision Matrix

Assets/Settings/
└── PC_Renderer.asset       # URP Renderer (Decal Feature)
```

### 문서
```
.claude/
├── code-rules.md                          # 작업 규칙
├── unity-prefab-creation-process.md       # Prefab 생성 프로세스
└── session-handoff.md                     # 이 문서

lecture/urts-course/.claude/
├── code-rules.md                          # 서브모듈 작업 규칙
└── unity-prefab-creation-process.md       # 서브모듈 Prefab 생성 프로세스

lecture/lecture-notes/
└── complete-lecture-notes.html            # 강의 노트 (12개 완료)
```

---

## 🎯 다음 작업 예상

### 🚨 진행 중인 작업: devil_top을 WorkerThreeRobot headPart에 적용

**현재 상태:**
- ✅ devil_top FBX 및 텍스처 파일 복사 완료
  - 위치: `/Users/hankyulee/Desktop/Uaps/lecture/urts-course/Assets/Models/devil_top/`
  - 파일: base_basic_pbr.fbx, texture_diffuse.png, texture_normal.png, texture_metallic.png, texture_roughness.png
- ✅ Unity MCP 설정 완료 (`.mcp.local.json`에 추가)
- ⏳ Unity 에디터에서 Material 생성 및 Prefab 적용 필요

**남은 작업 (Unity 에디터 또는 Unity MCP 사용):**

1. **Material 생성 및 설정**:
   - 위치: `Assets/Models/devil_top/`
   - 이름: `DevilTopMaterial`
   - Shader: URP/Lit
   - Albedo: texture_diffuse.png
   - Normal Map: texture_normal.png (Normal Map 체크)
   - Metallic: texture_metallic.png
   - Smoothness: texture_roughness.png

2. **WorkerThreeRobot Prefab 수정**:
   - Prefab 위치: `Assets/Units/Worker/WorkerThreeRobot.prefab`
   - 기존 headPart (Engineer_1) 제거
   - base_basic_pbr.fbx를 새 headPart로 할당
   - DevilTopMaterial 적용
   - Prefab 저장

3. **테스트 및 확인**:
   - Game Scene에서 WorkerThreeRobot이 devil_top head를 가지는지 확인
   - 회전 및 이동 시 정상 작동 확인
   - Material이 올바르게 렌더링되는지 확인

**Unity MCP 사용 시:**
- Unity MCP가 `.mcp.local.json`에 설정됨
- 새 세션에서 Unity MCP 도구 사용 가능
- Unity 에디터 실행 필수

**수동 작업 시:**
- Unity 에디터에서 위 단계 직접 수행
- Prefab Mode에서 작업 (더블클릭으로 열기)

---

### 다음 강의 추가 시
- Cursor에서 `lecture` 명령어로 처리
- 강의 번호: 현재 15개 완료, 다음은 16

### WorkerThreeRobot 확장 작업
- devil_top 적용 후 다른 파츠 추가 가능
- 파츠별 커스터마이징 시스템 활용

---

## 💡 유용한 명령어

### Git 서브모듈
```bash
# urts-course로 이동
cd lecture/urts-course

# 특정 커밋 체리픽
git cherry-pick [hash]

# 최근 커밋 확인
git log --oneline -10

# Uaps로 돌아가서 서브모듈 업데이트
cd ../..
git add lecture/urts-course
git commit -m "chore: Update urts-course submodule ..."
```

### Unity 파일 검색
```bash
# Layer 확인
grep -n "m_Layer:" Assets/Units/Worker/*.prefab

# Decal LayerMask 확인
grep -n "m_DecalLayerMask:" Assets/Units/Worker/*.prefab

# FBX 파일 찾기
find Assets -name "*.fbx" | head -10
```

### 강의 노트 확인
```bash
# 현재 강의 개수 확인
grep -o "강의 [0-9]*:" lecture/lecture-notes/complete-lecture-notes.html | tail -5

# 강의 통계 확인
grep "총.*강의" lecture/lecture-notes/complete-lecture-notes.html
```

---

## 📊 현재 상태 요약

**Git 상태:**
- urts-course: dev 브랜치, 커밋 71ce3fa (최신: Drag Selection UI)
- Uaps: main 브랜치, 커밋 f938a60 (강의 13 추가)

**완료된 기능:**
- ✅ 3파츠 로봇 시스템
- ✅ 유닛 선택 (ISelectable + Decal)
- ✅ 유닛 이동 (IMoveable + 우클릭)
- ✅ 드래그 선택 UI (Selection Box)
- ✅ Layer 시스템 (Rendering + Physics)
- ✅ 강의 노트 13개

**미완료 (예상):**
- ⏳ 다중 유닛 선택 (박스 드래그)
- ⏳ 유닛 그룹 관리
- ⏳ 공격 시스템
- ⏳ 건물 건설

---

## 🔑 핵심 컨텍스트

### 사용자 의도
- Unity RTS 강의를 학습하며 **노바1492 스타일 게임** 제작
- 강의마다 **Toss 스타일 HTML 문서**로 정리
- Worker 기능을 **WorkerThreeRobot에도 동일하게 적용**
- **성능 최적화** 중시 (50+ 유닛 대응)

### 코드 스타일 선호
- **간결함**: Guardian Pattern, Pattern Matching 선호
- **성능**: 캐싱, 조기 리턴, 메모리 할당 최소화
- **확장성**: 인터페이스 활용, SOLID 원칙

### 커뮤니케이션 스타일
- **간결한 응답** 선호
- 불필요한 설명 제거
- 코드 변경 시 파일 경로:라인 번호 명시

---

## 📞 인계 완료

새 세션에서는:
1. **루트 `.claude/` 폴더** 읽고 컨텍스트 파악
2. **서브모듈 `lecture/urts-course/.claude/` 폴더**도 읽기 (작업 규칙 포함)
3. 사용자가 새 강의 커밋 제공하면 동일한 프로세스 진행
4. 항상 두 `.claude/code-rules.md` 규칙 준수
5. WorkerThreeRobot에도 Worker 변경사항 적용

**작업 디렉토리**: `/Users/hankyulee/Desktop/Uaps`
**현재 날짜**: 2025-10-04 (시스템 env 기준)

---

## 🎨 3D 모델 포맷 가이드 (최근 질문)

**권장**: **FBX** (Unity 네이티브, 애니메이션/리깅 지원)

**사용 케이스:**
- FBX: 로봇 유닛, 캐릭터, 애니메이션 객체
- OBJ: 건물, 정적 소품 (애니메이션 없음)
- ❌ STL: 게임용 아님 (3D 프린팅용)
- ❌ USDZ: Unity 지원 제한적

**현재 프로젝트**: 대부분 FBX, 일부 작은 소품만 OBJ

---

**인계자**: Claude (Sonnet 4.5)
**인계 날짜**: 2025-10-04
**세션 버전**: v1.0
