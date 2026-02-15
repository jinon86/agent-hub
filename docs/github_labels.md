# GitHub Labels (운영 표준)

## 네이밍 규칙(prefix 고정)
- `status/*` : 상태(칸반/운영 상태)
- `needs/*`  : 행동요청(정보/결정/트리아지 필요)
- `priority-*` : 우선순위
- `track-*` : 트랙(안정성/폴리마켓/자동화/CI)
- `owner-*` : 담당자

> prefix를 고정하면 GitHub 라벨 리스트가 자동 정렬되어 운영이 쉬워집니다.

## 상태 라벨(강대비 색상 고정)
- `status/triage`  `fbca04`  (yellow)
- `status/doing`   `1d76db`  (blue)
- `status/blocked` `d73a4a`  (red)
- `status/hold`    `6a737d`  (gray)
- `status/done`    `0e8a16`  (green)

## needs 라벨(동일 톤)
- `needs/info`    `ff8c00`  (orange)
- `needs/triage`  `ff8c00`  (orange)

## 우선순위
- `priority-1` `d73a4a`
- `priority-2` `fbca04`
- `priority-3` `0e8a16`

## 트랙
- `track-stability`  `5319e7`
- `track-polymarket` `1d76db`
- `track-automation` `0052cc`
- `track-ci`         `c2e0c6`

## 담당자(owner)
- `owner-bangtong-ai`  `bfdadc`
- `owner-seoseo`       `fef2c0`
- `owner-sogyo`        `f9d0c4`
- `owner-sogyo-bot`    `6a737d`

## (기존 호환)
- 기존에 사용 중인 `needs-info`, `needs-triage` 라벨은 추후 `needs/info`, `needs/triage`로 통일(마이그레이션)합니다.
