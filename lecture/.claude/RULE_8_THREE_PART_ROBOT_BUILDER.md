# Rule 8: THREE_PART_ROBOT_BUILDER_RULE

**When creating or updating ThreePartRobotBuilder.cs (or similar Unity Editor automation tools), always ensure all required Unity components, serialized fields, and references are properly assigned. Never create incomplete prefabs that would require manual fixes.**

---

## 🎯 Purpose

ThreePartRobotBuilder automates the creation of 3-part robots (head/body/legs) from source folders. This rule ensures that:
- All generated prefabs are **immediately usable** without manual Unity Editor fixes
- New robot types automatically receive all required fields and components
- The tool stays synchronized with project architecture updates

---

## 📋 Required Components Checklist

When creating a robot prefab, **ALL** of the following must be automatically configured:

### 1. GameObject Configuration
- [x] **Layer**: Set to `6` (Selectable Units)
- [x] **Name**: `{RobotPrefix}ThreeRobot` (e.g., `MarineThreeRobot`)
- [x] **Children**: 4 GameObjects (head, body, legs, DecalProjector)

### 2. NavMeshAgent Component
- [x] `radius`: 0.5f
- [x] `height`: 2f
- [x] `speed`: 3.5f
- [x] Other defaults as needed

### 3. CapsuleCollider Component
- [x] `radius`: 0.5f
- [x] `height`: 2f
- [x] `center`: (0, 1, 0)

### 4. Robot Script Component (WorkerThreeRobot or AirTransportThreeRobot)

**Serialized Fields:**
- [x] `headPart`: Reference to head GameObject
- [x] `bodyPart`: Reference to body GameObject
- [x] `legsPart`: Reference to legs GameObject
- [x] `decalProjector`: Reference to DecalProjector component
- [x] `UnitSO`: ScriptableObject reference
  - Ground units → Worker SO (GUID: `f15d9d4a0e8b3d848a49aaba59177157`)
  - Air units → Air Transport SO (GUID: `f6b38bef630bb884195c6d3f702eefb9`)
- [x] `<AvailableCommands>k__BackingField`: Array with Move Action
  - Move Action (GUID: `5b88f1c2df63cea41a0b8cd466334886`)

### 5. DecalProjector GameObject

**Structure:**
```yaml
GameObject: "Decal Projector"
  - Transform
      Position: (0, 0, 0)
      Rotation: (90, 0, 0) - Euler angles
      Active: false (initially hidden)
  - DecalProjector Component
      Material: Selected Ring Decal Material
      Size: (1.5, 1.5, 8)
      Pivot: (0, 0, 4)
```

**Material Reference:**
- Path: `Assets/Materials/Selected Ring Decal Material.mat`
- GUID: `949870381fdaf4941aedc8857fe165e9`

---

## 💻 Implementation Template

```csharp
private string CreateRobotPrefab()
{
    // 1. Create base GameObject with correct Layer
    GameObject robot = new GameObject($"{CapitalizeFirst(robotPrefix)}ThreeRobot");
    robot.layer = 6; // Selectable Units

    // 2. Create part GameObjects FIRST
    GameObject headPart = CreatePartWithModel(robot.transform, $"{robotPrefix}_top", "head", new Vector3(0, 1.5f, 0));
    GameObject bodyPart = CreatePartWithModel(robot.transform, $"{robotPrefix}_center", "body", new Vector3(0, 1f, 0));
    GameObject legsPart = CreatePartWithModel(robot.transform, $"{robotPrefix}_leg", "legs", new Vector3(0, 0.5f, 0));

    // 3. Create DecalProjector GameObject
    GameObject decalProjector = new GameObject("Decal Projector");
    decalProjector.transform.SetParent(robot.transform);
    decalProjector.transform.localPosition = Vector3.zero;
    decalProjector.transform.localRotation = Quaternion.Euler(90, 0, 0);
    decalProjector.SetActive(false);

    // 4. Add DecalProjector component
    var projector = decalProjector.AddComponent<UnityEngine.Rendering.Universal.DecalProjector>();
    projector.material = AssetDatabase.LoadAssetAtPath<Material>("Assets/Materials/Selected Ring Decal Material.mat");
    projector.size = new Vector3(1.5f, 1.5f, 8f);
    projector.pivot = new Vector3(0, 0, 4);

    // 5. Add NavMeshAgent
    var agent = robot.AddComponent<UnityEngine.AI.NavMeshAgent>();
    agent.radius = 0.5f;
    agent.height = 2f;
    agent.speed = 3.5f;

    // 6. Add Capsule Collider
    var collider = robot.AddComponent<CapsuleCollider>();
    collider.radius = 0.5f;
    collider.height = 2f;
    collider.center = new Vector3(0, 1, 0);

    // 7. Add appropriate script
    MonoBehaviour script;
    if (robotType == RobotType.Ground)
    {
        script = robot.AddComponent<Units.WorkerThreeRobot>();
    }
    else
    {
        script = robot.AddComponent<Units.AirTransportThreeRobot>();
    }

    // 8. Assign ALL fields using SerializedObject
    SerializedObject serializedScript = new SerializedObject(script);

    // Assign robot parts
    serializedScript.FindProperty("headPart").objectReferenceValue = headPart;
    serializedScript.FindProperty("bodyPart").objectReferenceValue = bodyPart;
    serializedScript.FindProperty("legsPart").objectReferenceValue = legsPart;

    // Assign DecalProjector
    serializedScript.FindProperty("decalProjector").objectReferenceValue = projector;

    // Assign UnitSO
    SerializedProperty unitSOProperty = serializedScript.FindProperty("UnitSO");
    if (unitSOProperty != null)
    {
        string unitSOGuid = robotType == RobotType.Ground
            ? "f15d9d4a0e8b3d848a49aaba59177157"  // Worker SO
            : "f6b38bef630bb884195c6d3f702eefb9";  // Air Transport SO
        string unitSOPath = AssetDatabase.GUIDToAssetPath(unitSOGuid);
        var unitSO = AssetDatabase.LoadAssetAtPath<ScriptableObject>(unitSOPath);
        unitSOProperty.objectReferenceValue = unitSO;
    }

    // Assign AvailableCommands
    SerializedProperty commandsProperty = serializedScript.FindProperty("<AvailableCommands>k__BackingField");
    if (commandsProperty != null && commandsProperty.isArray)
    {
        string moveActionGuid = "5b88f1c2df63cea41a0b8cd466334886";
        string moveActionPath = AssetDatabase.GUIDToAssetPath(moveActionGuid);
        var moveAction = AssetDatabase.LoadAssetAtPath<ScriptableObject>(moveActionPath);

        commandsProperty.ClearArray();
        commandsProperty.InsertArrayElementAtIndex(0);
        commandsProperty.GetArrayElementAtIndex(0).objectReferenceValue = moveAction;
    }

    serializedScript.ApplyModifiedProperties();

    // 9. Save prefab
    string prefabFolder = robotType == RobotType.Ground ? "Assets/Units/Worker" : "Assets/Units/Air Transport";
    string prefabPath = $"{prefabFolder}/{CapitalizeFirst(robotPrefix)}ThreeRobot.prefab";

    PrefabUtility.SaveAsPrefabAsset(robot, prefabPath);
    DestroyImmediate(robot);

    return prefabPath;
}
```

---

## 🐛 Common Issues and Solutions

### Issue 1: Robot Cannot Be Selected/Dragged

**Symptoms:**
- Robot appears in scene but clicking doesn't select it
- Drag selection doesn't include the robot

**Root Causes:**
- ❌ Layer is 0 (Default) instead of 6 (Selectable Units)
- ❌ Missing DecalProjector component reference
- ❌ Empty AvailableCommands array

**Fix:**
```csharp
robot.layer = 6; // MUST set layer
serializedScript.FindProperty("decalProjector").objectReferenceValue = projector; // MUST assign
// MUST populate AvailableCommands with at least Move Action
```

### Issue 2: Null Reference Exceptions in Play Mode

**Symptoms:**
- Console error: `NullReferenceException: Object reference not set to an instance of an object`
- Robot parts don't rotate
- Unit doesn't move when clicked

**Root Causes:**
- ❌ Part GameObjects (head/body/legs) not assigned
- ❌ UnitSO is null
- ❌ DecalProjector is null

**Fix:**
```csharp
// Always verify ALL properties are assigned:
if (serializedScript.FindProperty("headPart") == null)
    Debug.LogError("headPart property not found!");

// Use proper assignment:
serializedScript.FindProperty("headPart").objectReferenceValue = headPart;
serializedScript.ApplyModifiedProperties(); // DON'T FORGET THIS
```

### Issue 3: Reflection vs SerializedObject

**Problem:**
Using C# Reflection to set private fields doesn't work reliably in Unity Editor scripts.

**❌ Bad (Reflection - unreliable):**
```csharp
var headField = scriptType.GetField("headPart", BindingFlags.NonPublic | BindingFlags.Instance);
headField?.SetValue(script, headPart);
```

**✅ Good (SerializedObject - Unity's proper way):**
```csharp
SerializedObject serializedScript = new SerializedObject(script);
SerializedProperty headProperty = serializedScript.FindProperty("headPart");
headProperty.objectReferenceValue = headPart;
serializedScript.ApplyModifiedProperties();
```

---

## 🔄 Update Workflow

**When AbstractUnit or AbstractCommandable gains new fields:**

1. **Identify New Fields**:
   ```csharp
   // Example: New field added to AbstractCommandable
   [field: SerializeField] public HealthBarUI HealthBar { get; private set; }
   ```

2. **Update ThreePartRobotBuilder**:
   ```csharp
   // Add to CreateRobotPrefab():
   SerializedProperty healthBarProperty = serializedScript.FindProperty("HealthBar");
   if (healthBarProperty != null)
   {
       // Create or load HealthBar prefab
       GameObject healthBar = CreateHealthBar(robot.transform);
       healthBarProperty.objectReferenceValue = healthBar.GetComponent<HealthBarUI>();
   }
   ```

3. **Fix Existing Prefabs** (if needed):
   - Manually update MarineThreeRobot.prefab, SniperThreeRobot.prefab, etc.
   - Or create a new Editor script to batch-update prefabs

4. **Test**:
   - Build a new robot using the tool
   - Verify ALL fields are assigned in Inspector
   - Test selection, movement, and all gameplay features

---

## 📝 Action Steps

**Before releasing ThreePartRobotBuilder updates:**

- [ ] Verify all components are added (Layer, NavMeshAgent, Collider, Script)
- [ ] Verify all serialized fields are assigned (parts, decalProjector, UnitSO, AvailableCommands)
- [ ] Test: Build a robot, spawn in scene, verify selection works
- [ ] Test: Verify right-click movement works
- [ ] Test: Verify DecalProjector shows when selected
- [ ] Update this rule if new required fields are added to AbstractUnit/AbstractCommandable

**When AbstractUnit/AbstractCommandable changes:**

- [ ] Read all new serialized fields
- [ ] Update CreateRobotPrefab() to assign new fields
- [ ] Test with a new robot build
- [ ] Update existing prefabs if needed
- [ ] Update this rule document

---

## 🎓 Key Learning Points

1. **Always use SerializedObject** for Editor scripting, not Reflection
2. **Layer 6 is critical** for unit selection system
3. **DecalProjector must be a child GameObject** with specific Transform settings
4. **GUID-based asset loading** is reliable across project moves
5. **SerializedProperty names** for auto-properties use `<PropertyName>k__BackingField` syntax
6. **ApplyModifiedProperties()** is required to persist SerializedObject changes

---

## 📌 Related Files

- `Assets/Editor/ThreePartRobotBuilder.cs` - Main implementation
- `Assets/Scripts/Units/AbstractUnit.cs` - Base class for units
- `Assets/Scripts/Units/AbstractCommandable.cs` - Command Pattern base
- `Assets/Scripts/Units/WorkerThreeRobot.cs` - Ground unit implementation
- `Assets/Scripts/Units/AirTransportThreeRobot.cs` - Air unit implementation

---

**Created**: 2025-10-10
**Purpose**: Ensure ThreePartRobotBuilder creates complete, functional prefabs
**Trigger**: When updating editor tools or fixing prefab generation issues
