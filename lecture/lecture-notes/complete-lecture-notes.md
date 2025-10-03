# Unity RTS Course - Complete Lecture Notes

## 강의 1-9: 기본 내용
*(이전 강의 내용은 complete-lecture-notes.html 참조)*

---

## 강의 10: Unit Selection (유닛 선택)

### 🎯 학습 목표
- 마우스 좌클릭으로 유닛 선택 구현
- Decal Projector를 사용한 선택 표시
- Interface를 활용한 확장 가능한 선택 시스템

---

### 📝 핵심 개념

#### 1. URP Decal Projector
**선택된 유닛 시각적 표시**
- Hierarchy → 우클릭 → Rendering → URP → Decal Projector
- 유닛 위에 원형 표시를 투영하는 방식

**설정 방법:**
```
1. Worker Prefab에 Decal Projector 추가
2. URP Renderer에 Decal Feature 추가
   - Project Settings → Graphics → URP Renderer
   - Add Renderer Feature → Decal
   - Use Rendering Layers 체크
3. Selection Decal Material 생성
   - Shader: Shader Graphs/Decal
   - Base Map: Selection Ring 텍스처
4. Size 조정: Width 1.5, Height 1.5, Projection Depth 8
```

#### 2. Raycast를 통한 유닛 감지
**Physics Raycast 개념**
- 원점에서 방향으로 무한히 얇은 선을 쏘는 Physics API
- 충돌 시 true 반환 및 RaycastHit 정보 제공
- LayerMask로 특정 레이어만 감지 가능

**Camera ScreenPointToRay**
```csharp
Ray cameraRay = camera.ScreenPointToRay(Mouse.current.position.ReadValue());
```
- 마우스 화면 좌표를 카메라 관점의 Ray로 변환
- 3D 공간에서 클릭 위치 계산

#### 3. Interface를 통한 확장성
**ISelectable Interface**
- 다양한 선택 가능 객체(유닛, 건물 등)에 대응
- 각 클래스가 독립적으로 선택/해제 동작 구현

```csharp
public interface ISelectable
{
    void Select();
    void Deselect();
}
```

**Interface 장점**
- `GetComponent<ISelectable>()` 동작
- Worker, Building 등 다양한 타입에 적용 가능
- 확장성 높은 설계

---

### 💻 구현 코드

#### PlayerInput.cs
```csharp
using GameDevTV.RTS.Units;

public class PlayerInput : MonoBehaviour
{
    [SerializeField] private new Camera camera;
    private ISelectable selectedUnit;

    private void Update()
    {
        HandlePanning();
        HandleZooming();
        HandleRotation();
        HandleLeftClick();
    }

    private void HandleLeftClick()
    {
        if (camera == null) return;

        Ray cameraRay = camera.ScreenPointToRay(Mouse.current.position.ReadValue());

        if (Mouse.current.leftButton.wasReleasedThisFrame)
        {
            // 기존 선택 해제
            if (selectedUnit != null)
            {
                selectedUnit.Deselect();
                selectedUnit = null;
            }

            // 새 유닛 선택
            if (Physics.Raycast(cameraRay, out RaycastHit hit, float.MaxValue, LayerMask.GetMask("Default"))
                && hit.collider.TryGetComponent(out ISelectable selectable))
            {
                selectable.Select();
                selectedUnit = selectable;
            }
        }
    }
}
```

#### ISelectable.cs
```csharp
namespace GameDevTV.RTS.Units
{
    public interface ISelectable
    {
        void Select();
        void Deselect();
    }
}
```

#### Worker.cs
```csharp
using UnityEngine.Rendering.Universal;

[RequireComponent(typeof(NavMeshAgent))]
public class Worker : MonoBehaviour, ISelectable
{
    [SerializeField] private DecalProjector decalProjector;

    public void Select()
    {
        if (decalProjector != null)
        {
            decalProjector.gameObject.SetActive(true);
        }
    }

    public void Deselect()
    {
        if (decalProjector != null)
        {
            decalProjector.gameObject.SetActive(false);
        }
    }
}
```

---

### ⚙️ Unity 설정

#### Decal Projector 설정
```
Width: 1.5
Height: 1.5
Projection Depth: 8
Material: Selection Decal
기본 상태: Disabled (선택되지 않음)
```

#### Worker Prefab 설정
```
1. Capsule Collider 추가
   - Center Y: 1
   - Height: 2
2. Decal Projector 컴포넌트 참조 연결
3. Prefab Overrides → Apply All
```

#### PlayerInput 설정
```
Camera 참조 연결 (Main Camera)
```

---

### 🐛 버그 수정

**문제: 같은 유닛 두 번 클릭 시 선택 해제**

**원인:**
```csharp
// 잘못된 순서
if (raycast hit && TryGetComponent)
{
    selectable.Select();
    selectedUnit = selectable;
}

if (selectedUnit != null)
{
    selectedUnit.Deselect();
    selectedUnit = null;
}
```

**해결:**
```csharp
// 올바른 순서: 먼저 해제, 그 다음 선택
if (Mouse.current.leftButton.wasReleasedThisFrame)
{
    if (selectedUnit != null)
    {
        selectedUnit.Deselect();
        selectedUnit = null;
    }

    if (raycast hit && TryGetComponent)
    {
        selectable.Select();
        selectedUnit = selectable;
    }
}
```

---

### 🎓 학습 포인트

#### 1. URP Decal System
- 지형에 관계없이 투영되는 시각적 표시
- Rendering Layers로 최적화 가능
- 높이 조정으로 지형 돌출 방지

#### 2. Raycast 활용
- ScreenPointToRay로 2D → 3D 변환
- float.MaxValue로 무한 거리 감지
- LayerMask로 필요한 객체만 감지

#### 3. Interface 패턴
- 다형성을 통한 확장 가능한 설계
- 유닛/건물 등 다양한 타입 지원
- GetComponent/TryGetComponent 호환

#### 4. 상태 관리
- selectedUnit으로 현재 선택 추적
- 선택 해제 후 새 선택 방식
- null 체크로 안전성 보장

---

### 📌 주요 변경 파일
```
Assets/Materials/Selection Decal.mat (신규)
Assets/Scripts/Player/PlayerInput.cs (수정)
Assets/Scripts/Units/ISelectable.cs (신규)
Assets/Scripts/Units/Worker.cs (수정)
Assets/Settings/PC_Renderer.asset (수정)
Assets/Units/Worker/Worker.prefab (수정)
```

---

### 🚀 다음 단계
- 다중 유닛 선택
- 드래그 박스 선택
- 선택된 유닛 그룹 관리
