# kit · exit-log — 무인 스크립트의 뼈대: 종료 코드 · 로그 · 사전 체크

- **조건:** 사람 없이 도는 스크립트를 만들고, 실패를 나중에 로그와 종료 코드로 가려야 한다
- **도구 의존:** 없음 (예시는 Python 표준 라이브러리)
- **쓰는 ID:** AUTO-09 · AUTO-16

## 1. 뼈대

```python
import datetime
import pathlib
import sys
import urllib.error

BASE = pathlib.Path(__file__).resolve().parent      # 실행 위치가 아니라 파일 위치 기준
LOG_PATH = BASE / 'logs' / 'job.log'

# 인터프리터가 쓰는 1(처리하지 않은 예외)·2(인자 오류)와 겹치지 않게 둔다
EXIT_OK, EXIT_BAD_RESPONSE, EXIT_HTTP, EXIT_OTHER = 0, 3, 4, 5


def _log(msg):
    line = f'{datetime.datetime.now():%Y-%m-%d %H:%M:%S} {msg}'
    print(line)
    try:
        LOG_PATH.parent.mkdir(parents=True, exist_ok=True)
        with open(LOG_PATH, 'a', encoding='utf-8') as f:
            f.write(line + '\n')
    except Exception:
        pass  # 로그 실패가 본작업을 막지 않게


def has_new_work():
    """토큰을 쓰지 않는 사전 체크 — 처리할 새 데이터가 있나. 당신 환경에 맞게 채운다."""
    raise NotImplementedError


def make_client(timeout):
    """인증·클라이언트 생성. 당신 환경의 인증 모듈을 부른다."""
    raise NotImplementedError


def main():
    _log('START')
    if not has_new_work():
        _log('OK SKIP 처리할 새 데이터 없음')
        return EXIT_OK

    client = make_client(timeout=10)   # 인증은 여기서 — 모듈 최상위에 두지 않는다
    status = client.send()
    if 200 <= status < 300:
        _log('OK 전송 성공')
        return EXIT_OK
    _log(f'WARN 비정상 응답 status={status}')
    return EXIT_BAD_RESPONSE


if __name__ == '__main__':
    try:
        sys.exit(main())
    except urllib.error.HTTPError as e:
        _log(f'FAIL HTTPError {e.code}')
        sys.exit(EXIT_HTTP)
    except Exception as e:
        _log(f'FAIL {type(e).__name__}')   # 예외 본문은 적지 않는다 — 주소·키가 섞일 수 있다
        sys.exit(EXIT_OTHER)
```

## 2. 왜 이 모양인가

| 자리 | 이유 |
|---|---|
| 인증·클라이언트 생성이 `main()` 안 | 모듈 최상위에서 만들면, 네트워크가 준비되지 않은 시점에 로그 한 줄 없이 시작 단계에서 멈춘다 |
| 타임아웃 명시 | 없으면 갱신 호출이 작업 실행 시간 제한까지 매달려 강제 종료된다 |
| 실패 종류별 종료 코드 | 스케줄러 기록만 보고도 원인을 좁힌다. 인터프리터 번호와 겹치면 "설계대로 멈춤"과 "크래시"를 가를 수 없다 |
| `START` → `OK`/`WARN`/`FAIL` 로그 | "오늘 안 왔는데?"의 첫 확인처가 이 로그다. `OK` 줄은 health-check의 오늘자 성공 표식으로 쓴다 |
| 로그 실패를 삼킴 | 로그 파일이 잠겨 있거나 디스크가 차도 본작업은 진행돼야 한다 |
| 사전 체크 후 `OK SKIP` | 새 데이터가 없으면 모델·외부 호출을 아예 하지 않는다. 건너뛴 것도 성공 표식으로 남긴다 |
| 예외는 타입만 로그 | 예외 메시지가 요청 주소나 키를 품는 경우가 있다 |

## 3. 알아 둘 것

- **종료 코드만 보지 말고 기대한 로그 줄이 나왔는지도 함께 본다.** 파손 테스트에서 기대한 실패 코드가 나왔는데, 설계대로 멈춘 것이 아니라 다른 곳의 크래시였던 사례가 한 번 있다. (1회 사례 — 규칙 아님)
- 재시도가 필요하면 이 스크립트 안에 구현한다. 스케줄러의 재시도 옵션이 스크립트의 실패 코드에 발동한다고 전제하지 않는다.
- 반복 실행에 판단이 필요 없다면 모델을 끼우지 않는다. 실행마다 달라지는 것이 데이터뿐이면 이 뼈대만으로 충분하다.
