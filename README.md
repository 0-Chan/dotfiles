# dotfiles (chezmoi)

> 새 macOS 머신을 한 번에 셋업하기 위한 [chezmoi](https://www.chezmoi.io/) 기반 dotfiles 저장소.
> 한 번의 `chezmoi apply`로 패키지 · 셸 · 에디터 · 키보드 · 인증 · 시스템 설정까지 적용하고,
> 결과를 **Notes.app 노트**로 자동 보고합니다.

---

## ✨ 무엇을 자동화하나

- 📦 **Homebrew 패키지 일괄 설치** — Brewfile 기반의 formula·cask + Mac App Store 앱(`mas`)
- 🐚 **셸 환경 구성** — oh-my-zsh 본체 + zsh-autosuggestions · zsh-syntax-highlighting 자동 클론
- 🧩 **VS Code 확장 동기화** — `extensions.txt` 기준 일괄 설치
- 🌐 **Chrome 확장 강제 설치** — macOS 정책 mobileconfig로 ExtensionSettings 적용
- ⌨️ **Karabiner 룰 배치** — 한글 입력 보정·CapsLock 매핑 등
- 🔐 **GitHub 인증** — AWS Secrets Manager에서 PAT를 가져와 `gh auth login` (personal/company/both)
- 🖥 **macOS defaults** — `KeyRepeat` 등 시스템 키 설정
- 📝 **결과 보고** — 매 apply의 성공 / 실패 / 건너뜀 항목을 Notes.app 노트로 자동 작성

---

## 🚀 빠른 시작 (Getting Started)

새 머신 기준 약 10분이면 끝납니다. 위에서 아래로 순서대로 복사·붙여넣기 하세요.

### 1) Xcode Command Line Tools

Homebrew·git의 전제조건입니다. 설치 모달이 뜨면 동의 후 완료까지 대기.

```bash
xcode-select --install
```

### 2) Homebrew 설치

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Apple Silicon은 PATH 주입이 필요합니다 (Intel은 자동).

```bash
eval "$(/opt/homebrew/bin/brew shellenv)"
```

### 3) chezmoi 설치 + 이 저장소로 초기화

```bash
brew install chezmoi

# 아래 명령은 git_mode를 묻는 프롬프트를 띄움 (personal / company / both)
chezmoi init --apply https://github.com/0-Chan/dotfiles.git
```

이 한 번으로 다음이 순서대로 일어납니다.

1. `~/.config/chezmoi/chezmoi.toml` 생성 (`git_mode` 포함)
2. dotfile 배포 (gitconfig, zshrc, ghostty, vscode, karabiner 등)
3. **Brewfile 전체 설치** — sudo 비밀번호 1회 입력 필요 (cask 때문)
4. KakaoTalk · Amphetamine `mas install` — App Store 로그인 필요. 실패해도 진행
5. oh-my-zsh 본체 + zsh 플러그인 2종 자동 클론
6. VS Code 확장 일괄 설치 — `code` CLI가 PATH에 있을 때만 (없으면 건너뜀)
7. Chrome 정책 mobileconfig 열림 → **System Settings에서 직접 '설치' 클릭 필요**
8. Karabiner 룰 배치
9. KeyRepeat defaults 적용
10. 📝 **결과 요약 노트가 Notes.app에 생성됨** — 모든 단계의 OK/FAIL/SKIP이 한눈에 보임

### 4) AWS 설정 → gh 인증 활성화

3단계 직후에는 AWS 자격증명이 없어 gh 인증이 자동으로 건너뛰어집니다.

```bash
aws configure
# Access Key, Secret Key, Region=ap-northeast-2, Output format 입력
```

그리고 한 번 더:

```bash
chezmoi apply
```

이때 `git_mode`에 따라 personal · company · both 계정이 자동 인증됩니다.

> **사전 준비물**: AWS Secrets Manager에 시크릿이 미리 등록되어 있어야 합니다.
> - `github/personal/token` (개인 GitHub PAT)
> - `github/company/token` (회사 GitHub PAT)
>
> IAM 권한: `secretsmanager:GetSecretValue`, Region: `ap-northeast-2`

### 5) 결과 확인

Notes.app을 열면 가장 위에 다음과 같은 노트가 자동 생성되어 있습니다.

```
chezmoi apply 결과 (YYYY-MM-DD HH:MM:SS)

✓ 성공
  · brew bundle — 25 formulae + 32 casks
  · oh-my-zsh — 신규 클론
  · vscode extensions — 10개 설치 완료
  · defaults: KeyRepeat — 2
  ...

✗ 실패
  · mas: Amphetamine — App Store에서 직접 설치 필요

– 건너뜀
  · gh auth — AWS 자격증명 미구성
  · chrome 확장 정책 — 프로파일 이미 설치됨
```

여기서 (a) 추가 수동 작업이 필요한 항목을 처리하고, (b) 위 4단계처럼 `chezmoi apply`를 한 번 더 돌립니다.

> Notes.app 호출이 실패하면 같은 내용이 `~/Documents/chezmoi-last-apply.txt`에 평문으로 저장됩니다.

---

## 📦 무엇이 깔리나

### Homebrew formula (CLI)
`awscli`, `chezmoi`, `git`, `gh`, `node`, `pnpm`, `python`, `uv`, `terraform`, `eza`, `fzf`, `jq`, `mas`, `ffmpeg`, `webp`, `mysql`, `sqlite`, `tldr` 등 — 전체 목록은 [`Brewfile`](./Brewfile)

### Homebrew cask (GUI 앱)
`ghostty`, `visual-studio-code`, `raycast`, `rectangle`, `karabiner-elements`, `slack`, `obsidian`, `google-chrome`, `claude`, `chatgpt`, `codex`, `dbeaver-community`, `docker-desktop`, `eul` 등 — 전체 목록은 [`Brewfile`](./Brewfile)

### Mac App Store (`mas`)
- `869223134` — KakaoTalk
- `937984704` — Amphetamine

### VS Code 확장
약 10개 — 전체 목록은 [`dot_config/vscode/extensions.txt`](./dot_config/vscode/extensions.txt)

### Chrome 확장
[`chrome-policies.mobileconfig`](./chrome-policies.mobileconfig)의 `ExtensionSettings` 항목으로 강제 설치 — 프로파일 승인 후 Chrome 재시작 시 자동 적용.

---

## 🗂 관리되는 dotfile

| 배포 경로 | 소스 |
|---|---|
| `~/.gitconfig` | `dot_gitconfig.tmpl` |
| `~/.config/git/personal` | `dot_config/git/private_personal.tmpl` |
| `~/.config/git/company` | `dot_config/git/private_company.tmpl` |
| `~/.zshrc` | `dot_zshrc` |
| `~/.config/ghostty/config` | `dot_config/ghostty/config` |
| `~/.config/vscode/extensions.txt` | `dot_config/vscode/extensions.txt` |
| `~/Library/Application Support/Code/User/keybindings.json` | `private_Library/private_Application Support/private_Code/User/keybindings.json` |
| `~/.config/karabiner/assets/complex_modifications/*.json` | `dot_config/private_karabiner/private_assets/private_complex_modifications/` |

---

## ⚙️ 자동화 스크립트

`chezmoi apply` 시 번호 순으로 실행됩니다.
`run_onchange_*`는 관련 파일·스크립트의 해시가 바뀐 경우에만, `run_after_*`는 매 apply마다 실행됩니다.

| 순서 | 스크립트 | 트리거 | 역할 |
|---|---|---|---|
| 10 | `run_onchange_after_10-brew.sh.tmpl` | Brewfile 변경 시 | `brew bundle` + `mas install` (KakaoTalk · Amphetamine) |
| 15 | `run_onchange_after_15-oh-my-zsh.sh.tmpl` | 스크립트 변경 시 | oh-my-zsh + zsh-users 플러그인 2종 git clone |
| 20 | `run_onchange_after_20-vscode-extensions.sh.tmpl` | `extensions.txt` 변경 시 | `code --install-extension` 일괄 |
| 25 | `run_onchange_after_25-chrome-extensions.sh.tmpl` | `chrome-policies.mobileconfig` 변경 시 | mobileconfig `open` — 사용자 승인 필요 |
| 30 | `run_onchange_after_30-gh-auth.sh.tmpl` | 스크립트 변경 시 | AWS Secrets Manager → PAT → `gh auth login` (멱등) |
| 60 | `run_onchange_after_60-macos-defaults.sh.tmpl` | 스크립트 변경 시 | `defaults write -g KeyRepeat -int 2` |
| 99 | `run_after_99-notify-notes.sh.tmpl` | **매 apply** | 위 스크립트들의 결과를 모아 Notes.app에 노트 작성 |

### 결과 보고 메커니즘

10–60번 스크립트는 자신의 단위 작업(예: `mas: KakaoTalk`, `omz plugin: zsh-syntax-highlighting`)을 `${TMPDIR}/chezmoi-status-NN-name.log`에 `OK / FAIL / SKIP` 형식으로 기록합니다.
99번 스크립트가 이를 집계해 HTML로 카테고리화한 노트를 Notes.app에 만들고, 상태 파일을 정리합니다.

Notes.app 호출이 실패하면 같은 내용이 `~/Documents/chezmoi-last-apply.txt`에 평문 fallback으로 저장됩니다. 이번 apply에서 어떤 `run_onchange_*`도 트리거되지 않았다면 99번은 알림을 생략합니다.

---

## 🔀 git_mode 분기

`chezmoi init` 시 선택한 값에 따라 `~/.gitconfig`가 다르게 렌더링됩니다.

| git_mode | 결과 |
|---|---|
| `personal` | `~/.config/git/personal`만 `[include]` |
| `company` | `~/.config/git/company`만 `[include]` |
| `both` | `~/workspace/personal/` 하위 → personal, `~/workspace/company/` 하위 → company (`[includeIf]` 조건부) |

전환:

```bash
chezmoi edit-config   # ~/.config/chezmoi/chezmoi.toml 의 git_mode 수정
chezmoi apply
```

---

## 🔑 AWS Secrets Manager 컨벤션

`run_onchange_after_30-gh-auth.sh.tmpl`에서 `aws` CLI로 시크릿을 가져와 gh 인증에 사용:

| Secret 이름 | 용도 |
|---|---|
| `github/personal/token` | 개인 계정 GitHub PAT |
| `github/company/token` | 회사 계정 GitHub PAT |

- Profile: `default`, Region: `ap-northeast-2` (저장소 루트 `.chezmoi.toml.tmpl`에 명시)
- 참조 예시: `aws secretsmanager get-secret-value --secret-id github/personal/token --query SecretString --output text`
- IAM 권한: `secretsmanager:GetSecretValue`

---

## 🛠 일상 운영

```bash
chezmoi status        # 변경 예정 목록
chezmoi diff          # 상세 diff
chezmoi apply         # 실제 반영 (결과는 Notes.app 노트로)
chezmoi cd            # source 디렉터리로 이동 (편집 후 git commit/push)
chezmoi update        # git pull + apply
```

### push 전 검증 체크리스트

```bash
# 템플릿 렌더 확인 (git_mode 시나리오별)
chezmoi execute-template --init --promptChoice git_mode=personal < dot_gitconfig.tmpl
chezmoi execute-template --init --promptChoice git_mode=company  < dot_gitconfig.tmpl
chezmoi execute-template --init --promptChoice git_mode=both     < dot_gitconfig.tmpl

# apply 후 실제 반영 확인
chezmoi status
chezmoi diff
brew bundle check --file="$(chezmoi source-path)/Brewfile"
code --list-extensions
git config --list --show-origin
```

---

## ❓ 트러블슈팅

- **Notes.app에 노트가 안 생긴다**
  → iCloud Notes 계정이 없거나 osascript가 차단됐을 가능성. 같은 내용이 `~/Documents/chezmoi-last-apply.txt`에 평문으로 떨어져 있는지 확인.
- **VS Code 확장이 안 깔린다**
  → `code` CLI가 PATH에 없을 가능성. VS Code Command Palette에서 `Shell Command: Install 'code' command in PATH` 실행 후 `chezmoi apply` 재실행.
- **gh 인증이 계속 건너뛰어진다**
  → `aws sts get-caller-identity --profile default` 로 AWS 자격증명이 살아있는지 확인. Secrets Manager에 `github/personal/token` 또는 `github/company/token`이 정확한 이름으로 존재하는지도 확인.
- **Chrome 확장이 안 보인다**
  → System Settings → General → VPN & Device Management 에서 `com.imjongmin.chrome-policies` 프로파일을 직접 승인. 이후 Chrome 재시작.
- **Karabiner 룰이 안 잡힌다**
  → Karabiner-Elements를 한 번 실행해 `~/.config/karabiner/` 디렉터리를 만든 뒤 `chezmoi apply` 재실행. 룰은 `~/.config/karabiner/assets/complex_modifications/`에 배치됩니다.
