# 연결/라우팅 안정성 체크리스트

## DoD
- 24시간 무중단
- 메시지 누락 0건
- 알림 정상

## 관측 항목
- 메시지 수신/처리 로그에 요청ID, 타임스탬프, 송수신 상태 포함
- 재시작/네트워크 지연 상황에서 중복/누락 여부
- 알림 채널로 에러/지연 감지 통지

## 2026-02-16 Incident 반영 (Issue-A 결과)

### 증상
- OpenClaw 2026.2.14 업데이트 직후 RPC probe `unauthorized` 발생
- 에러 시퀀스: `gateway token mismatch` → `device token mismatch`

### 원인(운영 관점)
- 서비스/크리덴셜/세션 레이어 간 토큰 정합 불일치 가능성
- 단순 rotate/reissue 및 identity/devices 재등록만으로 미해결 케이스 확인

### 조치
1. `openclaw doctor --fix` 적용(설정 마이그레이션)
2. token rotate/reissue, identity/devices 정합성 점검 및 재등록
3. 최종 수습: `reset(config+creds+sessions)` + `onboard --install-daemon`

### 재발방지
- 업스트림 레퍼런스 고정: #17185 / #17223 / #17353 / #17153
- 상태 보고 포맷 고정: `IN PROGRESS / <작업ID> / <상태>`
- 병목/장애 포맷: `BLOCKED / <작업ID> / <원인 1줄> / <필요 지원>`
