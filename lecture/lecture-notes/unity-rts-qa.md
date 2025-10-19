# Unity RTS 프로젝트 Q&A

Worker "밥먹기" 액션 구현 중 발생한 질문과 답변 정리

---

## Q1. Behavior Tree OnUpdate()가 뭐야?

### 답변:

**Unity Update()와 다른 점:**

```csharp
// MonoBehaviour (일반 컴포넌트)
void Update()  // 매 프레임 호출 (항상!)

// Behavior Tree Action
Status OnUpdate()  // 이 노드가 활성화되어 있을 때만 호출!
```

**차이점:**
- `Update()`: Worker가 게임에 있는 동안 **항상** 호출
- `OnUpdate()`: **Behavior Tree 노드가 실행 중일 때만** 호출

**예시:**
```csharp
// GatherSuppliesAction.OnUpdate()
// 3초~5초만 호출됨 (채취하는 2초 동안만!)
protected override Status OnUpdate()
{
    if (채취 완료)
        return Status.Success;  // 완료!

    return Status.Running;  // 계속 진행
}
```

---

## Q2. 바닥과 충돌했다는 게 무슨 말이야?

### 답변:

```csharp
Physics.Raycast(cameraRay, out RaycastHit hit, float.MaxValue,
                interactableLayers | floorLayers))
```

**의미:**
- 카메라에서 Ray 발사 → 3D 오브젝트와 충돌 감지

**충돌 가능한 것:**
- Crystal (interactableLayers)
- 바닥/Terrain (floorLayers)
- 건물 (interactableLayers)

**바닥 충돌 = 빈 땅 클릭:**
```
Crystal 없는 곳 우클릭 → 바닥과 충돌
→ MoveCommand가 처리
→ Worker가 그 위치로 이동
```

**시각화:**
```
🖱️ 우클릭 → Ray 발사 ━━━━━→ 💎 Crystal (충돌!)
🖱️ 우클릭 → Ray 발사 ━━━━━→ 🟩 바닥 (충돌!)
```

---

## Q3. foreach에서 ISelectable만 돌아?

### 질문:
```csharp
foreach(ISelectable selectable in selectedUnits)
{
    if (selectable is AbstractUnit unit)
    {
        abstractUnits.Add(unit);
    }
}
```

### 답변:

**아니! selectedUnits의 모든 요소를 돌아:**

```csharp
foreach(ISelectable selectable in selectedUnits)  // 전부 다 돌아!
{
    if (selectable is AbstractUnit unit)  // AbstractUnit인지 체크
    {
        abstractUnits.Add(unit);  // 맞으면 추가
    }
    // AbstractUnit 아니면 무시
}
```

**예시:**
```csharp
selectedUnits = [Worker, Building, Worker2]

Loop #1: Worker → AbstractUnit? ✅ → 추가
Loop #2: Building → AbstractUnit? ❌ → 무시
Loop #3: Worker2 → AbstractUnit? ✅ → 추가

결과: abstractUnits = [Worker, Worker2]
```

---

## Q4. List 생성자의 Count는 배열 크기인가?

### 질문:
```csharp
List<AbstractUnit> abstractUnits = new (selectedUnits.Count);
```
AbstractUnit를 원하는데 selectedUnits.Count는 int 아니야?

### 답변:

**의미: 초기 용량(Capacity) 설정**

```csharp
// 방법 1: 용량 미리 할당 (빠름!)
List<AbstractUnit> list = new(10);  // 10개 공간 미리 확보
list.Add(worker1);  // 공간 있음 → 빠름!

// 방법 2: 용량 자동 할당 (느림)
List<AbstractUnit> list = new();  // 공간 없음
list.Add(worker1);  // 공간 부족 → 재할당 → 느림!
```

**비유:**
```
new(10) = 10인분 식탁 미리 준비
new()   = 손님 올 때마다 의자 추가 (느림)
```

**여기서:**
```csharp
selectedUnits.Count = 3  // Worker 3명 선택
new(3)  // 3개 공간 미리 확보 (최적화!)
```

---

## Q5. selectedUnits이 AbstractUnit이 아닐 수 있어서 if문 하는 거야?

### 답변:

**맞아! 그래서 if문이 필요해:**

```csharp
List<ISelectable> selectedUnits;  // ISelectable 리스트
```

**ISelectable을 구현한 것들:**
```csharp
✅ Worker : AbstractUnit, ISelectable  // 유닛
✅ Building : ISelectable               // 건물 (AbstractUnit 아님!)
✅ Tank : AbstractUnit, ISelectable     // 유닛
```

**상황:**
```csharp
// 플레이어가 Worker + Building 함께 선택
selectedUnits = [Worker, Building, Tank]

// 우클릭 명령은 유닛만 가능!
// Building은 이동 못함 → AbstractUnit만 필터링
foreach(ISelectable selectable in selectedUnits)
{
    if (selectable is AbstractUnit unit)  // Building은 여기서 걸러짐!
    {
        abstractUnits.Add(unit);
    }
}

// 결과: [Worker, Tank] (Building 제외!)
```

---

## Q6. float.MaxValue가 무슨 말이야?

### 질문:
```csharp
Physics.Raycast(cameraRay, out RaycastHit hit,
                float.MaxValue,  // 거리 제한 없음
                interactableLayers | floorLayers)
```

### 답변:

**의미: Ray가 도달할 수 있는 최대 거리**

```csharp
float.MaxValue = 3.402823e+38f  // 엄청 큰 숫자!
// = 거의 무한대 = 거리 제한 없음!
```

**비교:**
```csharp
// 100m 이내만 감지
Physics.Raycast(ray, out hit, 100f)

// 무한대 거리 감지 (맵 어디든!)
Physics.Raycast(ray, out hit, float.MaxValue)
```

**시각화:**
```
100f:
카메라 ━━━━━→ (100m 이내만 감지)

float.MaxValue:
카메라 ━━━━━━━━━━━━━━━→ (맵 끝까지!)
```

---

## Q7. UnitIndex의 포메이션 용도는 언제 사용돼?

### 질문:
```csharp
CommandContext context = new(abstractUnits[i], hit, i);
                                                      ↑
                                                  UnitIndex
```

### 답변:

**용도: 여러 유닛을 다른 위치로 보낼 때**

**예시 1: Move 명령 (포메이션)**
```csharp
// Worker 3명 선택 → 바닥 우클릭

Worker #0 (UnitIndex = 0) → 클릭 지점
Worker #1 (UnitIndex = 1) → 클릭 지점 + 오른쪽 1m
Worker #2 (UnitIndex = 2) → 클릭 지점 + 오른쪽 2m

// MoveCommand.cs에서:
Vector3 offset = new Vector3(context.UnitIndex * 1f, 0, 0);
targetPosition = hit.point + offset;
```

**시각화:**
```
클릭 지점
   ↓
[W0][W1][W2]  ← 일렬로 배치!
```

**예시 2: 현재 프로젝트 (GatherCommand)**
```csharp
// Worker 3명 → Crystal 우클릭
// UnitIndex는 사용 안 함 (모두 같은 Crystal로)

Worker #0 → Crystal
Worker #1 → Crystal
Worker #2 → Crystal
```

**언제 사용?**
- ✅ Move 명령: 포메이션
- ✅ Attack 명령: 순서대로 공격
- ❌ Gather 명령: 사용 안 함 (모두 같은 자원)

---

## Q8. OnUpdate()는 BT Initialize 이후에 호출되는 거야?

### 질문:
worker의 비헤비어트리가 OnUpdate()될 때는 워커의 비헤비어 트리가 Initialize된 이후야?

### 답변:

**맞아! OnUpdate()는 반드시 Initialize 이후에 호출돼:**

**Behavior Tree 라이프사이클:**

```csharp
// 1. BehaviorGraphAgent 활성화 시 (최초 1회)
void Initialize()  // BT 전체 초기화
{
    // Blackboard 변수 설정
    // Self, Command 등 초기화
}

// 2. 노드 실행 시작 (노드당 1회)
Status OnStart()
{
    // 이 시점에는 이미 Initialize 완료됨!
    // Blackboard 변수들 사용 가능
}

// 3. 매 프레임 (Running 상태일 때만)
Status OnUpdate()
{
    // Initialize 완료 + OnStart 완료 후 호출됨!
}

// 4. 노드 종료 시 (노드당 1회)
void OnEnd()
{
    // 정리 작업
}
```

**Worker.cs에서 실제 순서:**

```csharp
// Worker 프리팹 생성 시
1. Awake/Start() → Worker 컴포넌트 초기화
2. BehaviorGraphAgent.Initialize() → BT 초기화
   - Blackboard["Self"] = this.gameObject
   - Blackboard["Command"] = UnitCommands.Stop

// 이후 Eat() 호출 시
3. Worker.Eat()
   → graphAgent.SetVariableValue("Command", UnitCommands.Eat)

4. BT가 "Eat Sequence" 조건 체크
   → Command == Eat? → True!

5. EatAction.OnStart() 호출
   - Unit.Value (Blackboard의 Self) 사용 가능! ✅

6. EatAction.OnUpdate() 반복 호출
   - 이미 Initialize + OnStart 완료 상태! ✅
```

**실행 순서 요약:**
```
Initialize → OnStart → OnUpdate (반복) → OnEnd
    ↑         ↑         ↑
  최초 1회   노드당 1회  Running일 때만
```

**따라서:**
- `OnUpdate()`에서 `Unit.Value` 같은 Blackboard 변수를 안전하게 사용 가능!
- Initialize가 완료된 후에만 OnUpdate()가 호출됨 보장!

---

## 요약

| 질문 | 핵심 답변 |
|------|-----------|
| OnUpdate() | BT 노드 활성화 중에만 호출 (Update()는 항상) |
| 바닥 충돌 | 빈 땅 클릭 = Ray가 바닥 레이어와 충돌 → 이동 |
| foreach | 전부 돌되, AbstractUnit만 필터링 |
| List(Count) | 초기 용량 설정 (최적화) |
| if문 필터링 | Building 같은 비유닛 제거 |
| float.MaxValue | 거리 무제한 (맵 끝까지 감지) |
| UnitIndex | 포메이션/순서 (Move에서 사용) |
| BT 라이프사이클 | Initialize → OnStart → OnUpdate (반복) → OnEnd |

---

**작성일:** 2025-10-18
**프로젝트:** Unity RTS (Lecture 15+)
**주제:** Worker Eat Action 구현 관련 Q&A
