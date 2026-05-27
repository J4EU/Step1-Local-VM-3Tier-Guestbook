Rocky Linux 9 환경에서 Web-WAS-DB 3계층 구조를 직접 구성해보는 연습용 Step Repo입니다.

완성도 높은 운영 환경이나 포트폴리오 결과물을 목표로 한 프로젝트라기보다,
VM 3대를 나누어 웹, 애플리케이션, 데이터베이스 계층이 어떻게 연결되는지 익히는 데 목적이 있습니다.

## 1. 사전 준비

- **고정 IP 설계 필수**: Web, WAS, DB 서버의 IP를 미리 고정해야 함
  - (예시: Web `.210` / WAS `.220` / DB `.230`)

- **환경 설정**: `backend/.env.example` 내용을 본인 환경에 맞춰 수정해야 함
  - 스크립트 실행 시 `.env`로 자동 복사됨

- **스크립트 실행 위치**: `setup_*.sh`, `common_setup.sh`, `db_init.sql` 이 상대경로 기준으로 연결되어 있음
  - 따라서 `scripts/` 디렉터리로 이동한 뒤 실행하는 것을 전제로 함

## 2. 구축 순서

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

- 이 저장소는 **실습/학습용** 기준으로 작성되었음
- 운영 배포 수준의 보안, 장애 복구, 프로세스 관리까지 포함하지는 않음
  - 예를 들어 WAS는 `systemd` 서비스가 아니라 `nohup uvicorn` 방식으로 실행됨
- DB 계정, 방화벽, 설정값도 학습 목적의 단순한 구성을 우선함
- 따라서 "3-tier 구조가 어떻게 연결되는지 확인하는 실습" 범위로 보는 것이 가장 적절함
