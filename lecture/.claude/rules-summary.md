# 핵심 룰 요약

**상세 내용**: `code-rules.md` 참고

---

## 🚀 새 세션 시작 가이드

**명령어:** `인수인계`

**실행 후:**
1. 최신 HANDOFF 문서의 "최우선 과제" 확인
2. 과제가 있으면 먼저 해결
3. 완료 시 HANDOFF에 표시 후 대기

---

## ⚠️ CRITICAL RULES (항상 확인)

### Rule 5: MODULAR_ARCHITECTURE_RULE ⭐⭐⭐
**핵심**: Event Bus 사용, 명확한 네이밍, 단일 책임 원칙

---

### Rule 6: LECTURE_COMMAND_PROCESSING_RULE ⭐⭐
**워크플로우**: 노트→체리픽→동기화→아키텍처 업데이트 (4단계, 듀얼커밋)

---

### Rule 4: GIT_COMMIT_MESSAGE_RULE ⭐⭐
**형식**: `type: English title (한글 제목)` + 듀얼커밋 자동

---

## 📋 일반 규칙

### Rule 12: PROJECT_FILE_LOCATIONS_RULE ⭐⭐
**주요 파일 위치**: Worker BT, Worker.cs, WorkerThreeRobot.cs, RobotPart.cs
**상세**: `.claude/RULE_12_PROJECT_FILE_LOCATIONS.md`

---

### Rule 3: LECTURE_NOTES_DOCUMENTATION_RULE
**강의 노트 HTML (Toss 스타일, 6섹션)**

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

### Rule 6: DETAILED_CODE_COMMENTS_RULE ⭐⭐
**강의 노트 코드는 줄마다 한글 주석 (4가지: 목적+맥락+기술+교육)**

---

**업데이트**: 2025-10-20 (룰 간소화 - Rule 3,6,8 제거, 번호 재정렬)
