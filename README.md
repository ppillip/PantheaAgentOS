# PantheaAgentOS

AI 에이전트 팀을 로컬에서 운용하는 에이전트 오퍼레이팅 시스템입니다.

> **상용 소프트웨어입니다.** 저장소·릴리즈가 공개되어 있어도 사용 허가를 의미하지 않습니다. 사용하려면 서면 계약과 라이선스 발급이 필요합니다. [LICENSE](./LICENSE) 참조.

## 지원 환경

- macOS (Apple Silicon)
- 필수 도구: `git`, `rsync`, `tmux`
- 에이전트 CLI(팀 부팅 전 설치·로그인): Claude Code(`claude`), Codex(`codex`), jcode(`jcode`)

## 설치

```bash
curl -fsSL https://github.com/ppillip/PantheaAgentOS/releases/latest/download/install-panthea.sh | bash
```

발급받은 라이선스 파일을 넣습니다. DB 접속 정보는 라이선스에 들어 있습니다.

```bash
cp <발급받은파일> ~/PantheaAgentOS/etc/panthea.license
chmod 600 ~/PantheaAgentOS/etc/panthea.license
```

## 실행

```bash
~/PantheaAgentOS/bin/teamkernel     # 커널 (별도 터미널)
```

관제는 설치된 HATC 데스크톱 앱을 실행하거나, `~/PantheaAgentOS/bin/teamcenter` 실행 후 브라우저에서 `http://127.0.0.1:1420`에 접속합니다.

## 업데이트

HATC 앱이 새 버전을 확인하고 [업데이트] 버튼으로 적용합니다. 설정과 데이터는 보존됩니다.

## 문의

- 계약·라이선스 발급·기술 문의: ppillip@gmail.com
