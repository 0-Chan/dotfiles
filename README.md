# dotfiles (chezmoi)

새 macOS 머신을 빠르게 셋업하기 위한 [chezmoi](https://www.chezmoi.io/) 저장소.

## 관리 대상

| 대상 | 경로 | source |
|---|---|---|
| Git 전역 설정 | `~/.gitconfig` | `dot_gitconfig.tmpl` |
| Git personal 프로필 | `~/.config/git/personal` | `dot_config/git/private_personal.tmpl` |
| Git company 프로필 | `~/.config/git/company` | `dot_config/git/private_company.tmpl` |
| Ghostty 터미널 | `~/.config/ghostty/config` | `dot_config/ghostty/config` |
| VS Code 확장 목록 | `~/.config/vscode/extensions.txt` | `dot_config/vscode/extensions.txt` |
| Homebrew 패키지 | (설치용, 배포 X) | `Brewfile` |
| Mac App Store 앱 | (스크립트에서 `mas` 직접 실행) | `run_onchange_after_10-brew.sh.tmpl` |

자동화 스크립트(`chezmoi apply` 시 실행):
- `run_onchange_after_10-brew.sh.tmpl` — Brewfile이 바뀌면 `brew bundle` 재실행 + Mac App Store 앱 설치(실패 시 경고만 출력)
- `run_onchange_after_20-vscode-extensions.sh.tmpl` — extension 리스트가 바뀌면 VS Code 확장 재설치

## 새 머신 부트스트랩

복사-붙여넣기 순서:

```bash
# 1. Xcode Command Line Tools (Homebrew/git 전제조건)
xcode-select --install

# 2. Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Apple Silicon인 경우 PATH 주입 (Intel은 자동)
eval "$(/opt/homebrew/bin/brew shellenv)"

# 3. chezmoi 설치
brew install chezmoi

# 4. 이 저장소로 chezmoi 초기화 + 바로 적용
#    (git_mode를 묻는 프롬프트가 뜸: personal / company / both)
chezmoi init --apply <git-repo-url>
```

`chezmoi init --apply`가 끝나면 다음이 자동으로 수행됨:
1. `~/.config/chezmoi/chezmoi.toml` 생성(선택한 `git_mode` 포함)
2. 관리 대상 dotfile 배포
3. `brew bundle`로 Brewfile의 모든 패키지/앱 설치 (sudo 비밀번호 1회 입력)
   - Mac App Store 앱(KakaoTalk, Amphetamine)은 별도로 `mas install` 실행 — 실패해도 나머지 설치에 영향 없음
4. VS Code가 깔리고 `code` CLI가 PATH에 잡혀 있으면 확장 일괄 설치
   - `code` CLI가 없으면 해당 단계만 건너뜀. VS Code에서 `Shell Command: Install 'code' command in PATH` 실행 후
     `chezmoi apply` 한 번 더.

## git_mode 분기

`chezmoi init` 시 선택한 값에 따라 `~/.gitconfig`가 다르게 렌더링됨:

| git_mode | 결과 |
|---|---|
| `personal` | `~/.config/git/personal`만 `[include]` |
| `company` | `~/.config/git/company`만 `[include]` |
| `both` | `~/workspace/personal/` 하위는 personal, `~/workspace/company/` 하위는 company 프로필 (`[includeIf]` 조건부) |

전환이 필요하면:
```bash
chezmoi edit-config   # ~/.config/chezmoi/chezmoi.toml의 git_mode 수정
chezmoi apply
```

## AWS Secrets Manager 네이밍 컨벤션

현재 실제 참조 템플릿은 없으나, 후속 작업에서 토큰을 주입할 때 사용할 네이밍을 고정:

| secret 이름 | 용도 |
|---|---|
| `github/personal/token` | 개인 계정 GitHub PAT |
| `github/company/token` | 회사 계정 GitHub PAT |

- Profile: `default`, Region: `ap-northeast-2` (repo 루트 `.chezmoi.toml.tmpl`에 명시됨)
- chezmoi 템플릿에서 참조 예: `{{ (awsSecretsManager "github/personal/token").SecretString }}`

## 일상 운영 명령

```bash
chezmoi status        # 변경 예정 목록
chezmoi diff          # 상세 diff
chezmoi apply         # 실제 반영
chezmoi cd            # source 디렉터리로 이동 (편집 후 git commit/push)
chezmoi update        # git pull + apply
```

## 검증 체크리스트 (push 전)

```bash
# 템플릿 렌더 점검 (git_mode 시나리오별)
chezmoi execute-template --init --promptChoice git_mode=personal < dot_gitconfig.tmpl
chezmoi execute-template --init --promptChoice git_mode=company  < dot_gitconfig.tmpl
chezmoi execute-template --init --promptChoice git_mode=both     < dot_gitconfig.tmpl

# apply 후 실제 반영 확인
chezmoi status
chezmoi diff
ls -la ~/.config/git/ ~/.config/ghostty/ ~/.config/vscode/
git config --list --show-origin

# Brewfile/extension 반영 확인
brew bundle check --file="$(chezmoi source-path)/Brewfile"
code --list-extensions
```
