# logs/

## polymarket_trades.csv 규칙
- 한 행 = 한 트레이드(진입~청산 완료) 기록
- ts는 KST 기준 ISO-8601로 기록(예: 2026-02-14T15:04:05+09:00)
- 커밋 주기: 하루 1회(배치 커밋)
- 필드:
  - ts: ISO-8601 timestamp (KST 또는 UTC 명시)
  - event: 마켓/이벤트 식별자(제목 또는 URL)
  - side: YES/NO (또는 LONG/SHORT로 통일)
  - price: 진입 평균가
  - shares: 수량
  - prob: 진입 시점 확률(있으면)
  - entry_reason / exit_reason: 근거 1~2문장
  - stop_rule: 적용한 손절/중단 규칙(예: per-trade $20 cap, daily $10 loss cap)
  - pnl: 손익(달러)
  - fees: 수수료(달러)
  - notes: 메모
  - source_link: 참고 링크

## 커밋 규칙(권장)
- 하루 1회 배치 커밋 또는 트레이드 단위 커밋
- 커밋 메시지 예: `logs: add polymarket trade 2026-02-14`
