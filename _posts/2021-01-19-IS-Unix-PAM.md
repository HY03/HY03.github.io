---
title: 정보보안 - 유닉스 PAM
date: '2021-01-19 15:00:00'
tags:
- 정보보안기사
- UNIX
related: true
categories:
- IS_Certification
toc: true
---

# PAM

- PAM(Pluggable Authentication Module : 탈착형 인증 모듈)
    + 개요
        * 사용자를 인증
        * 서비스에 대한 액세스를 제어하는 라이브러리
        * 권한을 부여하는 소프트웨어의 개발과 안전하고 적정한 인증의 개발을 분리
        * 로그인, telnet, ftp 등의 프로그램 사용시 PAM 을 이용
    + 절차
        * 각종 프로그램 (로그인, telnet, ftp 등) 에서 PAM 라이브러리 호출
        * PAM 라이브러리에서 사용자 인증, 결과값 전송
    + 구성파일
        * `/etc/pam.d` 디렉터리 내에 있는 서비스에서 PAM을 사용
        * `/etc/pam.d/other` : `/etc/pam.d` 디렉터리 외부에서 PAM을 사용할 경우
    + 기본 구조
        * `cat /etc/pam.d/login` : remote 서비스

        |type|control|module-path|module-arguments|
        |:---|:---|:---|:---|
        |auth|required|/lib/security/pam_securetty.so||
        |account|required|/lib/security/pam_stack.so|service=system-auth|
        |password|required|/lib/security/pam_stack.so|service=system-auth|
        |session|required|/lib/security/pam_stack.so|service=system-auth|
        |session|optional|/lib/security/pam_console.so||

        * type : PAM 모듈의 종류
            - account : 사용자의 시스템 사용 권한을 확인하는 모듈
            - auth : 패스워드 확인 등 실질적인 인증기능을 하는 모듈
            - password : 패스워드 설정, 패스워드 확인 모듈
            - session : 인증 성공시 세션을 연결시켜 주는 모듈
        * control : 모듈 성공/실패에 따른 라이브러리 동작 결정
            - requisite : 실패했을 경우 즉시 인증 거부
            - required : 인증이 실패하더라도 다음 라인 수행, 최종적으론 인증 실패
            - sufficient
                + 이전 요청 모듈이 실패하더라도 현재 성공시 PAM 인증승인
                + 실패할 경우 다음 라인 수행
            - optional : 거의 사용하지 않음 (모듈의 성공/실패와 연관없음)
            - include : 모듈이 아닌 PAM 파일의 결과를 사용
        * module-path : 실제 모듈 파일 경로
            - `/lib/security/*.so`
        * module-arguments : 모듈 전달 인수값
    + 지원 모듈 목록
        * pam_securetty.so
            - /etc/securetty 터미널을 사용하는 경우 root 원격 접속 허용
            - 허용할 터미널만을 기재
            - 원칙 : root 계정은 콘솔로 연결해야 함
        * pam_listfile.so
            - /etc/pam.d/vsftpd ftp 허용/거부 관련
        * pam_nologin.so
            - /etc/nologin 파일이 있으면 root 로만 로그인 가능
        * pam_deny.so
        * pam_cracklib.so
            - 사전에 등록된 단어를 이용한 공격(사전공격) 차단을 방지 
        * pam_wheel.so
            - `/etc/pam.d/su` 와 연계하여 사용
            - root 권한을 얻을 수 있는 그룹
        * pam_rootok.so
            - UID : 0 인 사용자를 인증하는 모듈
            - root 가 암호입력 없이 서비스를 사용하는 것을 지원
        * pam_tally.so
            - 로그인 시도횟수를 세는 모듈
        * pam_limits.so
            - 시스템 자원에 대한 사용자 제한을 설정하는 모듈

- PAM 사용 예
    + /etc/pam.d/login(remote) 파일 열기
        * 한단계 씩 진행
        * #PAM-1.0 : 주석절
        * auth required pam_securetty.so
            - auth : 인증기능 모듈
            - pam_securetty.so : `/etc/securetty` 에 리스팅되어 있는 터미널을 제외한 나머지는 root 접속 실패
        * auth required pam_unix.so nullok
            - pam_unix.so : `/etc/passwd`, `/etc/shadow` 를 이용한 인증
            - nullok : null password 허용
        * auth required pam_nologin.so
            - pam_nologin.so : `/etc/nologin` 파일 여부 체크, 있으면 root 제외한 모든 사용자 접속 차단
        * account required pam_unix.so
            - account : 사용자의 시스템 자원 권한 확인
            - pam_unix.so : account 인터페이스를 사용할 경우 유저 계정 활성화여부, 비밀번호 만료여부 등을 확인
        * password required pam_cracklib.so retry=3
            - pam-cracklib.so : 패스워드 변경 시 취약한 패스워드인지 확인 / 차단
        * password required pam_unix.so shadow nullok
            - PAM 사용 서비스에서 패스워드 변경 시 shadow, null ok

- /etc/securetty
    + pam_securetty.so 모듈이 사용하는 파일
    + 원격 root 접속을 허용할 터미널을 기록
        * tty10 : 서버와 연결된 키보드를 통해서 콘솔로 직접 로그인한 터미널
        * tty11
        * tty9
        * # pty/10 : 가상터미널 (원격접속) - telnet, ssh 등. 주석처리 - 불가
        * # pty/11 : 주석처리 - 불가
    + /etc/pam.d/remote (혹은 login)
        * auth required pam_securetty.so

- OS 기종별 root 원격접속 제어
    + SunOS
        * `/etc/default/login` : root에 대한 원격접속 등
            - `console = /dev/console` : 콘솔에서만 root 접속 가능
    + HP-UX
        * `/etc/securetty`
            - `console` : 콘솔에서만 root 접속 가능
    + AIX
        * `/etc/security/user`
            - `rlogin=false` : 콘솔에서만 root 접속 가능
    + Linux 
        * `/etc/securetty` 에서 설정

- pam_tally.so -> pam_tally2.so
    + 계정 잠금 임게값 설정
        * 5회 초과 시 Password Lock
    + 패스워드 공격에 대한 방어
        * 무작위/무차별 공격 (Brute force)
        * 사전 공격 (Dictionary Attack)
    + `/etc/pam.d/system_auth` 파일에 추가
        * `auth required pam_tally2.so deny=5 unlock_time=120`;
            - deny=5 : 5번 실패시 잠금
            - unlock_time=120 : 2분간 잠금
        * `/var/log/secure` : 실패로그 저장위치
        * user1 이 로그인 횟수 5회 초과시 확인 방법
            - `#pam_tally2 -u [계정명]` : 결과값 확인
                + 결과값 : `user1 6 시간값 IP주소값`
            - `#pam_tally -u [계정명] -r` : 횟수 리셋

- OS 별 계정 잠금 임계값 설정
    + SunOS
        * `/etc/security/policy.conf` : LOCK_AFTER_RETRIES=YES > 잠금설정
        * `/etc/default/login` : RETRIES=5 > 횟수설정
    + HP-UX
        * `/tcb/files/auth/system/default` : u_maxtries#5
    + AIX
        * `/etc/security/user` : logintries=5
    + LINUX
        * `/etc/pam.d/system_auth`
            - `auth required /lib/security/pam_tally.so deny=5 unlock_time=120 no_magic_root` : no_magic_root > root 패스워드 잠금 X

- su 명령
    + `/etc/pam.d/su`
        * auth sufficient pam_rootok.so
        * auth include system_auth : 다른 PAM 파일에 대한 결과 사용
    + root 가 타 사용자로 변환할 경우 -> 패스워드 확인 X
    + 일반사용자가 타 일반사용자 혹은 root 로 변환 -> 패스워드 확인 O