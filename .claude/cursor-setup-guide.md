# 🚀 새로운 Cursor를 위한 프로젝트 가이드

## 📋 필수 읽기 파일 (우선순위 순)

### 1. 핵심 룰 요약 ⭐⭐⭐
**파일**: `/Users/hankyulee/Desktop/Uaps/lecture/urts-course/.claude/rules-summary.md`
- **50줄 핵심 요약**
- **7개 룰 우선순위**
- 현재 프로젝트 상태
- **반드시 먼저 읽기!**

### 2. 상세 코드 룰 ⭐⭐
**파일**: `/Users/hankyulee/Desktop/Uaps/lecture/urts-course/.claude/code-rules.md`
- **597줄 상세 룰**
- Rule 1-7 전체 설명
- 예제 코드 포함
- **코드 수정 전 필수 확인**

### 3. 프로젝트 인계 문서 ⭐
**파일**: `/Users/hankyulee/Desktop/Uaps/.claude/session-handoff.md`
- 프로젝트 개요
- 작업 흐름
- 주요 파일 위치
- 현재 상태 요약

### 4. Unity Prefab 생성 프로세스 ⭐
**파일**: `/Users/hankyulee/Desktop/Uaps/lecture/urts-course/.claude/unity-prefab-creation-process.md`
- Unity Prefab 생성 프로세스
- Scene 배치 방법
- **Unity 작업 시 필수**

---

## 🎯 추천 읽기 순서

1. **rules-summary.md** (빠른 파악)
2. **session-handoff.md** (프로젝트 구조)
3. **code-rules.md** (상세 룰)
4. **unity-prefab-creation-process.md** (Unity 작업 시)

---

## ⚡ 최소 세트 (Cursor가 꼭 읽어야 할 것)

- **rules-summary.md** (필수)
- **code-rules.md** (필수)

**이 2개만 있으면 작업 가능합니다.**

---

## 🎯 프로젝트 컨텍스트

### 📋 프로젝트 개요
- **목표**: 노바1492 스타일 로봇 대전 게임 (Unity RTS)
- **특징**: 3개 파츠로 조립 가능한 로봇 시스템
- **작업 위치**: `/Users/hankyulee/Desktop/Uaps`
- **Unity 프로젝트**: `lecture/urts-course` (Git 서브모듈)

### 📊 현재 상태
- **강의**: 15개 완료 (11-12시간 분량)
- **최근 작업**: Event Bus 패턴 구현 (UnitDeselectedEvent)
- **Git 브랜치**: urts-course(dev), Uaps(main)
- **완료된 기능**: 유닛 선택, 이동, 드래그 선택, Event Bus

### 🏗️ 핵심 구현
- ✅ **WorkerThreeRobot**: 3파츠 로봇 (head/body/legs 독립 회전)
- ✅ **Event Bus**: 느슨한 결합을 위한 이벤트 시스템
- ✅ **ISelectable/IMoveable**: 인터페이스 기반 설계
- ✅ **강의 노트**: Toss 스타일 HTML 문서 (15개 완료)

---

## 🚨 중요한 룰 (빠른 참조)

### Rule 7: MODULAR_ARCHITECTURE_RULE ⭐⭐⭐
- Event Bus로 느슨한 결합 유지
- 명확한 폴더 구조
- 정확한 네이밍 (축약 금지)
- 서버 통합 대비 설계

### Rule 6: CROSS_CLASS_FEATURE_SYNC_RULE ⭐⭐
- Worker → WorkerThreeRobot 기능 적용 시 충돌 체크 필수
- 두 클래스 전체 읽기
- Update() 충돌 확인

### Rule 5: GIT_COMMIT_MESSAGE_RULE ⭐⭐
- 영어 + 한글 제목 필수
- 표준 형식 준수

### Rule 4: LECTURE_NOTES_DOCUMENTATION_RULE
- 강의 노트는 HTML에 추가
- Toss 스타일 (이모지 + 명확한 구조)
- 맨 아래 APPEND

### Rule 3: PERFORMANCE_OPTIMIZATION_RULE
- 50+ 유닛 대응 최적화
- Update()에서 GetComponent() 금지
- 객체 풀링 사용

### Rule 2: CODE_IMPROVEMENT_RULE
- 코드 분석 시 적극적으로 개선 제안
- 중복 코드/데이터 발견 시 즉시 제안

### Rule 1: CODE_MODIFICATION_RULE
- 애매한 요청은 수정하지 말고 질문

---

## 📂 주요 파일 위치

### 코드
```
Assets/Scripts/Units/
├── ISelectable.cs          # 선택 가능 인터페이스
├── IMoveable.cs             # 이동 가능 인터페이스
├── Worker.cs               # 기본 Worker (1파츠)
├── WorkerThreeRobot.cs     # 3파츠 로봇
└── RobotPart.cs            # 파츠별 회전 컴포넌트

Assets/Scripts/Player/
├── PlayerInput.cs          # 입력 처리 (선택, 이동)
└── CameraConfig.cs         # 카메라 설정

Assets/Scripts/EventBus/
├── Bus.cs                  # 제네릭 이벤트 버스
└── IEvent.cs               # 이벤트 인터페이스

Assets/Scripts/Events/
├── UnitSelectedEvent.cs    # 유닛 선택 이벤트
└── UnitDeselectedEvent.cs # 유닛 선택 해제 이벤트
```

### 문서
```
.claude/
├── cursor-setup-guide.md              # 이 문서
├── session-handoff.md                 # 프로젝트 인계 문서
└── settings.local.json                # 설정 파일

lecture/urts-course/.claude/
├── rules-summary.md                   # 핵심 룰 요약
├── code-rules.md                     # 상세 코드 룰
└── unity-prefab-creation-process.md   # Unity Prefab 생성 프로세스

lecture/lecture-notes/
└── complete-lecture-notes.html        # 강의 노트 (15개 완료)
```

---

## 🔄 작업 흐름

### 📚 Lecture 명령어 자동화 프로세스

**사용자 명령어**: `lecture <커밋해시> <강의 대본 전체>`

**8단계 자동화 프로세스**:

1. **커밋 분석**: `git show [hash]`로 변경사항 분석
2. **코드 변경사항 추출**: 수정된 파일들과 변경 내용 파악
3. **강의 대본 분석**: 학습 목표와 핵심 개념 추출
4. **HTML 강의 노트 생성**: Toss 스타일로 구조화
5. **Worker → WorkerThreeRobot 동기화**: Rule 6 준수하여 충돌 체크
6. **Unity 설정 확인**: 필요한 에디터 설정 변경사항 확인
7. **문서 업데이트**: `complete-lecture-notes.html`에 강의 추가
8. **커밋**: Rule 5 준수하여 영어+한글 메시지로 커밋

**예시 사용법**:
```
lecture c5678e62737ccb0da2c11ccc558017d2431a981f "강의 15: Deselecting Units with Event Bus

학습 목표:
- Event Bus 패턴 이해
- UnitDeselectedEvent 구현
- 메모리 누수 방지

핵심 개념:
- Discrete Event Structs
- 이벤트 핸들러 분리
- Unsubscribe 패턴"
```

### 새로운 강의 추가 시 (수동)
1. 사용자가 커밋 해시 제공
2. `git show [hash]`로 변경사항 분석
3. `complete-lecture-notes.html`에 강의 추가 (Toss 스타일)
4. Worker 변경사항을 WorkerThreeRobot에도 적용 (Rule 6 준수)
5. 커밋 (Rule 5 준수)

### WorkerThreeRobot 기능 동기화
- Worker에 새 기능 추가 시 WorkerThreeRobot에도 동일하게 적용
- **Rule 6 준수**: 전체 파일 읽고 Update() 충돌 체크
- 기존 로직과 충돌 확인 후 적용

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

---

## 🎨 코드 스타일 선호

- **간결함**: Guardian Pattern, Pattern Matching 선호
- **성능**: 캐싱, 조기 리턴, 메모리 할당 최소화
- **확장성**: 인터페이스 활용, SOLID 원칙
- **네이밍**: 정확한 이름 (축약 금지)

---

## 🚨 주의사항

### Unity Prefab 작업
- **Prefab 생성 후 반드시 Scene에도 배치** (PrefabInstance 추가)
- 위치: `unity-prefab-creation-process.md` 참고

### Vector3 임포트 주의
- ❌ `System.Numerics.Vector3`
- ✅ `UnityEngine.Vector3`

### Layer 변경 후 확인
- 새 Physics Layer 추가 시 **Layer Collision Matrix 반드시 확인**

### 서브모듈 커밋 순서
1. `urts-course`에서 변경 후 커밋
2. `Uaps`로 이동
3. `git add lecture/urts-course` (서브모듈 참조 업데이트)
4. 커밋 (chore: Update urts-course submodule ...)

---

## 📞 인계 완료

새 세션에서는:
1. **이 문서 먼저 읽기**
2. **rules-summary.md** 읽고 컨텍스트 파악
3. **code-rules.md** 읽고 작업 규칙 확인
4. 사용자가 새 강의 커밋 제공하면 동일한 프로세스 진행
5. 항상 두 룰 파일 규칙 준수
6. WorkerThreeRobot에도 Worker 변경사항 적용

**작업 디렉토리**: `/Users/hankyulee/Desktop/Uaps`
**현재 날짜**: 2025-01-27

---

**생성자**: Claude (Sonnet 4.5)
**생성 날짜**: 2025-01-27
**문서 버전**: v1.0
