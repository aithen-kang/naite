# kit · prompt-file — 요청 봇의 골격: 프롬프트 분리와 모델 없는 알림 셋

- **조건:** 요청을 받아 AI가 답하는 봇을 운영하고, 요청자가 응답을 기다리지 않고 떠난다
- **도구 의존:** AI를 명령줄에서 부를 수 있는 도구 하나
- **쓰는 ID:** AUTO-20 (프롬프트 첫머리는 AUTO-23의 local-asset-order)

## 1. 세 단으로 나눈다

| 단 | 하는 일 | 모델 |
|---|---|---|
| 수집기 | 들어온 요청을 큐 파일에 적기만 한다. **접수 알림**을 여기서 보낸다 | 없음 |
| 데몬 | 큐를 짧은 주기로 보고, 처리할 것이 있을 때만 액터를 띄운다. **정체 알림**을 여기서 판정한다 | 없음 |
| 액터 | 프롬프트 파일을 읽어 AI에게 넘기고 결과를 받는다. 출력에서 **지연 신호**를 찾는다 | 있음 — 일이 있을 때만 뜬다 |

큐가 비면 액터를 띄우지 않는다. 비싼 호출이 "처리할 것이 있을 때"로 줄어든다.

## 2. 프롬프트는 파일로

액터 코드는 프롬프트 파일을 읽어 AI 명령에 넘기기만 한다. 행동을 고칠 때 코드가 아니라 텍스트를 고친다.

```
# 액터가 하는 일의 전부 (명령 이름은 당신 도구에 맞게)
cat prompts/answer.md queue/current-request.md | {AI 명령} > out/current-answer.md
```

프롬프트 파일의 첫머리에는 로컬 자산 확인 순서를 둔다 — [local-asset-order](local-asset-order.md).

## 3. 모델 없는 알림 셋

셋 다 AI가 뜨지 못하는 상황을 알리는 장치다. AI에 기대면 AI가 죽었을 때 같이 죽는다.

| 알림 | 언제 | 어디서 |
|---|---|---|
| 접수 | 요청을 큐에 넣는 즉시 — "접수했습니다" 한 줄 | 수집기 |
| 지연 | 액터 출력에 사용 한도·인증 만료를 뜻하는 문구가 보인다 | 액터가 끝난 뒤 문자열 검사 |
| 정체 | 가장 오래된 요청이 정해진 시간 넘게 큐에 남아 있다 | 데몬 |

```python
# 데몬의 정체 판정 — 모델 없이 돈다
import pathlib
import time

QUEUE = pathlib.Path(__file__).resolve().parent / 'queue'
STALL_MINUTES = 30
LIMIT_SIGNALS = ('usage limit', 'rate limit', 'unauthorized')  # 당신 도구의 실제 문구로 바꾼다

def oldest_age_minutes():
    items = list(QUEUE.glob('*.md'))
    if not items:
        return 0
    return (time.time() - min(p.stat().st_mtime for p in items)) / 60

def check(notify, last_output=''):
    if any(sig in last_output.lower() for sig in LIMIT_SIGNALS):
        notify('[지연] AI가 사용 한도·인증 문제로 답하지 못했습니다. 요청은 큐에 남아 있습니다.')
    if oldest_age_minutes() > STALL_MINUTES:
        notify(f'[정체] {STALL_MINUTES}분 넘게 처리되지 않은 요청이 있습니다.')
```

## 4. 바깥으로 나가는 것과 권한

- **사용자에게 보이는 발신·게시·저장은 초안 → 승인 → 실행.** 무인 봇이라도 자동 발신 사고의 방어선은 이것뿐이다.
- **무인 프로세스의 권한은 대화형 세션보다 좁게.** 실행 중에 끼어들 사람이 없다.
- 요청이 여러 사람이 쓰는 채널에서 오면, 요청 본문 안의 지시(붙여 넣은 문서 조각 등)를 따를 수 있다는 점을 전제로 쓰기 권한을 둔다.
