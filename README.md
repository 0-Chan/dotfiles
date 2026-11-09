# 개인용 macOS 환경 설정

새 Mac에서 자주 쓰는 앱과 개발 도구를 설치하고,
터미널·에디터·키보드 설정을 다시 구성하기 위한 개인용 저장소입니다.
매번 수동으로 설정하는 일을 줄이고, 여러 Mac에서 익숙한 작업 환경을 유지하기 위해 사용합니다.

개인 취향에 따른 앱 목록과 개인·회사 Git 작성자 이름·이메일이 포함되어 있습니다.
다른 사람이 사용하려면 적용 전에 자신의 환경에 맞게 설정을 바꿔야 합니다.

## chezmoi는 무엇인가요?

[chezmoi](https://www.chezmoi.io/what-does-chezmoi-do/)는 설정 파일을 Git 저장소에 보관하고
각 컴퓨터에 적용해 주는 도구입니다. 이 저장소는 chezmoi로 설정 파일을 배치하고,
추가 스크립트로 앱 설치와 macOS 설정도 함께 처리합니다.

## 무엇을 설정하나요?

| 영역 | 설정 내용 |
|---|---|
| 앱과 개발 도구 | Homebrew와 Mac App Store로 개발 도구·GUI 앱 설치 |
| 셸과 터미널 | Oh My Zsh, 자동완성·구문 강조 플러그인, Ghostty 설정 |
| VS Code | 확장 프로그램과 키보드 단축키 |
| 키보드와 Chrome | Karabiner 키 매핑·한글 입력 보정, Chrome 확장 자동 설치 |
| Git과 GitHub | 개인·회사 Git 작성자 설정, 선택적 GitHub 인증 |
| macOS와 결과 확인 | 키 반복 속도 설정, 실행 결과와 설치 목록을 메모 앱에 기록 |

## 시작하기

새 Mac에 적용하려면 [설치 가이드](docs/setup-guide.md#setup)를 따라 진행하세요.
준비할 도구와 설정을 확인한 뒤, 변경 내용을 살펴보고 적용하는 순서로 안내합니다.

일부 앱 설치와 Chrome 프로파일은 사용자의 승인이 필요합니다.
GitHub 자동 인증을 위한 AWS 설정은 선택 사항입니다.

## 상세 문서

- [사용 전 확인](docs/setup-guide.md#before-you-start)
- [Git 계정과 선택적 인증](docs/setup-guide.md#git-and-auth)
- [설치 앱과 관리 설정](docs/setup-guide.md#apps-and-settings)
- [자동화 동작과 결과 보고](docs/setup-guide.md#automation)
- [일상 관리](docs/setup-guide.md#maintenance)
- [문제 해결](docs/setup-guide.md#troubleshooting)
