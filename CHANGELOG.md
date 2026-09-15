# CHANGELOG

저장소 전체에 버전 하나를 둡니다. 파일마다 버전 표기를 두지 않습니다.

판마다 **추가 · 변경 · 폐기된 항목 ID**를 적습니다. AI가 재진단할 때, 당신 지침에 남은 `(naite ID · 판)` 표시와 이 목록을 대조해 새로 볼 것만 추리기 때문입니다. 변경·폐기 ID에는 괄호로 한 줄 이유를 붙입니다 — 재진단이 "무엇이 왜 바뀌었는지"를 보여 줘야 하기 때문입니다. 이미 붙은 ID는 재사용하거나 번호를 바꾸지 않습니다.

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
