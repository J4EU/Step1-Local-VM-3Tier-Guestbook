Rocky Linux 9 환경에서 Web, WAS, DB 서버를 각각 VM으로 분리하고,
3-Tier 아키텍처의 기본 구조를 직접 구성해보는 실습 프로젝트입니다.

## 1. 사전 준비

- **고정 IP 설계 필수**: Web, WAS, DB 서버의 IP를 미리 고정해야 함
  - (예시: Web `.210` / WAS `.220` / DB `.230`)

- **환경 설정**: `backend/.env.example` 내용을 본인 환경에 맞춰 수정해야 함
  - 스크립트 실행 시 `.env`로 자동 복사됨

- **스크립트 실행 위치**: `setup_*.sh`, `common_setup.sh`, `db_init.sql` 이 상대경로 기준으로 연결되어 있음
  - 따라서 `scripts/` 디렉터리로 이동한 뒤 실행하는 것을 전제로 함

## 2. 실행 순서

의존성 관계에 따라 아래 순서대로 스크립트 실행을 권장함

1. **DB Server**: `cd scripts && bash setup_db.sh`
   - MariaDB 설치, DB/계정 생성, 초기 테이블 생성
2. **WAS Server**: `cd scripts && bash setup_was.sh`
   - Python 환경 구성, `.env` 복사, `uvicorn` 실행
3. **Web Server**: `cd scripts && bash setup_web.sh`
   - Nginx 설치, 정적 페이지 배포, WAS 프록시 연결

실행 예시:

```bash
cd scripts
bash setup_db.sh
```

## 3. 주요 체크리스트

- **502 에러 발생 시**: SELinux 설정뿐 아니라 WAS 프로세스 실행 여부, 방화벽, Nginx upstream IP 설정을 함께 확인해야 함
- **DB 연결 실패 시**: `.env`에 기재된 DB 서버 IP와 비밀번호가 실제와 일치하는지 확인해야 함
- **한글 깨짐 발생 시**: DB 테이블 생성 시 `utf8mb4` 설정이 반영되었는지 확인해야 함

## 4. 이 저장소의 범위

이 저장소는 3-Tier 구조와 서버 간 연동 흐름을 학습하는 데 초점을 두고 있습니다.

따라서 운영 환경을 위한 보안 강화, 장애 복구, 프로세스 관리 등은 범위에 포함하지 않았습니다.
