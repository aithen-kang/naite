# CHANGELOG

저장소 전체에 버전 하나를 둡니다. 파일마다 버전 표기를 두지 않습니다.

판마다 **추가 · 변경 · 폐기된 항목 ID**를 적습니다. AI가 재진단할 때, 당신 지침에 남은 `(naite ID · 판)` 표시와 이 목록을 대조해 새로 볼 것만 추리기 때문입니다. 변경·폐기 ID에는 괄호로 한 줄 이유를 붙입니다 — 재진단이 "무엇이 왜 바뀌었는지"를 보여 줘야 하기 때문입니다. 이미 붙은 ID는 재사용하거나 번호를 바꾸지 않습니다. 폐기한 ID의 gate 본문은 지우지 않고 제목 뒤에 `(폐기 vX.Y.Z: 이유)`를 붙여 남깁니다 — 폐기는 오류 판정이 아니라 조건이 바뀐 것이고, 알고서 가져다 쓰는 것은 자유입니다. 폐기한 규칙을 되살리면 새 ID를 받고, 추가 ID에 `SPEC-25 (SPEC-04 재도입: 이유)`처럼 계보를 적습니다.

## v0.3.0 — 2026-09-16

- automation 트랙 — `cases.md` 17항목, `gates.md` 12항목, `kit/` 4종. 추가 ID: AUTO-01 · 02 · 03 · 04 · 05 · 06 · 09 · 14 · 15 · 16 · 17 · 18 · 19 · 20 · 21 · 22 · 23 (gate 없음: 04 · 06 · 14 · 17 · 22)
- numbers 트랙 — `cases.md` 28항목, `gates.md` 11항목, `kit/` 3종. 추가 ID: NUM-01 ~ NUM-28 (gate: 02 · 04 · 05 · 09 · 11 · 12 · 13 · 20 · 25 · 27 · 28, 나머지는 NUM-27 체크리스트의 줄)
- AI-START — 트랙 표에 automation · numbers, 거둠·복귀 판정과 트리거, 제안 순서 조정(충돌 질문을 제거 질문 바로 뒤로), 상한을 넘친 충돌은 숨기지 않고 충돌 절에 한 줄씩(고른 것만 다음 응답에서 펼침), 폐기 gate 판정 제외, 재도입 계보, 제안 상한 5개의 유래 표기, 적용됨·거둠 gate의 진단표 제외
- `retire.md` 신설 — 거둠 표시 형식, 원칙 넷, AI에게 건넬 발주문(판단형)
- setup `gates.md` · `cases.md` — SETUP-08 원칙 추상화
- setup kit `auth-module` — 참조 골격이라는 신분 명시
- README — 거둠 표시와 거둠·복귀 사례 issue 안내
- setup `cases.md` · kit `status-table` — 폐기 번호 SETUP-20의 사건이 옮겨 간 곳과 무인 실행 항목으로의 연결 안내(ID 변경 없음)
- `adapters/` — automation 트랙 적용 메모

**추가 ID:** AUTO-01 · AUTO-02 · AUTO-03 · AUTO-04 · AUTO-05 · AUTO-06 · AUTO-09 · AUTO-14 · AUTO-15 · AUTO-16 · AUTO-17 · AUTO-18 · AUTO-19 · AUTO-20 · AUTO-21 · AUTO-22 · AUTO-23 · NUM-01 ~ NUM-28 (AUTO-07 · 08 · 10 · 11 · 12 · 13은 이 판에 넣지 않은 번호)
**변경 ID:** SETUP-08 (원칙을 "한 파일"에서 "정본 하나"로 추상화 — 옛 제목 "자격증명은 한 파일, 스크립트는 모르게". OS 키체인·비밀 저장소 환경을 포함하도록)
**폐기 ID:** 없음

## v0.2.0 — 2026-09-15

- setup 트랙 — `cases.md` 21항목, `gates.md` 13항목, `kit/` 5종
- spec 트랙 — 사례만 있던 SPEC-09·10·11·13에 논리형 gate 추가(`gates.md` 15 → 19항목)
- AI-START — 트랙 표에 setup, 논리형 gate의 판정, starter 표시와 트랙 항목의 대응, 진단 판정 "부분"·"확인 필요", 지침 파일 신설 제안의 표기, 사용자 지침이 요구하는 응답 머리말 허용, 격차 경로의 읽는 범위, 수준이 애매할 때 멈추고 묻는 칸, 판정 칸에는 값 하나(한정 조건은 근거 칸), starter S5와 지침 파일 신설 제안을 제안 상한에서 제외, kit 파일은 이름만 제안
- spec `gates.md` — 표시 형식에 판을 붙이도록 교정
- `starter.md` — 트랙 항목 대응표
- `adapters/claude-code.md` — 대화 기록 보존 행, setup 트랙 적용 메모

**추가 ID:** SETUP-01 · SETUP-02 · SETUP-03 · SETUP-04 · SETUP-05 · SETUP-06 · SETUP-07 · SETUP-08 · SETUP-09 · SETUP-10 · SETUP-11 · SETUP-12 · SETUP-13 · SETUP-14 · SETUP-15 · SETUP-16 · SETUP-17 · SETUP-18 · SETUP-19 · SETUP-21 · SETUP-22 (SETUP-20은 공개 전에 폐기한 번호)
**변경 ID:** SPEC-09 · SPEC-10 · SPEC-11 · SPEC-13 (gate 추가)
**폐기 ID:** 없음

## v0.1.0 — 2026-09-15

- AI 진입점 `AI-START.md` — 전제 확인부터 적재 훅까지 8단계, 처음 진단과 재진단 두 경로, 금지 다섯 가지, 고정 출력 양식. 파일을 직접 읽는 에이전트 전용
- `starter.md` — 지침 파일이 없는 사용자용 최소셋 5항목
- spec 트랙 — `cases.md` 19항목, `gates.md` 15항목, `kit/` 9종
- `adapters/` 2종 — claude-code · agents-md
- 라이선스 — 사용자 환경에 넣는 조각은 MIT-0, 그 밖의 문서는 CC BY 4.0

**추가 ID** (항목 기준 — SPEC 19개 중 gate가 있는 것은 15개, SPEC-09·10·11·13은 사례만 있다): SPEC-01 · SPEC-02 · SPEC-03 · SPEC-04 · SPEC-05 · SPEC-06 · SPEC-07 · SPEC-08 · SPEC-09 · SPEC-10 · SPEC-11 · SPEC-12 · SPEC-13 · SPEC-14 · SPEC-15 · SPEC-16 · SPEC-17 · SPEC-18 · SPEC-19 · S1 · S2 · S3 · S4 · S5
**변경 ID:** 없음
**폐기 ID:** 없음
