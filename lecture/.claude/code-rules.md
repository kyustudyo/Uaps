# Code Modification and Improvement Rules

## 📜 Core Principles

### Rule 1: CODE_MODIFICATION_RULE
**When facing ambiguous questions or requests that present any difficulty in providing concrete solutions, do not modify code. Instead, ask for additional information or clarification before proceeding with any code changes.**

**Examples:**
- ❌ User: "Fix the bug" → Don't guess, ask which bug
- ✅ User: "Fix the null reference in PlayerController line 45" → Clear, proceed
- ❌ User: "Make it better" → Ask what aspect to improve
- ✅ User: "Optimize the enemy spawn rate to reduce lag" → Clear, proceed

**Action Steps:**
1. Identify if request is ambiguous
2. Ask specific clarifying questions
3. Wait for user response
4. Only then modify code

---

### Rule 2: CODE_IMPROVEMENT_RULE
**When analyzing code or data structures, proactively identify redundancy, inefficiency, or structural improvements. Always suggest optimizations like removing duplicate data, simplifying structures, or improving data flow. Don't wait for the user to point out these opportunities.**

**What to Look For:**
- Duplicate code/data
- Inefficient loops or algorithms
- Redundant data structures
- Complex structures that could be simplified
- Poor data flow or unnecessary conversions
- Missing abstractions or patterns

**Examples:**

#### ❌ Bad (Reactive):
```
User: "Why is this slow?"
Assistant: "There's duplicate data in arrays A and B"
```

#### ✅ Good (Proactive):
```
User: "Here's my code"
Assistant: "I notice you're storing the same position data in both
playerPositions[] and gameObjectTransforms[]. This is redundant and
can cause sync issues. Suggest using a single source of truth with
a Dictionary<GameObject, Vector3> instead."
```

**Action Steps:**
1. Always scan for optimization opportunities
2. Point out issues immediately when reviewing code
3. Suggest concrete improvements
4. Explain why the improvement matters

---

## 🎯 Practical Guidelines

### When to Apply Each Rule

**CODE_MODIFICATION_RULE applies when:**
- User request lacks specifics
- Multiple interpretations possible
- Context is missing
- Requirements unclear

**CODE_IMPROVEMENT_RULE applies when:**
- Reading/analyzing any code
- Reviewing data structures
- Examining Unity Prefabs/Scenes
- Looking at system architecture

**Combined Example**: Ask for specifics first, then suggest improvements while fixing.

---

## 📋 Checklist

Before modifying code, ask yourself:

- [ ] Is the request specific and clear?
- [ ] Do I understand what needs to change?
- [ ] Do I know why it needs to change?
- [ ] If any answer is "no", did I ask for clarification?

When reviewing code, ask yourself:

- [ ] Are there any duplications?
- [ ] Are there inefficiencies?
- [ ] Can structures be simplified?
- [ ] Is data flow optimal?
- [ ] Did I suggest improvements?

---

### Rule 3: PERFORMANCE_OPTIMIZATION_RULE
**Always consider performance when implementing features, especially for multiplayer scenarios with many units. This project aims to support online multiplayer similar to Nova 1492, where dozens of units may exist simultaneously. Optimize for scalability from the start to prevent lag and performance issues.**

**Performance Considerations:**
- **Avoid expensive operations in Update()** - Cache references, minimize GetComponent calls
- **Use object pooling** - For frequently spawned/destroyed objects
- **Optimize rotation/movement** - Use Quaternion.Slerp wisely, avoid unnecessary calculations
- **Minimize allocations** - Avoid creating new objects in loops, reuse collections
- **Efficient data structures** - Use appropriate collections (Dictionary vs List vs Array)
- **LOD and culling** - Consider Level of Detail for distant units
- **Network optimization** - Design with server-client architecture in mind

**Examples:**

#### ❌ Bad (Performance Issues):
```csharp
void Update() {
    // GetComponent every frame - expensive!
    transform.rotation = Quaternion.Slerp(
        transform.rotation,
        Quaternion.LookRotation(GameObject.Find("Target").transform.position - transform.position),
        rotationSpeed * Time.deltaTime
    );
}
```

#### ✅ Good (Optimized):
```csharp
private Transform cachedTarget;
private Quaternion targetRotation;

void Start() {
    cachedTarget = GameObject.Find("Target").transform; // Cache once
}

void Update() {
    if (cachedTarget == null) return;

    // Calculate only when needed
    targetRotation = Quaternion.LookRotation(cachedTarget.position - transform.position);
    transform.rotation = Quaternion.Slerp(
        transform.rotation,
        targetRotation,
        rotationSpeed * Time.deltaTime
    );
}
```

**Action Steps:**
1. Cache references in Awake/Start, not Update
2. Avoid allocations in frequently-called methods
3. Use appropriate data structures for the use case
4. Consider scalability: "Will this work with 50+ units?"
5. Profile and measure when optimizing

---

### Rule 4: LECTURE_NOTES_DOCUMENTATION_RULE
**When user requests to add lecture notes with a commit hash and transcript, analyze the commit changes and create comprehensive documentation in Toss style format. Always update the existing lecture notes file at `/Users/hankyulee/Desktop/Uaps/lecture/lecture-notes/complete-lecture-notes.html`.**

**Documentation Process:**
1. **Analyze Commit**: Use `git show [commit-hash]` to examine all changed files
2. **Review Code Changes**: Understand what was added/modified
3. **Extract Key Concepts**: Identify main learning objectives from transcript
4. **Create Structured Notes**: Follow Toss style with clear sections

**Toss Style Format:**
```markdown
## 강의 N: [Title]

### 🎯 학습 목표
- Bullet point goals

### 📝 핵심 개념
#### Concept 1
Explanation with code examples

### 💻 구현 코드
Full implementation code blocks

### ⚙️ Unity 설정
Step-by-step configuration

### 🐛 버그 수정
Problems and solutions

### 🎓 학습 포인트
Key takeaways

### 📌 주요 변경 파일
List of modified files

### 🚀 다음 단계
Next learning steps
```

**Required Sections:**
- 🎯 학습 목표 (Learning Objectives)
- 📝 핵심 개념 (Core Concepts)
- 💻 구현 코드 (Implementation Code)
- ⚙️ Unity 설정 (Unity Settings)
- 🐛 버그 수정 (Bug Fixes) - if applicable
- 🎓 학습 포인트 (Key Learning Points)
- 📌 주요 변경 파일 (Modified Files)
- 🚀 다음 단계 (Next Steps)

**Action Steps:**
1. User provides: commit hash + transcript
2. Run `git show [hash] --name-only` to see changed files
3. Run `git show [hash]:[filepath]` to see code changes
4. Analyze transcript for learning objectives
5. Create comprehensive notes in Toss style
6. **APPEND to the END** of `/Users/hankyulee/Desktop/Uaps/lecture/lecture-notes/complete-lecture-notes.md` (add below the last lecture)
7. Use clear emoji indicators for each section
8. Include full code examples with syntax highlighting
9. Add Korean translations for clarity

**Example Trigger:**
```
User: "와 [commit-hash] 커밋을 참고해서 complete-lecture-notes에 다음강의를 추가해줘. 토스스타일."
[transcript provided]

→ Analyze commit, create Toss-style notes, append to lecture notes file
```

---

### Rule 5: GIT_COMMIT_MESSAGE_RULE
**Always write commit messages with both English and Korean titles so the user can understand. The Korean translation helps the user quickly grasp what changes were made without reading the entire commit message.**

**Commit Message Format:**
```
type: English title (한글 제목)

한글 상세 설명:
- 변경사항 1
- 변경사항 2

Changes:
- Technical details in English/Korean mix

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
```

**Examples:**

#### ✅ Good (Bilingual):
```
feat: Add Nova 1492 style part rotation (노바1492 스타일 부품 회전 추가)

부품별 독립적인 회전 속도 시스템 구현:
- 머리: 180°/s (빠름)
- 몸통: 90°/s (중간)
- 다리: 45°/s (느림)

Changes:
- Add RobotPart.cs: Individual part rotation component
- Update WorkerThreeRobot.cs: Independent rotation system
```

#### ✅ Good (Submodule):
```
chore: Update urts-course submodule (urts-course 서브모듈 업데이트)

urts-course 서브모듈 최신 커밋 참조 업데이트:
- Nova 1492 style part rotation system
- Performance optimization rules
```

#### ❌ Bad (English only):
```
feat: Add part rotation system

Added rotation system for robot parts with different speeds.

Changes:
- RobotPart.cs added
- WorkerThreeRobot.cs updated
```

**Commit Types (with Korean):**
- `feat`: New feature (새 기능)
- `fix`: Bug fix (버그 수정)
- `docs`: Documentation (문서)
- `chore`: Maintenance (유지보수)
- `refactor`: Code refactoring (리팩토링)
- `perf`: Performance improvement (성능 개선)
- `test`: Tests (테스트)
- `style`: Code style (코드 스타일)

**Action Steps:**
1. Determine commit type
2. Write English title
3. **Add Korean translation in parentheses**
4. Write detailed Korean explanation
5. List specific changes
6. Add standard footer
7. **ALWAYS commit root folder after submodule commit** (see below)

**Dual Commit Workflow (Submodule + Root):**

When working in `urts-course` (submodule):
1. **First**: Commit changes in submodule (`/Users/hankyulee/Desktop/Uaps/lecture/urts-course`)
2. **Second**: AUTOMATICALLY commit root folder (`/Users/hankyulee/Desktop/Uaps/lecture`) to update submodule reference

**Implementation:**
```bash
# Step 1: Commit in submodule
cd /Users/hankyulee/Desktop/Uaps/lecture/urts-course
git add .
git commit -m "feat: Add feature (기능 추가)"

# Step 2: ALWAYS commit root folder
cd /Users/hankyulee/Desktop/Uaps/lecture
git add urts-course
git commit -m "chore: Update urts-course submodule (urts-course 서브모듈 업데이트)

urts-course 서브모듈 최신 커밋 참조 업데이트:
- [Brief summary of submodule changes]

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>"
```

**⚠️ CRITICAL**: When user says "커밋" or "commit", ALWAYS execute BOTH commits (submodule + root). Never ask user if they want root commit - just do it automatically.

**Why Korean Matters:**
- User needs to quickly understand commits in their native language
- Technical terms can be kept in English, but context should be in Korean
- Commit history becomes searchable and understandable at a glance

---

### Rule 6: CROSS_CLASS_FEATURE_SYNC_RULE
**When applying features from Worker to WorkerThreeRobot (or any similar class pairs), always check for existing logic conflicts. Don't just copy-paste interfaces and methods - understand how the new feature interacts with existing code.**

**Why This Rule Exists:**
- Worker and WorkerThreeRobot have different implementations
- Worker is simple, WorkerThreeRobot has custom movement/rotation logic
- Blindly copying features can introduce hidden bugs

**Conflict Detection Steps:**

1. **Read Both Classes Fully**:
   - Before applying Worker changes to WorkerThreeRobot, read BOTH complete files
   - Understand the architectural differences

2. **Check for Logic Conflicts**:
   - Does WorkerThreeRobot have Update() that conflicts with new feature?
   - Does it have fields that might override the new behavior?
   - Are there existing systems that need refactoring?

3. **Test Integration Points**:
   - After adding interface, verify ALL methods interact correctly
   - Check Update(), Awake(), Start() for conflicts

**Key Problem**: Update() may override new methods every frame. Check for field/logic conflicts.

**Action Steps:**

When user says: *"Worker에 X 기능 추가했는데, WorkerThreeRobot에도 추가"*

1. **Read Worker changes** (git diff or file read)
2. **Read FULL WorkerThreeRobot** (not just the sections to modify)
3. **Identify conflicts**:
   - Search for conflicting fields (e.g., `target`)
   - Search for conflicting Update/FixedUpdate logic
   - Search for methods that might override new behavior
4. **Refactor if needed**: Don't just add, adapt the implementation
5. **Verify**: Think through the execution flow frame-by-frame

**Common Conflict Patterns:**

| Pattern | What to Check |
|---------|---------------|
| Adding IMoveable | Does Update() override agent.SetDestination? |
| Adding targeting system | Does existing movement conflict? |
| Adding state machine | Are there hardcoded state checks in Update? |
| Adding pooling | Does Awake/Start assume single instantiation? |

**Quick Checklist:**

Before applying Worker feature to WorkerThreeRobot:
- [ ] Read both complete files
- [ ] List architectural differences
- [ ] Identify potential conflicts (Update, fields, etc.)
- [ ] Plan refactoring if needed
- [ ] Apply changes with adaptations
- [ ] Mentally simulate frame-by-frame execution

---

### Rule 7: MODULAR_ARCHITECTURE_RULE
**This project follows a rapid prototyping approach: build core features first, then integrate server/network and other components later. Design all code to be easily modifiable and extensible to accommodate future integration without major refactoring.**

**Why This Rule Exists:**
- Server, networking, and other systems will be added after core gameplay is complete
- Current code must be compatible with future server-client architecture
- Avoid tight coupling that makes it difficult to add new layers later
- Minimize breaking changes when integrating multiplayer/backend systems

**Design Principles:**

1. **Modularization**:
   - Separate concerns clearly (UI, Game Logic, Data, Input)
   - Use interfaces and abstractions instead of concrete implementations
   - Keep dependencies unidirectional (avoid circular dependencies)

2. **Clear Folder Structure**:
   ```
   Assets/Scripts/
   ├── Core/           # Game-agnostic systems (can work without Unity)
   ├── Units/          # Unit-specific logic
   ├── Player/         # Player input and control
   ├── UI/             # User interface
   ├── Network/        # Future: Network communication (placeholder)
   ├── EventBus/       # Event system (loose coupling)
   └── Events/         # Event definitions
   ```

3. **Precise Naming**:
   - **Classes**: `WorkerThreeRobot` (not `Robot3` or `WTR`)
   - **Methods**: `MoveTo(Vector3 position)` (not `Move()` or `Go()`)
   - **Fields**: `headRotationSpeed` (not `speed1` or `hrs`)
   - **Events**: `UnitSelectedEvent` (not `SelectEvent` or `UnitEvt`)

4. **Server-Ready Patterns**:
   - Use **Event Bus** instead of direct method calls (easy to intercept for networking)
   - Separate **input** from **logic** (PlayerInput → Game Logic → Unit)
   - Design for **validation**: Client sends requests, server validates
   - Avoid `GameObject.Find()` - use dependency injection or registries

**Examples:**

**Key Pattern**: Use Event Bus instead of direct calls for server-ready code.

❌ Bad: `selectedUnit.transform.position = hitPoint;` (direct modification)
✅ Good: `Bus<MoveCommandEvent>.Raise(new MoveCommandEvent(selectedUnit, hitPoint));` (event-based)

**Naming Conventions:**

| Type | Good | Bad | Why |
|------|------|-----|-----|
| Class | `WorkerThreeRobot` | `WTR`, `Robot3` | Clear, searchable, self-documenting |
| Method | `MoveTo(Vector3)` | `Move()`, `Go()` | Specific parameter intent |
| Field | `headRotationSpeed` | `speed`, `hrs` | Context clear without reading code |
| Event | `UnitSelectedEvent` | `Select`, `UEvt` | Event suffix, clear purpose |
| Interface | `IMoveable` | `IMove`, `Moveable` | Standard C# convention |
| Folder | `EventBus/` | `Events/`, `Sys/` | Describes system, not data |

**Folder Organization Rules:**

1. **Group by Feature, Not Type**:
   - ✅ `Units/Worker.cs`, `Units/WorkerThreeRobot.cs`, `Units/ISelectable.cs`
   - ❌ `Scripts/Worker.cs`, `Interfaces/ISelectable.cs` (hard to find related files)

2. **Separate Layers**:
   - `Core/` - Pure C# logic (no MonoBehaviour)
   - `Scripts/` - Unity-specific implementations
   - `Events/` - Event definitions only
   - `EventBus/` - Event system implementation

3. **Future-Proof Structure**:
   - Reserve `Network/` folder even if empty
   - Keep `Server/` and `Client/` in mind when designing classes

**Pre-Integration Checklist:**

Before adding server/network systems, ensure:
- [ ] Game logic uses Event Bus (not direct calls)
- [ ] Input is separated from logic
- [ ] No GameObject.Find() in core systems
- [ ] Classes have single responsibility
- [ ] Folder structure is clear and organized
- [ ] Naming is precise and self-documenting
- [ ] Dependencies flow in one direction

**Action Steps:**

1. When creating new features, ask: "How will this work with a server?"
2. Use Event Bus for all cross-system communication
3. Name classes/methods precisely - never abbreviate
4. Organize files by feature domain
5. Keep UI, Logic, and Data separate
6. Document architectural decisions in comments

---

### Rule 8: THREE_PART_ROBOT_BUILDER_RULE
**When creating or updating ThreePartRobotBuilder.cs (or similar Unity Editor automation tools), always ensure all required Unity components, serialized fields, and references are properly assigned. Never create incomplete prefabs that would require manual fixes.**

**See:** `.claude/RULE_8_THREE_PART_ROBOT_BUILDER.md` for complete implementation details.

**Quick Checklist:**
- [ ] Layer set to 6 (Selectable Units)
- [ ] All components added (NavMeshAgent, CapsuleCollider, Script)
- [ ] All serialized fields assigned (headPart, bodyPart, legsPart, decalProjector, UnitSO, AvailableCommands)
- [ ] DecalProjector GameObject created as child
- [ ] Use SerializedObject for field assignment (not Reflection)
- [ ] Test: Selection, movement, and DecalProjector visibility work

---

### Rule 9: DETAILED_CODE_COMMENTS_RULE
**When writing code examples in lecture notes or documentation, always include detailed line-by-line comments that explain what each line does, why it's needed, and how it fits into the overall system. Make code examples educational and self-documenting.**

**Requirements:**
- Every significant line must have a comment explaining its purpose
- Comments should explain the "why" not just the "what"
- Include context about how the code fits into the larger system
- Use Korean comments for better understanding
- Explain technical concepts in simple terms

**Comment Types:**
1. **Purpose Comments**: What this line accomplishes
2. **Context Comments**: How it relates to other systems
3. **Technical Comments**: Why this approach was chosen
4. **Educational Comments**: What beginners should understand

**Key Principle**: Every line needs Korean comments explaining "why", not just "what".

❌ Bad: `private Camera camera;` (no comment)
✅ Good: `// 카메라 참조 - 화면 좌표를 월드 좌표로 변환하기 위해 필요`

**Action Steps:**

1. **For New Code Examples**: Always include detailed comments
2. **For Existing Code**: Update with comprehensive comments
3. **For Complex Logic**: Break down into smaller, commented sections
4. **For Technical Concepts**: Explain in simple terms
5. **For System Integration**: Show how code connects to other systems

**Comment Guidelines:**

- **Korean Comments**: Use Korean for better understanding
- **Educational Focus**: Explain concepts, not just syntax
- **Context Awareness**: Show how code fits into the bigger picture
- **Beginner Friendly**: Assume readers are learning
- **Technical Depth**: Include "why" explanations, not just "what"

**When to Apply:**
- All lecture note code examples
- Architecture documentation code snippets
- Tutorial and educational content
- Code reviews and explanations
- Any code shared with learners

This rule ensures that all code examples serve as effective learning tools, making complex concepts accessible to developers at all skill levels.

---

**Created**: 2024-10-03
**Updated**: 2025-10-10 (Added Rule 6: CROSS_CLASS_FEATURE_SYNC_RULE, Rule 7: MODULAR_ARCHITECTURE_RULE, Rule 8: THREE_PART_ROBOT_BUILDER_RULE)
**Project**: urts-course
**Purpose**: Establish clear rules for code modification and improvement
