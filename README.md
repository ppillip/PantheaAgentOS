# PantheaAgentOS

AI 에이전트 팀을 로컬에서 운용하는 에이전트 오퍼레이팅 시스템입니다.

> **상용 소프트웨어입니다.** 이 저장소와 릴리즈가 공개되어 있다는 사실은 어떠한 사용 허가도 의미하지 않습니다. 사용하려면 저작권자와의 서면 계약과 서비스 계정(database URL) 발급이 필요합니다. 자세한 내용은 [LICENSE](./LICENSE)를 확인하세요.

## 지원 환경

- macOS (Apple Silicon)
- **설치 필수 도구**: `git`, `rsync`, `tmux` — 하나라도 없으면 설치가 진행되지 않습니다.
- **에이전트 CLI**: Claude Code(`claude`), Codex(`codex`), jcode(`jcode` — OpenAI 호환 API 서버용 드라이버) — 없어도 설치는 계속되지만(경고만 출력), 팀 부팅 전에 사용할 에이전트 CLI를 설치·로그인(jcode는 API 서버 설정)해야 합니다.

## 설치 방법

터미널에서 아래 한 줄을 실행합니다.

```bash
curl -fsSL https://github.com/ppillip/PantheaAgentOS/releases/latest/download/install-panthea.sh | bash
```

스크립트가 최신 릴리즈 확인, 설치 프로그램 다운로드와 무결성 검증, 본체 설치(`~/PantheaAgentOS`), HATC 데스크톱 앱 배치까지 자동으로 수행합니다. macOS 격리 속성(quarantine) 처리도 스크립트가 알아서 합니다.

설치 위치를 바꾸려면 내려받아 실행하며 `--home <경로>`를 붙입니다.

### 라이선스 파일 설정

서비스 제공자에게 발급받은 라이선스 파일을 설치 폴더에 넣습니다. 데이터베이스 접속 정보는 라이선스에 포함되어 있어 별도 입력이 필요 없습니다.

```bash
cp <발급받은파일> ~/PantheaAgentOS/etc/panthea.license
chmod 600 ~/PantheaAgentOS/etc/panthea.license
```

> ⚠️ 라이선스 파일은 접속 비밀을 포함합니다. 채팅·로그 등 외부에 노출하지 마세요.

### 데이터베이스 확인 (선택)

```bash
~/PantheaAgentOS/bin/teamkernel migrate --status --json
```

출력의 `verdict` 값에 따라 진행합니다.

- `up_to_date` — 변경 없음. 서비스 제공자가 이미 마이그레이션을 마친 DB를 발급하는 경우의 기대값입니다.
- `empty` 또는 `pending` — `~/PantheaAgentOS/bin/teamkernel migrate`를 실행합니다.
- `baseline_required` — 임의로 적용하지 말고 서비스 제공자에게 문의하세요.
- `dirty` — 중단하고 서비스 제공자에게 문의하세요.

### 설치 확인

설치 완료 여부는 설치 스크립트의 성공 종료와 마지막 완료 안내 메시지가 기준입니다. 아래 명령은 본체 바이너리가 실행되는지 추가로 확인하는 용도입니다.

```bash
~/PantheaAgentOS/bin/teamkernel --version
```

## 실행

각각 별도의 터미널에서 실행합니다.

```bash
~/PantheaAgentOS/bin/teamkernel           # 커널
~/PantheaAgentOS/bin/teamcenter           # 관제 센터
```

관제 센터는 브라우저에서 `http://127.0.0.1:1420`으로 접속합니다. 데스크톱 앱(HATC)을 발급받은 경우 앱으로도 이용할 수 있습니다.

## 업데이트

- **HATC(데스크톱 앱)**: 앱이 새 버전을 자동으로 확인하고 업데이트합니다. **본체 업데이트의 표준 경로입니다.** 기존 설정(`etc/os.toml`)과 데이터(`var/`)는 보존됩니다.
- **본체 수동 업데이트(보조 절차)**: `install`은 첫 설치 전용입니다 — 기존 설치가 있으면 실행되지 않습니다(`existing_install_detected`). HATC를 쓸 수 없는 환경에서는 **커널 API 경유**로 진행합니다. 업데이트 적용(handoff 작성·적용 프로세스 실행)은 오직 커널이 수행하므로, **커널이 실행 중이어야 합니다 — 커널이 꺼진 환경에서는 수동 업데이트를 수행할 수 없습니다.** API는 같은 머신(127.0.0.1)에서만 호출할 수 있습니다.

  1. (선택) 새 버전을 미리 내려받아 검증·스테이징해 둘 수 있습니다(실행 중인 시스템은 변경되지 않으며, 이 단계만으로는 적용되지 않습니다).

     ```bash
     ./panthea-updater_*_darwin-arm64 prepare --home "$HOME/PantheaAgentOS"
     ```

  2. 커널에서 최신 릴리즈 정보를 확인합니다. 응답에서 `data.remote.release_run_id`(최신 릴리즈 ID)와 `data.capabilities.platform`(이 머신의 플랫폼 — 다음 단계의 `target` 값)을 사용합니다.

     ```bash
     curl -s http://127.0.0.1:8000/system/update/status
     # 정보가 오래됐으면: curl -s -X POST http://127.0.0.1:8000/system/update/check -H 'Content-Type: application/json' -d '{"force":true}'
     ```

  3. 커널에 준비를 요청합니다(202 응답, 백그라운드 다운로드·검증).

     ```bash
     curl -s -X POST http://127.0.0.1:8000/system/update/prepare \
       -H 'Content-Type: application/json' \
       -d '{"release_run_id":"<release_run_id>","target":"darwin-arm64"}'
     ```

  4. `status`를 다시 조회해 `data.capabilities.preparation_plans[]`에서 `bin_verified: true`인 `preparation_id`를 확인한 뒤, 적용을 요청합니다.

     ```bash
     curl -s -X POST http://127.0.0.1:8000/system/update/apply \
       -H 'Content-Type: application/json' \
       -d '{"preparation_id":"<preparation_id>"}'
     ```

     202 응답(`operation_id`, `state: handoff_ready`) 후 **커널이 handoff 파일을 작성하고 적용 프로세스를 실행**합니다 — 전체 정지 → 교체 → 재기동이 자동으로 진행되며(커널 자신도 재시작됩니다), 실패 시 이전 버전으로 자동 롤백됩니다.

  **적용이 중단된 경우의 재개(복구 전용)**: 적용 프로세스가 중간에 죽어 handoff 파일(`~/PantheaAgentOS/.update/handoff/<operation_id>.json`)은 있는데 교체가 끝나지 않았다면, 그 파일 경로로 직접 재개할 수 있습니다. 이때 파일명의 `<operation_id>`는 **4단계 `apply` 응답의 커널 발급 ID**입니다 — 1단계 `prepare`가 출력하는 ID와는 다른 값이니 혼동하지 마세요.

  ```bash
  ./panthea-updater_*_darwin-arm64 apply --handoff ~/PantheaAgentOS/.update/handoff/<operation_id>.json --home "$HOME/PantheaAgentOS"
  ```
- 업데이트 후 이상이 의심되면 위 **데이터베이스 확인 (선택)** 절의 `migrate --status` 명령으로 `verdict`를 확인하세요 — `empty`/`pending`일 때만 `teamkernel migrate`를 실행하고, `baseline_required`/`dirty`는 적용하지 말고 서비스 제공자에게 문의합니다.

## 문의

- 계약·계정 발급·기술 문의: ppillip@gmail.com
