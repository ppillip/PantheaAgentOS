# PantheaAgentOS

AI 에이전트 팀을 로컬에서 운용하는 에이전트 오퍼레이팅 시스템입니다.

> **상용 소프트웨어입니다.** 이 저장소와 릴리즈가 공개되어 있다는 사실은 어떠한 사용 허가도 의미하지 않습니다. 사용하려면 저작권자와의 서면 계약과 서비스 계정(database URL) 발급이 필요합니다. 자세한 내용은 [LICENSE](./LICENSE)를 확인하세요.

## 지원 환경

- macOS (Apple Silicon)
- 필수 도구: `tmux`, 에이전트 CLI(Claude Code / Codex)

## 설치 방법

### 1. 설치 프로그램 다운로드

[**최신 릴리즈**](https://github.com/ppillip/PantheaAgentOS/releases/latest) 페이지의 **Assets**에서 updater 바이너리를 내려받습니다.

- 파일명: `panthea-updater_<버전>_darwin-arm64`

### 2. 실행 권한 부여

터미널에서 내려받은 파일에 실행 권한을 줍니다.

```bash
chmod +x panthea-updater_*_darwin-arm64
```

macOS가 실행을 차단하는 경우 격리 속성을 해제합니다.

```bash
xattr -d com.apple.quarantine panthea-updater_*_darwin-arm64
```

### 3. 설치 실행

```bash
./panthea-updater_*_darwin-arm64 install
```

본체가 자동으로 다운로드되어 `~/PantheaAgentOS`에 설치됩니다.

### 4. 서비스 계정 설정

서비스 제공자에게 발급받은 **database URL**을 설정 파일에 입력합니다.

```bash
open -e ~/PantheaAgentOS/etc/os.toml
```

`[database]` 섹션의 `url` 값을 발급받은 URL로 바꿉니다.

> ⚠️ database URL은 비밀번호를 포함합니다. 채팅·로그 등 외부에 노출하지 마세요.

### 5. 설치 확인

```bash
~/PantheaAgentOS/bin/teamkernel doctor
```

진단이 통과하면 설치가 완료된 것입니다.

## 실행

각각 별도의 터미널에서 실행합니다.

```bash
~/PantheaAgentOS/bin/teamkernel           # 커널
~/PantheaAgentOS/bin/teamcenter           # 관제 센터
```

관제 센터는 브라우저에서 `http://127.0.0.1:1420`으로 접속합니다. 데스크톱 앱(HATC)을 발급받은 경우 앱으로도 이용할 수 있습니다.

## 업데이트

- **HATC(데스크톱 앱)**: 앱이 새 버전을 자동으로 확인하고 업데이트합니다.
- **본체**: updater를 다시 실행하면 최신 버전으로 갱신됩니다. 기존 설정(`etc/os.toml`)과 데이터(`var/`)는 보존됩니다.
- 업데이트 후 `~/PantheaAgentOS/bin/teamkernel doctor`를 한 번 실행하세요. 데이터베이스 마이그레이션 미적용이 안내되면 `~/PantheaAgentOS/bin/teamkernel migrate`를 실행하면 됩니다.

## 문의

- 계약·계정 발급·기술 문의: ppillip@gmail.com
