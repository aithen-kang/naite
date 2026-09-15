# kit · auth-module — 인증 위임 모듈 뼈대

- **조건:** 스크립트가 외부 서비스 자격증명을 쓰고, 자격증명 종류가 둘 이상이거나 만료 주기가 서로 다르다
- **도구 의존:** Python 표준 라이브러리만. 다른 언어로 옮길 때도 세 단의 구조는 같다
- **쓰는 ID:** SETUP-08 · SETUP-09
- **신분:** 참조 골격이다. 세 단의 구조를 보여 주는 예시이지 비밀 관리 구현이 아니다 — 환경 파일 문법(따옴표 안의 이스케이프·여러 줄 값·변수 확장), 파일 권한, 프로세스 환경 변수와의 우선순위는 다루지 않는다. OS 키체인·비밀 저장소가 정본인 환경은 1단(파일 탐색)을 그 조회로 바꾼다(SETUP-08)

**규칙: 어떤 스크립트도 자격증명을 직접 들고 있지 않는다.** 모든 외부 호출은 이 모듈 하나를 거친다. 모듈은 세 단으로 짠다.

1. **위로 올라가며 환경 파일을 찾는다** — 스크립트가 어느 깊이에 있든, 스케줄러가 어느 폴더에서 실행하든 동작한다.
2. **필수 키를 검증하고, 없으면 무엇이 없고 어떻게 채우는지로 실패한다** — 늦게 모호하게 죽는 "401"보다 일찍 분명하게 죽는 편이 비개발자를 살린다.
3. **호출 함수까지 내보낸다** — 인증 헤더 조립, 만료 판별, 갱신을 모듈 안에 둔다. 스크립트는 자격증명의 존재조차 모른다.

```python
import re
from pathlib import Path

ENV_RELPATH = Path('.env')   # 환경에 맞게 바꾼다 — 예: Path('.claude') / 'env'
_LINE = re.compile(r"^\s*(?:export\s+)?([A-Z_][A-Z0-9_]*)\s*=\s*(['\"]?)(.*?)\2\s*$")


def _find_env(depth=6):
    cur = Path(__file__).resolve().parent
    for _ in range(depth):
        cand = cur / ENV_RELPATH
        if cand.exists():
            return cand
        if cur.parent == cur:
            break
        cur = cur.parent
    raise FileNotFoundError(
        f'{ENV_RELPATH} 파일을 찾을 수 없습니다. 작업 폴더 루트에 만들고 '
        f'템플릿(env.example)을 참고해 채우세요.')


def load_env(strict=True):
    env, seen_twice = {}, []
    for line in _find_env().read_text(encoding='utf-8').splitlines():
        m = _LINE.match(line)
        if not m:
            continue
        key = m.group(1)
        if key in env:
            seen_twice.append(key)
        env[key] = m.group(3)
    if strict and seen_twice:
        raise ValueError(
            f'같은 키가 두 번 선언돼 있습니다: {sorted(set(seen_twice))}. '
            f'새 줄을 지우고 기존 줄을 고치세요 — 로더는 마지막 선언을 채택합니다.')
    return env


def need(env, key, hint=''):
    value = env.get(key)
    if not value:
        raise RuntimeError(
            f'{key} 누락. {hint}\n→ 환경 파일을 채우거나 갱신 절차를 실행하세요.')
    return value
```

`strict` 중복 키 검사는 원 기록의 모듈에는 없던 것을 SETUP-09에 맞춰 더한 것이다 `[⚠️ AI추론]`.

## 호출 헬퍼에 넣을 것

- **도메인 판별** — 게이트웨이가 인증 실패 때 "200 OK + 빈 몸통"을 돌려주는 시스템이면 "응답이 비었습니다 → 권한 확인"을, 만료 시 로그인 화면 HTML이 오는 시스템이면 "응답이 HTML → 만료 의심, 갱신 절차 안내"를 헬퍼에 둔다. 한 곳에 있으면 모든 스크립트가 좋은 에러 메시지를 공짜로 얻는다.
- **갱신과 영속화** — 만료를 감지해 갱신하고, 새 값을 파일에 되써 다음 실행이 재발급 없이 동작하게 한다. 파일 쓰기 실패는 경고만 하고 진행한다.
- **예외 메시지에 원문을 싣지 않는다** — 주소·키가 담긴 예외를 그대로 출력하면 자격증명이 성공 경로가 아니라 에러 메시지로 샌다. 예외는 종류만 출력하고, 주소는 스킴과 호스트만 남긴다. (1회 사례 — 규칙 아님)
