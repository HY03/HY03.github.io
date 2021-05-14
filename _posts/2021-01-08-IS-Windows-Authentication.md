---
title: 정보보안 - 윈도우 인증
date: '2021-01-08 10:00:00'
tags:
- 정보보안기사
- 윈도우 보안
- 윈도우 인증 구성
related: true
categories:
- IS_Certification
toc: true
---

# 윈도우 인증

![Security-Architecture-of-Windows](/assets/images/posts/Security-Architecture-of-Windows.jpg)
[이미지 출처](https://networkencyclopedia.com/security-reference-monitor/)

윈도우 인증 구성 요소

- Winlogon : 로그인 프로세스 (ID/PW 입력 윈도우 창)
    + GINA(msgina.dll) : 계정정보와 암호화 된 패스워드를 LSA에 전달
- LSA (Local Security Authority) - 보안 서브시스템 수행 (lsas.exe)
    + NTLM (윈도우 기본 인증 프로토콜), SAM 포함
    + 모든 계정의 로그인(로컬, 원격 모두 포함)에 대한 검증
    + 시스템 자원 및 파일 등에 대한 접근 권한 검사
    + 계정명과 SID (Security ID) 매칭
    + SRM이 생성한 감사 로그 기록
    + 전달받은 계정과 비밀번호를 검증하기 위해 NTLM 모듈을 로드하고 이를 통해 SAM에 접근
- SAM (Security Account Manager) 수행
    + 사용자/그룹 계정, 암호화된 패스워드 정보(해시값) 에 대한 DB를 관리
    + SAM 파일 위치는 대게 %SystemRoot%\System32\config\sam 임
    + 사용자 로그인 정보와 SAM 데이터베이스를 비교해서 일치여부를 SRM에 알림
- SRM (Security Reference Monitor) 수행
    + 사용자에게 SID를 부여
    + SID에 기반하여 자원(파일, 디렉터리)에 대한 접근 허용 여부를 결정함

SID : Security ID
- 사용자 로그인 후 Access token 생성 : SID (보안 식별자) 포함
- S - 1 - 5 - 21 - xxxxxxxxx - 500|501|1000|...
    + S - 1 : 윈도우시스템
    + 5 - 21 : 도메인컨트롤러 (AD에 대한 데이터베이스 소유) or 단독시스템
    + xxxxxxxxx : 컴퓨터 고유식별번호 (리부팅 시 변동)
    + 500|501|1000 : 사용자 식별자
        * 500 : 관리자
        * 501 : 게스트
        * 1000 이상 : 일반사용자
- 확인법 : WMIC 실행 후 useraccount list brief

로컬인증의 흐름
- 로그인 프로세스 시작
- Winlogon 에 ID / PW 입력
- GINA 를 통해 계정정보와 암호화 된 패스워드를 LSA에 전달
- LSA 는 NTLM 을 통해 SAM과 대조하여 계정을 검증
- Access Token을 생성, 전달
- 로그인 프로세스 종료, 새로운 프로세스를 시작

원격인증 (Domain 인증) 의 흐름
- 로그인 프로세스 시작
- Winlogon 에 ID / PW 입력
- GINA 를 통해 계정정보와 암호화 된 패스워드를 LSA에 전달
- LSA 는 도메인  인증인지 판단 후 도메인컨트롤러에 커버로스 프로토콜로 인증 시도
- 도메인컨트롤러 의 LSA (커버로스, Active Directory) 를 통해 인증
- Access Token을 생성, 전달
- 로그인 프로세스 종료, 새로운 프로세스를 시작 

SAM 취약점
- SAM 은 사용자, 그룹 계정의 패스워드, SID 관리하는 중요파일 -> 접근통제 필요
- %SystemRoot%\System32\config\sam -> 속성 -> 보안 탭
    + 그룹 또는 사용자 이름 : Everyone, System, Administrator
    + Everyone 을 제거해야 함.

윈도우 인증 개요
- ID, PW 인증방식은 PW 노출 및 패스워드 재사용 공격에 취약
- 윈도우는 Challenge & Response 방식의 인증 구조를 사용
    + 인증 요청 : 클라이언트가 서버에 인증 요청
    + Challenge 값 생성 / 전송 : 서버는 Challenge 값을 생성, 클라이언트에 전달
    + Response 값 생성 / 전송 : 전달받은 Challenge 값과 패스워드를 이용, Response 전송
    + Response 값 확인 및 인증성공

인증 암호 알고리즘
- LM (Lan Manager) 해시 : 윈도우 2000, XP 기본 알고리즘, 취약
- NTLM 해시 : LM 해시에 MD4 해시(취약) 가 추가, 취약
- NTLMv2 해시 : Vista 이후 기본 인증 프로토콜.
- Lan Manager
    + 네트워크를 통한 파일/프린터 공유 작업시 인증을 담당하는 서비스
    + 인증 수준 설정을 통해 네트워크 로그온에 사용할 Challenge/Response 인증 프로토콜을 결정하면 클라이언트가 사용하는 인증 프로토콜 수준, 협상된 세션 보안 수준, 서버가 사용하는 인증 수준에 영향을 줌. -> NTLMv2 사용을 권장
- 로컬 보안 정책 -> 로컬 정책 -> 보안 옵션 -> 네트워크 보안: LAN Manager 인증 수준

패스워드 크래킹
- 사전공격 (Dictionary Attack)
- 무차별/무작위 대입공격 (Brute Force Attack)
- 혼합공격 (Hybrid Attack) : 사전공격 + 무차별 공격 (문구 + 숫자나 특문)
- 레인보우 테이블 (Rainbow Table) 을 이용한 공격
    + 해시 테이블과 R(Reduction) 함수를 이용
