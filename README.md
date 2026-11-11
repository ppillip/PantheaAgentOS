# PantheaAgentOS

AI 에이전트 팀(Claude Code·Codex·jcode 등 코딩 에이전트 여러 명)을 한 PC 안에서 하나의 팀으로 묶어 운용하는 **에이전트 오퍼레이팅 시스템**입니다. 커널(teamkernel)이 팀의 부팅·통신·상태를 관리하고, 데스크톱 콘솔 앱 **HATC(Hyper Agent Team Center)** 에서 프로젝트 생성·팀 실행·대화·업데이트를 수행합니다.

> **상용 소프트웨어입니다.** 저장소·릴리즈가 공개되어 있어도 사용 허가를 의미하지 않습니다. 사용하려면 서면 계약과 라이선스 발급이 필요합니다. [LICENSE](./LICENSE) 참조.
>
> 이 저장소는 **배포 선반**입니다. 소스 코드는 포함하지 않으며, [Releases](https://github.com/ppillip/PantheaAgentOS/releases)에 설치 파일만 게시합니다. 항상 **Latest** 표시가 붙은 릴리즈의 파일을 사용하십시오.

## 지원 환경

| 플랫폼 | 요건 | 설치 방식 |
|---|---|---|
| **Windows** | Windows 10 2004 이상 또는 Windows 11, 64비트. BIOS 가상화 사용, WSL2 설치 가능. 디스크 여유 10GB 이상 | 통합 설치기 `panthea-setup_<버전>_windows-x64.zip` |
| **macOS** | Apple Silicon(arm64). `git`, `rsync`, `tmux` | 설치 스크립트 `install-panthea.sh` |
| **Linux** | x86_64. `git`, `rsync`, `tmux`, `python3`, `sha256sum` | 설치 스크립트 `install-panthea.sh` |

- 에이전트 CLI **Claude Code(`claude`)** 와 **Codex(`codex`)** 는 팀 부팅 전에 각 PC에 설치·로그인되어 있어야 합니다. **jcode** 는 설치본에 포함되어 있어 따로 설치하지 않습니다.
- 발급받은 **라이선스 파일**이 있어야 설치·실행이 완료됩니다(아래 "라이선스" 참조).

## 설치

릴리즈에는 업데이트용 파일이 여럿 올라가 있지만, 사용자가 직접 내려받는 것은 Windows 의 설치 zip 과 macOS/Linux 의 설치 스크립트 두 가지입니다. 나머지는 HATC 가 업데이트 때 자동으로 받습니다.

### Windows

1. [Latest 릴리즈](https://github.com/ppillip/PantheaAgentOS/releases/latest)에서 `panthea-setup_<버전>_windows-x64.zip` 을 내려받습니다. 함께 있는 `.sha256` 파일로 무결성을 확인할 수 있습니다.
   ```powershell
   certutil -hashfile panthea-setup_<버전>_windows-x64.zip SHA256
   ```
2. zip 을 풀고 `PantheaSetup.exe` 를 실행합니다. 관리자 권한은 필요하지 않습니다.
3. 설치기가 WSL2 배포판(`Panthea`)과 HATC 콘솔 앱을 설치합니다. 도중에 라이선스 파일을 묻는 단계에서 발급받은 파일을 지정합니다.
4. 설치가 끝나면 시작 메뉴의 **Hyper Agent Team Center** 로 실행합니다.

WSL2 가 처음인 PC는 설치 전에 PowerShell 에서 `wsl --install --no-distribution` 을 실행하고 재부팅해 두면 설치가 한 번에 지나갑니다. 설치 zip 이름의 버전이 릴리즈 번호보다 낮을 수 있습니다(설치본이 바뀌지 않은 릴리즈). 설치 뒤 HATC 의 업데이트 버튼으로 최신 구성요소를 받으면 됩니다.

### macOS / Linux

```bash
curl -fsSL https://github.com/ppillip/PantheaAgentOS/releases/latest/download/install-panthea.sh | bash
```

OS·CPU 를 자동 감지해 `~/PantheaAgentOS` 에 설치합니다. macOS 에는 HATC 앱도 함께 설치됩니다. 설치 뒤 라이선스 파일을 배치합니다.

```bash
cp <발급받은파일> ~/PantheaAgentOS/etc/panthea.license
chmod 600 ~/PantheaAgentOS/etc/panthea.license
```

## 라이선스

- 라이선스는 계약 뒤 발급됩니다. 파일 하나로 제공되며, Windows 는 설치기에 지정하고 macOS/Linux 는 `~/PantheaAgentOS/etc/panthea.license` 에 둡니다.
- 라이선스 파일이 없거나 만료되면 커널이 기동되지 않습니다.

## 실행

- **Windows / macOS**: HATC 앱을 실행합니다. 커널은 HATC 가 다룹니다.
- **Linux**: `~/PantheaAgentOS/bin/teamkernel` 과 `~/PantheaAgentOS/bin/teamcenter` 를 시작한 뒤 브라우저에서 `http://127.0.0.1:1420` 에 접속합니다.

HATC 에서 프로젝트를 만들고 **팀 실행** 을 누르면 역할별 에이전트가 부팅됩니다.

## 업데이트

HATC 가 선반의 새 릴리즈를 확인하고 **업데이트** 버튼으로 적용합니다. 설정·프로젝트·데이터는 보존됩니다. 업데이트 전에 열려 있는 HATC 창을 모두 닫아 주십시오.

## 문의

- 계약·라이선스 발급·기술 문의: ppillip@gmail.com
