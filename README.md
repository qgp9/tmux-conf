# 2-Layer tmux Configuration

2-layer tmux 구조로, 외부(base)와 내부(inner) tmux를 중첩 실행하여 prefix 충돌 없이 독립적으로 관리한다.

## 파일 구조

```
~/.config/tmux/
├── common.conf        # 공통 설정 (양쪽 레이어가 source)
├── tmux.conf          # inner layer (prefix: C-a)
├── tmux_base.conf     # outer/base layer (prefix: C-q)
└── tbase              # base tmux 실행 스크립트
```

### 로드 순서

- **Base**: `common.conf` → `tmux_base.conf`
- **Inner**: `common.conf` → `tmux.conf` → TPM (plugins)

## 사용법

```bash
# base tmux 실행
tbase

# base 안에서 inner tmux 실행
tmux
```

## 레이어 구분

| | Inner (WING) | Base (BASE) |
|---|---|---|
| **Prefix** | `C-a` | `C-q` |
| **Status bar 배경** | 검은색 | 회색 (colour235) |
| **태그** | `WING` (노란 볼드) | `BASE` (빨간 볼드) |
| **플러그인** | TPM, sensible, tilish | 없음 |

Status bar 예시:

```
Inner: [WING ▸ myhost:session][M:On ] 0:zsh 1:vim*    [~/wrkp][title][2026-03-01 14:30]
Base:  [BASE ▸ myhost:session][M:Off] 0:zsh*           [~/wrkp][title][2026-03-01 14:30]
```

## 키바인딩

### 공통 (common.conf)

| 키 | 동작 |
|---|---|
| `prefix + m` | 마우스 모드 토글 |
| `prefix + h/j/k/l` | vim 스타일 pane 이동 |
| `prefix + s` | synchronize-panes 토글 |
| `prefix + Space` | 다음 윈도우 |
| `prefix + BSpace` | 이전 윈도우 |
| `prefix + "` | 수평 분할 (현재 경로) |
| `prefix + %` | 수직 분할 (현재 경로) |
| `prefix + c` | 새 윈도우 (현재 경로) |
| `prefix + A` | 윈도우 이름 변경 |
| `prefix + r` | 설정 리로드 |

### VI Copy-mode

| 키 | 동작 |
|---|---|
| `prefix + [` | copy-mode 진입 |
| `v` | 비주얼 선택 시작 |
| `y` | 선택 영역 복사 |
| `yy` | 현재 줄 전체 복사 |

복사된 텍스트는 OSC52를 통해 시스템 클립보드로 자동 전달된다 (SSH 원격 환경 포함).

### Inner 전용

| 키 | 동작 |
|---|---|
| `Alt + Enter` | 우하단 pane에서 분할 |
| `Alt + 숫자` | 워크스페이스 전환 (tilish) |

### Base 전용

| 키 | 동작 |
|---|---|
| `prefix + Tab` | 마지막 윈도우 전환 |

## 주요 설정

- **터미널**: `tmux-256color`
- **escape-time**: `0` (2-layer 지연 최소화)
- **focus-events**: `on` (inner에 포커스 이벤트 전달)
- **클립보드**: OSC52 (`~/.local/bin/copy-osc52.sh` 필요)
- **경로 표시**: `$HOME` → `~` 치환 (tmux 내장 포맷, 3.1+)

## 요구사항

- tmux 3.1+
- `~/.local/bin/copy-osc52.sh` (클립보드 연동)
- [TPM](https://github.com/tmux-plugins/tpm) (inner 플러그인 관리)
