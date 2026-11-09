# macOS 환경 설정 상세 가이드

[저장소 소개로 돌아가기](../README.md)

새 Mac에 환경을 구성하고, 설정을 관리하거나 문제를 해결할 때 참고하는 가이드입니다.
저장소의 목적과 주요 기능은 [루트 README](../README.md)에서 확인할 수 있습니다.

## 목차

- [사용 전 확인](#before-you-start)
- [새 Mac에 설정하기](#setup)
- [적용 후 확인](#after-setup)
- [Git 계정과 선택적 인증](#git-and-auth)
- [설치 앱과 관리 설정](#apps-and-settings)
- [자동화 동작](#automation)
- [일상 관리](#maintenance)
- [문제 해결](#troubleshooting)

<a id="before-you-start"></a>

## 사용 전 확인

이 저장소는 개인용 macOS 개발 환경을 재현하기 위한 설정 모음입니다.
적용하면 앱을 설치하고 홈 디렉터리의 셸·Git·에디터 설정 등을 변경합니다.
다른 사람이 사용한다면 [개인 Git 설정](../dot_config/git/private_personal.tmpl)과
[회사 Git 설정](../dot_config/git/private_company.tmpl)의 이름·이메일,
[Brewfile](../Brewfile)의 앱 목록부터 자신의 환경에 맞게 바꾸세요.

현재 Git 인증 도우미 경로는 `/opt/homebrew/bin/gh`로 지정되어 있어 Apple Silicon의
기본 Homebrew 설치 경로를 가정합니다. Intel Mac이나 다른 설치 경로에서는
[Git 설정 템플릿](../dot_gitconfig.tmpl)의 경로도 적용 전에 조정해야 합니다.

설치 시간과 비밀번호 입력 횟수는 다운로드 속도, 이미 설치된 앱, macOS 승인 상태에 따라 달라집니다.
앱 설치 승인, App Store 로그인, Chrome 프로파일 승인은 사용자가 직접 진행합니다.

<a id="setup"></a>

## 새 Mac에 설정하기

### 1. Xcode Command Line Tools 설치

Homebrew와 Git에 필요한 기본 개발 도구를 설치합니다.
설치 창이 열리면 안내에 따라 완료한 뒤 다음 단계로 진행하세요.

```bash
xcode-select --install
```

### 2. Homebrew 설치

[Homebrew 공식 설치 안내](https://brew.sh/)에 따라 설치합니다.

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

설치 마지막에 표시되는 PATH 설정 안내도 적용하세요. Apple Silicon의 기본 설치 경로라면
현재 터미널에서는 다음 명령으로 Homebrew를 사용할 수 있습니다.
새 터미널에도 적용되도록 설치 안내의 셸 설정을 함께 진행합니다.

```bash
eval "$(/opt/homebrew/bin/brew shellenv)"
```

### 3. chezmoi 설치

chezmoi는 저장소의 설정 파일을 내 Mac에 적용하는 도구입니다.

```bash
brew install chezmoi
```

**GitHub 자동 인증이 필요한 경우에만**, 적용 전에 [AWS 인증 준비](#aws-auth)를 진행하세요.
AWS를 사용하지 않아도 나머지 설정은 적용할 수 있으며, GitHub 인증 단계는 건너뜁니다.

### 4. 저장소 초기화

저장소를 내려받고, 이 Mac에서 사용할 Git 모드를 선택합니다.

```bash
chezmoi init https://github.com/0-Chan/dotfiles.git
```

질문이 나오면 `personal`, `company`, `both` 중 하나를 선택합니다.
자세한 경로 조건은 [Git 작성자 설정](#git-mode)에서 확인하세요.
이 단계에서는 로컬 chezmoi 설정을 만들며, 앱 설치와 홈 디렉터리 설정 적용은 다음 단계에서 진행합니다.

기본 저장소 위치는 `~/.local/share/chezmoi`, 이 Mac의 설정 파일은
`~/.config/chezmoi/chezmoi.toml`입니다. 개인 설정을 수정할 때는 내려받은 저장소에서 편집합니다.

### 5. 변경 확인 후 적용

먼저 설정 파일의 변경 내용과 실행 예정 스크립트를 확인합니다.

```bash
chezmoi diff
```

확인이 끝나면 적용합니다.

```bash
chezmoi apply
```

설정 파일을 배치한 뒤, 실행 대상인 자동화 단계가 번호 순으로 진행됩니다.
Homebrew 설치가 실패하면 적용이 중단될 수 있으므로 터미널 출력도 확인하세요.

<a id="after-setup"></a>

## 적용 후 확인

| 항목 | 직접 확인할 내용 |
|---|---|
| 앱 설치 | 설치 프로그램이 요청하는 비밀번호와 권한 승인 |
| Mac App Store | 로그인 여부와 KakaoTalk·Amphetamine 설치 결과 |
| VS Code | 앱 첫 실행 승인과 `code` 명령 사용 가능 여부 |
| Chrome | 시스템 설정 → 일반 → VPN 및 기기 관리에서 프로파일 설치 승인 후 Chrome 재시작 |
| Karabiner | 앱을 실행하고 Complex Modifications에서 사용할 규칙 추가 |
| Git | 작업할 저장소에서 작성자 이름·이메일과 GitHub 활성 계정 확인 |
| 키 반복 | `KeyRepeat=2`가 적용되며 재로그인 후 반영 여부 확인 |

마지막 보고 단계까지 도달하면 메모 앱(Notes.app)에
`chezmoi apply 결과 (날짜와 시간)` 제목으로 결과 노트를 만듭니다.
실행된 작업의 성공·실패·건너뜀 항목과 현재 설치된 패키지 목록을 확인할 수 있습니다.

메모 앱 호출이 실패하면 `~/Documents/chezmoi-last-apply.txt`에 같은 내용을 텍스트로 저장하도록 시도합니다.
앞 단계에서 적용이 중단되면 보고 단계에 도달하지 못할 수 있습니다.
결과가 없다면 [문제 해결](#troubleshooting)을 확인하세요.

<a id="git-and-auth"></a>

## Git 계정과 선택적 인증

<a id="git-mode"></a>

### Git 작성자 설정

`git_mode`는 커밋에 기록할 이름·이메일을 선택합니다.

| 모드 | 적용되는 작성자 설정 |
|---|---|
| `personal` | 개인 설정만 포함 |
| `company` | 회사 설정만 포함 |
| `both` | `~/workspace/personal/` 아래는 개인, `~/workspace/company/` 아래는 회사 설정을 조건부로 포함 |

`both` 모드에서 위 두 경로 밖의 저장소에는 이 템플릿이 작성자 설정을 지정하지 않습니다.
다른 Git 설정이 없다면 커밋 전에 작성자 이름·이메일을 별도로 정해야 합니다.

모드를 바꾸려면 로컬 chezmoi 설정의 `git_mode`를 수정한 뒤 확인하고 적용합니다.

```bash
chezmoi edit-config
chezmoi diff
chezmoi apply
```

<a id="github-cli"></a>

### GitHub CLI 계정

Git 작성자 설정과 GitHub CLI(`gh`)의 로그인·활성 계정은 별개입니다.
폴더별 작성자 설정이 바뀌어도 GitHub CLI 활성 계정이 자동으로 전환되지는 않습니다.

현재 자동 인증 스크립트는 로그인된 계정 수로 완료 여부를 판단합니다.
`personal`·`company`는 1개 이상, `both`는 2개 이상이면 인증을 건너뜁니다.
예를 들어 회사 모드에서 개인 계정만 로그인되어 있어도 건너뛸 수 있으므로 계정 이름도 직접 확인하세요.

```bash
gh auth status --hostname github.com
```

AWS 자동 인증을 쓰지 않는 경우에는 직접 로그인할 수 있습니다.
여러 계정이 등록되어 있다면 `YOUR_GITHUB_LOGIN`을 사용할 계정 이름으로 바꿔 전환합니다.

```bash
gh auth login --hostname github.com
gh auth switch --hostname github.com --user YOUR_GITHUB_LOGIN
```

<a id="aws-auth"></a>

### AWS 인증 준비 — GitHub 자동 인증을 사용할 때만

[인증 스크립트](../run_onchange_after_30-gh-auth.sh.tmpl)는 AWS Secrets Manager에서
GitHub 개인 액세스 토큰(PAT)을 가져와 `gh auth login --with-token`에 전달합니다.

```bash
brew install awscli
```

아래 방법 중 자신의 AWS 환경에 맞는 하나를 선택하세요. 프로파일 이름은 `default`를 사용합니다.

**정적 액세스 키를 사용하는 경우**

```bash
aws configure --profile default
```

Access Key, Secret Key, 리전 `ap-northeast-2`, 출력 형식 `json`을 입력합니다.
키는 `~/.aws/credentials`에 저장되며, 사용 가능 여부는 키의 활성 상태와 권한에 따라 달라집니다.

**IAM Identity Center(SSO)를 사용하는 경우**

```bash
aws configure sso --profile default
aws sso login --profile default
```

조직에서 제공한 시작 URL, SSO 리전, 계정·역할을 선택합니다.
SSO 리전은 조직의 설정을 따르고, Secrets Manager를 조회하는 리전은 `ap-northeast-2`입니다.
SSO 세션이 만료되면 다시 로그인하세요. 자세한 동작은
[AWS 공식 SSO 안내](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html)를 참고하세요.

준비가 끝나면 자격증명이 유효한지 확인합니다.

```bash
aws sts get-caller-identity --profile default --region ap-northeast-2
```

선택한 Git 모드에 맞춰 Secrets Manager에 다음 시크릿이 있어야 합니다.
각 `SecretString`에는 PAT 문자열 자체를 저장하며, 조회할 AWS 주체에 `secretsmanager:GetSecretValue` 권한이 필요합니다.

| 시크릿 이름 | 사용하는 모드 |
|---|---|
| `github/personal/token` | `personal`, `both` |
| `github/company/token` | `company`, `both` |

프로파일 `default`와 리전 `ap-northeast-2`는 [chezmoi 설정 템플릿](../.chezmoi.toml.tmpl)뿐 아니라
인증 스크립트에도 지정되어 있습니다. 값을 바꾸려면 두 위치를 함께 확인하세요.
이전에 인증 단계가 건너뛰어졌다면 자격증명 준비 후 [해당 단계 재시도](#retry)를 진행합니다.

<a id="apps-and-settings"></a>

## 설치 앱과 관리 설정

### 앱과 개발 도구

설치할 Homebrew 패키지의 기준은 [Brewfile](../Brewfile)입니다.
CLI 도구는 formula, GUI 앱은 cask 항목으로 관리합니다.

- CLI: `awscli`, `chezmoi`, `curl`, `docker`, `docker-compose`, `eza`, `ffmpeg`, `fzf`, `gh`, `git`, `jq`, `mas`, `mysql`, `node`, `pnpm`, `python`, `sqlite`, `hashicorp/tap/terraform`, `tlrc`, `tree`, `uv`, `vim`, `webp`, `wget`
- `python`은 Homebrew의 현재 Python 3 버전을 따릅니다. 버전을 고정하려면 Brewfile에서 버전별 패키지로 변경합니다.
- Docker CLI와 Docker Desktop은 각각 별도 패키지로 설치됩니다.
- Google Cloud SDK 도구가 PATH에 없다면 Brewfile 상단의 PATH 안내를 확인하세요.

| GUI 앱 범주 | 패키지 |
|---|---|
| 터미널·에디터 | `ghostty`, `visual-studio-code`, `cmux`, `openinterminal-lite` |
| 런처·창 관리 | `raycast`, `rectangle`, `alt-tab`, `itsycal` |
| 키보드·입력 | `karabiner-elements`, `keyboardcleantool`, `logi-options+` |
| 브라우저·차단 | `google-chrome`, `adguard` |
| AI 도구 | `claude`, `claude-code`, `chatgpt`, `codex`, `codex-app` |
| 협업·메모 | `slack`, `discord`, `obsidian`, `heynote` |
| 개발·DB·인프라 | `dbeaver-community`, `docker-desktop`, `gcloud-cli` |
| 미디어·캡처·유틸 | `darktable`, `shottr`, `keka`, `monitorcontrol`, `eul`, `hidock`, `rustdesk` |

Mac App Store 앱은 Brewfile이 아닌 10번 스크립트에서 `mas`로 설치합니다.

| 앱 | App Store ID |
|---|---|
| KakaoTalk | `869223134` |
| Amphetamine | `937984704` |

### 관리되는 설정 파일

| 적용 위치 | 저장소의 소스 |
|---|---|
| `~/.gitconfig` | [Git 설정 템플릿](../dot_gitconfig.tmpl) |
| `~/.config/git/personal` | [개인 작성자 설정](../dot_config/git/private_personal.tmpl) |
| `~/.config/git/company` | [회사 작성자 설정](../dot_config/git/private_company.tmpl) |
| `~/.zshrc` | [Zsh 설정](../dot_zshrc) |
| `~/.config/ghostty/config` | [Ghostty 설정](../dot_config/ghostty/config) |
| `~/.config/vscode/extensions.txt` | [VS Code 확장 목록](../dot_config/vscode/extensions.txt) |
| `~/Library/Application Support/Code/User/keybindings.json` | [VS Code 키바인딩](../private_Library/private_Application%20Support/private_Code/User/keybindings.json) |
| `~/.config/karabiner/assets/complex_modifications/*.json` | [Karabiner 규칙](../dot_config/private_karabiner/private_assets/private_complex_modifications/) |

README, 상세 문서, Brewfile은 홈 디렉터리에 배포하지 않습니다.
Brewfile은 설치 스크립트가 저장소에서 직접 읽습니다.

### 셸·터미널·에디터

- Zsh 테마는 `af-magic`, 활성 플러그인은 `git`, `zsh-syntax-highlighting`, `zsh-autosuggestions`, `fzf`, `tldr`입니다.
- 15번 스크립트가 Oh My Zsh와 외부 플러그인 두 개를 클론합니다. 플러그인의 기본 위치는 `~/.oh-my-zsh/custom/plugins/`이며, 이미 존재하면 건너뜁니다.
- Zsh PATH 앞에 `~/.local/bin`을 추가합니다.
- Ghostty에는 글꼴, 화면 하단 빠른 터미널, 분할 화면 이동 등의 설정이 들어 있습니다.
- VS Code는 [확장 목록](../dot_config/vscode/extensions.txt)의 11개 확장을 설치합니다. GitLens, Biome, Tailwind CSS IntelliSense, Vitest Explorer 등이 포함됩니다.
- VS Code의 `Command+B`는 편집기에 포커스가 있을 때 `bracket-select.select` 명령을 실행합니다.

### 키보드·브라우저

- Karabiner 규칙은 CapsLock+백틱의 왼쪽 클릭 매핑과 한글 입력 중 Shift+O 보정을 포함합니다. 파일 배치 후 사용할 규칙은 Karabiner에서 추가합니다.
- [Chrome 프로파일](../chrome-policies.mobileconfig)의 `ExtensionSettings`로 확장을 자동 설치합니다. 사용자가 프로파일을 승인하고 Chrome을 재시작해야 합니다.
- Chrome 설치 모드는 `normal_installed`이므로 확장은 사용자가 제거할 수 있습니다.

<a id="automation"></a>

## 자동화 동작

`chezmoi apply`는 설정 파일을 배치한 뒤 아래 스크립트를 번호 순으로 실행합니다.
`run_onchange_`는 처음 실행하거나, 이전에 성공적으로 실행된 내용과 렌더링 결과가 달라졌을 때 실행됩니다.
`run_after_99-...`는 적용이 마지막 보고 단계까지 도달할 때마다 실행됩니다.

| 순서 | 스크립트 | 실행 조건 | 역할 |
|---|---|---|---|
| 10 | [brew](../run_onchange_after_10-brew.sh.tmpl) | Brewfile 또는 스크립트 변경 | Homebrew 패키지와 MAS 앱 설치 |
| 15 | [oh-my-zsh](../run_onchange_after_15-oh-my-zsh.sh.tmpl) | 스크립트 변경 | Oh My Zsh와 외부 플러그인 클론 |
| 20 | [vscode-extensions](../run_onchange_after_20-vscode-extensions.sh.tmpl) | 확장 목록 또는 스크립트 변경 | VS Code 확장 설치 |
| 25 | [chrome-extensions](../run_onchange_after_25-chrome-extensions.sh.tmpl) | 프로파일 또는 스크립트 변경 | Chrome 정책 프로파일 열기 |
| 30 | [gh-auth](../run_onchange_after_30-gh-auth.sh.tmpl) | Git 모드 또는 스크립트 변경 | AWS의 PAT로 GitHub CLI 인증 |
| 60 | [macos-defaults](../run_onchange_after_60-macos-defaults.sh.tmpl) | 스크립트 변경 | `KeyRepeat=2` 적용 |
| 99 | [notify-notes](../run_after_99-notify-notes.sh.tmpl) | 보고 단계 도달 시 | 결과와 현재 패키지 목록을 메모 앱에 기록 |

Brewfile, VS Code 확장 목록, Chrome 프로파일의 변경은 스크립트에 포함한 해시로 감지합니다.
건너뛴 단계가 정상 종료하면 실행 이력이 남으므로, 필요한 도구를 나중에 준비해도 자동으로 재시도하지 않을 수 있습니다.

### 실패와 결과 보고

10번 스크립트에서 `brew bundle`이 실패하면 오류 코드로 종료합니다.
MAS 앱과 VS Code 확장 등은 실패를 기록한 뒤 다음 작업으로 진행할 수 있습니다.
따라서 적용 명령의 종료 여부와 결과 노트의 실패 항목을 함께 확인하세요.

10–60번 스크립트는 `${TMPDIR:-/tmp}/chezmoi-status-NN-name.log`에 작업별
`OK`·`FAIL`·`SKIP`을 기록합니다. 99번 스크립트는 남아 있는 상태 로그를 모으고,
현재 머신의 Homebrew·cask·MAS 패키지 목록을 버전과 함께 추가한 뒤 로그를 정리합니다.
이 보고서는 모든 설정을 매번 다시 검사하는 결과가 아닙니다.
중단된 적용의 로그가 남았다면 다음 보고서에 포함될 수 있습니다.

예를 들어 다음과 같이 결과를 확인할 수 있습니다. 실제 항목과 개수는 실행 상태에 따라 달라집니다.

```text
chezmoi apply 결과 (날짜와 시간)

✓ 성공
  · brew bundle — 설치 목록 요약
  · vscode extensions — 설치 완료 개수

✗ 실패
  · mas: Amphetamine — App Store에서 직접 설치 필요

– 건너뜀
  · gh auth — AWS 자격증명 미구성

📦 현재 설치된 패키지
  · Homebrew Formulae / Casks / Mac App Store의 버전 포함 목록
```

<a id="maintenance"></a>

## 일상 관리

### 변경 확인과 적용

```bash
chezmoi status        # 변경 예정 목록
chezmoi diff          # 설정 변경과 실행 예정 스크립트
chezmoi apply         # 실제 적용
```

### 설정 편집과 업데이트

```bash
chezmoi cd            # 관리 중인 저장소로 이동
chezmoi edit-config   # 이 Mac의 Git 모드 등 수정
chezmoi update        # 저장소를 업데이트한 뒤 적용
```

저장소의 파일을 편집한 뒤 변경 내용을 확인하고 적용합니다.
`chezmoi update`는 업데이트와 적용을 함께 하므로, 변경을 먼저 검토하려면
`chezmoi cd`로 이동해 Git으로 변경 내용을 가져오고 `chezmoi diff`를 확인한 뒤 적용하세요.

### 변경 후 검증

Git 템플릿은 적용 전에 세 모드로 렌더링해 볼 수 있습니다.
아래 명령은 각 실행에서만 데이터를 덮어쓰므로 로컬 Git 모드를 바꾸지 않습니다.

```bash
chezmoi execute-template --override-data '{"git_mode":"personal"}' --file "$(chezmoi source-path)/dot_gitconfig.tmpl"
chezmoi execute-template --override-data '{"git_mode":"company"}' --file "$(chezmoi source-path)/dot_gitconfig.tmpl"
chezmoi execute-template --override-data '{"git_mode":"both"}' --file "$(chezmoi source-path)/dot_gitconfig.tmpl"
```

적용 후에는 변경 잔여분, 패키지, 확장과 Git 계정을 확인합니다.
Git 작성자 확인 명령은 실제 작업할 저장소에서 실행하세요.

```bash
chezmoi status
chezmoi diff
brew bundle check --file="$(chezmoi source-path)/Brewfile"
code --list-extensions
git config --show-origin --get user.name
git config --show-origin --get user.email
gh auth status --hostname github.com
```

<a id="troubleshooting"></a>

## 문제 해결

<a id="retry"></a>

### 준비를 마쳤는데 건너뛴 단계가 다시 실행되지 않아요

`run_onchange_`는 필요한 도구가 설치되었는지 매번 확인하는 방식이 아닙니다.
건너뜀이나 일부 작업의 실패를 기록하고 스크립트가 정상 종료했다면,
해당 실행 기록을 지운 뒤 다시 적용해야 합니다.

기본 적용 위치가 홈 디렉터리인 경우, 실행 기록의 키는 다음과 같습니다.

| 다시 시도할 단계 | `entryState`의 키 |
|---|---|
| Homebrew·MAS | `$HOME/10-brew.sh` |
| Oh My Zsh | `$HOME/15-oh-my-zsh.sh` |
| VS Code 확장 | `$HOME/20-vscode-extensions.sh` |
| Chrome 프로파일 | `$HOME/25-chrome-extensions.sh` |
| GitHub 인증 | `$HOME/30-gh-auth.sh` |
| macOS 키 반복 | `$HOME/60-macos-defaults.sh` |

예를 들어 VS Code의 `code` 명령을 준비한 뒤에는 다음과 같이 재시도합니다.

```bash
chezmoi state delete --bucket=entryState --key="$HOME/20-vscode-extensions.sh"
chezmoi diff
chezmoi apply
```

AWS 자격증명을 준비한 뒤 GitHub 인증만 재시도하려면 아래 키를 사용합니다.

```bash
chezmoi state delete --bucket=entryState --key="$HOME/30-gh-auth.sh"
chezmoi diff
chezmoi apply
```

기록 삭제는 선택한 단계가 다음 적용의 실행 대상이 되도록 합니다.
다른 변경이나 실행 대상이 있으면 함께 적용됩니다.
별도의 destination을 사용한다면 키의 홈 경로도 그 위치에 맞춰야 합니다.
`run_onchange_` 기록은 `entryState`, `run_once_` 기록은 `scriptState`에 저장됩니다.
자세한 구분은 [chezmoi 공식 스크립트 안내](https://www.chezmoi.io/user-guide/use-scripts-to-perform-actions/)를 참고하세요.

### 메모 앱에 결과가 없어요

1. 메모 앱에서 `chezmoi apply 결과` 제목을 검색합니다. 노트가 생성되어도 앱이 앞으로 나오지는 않을 수 있습니다.
2. `~/Documents/chezmoi-last-apply.txt`를 확인합니다.
3. 둘 다 없다면 터미널에서 적용이 어느 단계까지 진행되었는지 확인합니다. 텍스트 저장도 실패했다면 Documents 디렉터리와 쓰기 권한을 확인합니다.

메모 앱 호출이 계속 실패한다면 앱을 직접 한 번 실행하고, 사용할 메모 계정이 있는지 확인하세요.
macOS 시스템 설정 → 개인정보 보호 및 보안 → 자동화에서 사용 중인 터미널의 메모 접근 권한도 확인합니다.

### VS Code 확장이 설치되지 않거나 해당 단계에서 멈춰요

`code` 명령을 찾지 못하면 VS Code의 Command Palette에서
`Shell Command: Install 'code' command in PATH`를 실행합니다.
보안 경고가 표시되면 Finder에서 VS Code를 직접 열고 macOS 안내에 따라 승인하세요.
원인을 해결한 뒤 [20번 단계 재시도](#retry)를 진행합니다.

### GitHub 인증이 건너뛰어져요

`aws sts get-caller-identity --profile default --region ap-northeast-2`로 자격증명을 확인합니다.
SSO를 사용한다면 필요한 경우 다시 로그인하고, 선택한 Git 모드에 필요한 시크릿과 조회 권한을 확인하세요.
준비 후에는 [30번 단계 재시도](#retry)를 진행합니다.

이미 인증됨으로 표시되지만 원하는 계정이 없다면 [GitHub CLI 계정](#github-cli) 안내대로
계정 이름과 활성 계정을 확인하고 직접 로그인·전환하세요. 실행 기록을 지워도 계정 수 검사는 그대로입니다.

### Chrome 확장이 보이지 않거나 변경이 반영되지 않아요

시스템 설정 → 일반 → VPN 및 기기 관리에서 `com.imjongmin.chrome-policies`
프로파일을 승인한 뒤 Chrome을 재시작합니다.
25번 스크립트의 성공은 프로파일을 열었다는 뜻이며, 설치 승인이 끝났다는 뜻은 아닙니다.

같은 식별자의 프로파일이 이미 설치되어 있으면 내용이 바뀌어도 스크립트는 건너뜁니다.
프로파일을 갱신하려면 기존 프로파일을 시스템 설정에서 제거하고 [25번 단계 재시도](#retry) 후 다시 승인하세요.

### Karabiner 규칙이 보이지 않아요

Karabiner-Elements를 한 번 실행하고, 규칙 파일이
`~/.config/karabiner/assets/complex_modifications/`에 배치되었는지 확인합니다.
파일이 없다면 `chezmoi diff`로 변경을 확인한 뒤 적용합니다.
파일이 있다면 Karabiner의 Complex Modifications에서 사용할 규칙을 추가하세요.

### Git 모드를 다시 선택하고 싶어요

초기화에서 사용하는 `promptChoiceOnce`는 기존 `git_mode` 값이 있으면 재사용합니다.
`chezmoi edit-config`로 값을 수정한 뒤 변경을 확인하고 적용하세요.

### App Store 앱 설치가 실패했어요

App Store에 로그인한 뒤 필요한 앱을 직접 설치하거나 다음 명령으로 재시도합니다.

```bash
mas install 869223134  # KakaoTalk
mas install 937984704 # Amphetamine
```

### 앱 설치 중 비밀번호를 여러 번 요청해요

Homebrew 스크립트는 sudo 인증을 시도하고 설치 중 세션을 유지합니다.
앱 설치 프로그램이 별도로 비밀번호나 시스템 권한을 요청할 수도 있습니다.
Brewfile은 `adguard`, `docker-desktop`, `karabiner-elements`, `logi-options+`를
cask 목록 앞쪽에 두었지만, 실제 승인 횟수와 시점은 설치 환경에 따라 달라집니다.
