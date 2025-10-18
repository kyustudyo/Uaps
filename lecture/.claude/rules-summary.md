# 핵심 룰 요약

**상세 내용**: `code-rules.md` 참고

---

## 🚀 새 세션 시작 가이드

**명령어:** `인수인계`

이 명령으로 자동 실행:
1. `.claude/rules-summary.md` (이 파일) - 핵심 룰 빠른 참조
2. `.claude/code-rules.md` - 상세 룰 전체
3. `.claude/RULE_*.md` - 개별 상세 룰 문서들
4. `.claude/HANDOFF_*.md` - 모든 인수인계 문서 (최신순)
5. `/Users/hankyulee/Desktop/Uaps/lecture/.claude/*` - 루트 폴더 .claude 파일들

**실행 후:**
1. ⚠️ **최우선**: 최신 HANDOFF 문서의 "최우선 과제" 확인
2. 최우선 과제가 있으면 먼저 해결 (다른 작업 금지)
3. 해결 완료 시:
   - LONG_TERM_TODO.md에서 해당 항목 체크 또는 삭제
   - HANDOFF 문서에 완료 표시
4. 모든 파일을 읽고 대기 상태로 전환

---

## ⚠️ CRITICAL RULES (항상 확인)

### Rule 7: MODULAR_ARCHITECTURE_RULE ⭐⭐⭐
**빠른 프로토타입 → 나중에 서버 통합**

**핵심:**
- Event Bus로 느슨한 결합 유지 (서버 추가 용이)
- 명확한 폴더 구조 (Units/, UI/, EventBus/, Events/)
- 정확한 네이밍 (축약 금지): `WorkerThreeRobot` ✅, `WTR` ❌
- 단일 책임 원칙 (UI, Logic, Data 분리)

**체크리스트:**
- [ ] Event Bus 사용? (직접 호출 ❌)
- [ ] 이름이 명확한가? (축약 없이)
- [ ] 서버 추가 시 수정 최소화 가능한가?

---

### Rule 6: LECTURE_COMMAND_PROCESSING_RULE ⭐⭐
**lecture 워크플로우: 노트작성→체리픽→동기화→아키텍처 업데이트→듀얼커밋**

**4단계:**
1. 강의 노트 HTML 추가 (코드 설명 + 왜 필요한가? + 초등학생도 이해 가능하게)
2. 커밋 체리픽
3. WorkerThreeRobot 동기화
4. 아키텍처 문서 업데이트 (PlantUML 다이어그램) ⚠️ **절대 누락 금지**

**듀얼 커밋 (자동):**
서브모듈 커밋 → 루트 폴더 커밋 (서브모듈 참조 업데이트)

---

### Rule 5: GIT_COMMIT_MESSAGE_RULE ⭐⭐
**형식: `type: English title (한글 제목)` + 듀얼커밋**

**타입**: feat, fix, docs, chore, refactor, perf

**듀얼 커밋**: 서브모듈 → 루트 (자동, 물어보지 말 것)

---

## 📋 일반 규칙

### Rule 12: PROJECT_FILE_LOCATIONS_RULE ⭐⭐
**프로젝트 파일 위치 빠른 참조**

**핵심:**
- Worker BT: `Assets/Units/Worker/Worker BT.asset`
- Worker.cs: `Assets/Scripts/Units/Worker.cs`
- WorkerThreeRobot.cs: `Assets/Scripts/Units/WorkerThreeRobot.cs`
- RobotPart.cs: `Assets/Scripts/Units/RobotPart.cs`
- 강의 노트: `lecture/lecture-notes/complete-lecture-notes-4.html`

**길 가면서 주석 읽기:**
1. Event Bus 시스템 (Bus.cs, IEvent.cs) - 5분
2. 유닛 구조 (AbstractUnit.cs, Worker.cs) - 10분
3. 3-파트 로봇 (RobotPart.cs, WorkerThreeRobot.cs) - 15분

**체크리스트:**
- [ ] Worker BT 파일 위치 확인?
- [ ] 주요 스크립트 파일 위치 확인?
- [ ] 강의 노트에서 상세 설명 읽기?

**상세:** `.claude/RULE_12_PROJECT_FILE_LOCATIONS.md`

---

### Rule 4: LECTURE_NOTES_DOCUMENTATION_RULE
**강의 노트 HTML (Toss 스타일, 맨 아래 APPEND)**

**6섹션**: 🎯학습 목표 → 📝핵심 개념 → 💻구현 코드 → ⚙️Unity 설정 → 🎓학습 포인트 → 🎮노바1492 적용

---

### Rule 3: PERFORMANCE_OPTIMIZATION_RULE
**50+ 유닛 대응 최적화**

- Update()에서 GetComponent() 금지 → Awake/Start에서 캐싱
- 객체 풀링 사용
- 메모리 할당 최소화
- "50개 유닛에서도 작동할까?" 항상 체크

---

### Rule 2: CODE_IMPROVEMENT_RULE
**코드 분석 시 적극적으로 개선 제안**

- 중복 코드/데이터 발견 시 즉시 제안
- 비효율적인 구조 개선
- 사용자가 물어보기 전에 먼저 제안

---

### Rule 1: CODE_MODIFICATION_RULE
**애매한 요청은 수정하지 말고 질문**

- ❌ "버그 수정해" → 어떤 버그인지 질문
- ✅ "PlayerController 45번 라인 null reference 수정" → 진행

---

---

### Rule 10: TODO_MANAGEMENT_RULE
**TODO는 `/Users/hankyulee/Desktop/Uaps/TODO/index.html`에 추가 (룰에 추가 금지)**

---

### Rule 11: CHERRY_PICK_STRATEGY_RULE ⭐⭐⭐
**Phase 1: 모든 강의 체리픽 (커스텀❌) → Phase 2: 커스텀 통합 (1회)**

**장점**: 충돌 N번→1번, Unity 캐시 문제 방지, Git 히스토리 깔끔

---

### Rule 8: THREE_PART_ROBOT_BUILDER_RULE ⭐
**Unity Editor 도구는 완전 자동화 (수동 수정 불필요)**

**필수**: Layer 6 + DecalProjector + 모든 SerializedField 자동할당 + SerializedObject 사용

---

### Rule 9: DETAILED_CODE_COMMENTS_RULE ⭐⭐
**강의 노트 코드는 줄마다 한글 주석 (초보자 이해 가능하게)**

**4가지 주석**: 목적 + 맥락 + 기술 + 교육

---

## 🚀 현재 프로젝트 상태
- 강의: 15개 완료 (11-12시간)
- 최근 작업: UnitDeselectedEvent (Event Bus 확장)
- 툴: ThreePartRobotBuilder (3-파트 로봇 자동 생성)
- Git 브랜치: urts-course(dev), Uaps(main)

---

**업데이트**: 2025-10-18 (Rule 12 추가 - Project File Locations)
