---
title: 정보보안 - FTP
date: '2021-04-27 18:00:00'
tags:
- 정보보안기사
- 어플리케이션
related: true
categories:
- IS_Certification
toc: true
---

# FTP(File Transfer Protocol)
## FTP 개요
- FTP 는 2개의 연결이 필요
    + 제어 연결 (Control Connection)
        * 21 번 포트 사용
        * 명령을 전송할 때 사용 / 계속 연결 지속
    + 데이터 연결 (Data Connection)
        * 20 번 포트 사용 or 1024 번 이후 포트 사용
        * 파일이 전송될 때 사용

## FTP 서비스의 취약점
- 아이디 및 패스워드가 암호화 되지 않은 채로 전송됨
    + root 로 FTP를 사용할 수 없게 차단    
- 암호화된 FTP 서비스
    + SFTP(SSH File Transfer Protocol) : 보안 파일전송 프로토콜
        * Secure Shell 이용. SSH 의 일부 
        * SSH Client 와 SSH Server 간 보안연결 성립해야 함
        * 22/tcp 포트 사용
    + FTPS(SSL/TLS)
        * 990/tcp 포트 사용

## FTP 모드
- Client 가 모드를 결정함.
- 능동모드 (Active)
    + FTP 의 기본 값
    + 클라이언트에서 서버 쪽 21번 포트 (제어포트) 접속
    + 클라이언트에서 자신의 데이터 포트, 명령어를 서버에 알려줌
    + 서버에서 클라이언트에 접속해 명령어 처리 데이터를 전송
        * 클라이언트에 방화벽 (외부접속 X) 설정된 경우
            - 제어 포트는 연결 O
            - 데이터 송수신 연결 X
- 수동모드 (Passive)
    + 클라이언트에서 서버 쪽 21번 포트 (제어포트) 접속
    + 클라이언트에서 명령어와 자신의 데이터모드가 PASV 모드임을 서버에 알려줌
    + 서버에서 클라이언트에 자신의 임시 데이터포트를 전송
    + 클라이언트에서 서버의 임시 데이터포트로 접속
    + 서버에서 클라이언트에 명령어 처리 데이터를 전송

## FTP공격
- FTP는 보안 이슈가 부각되기 전에 설계된 프로토콜 (취약점이 존재)
    + 전송 내용이 평문으로 전송됨 - 비밀번호 노출
        * SSL/TLS 레이어 (FTPS) 이용
        * SSH (SFTP) 이용
- FTP bounce attack
    + 데이터 채널을 생성할 때 목적지를 확인하지 않는 FTP 설계 구조의 취약점 이용
    + active 모드에서 FTP 서버 파일 요청을 할 때 파일을 받을 포트와 IP를 전송
        * get aa.txt PORT 192.168.172.10, 200 
    + IP와 포트를 임의의 주소로 지정
        * 네트워크 스캔 공격
            - 공격할 대상 서버 구간의 FTP 서버를 이용, 네트워크 구조 파악
- TFTP (Trivial FTP) attack
    + TFTP 는 보안이 고려되지 않음
        * 사용자 확인 절차 X
    + 일긱 전용 메모리나 디스크가 없는 워크스테이션에 설치될 수 있을 정도의 단순한 파일 송수신을 위한 프로토콜
    + 69/udp 포트를 이용, 별도의 인증과정 없이 지정된 디렉토리에 접근 가능 (취약점)
    + 자체 디스크가 없는 워크스테이션에 부팅이미지를 전달하기 위한 목적으로 주로 사용
    + 사용해야 할 경우 secure mode 를 사용해야 함
        * chroot 기능 이용 (지정디렉토리를 최상위 디렉토리로 설정)
            - xinetd.conf or inetd.conf
                + server_args=-s [디렉터리명]
- Anonymous FTP attack
    + 익명 FTP 서비스 : 익명계정으로 FTP 접속이 가능한 서비스
        * anonymous 란 ID 를 사용, 비밀번호가 없음
    + 익명계정은 원격에서 인증받지 않은 어떤 사용자라도 서버의 파일에 접근 가능
        * 보안이슈 야기
        * 쓰기 권한 부여 시 큰 취약점이 됨
    + Anonymous FTP 서비스 제거
        * redhat : vsftpd (very secure ftp daemon)
            - vsftpd.conf 에서 anonymous_enable=no 로 설정
                + 서비스 사용 시도시 login incorrect 출력됨
                    * ftp 192.168.172.10
                    * user anonymous
                    * password
                    * -> login incorrect
    + 디렉토리 퍼미션 관리
        * Anonymous 사용자 루트 디렉토리 퍼미션 관리
        * bin, etc, pub 디렉터리 소유자, 퍼미션 관리
    + ftp 접근제어 설정
        * ftpusers 파일 이용한 접근제어 설정
            - 시스템관리자 (root 등) 계정의 접속 제한
            - ftpuser : 접속을 제한할 계정정보를 포함
        * tcp wrapper 를 이용한 접근제어
            - hosts.allow / hosts.deny 파일 : IP 기반 접근제어
            - ftp 프로그램별 tcp wrapper 와 연동설정
                + vsftpd.conf 파일에서 tcp_wrappers=yes 설정

## ftpd 종류
- proftpd : unix에서 사용, wuftp 의 대안
    + 접속시 확인 설정
        * /etc/passwd, /etc/shadow 사용자 계정 검사 (사용자 유무)
        * /etc/ftpusers id 존재여부 검사 (있으면 접속 거부)
        * /etc/shell 에 등록되지 않은 shell 사용자 접속 거부
    + proftpd 설정 (proftpd.conf)
        * ServerType [standalone|inetd|xinetd]
        * RootLogin [off] : 루트계정 로그인 허용 X
        * ServerIde [On] <보여줄메시지> : 버전정보 숨기기
        * MaxClients [10] <보여줄메시지> : anonymous user로 접속시 클라이언트 최대 접속 수 
        * TimeoutIdleftp [시간] : 유휴 제한시간 지난 후 Timeout
        * TimeoutSession [시간] : 접속 제한시간 지난 후 Timeout
        * Limit Command : [MKD, CWD, RNFR, RNTD, DELE, RMD, RETR] : 명령어 제한
- vsftpd : 레드햇리눅스에서 사용, PAM 지원
    + 주요파일
        * /etc/vsftpd : 메인디렉토리
        * /etc/vsftpd/vsftpd.conf : 설정파일
        * /etc/vsftpd/ftpusers
        * /etc/rc.d/init.d/vsftpd : 시작, 종료 관련 스크립트 지정
    + vsftpd 설정 (vsftpd.conf)
        * anonymous_enable=NO
        * idle_session_timeout=600 (초)
        * listen=YES (standalone 설정여부)
        * max_clients=10

