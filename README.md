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

# base 안에서 inner(wing) tmux 실행
# TMUX 변수가 설정되어 있으면 tmux가 중첩 실행을 거부하므로 해제 필요
unset TMUX
tmux
```

> **Note**: 원격지에서 wing을 실행하거나, base 없이 단독 실행하는 경우에는
> `TMUX` 변수가 없으므로 `unset TMUX` 없이 바로 `tmux`로 실행하면 된다.

## 레이어 구분

| | Inner (WING) | Base (BASE) |
|---|---|---|
| **Prefix** | `C-a` | `C-q` |
| **Status bar 배경** | 검은색 | 회색 (colour235) |
| **태그** | `WING` (노란 볼드) | `BASE` (빨간 볼드) |
| **플러그인** | TPM, sensible, tilish | 없음 |

Status bar 예시:

```
Inner: [WING ▸ myhost:session][M:On ]      0:zsh 1:vim*    [~/wrkp][title][2026-03-01 14:30]
Base:  [BASE ▸ myhost:session][M:Off]      0:zsh*           [~/wrkp][title][2026-03-01 14:30]
Sync:  [WING ▸ myhost:session][M:On ] SYNC 0:zsh 1:vim*    [~/wrkp][title][2026-03-01 14:30]
```

## 키바인딩

### Prefix

| 레이어 | Prefix | 최근 윈도우 | prefix 패스스루 |
|---|---|---|---|
| Inner (WING) | `C-a` | `C-a C-a` | `C-a a` |
| Base (BASE) | `C-q` | `C-q C-q` | `C-q q` |

### 공통 (common.conf)

| 키 | 동작 |
|---|---|
| `prefix + m` | 마우스 모드 토글 |
| `prefix + h/j/k/l` | vim 스타일 pane 이동 |
| `prefix + H/J/K/L` | vim 스타일 pane 리사이즈 (반복 가능) |
| `prefix + s` | synchronize-panes 토글 (SYNC 표시) |
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
| `Escape` | copy-mode 종료 |

복사된 텍스트는 OSC52를 통해 시스템 클립보드로 자동 전달된다 (SSH 원격 환경 포함).

### Inner 전용

| 키 | 동작 |
|---|---|
| `Alt + Enter` | 우하단 pane에서 분할 |
| `Alt + 숫자` | 워크스페이스 전환 (tilish) |

## 주요 설정

- **터미널**: `tmux-256color`
- **escape-time**: `0` (2-layer 지연 최소화)
- **focus-events**: `on` (inner에 포커스 이벤트 전달)
- **클립보드**: OSC52 (`~/.config/tmux/copy-osc52.sh` 필요)
- **경로 표시**: `$HOME` → `~` 치환 (tmux 내장 포맷, 3.1+)

## 설치

### TPM (Tmux Plugin Manager)

inner(WING)에서 플러그인(sensible, tilish)을 사용하기 위해 TPM이 필요하다.

```bash
git clone https://github.com/tmux-plugins/tpm ~/.config/tmux/plugins/tpm
```

설치 후 inner tmux에서 `prefix + I`를 눌러 플러그인을 설치한다.

### 플러그인

| 플러그인 | 설명 |
|---|---|
| [tmux-sensible](https://github.com/tmux-plugins/tmux-sensible) | 대부분의 사용자에게 적합한 기본 설정 모음 (utf8, status-keys 등) |
| [tmux-tilish](https://github.com/jabirali/tmux-tilish) | i3wm 스타일 윈도우 관리. `Alt+숫자`로 워크스페이스 전환, `Alt+Enter`로 새 pane 생성 |

## 요구사항

- tmux 3.1+
- `~/.config/tmux/copy-osc52.sh` (클립보드 연동)
- [TPM](https://github.com/tmux-plugins/tpm) (inner 플러그인 관리, 위 설치 참고)
