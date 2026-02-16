# Agent Team Ops Playbook

Last updated: 2026-02-16 (KST)
Owner: seoseo-ai
Scope: OpenClaw-based multi-agent automation (Telegram/Discord + GitHub workflow)

## 0) 목적

이 문서는 “자동화가 돌아가되, 사고 없이, 추적 가능하게” 운영하기 위한 최소/표준 지침이다.

핵심 원칙:
- 재현 가능성: 누가/언제/무엇을 바꿨는지 추적 가능
- 안전성: 자동화는 항상 중단 조건과 승인 경계가 있어야 함
- 단순성: 운영자가 바로 실행 가능한 절차 우선

---

## 1) 운영 최소기준 (MVP 5)

1. 모든 자율 작업은 timeout + max retry + stop condition 필수
2. 메시지/작업 상태 전이 로그(received→preprocessed→routed→sent/failed) 표준화
3. 에이전트 역할 분리(Orchestrator / Worker / Reviewer)
4. 연속 실패 자동격리(2회 이상) + 사람 승인 후 복귀
5. 변경은 브랜치→PR→머지 후 반영 (main 직접 수정 금지)

---

## 2) 역할 모델 (권장)

### 2.1 Orchestrator
- 업무: 작업 분해, 라우팅, 우선순위 조정
- 권한: 최소화 (외부 쓰기 권한 기본 비활성)
- 출력: 작업 계획 + 실행 ID(issue/PR/run id)

### 2.2 Worker
- 업무: 단일 작업 실행(스크립트, 문서 생성, 점검)
- 권한: 작업 범위에 필요한 최소 권한만
- 출력: 결과물 + 증적(log/path/command)

### 2.3 Reviewer
- 업무: 결과 검증, 정책 위반/품질 체크
- 권한: 승인/반려만 가능하게 제한
- 출력: pass/fail + 사유 + 재작업 항목

---

## 3) 실행 상태머신 (표준)

모든 자동화 태스크는 아래 상태를 사용한다.

`queued -> running -> validating -> done`
`queued -> running -> failed -> quarantined`

필수 필드:
- task_id
- source (channel/user/system)
- started_at / ended_at
- retry_count
- failure_reason (실패 시)
- artifacts[] (로그/파일/링크)

권장 규칙:
- `running` 10분 초과 시 `stuck` 경고
- 동일 failure_reason 2회 연속 시 `quarantined`

---

## 4) 실패/격리 정책

### 4.1 자동 재시도
- 네트워크/일시 장애: 최대 2회, 지수 백오프(예: 10s, 30s)
- 인증/권한 오류: 즉시 중단(재시도 금지)

### 4.2 격리(Quarantine) 조건
- 같은 작업군 2회 연속 실패
- 토큰/권한 mismatch 반복 감지
- 출력이 정책 위반(민감정보 노출 가능성)

### 4.3 격리 해제
- 사람 승인 + 원인/재발방지 코멘트가 있어야 해제
- 해제 시점의 실행자는 반드시 실행 로그 링크 첨부

---

## 5) GitHub 운영 규칙 (Issue/PR 중심)

## 5.1 작업 ID 규칙
- 모든 작업은 Issue 번호를 기본 ID로 사용
- 커밋 메시지 접두어 예시:
  - `feat(ops): ... (#123)`
  - `fix(agent): ... (#123)`
  - `docs(brief): ... (#123)`

### 5.2 브랜치 규칙
- `ops/<short-topic>`
- `docs/<short-topic>`
- `fix/<short-topic>`

### 5.3 PR 체크리스트
- [ ] 목적/범위 명확
- [ ] 롤백 방법 기재
- [ ] 영향 범위(채널/서버/권한) 표기
- [ ] 테스트 결과(명령 + 출력 요약) 포함

### 5.4 Fast-lane
- `docs/briefs/**` 경로 문서는 빠른 스캔 후 즉시 머지 가능
- 코드/운영설정 변경은 일반 리뷰 절차 유지

---

## 6) 채널 운영 가이드 (Discord/Telegram)

### 6.1 Discord bot-to-bot
- `allowBots=true` 사용 시 반드시 아래 동시 적용:
  - `groupPolicy=allowlist`
  - 특정 guild/channel만 허용
  - `requireMention=true`
  - 대상 user(bot) allowlist 명시
- 목적: 봇 간 루프/잡담 폭주 방지

### 6.2 Telegram
- 그룹 무응답 이슈는 `groupPolicy=allowlist` + groups 매핑 누락 여부 우선 확인
- DM 전용 운영 시 groups 비우고 allowlist 유지

---

## 7) 보안/비밀정보 규칙

- 토큰/API키는 git tracked 파일 저장 금지 (`.env`, `.secrets` + ignore)
- 로그/리포트에 비밀값 마스킹
- 인증 오류는 재시도보다 “자격 재발급/동기화”를 우선
- 외부 전송 자동화는 기본 OFF, 필요 시 명시적 승인

---

## 8) 점검 루틴 (운영 캘린더)

### 매일 (Daily)
- gateway 상태 확인
- channels probe 확인
- 실패/격리 큐 확인

### 매주 (Weekly)
- doctor/fix 필요 여부 점검
- 백업 파일 생성/복구 테스트 1회
- 오래된 세션/로그 보존정책 준수 여부 확인

### 변경 직후 (Post-change)
- 재시작 후 health/probe
- 핵심 플로우 1회 수동 테스트
- 결과를 Issue/PR 코멘트로 기록

---

## 9) 운영 명령 템플릿

```bash
# 상태 점검
openclaw status
openclaw channels status --probe

# 게이트웨이
openclaw gateway status
openclaw gateway restart

# 진단
openclaw doctor --non-interactive
openclaw doctor --fix

# 보안(권고)
openclaw security audit --deep
```

---

## 10) Incident 템플릿 (복붙용)

```md
### Incident Summary
- Time (KST):
- Impact:
- Trigger:
- Root cause:
- Mitigation:
- Verification:
- Follow-ups:
```

---

## 11) 즉시 실행 액션 아이템

1. 이 문서를 기준으로 GitHub Issue 템플릿(ops/incident) 추가
2. Discord 운영 채널 1곳에만 bot-to-bot 허용 정책 적용
3. 격리 정책(2회 실패 시 quarantine) 자동화 스크립트 연결
4. 주간 점검 결과를 `docs/stability_checklist.md`와 연동
