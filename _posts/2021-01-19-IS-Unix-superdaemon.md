---
title: 정보보안 - 유닉스 슈퍼데몬
date: '2021-01-19 10:00:00'
tags:
- 정보보안기사
- UNIX
related: true
categories:
- IS_Certification
toc: true
---

# 보안 쉘

- 원격접속의 종류
    + 평문 송수신 (스니퍼에 노출)
        * rlogin
        * telnet
        * rsh
        * FTP
    + 암호화된 송수신 (터미널)
        * SSH

# 수퍼데몬 xinetd

- 리눅스 일반 데몬은 자기자신 스스로가 직접 서비스를 함
    + HTTP, FTP ...
    + 백그라운드 대기하고 있다가 요청이 들어오면 자식 프로세스를 생성하여 서비스
- xinetd 는 여러 서비스들을 통합관리하기 위한 것
    + 데몬의 공통적인 기능을 제공 (요청이 들어오면 호출...)
- 데몬의 종류
    + standalone
        * 속도가 빠름
        * 공통된 기능을 포함하여 중복 생성, 리소스를 많이 사용
    + xinetd
        * 속도가 느림
        * 공통된 기능은 xinetd 에 통합됨

- xinetd 서비스 특징
    + 여러 서비스 (telnet, ftp) 을 통합관리하는 일종의 서비스(데몬)
    + 각각의 서비스별로 별도의 파일에 설정 가능 (`/etc/xinetd.d/*`)
    + `/etc/xinetd.conf` 파일을 읽어 정보를 얻어옴. 미리 정의 필요
    + 접근제어 요소
        * timeout 설정
        * 접속시도 횟수로 접근제어 (DoS 방지 - IP별 동시접속수 제어)
        * tcp_wrapper (tcpd) 을 내장 (`/etc/hosts.allow, /etc/hosts.deny`)
    + 로깅 요소
        * 로그파일 크기를 제한할 수 있음
        * xinetd 에서 제어되는 각 서비스들에 대한 syslog 로깅 레벨 설정 가능
        * 접속하는 클라이언트들의 서비스 이용시간 기록 가능
        * 서비스를 거부하거나 서비스 접근제어가 되었을 경우 상세로그를 기록

- etc/xinetd.conf
    + `/etc/xinetd.d/` 디렉터리 내에 존재
    + xinetd 서비스파일들에 공통적으로 적용하는 설정 내용이 저장되어 있음
    + xinetd.conf 파일 구조

        |service|socket_type|protocol|wait_flag|login_name|server|argument|
        |:---|:---|:---|:---|:---|:---|:---|
        |ftp|stream|tcp|nowait|root|/usr/sbin/tcpd|in.ftpd -l -a|
        |telnet|stream|tcp|nowait|root|/usr/sbin/tcpd|in.telnetd|

        * service : 서비스명, 포트 번호 결정 (`/etc/services` 에서 서비스명 참조)
        * socket_type : tcp 기반 - stream, udp 기반 - datagram
        * protocol
        * wait_flag
            - nowait : 서비스 요청을 받은 뒤 바로 서비스 처리
            - wait : 기존에 받은 요청을 모두 처리한 뒤 서비스 요청을 받음
        * login_name : 해당 프로그램을 실행하는 사용자 계정 설정
        * server : 실행경로
            - /usr/sbin/tcpd : TCP Wrapper 사용
            - /usr/sbin/in.telnetd (tcpd X): TCP Wrapper 미사용
        * argument : 프로그램을 실행할 때 인수 설정

    + 설정 내용 변경 후 데몬 재시작 필요
    + 보안상 취약한 서비스는 비활성화 필요
        * Simple TCP 서비스 - Dos 공격에 취약
            - echo
            - daytime
            - discard
            - chargen
        * r 계열 서비스 - 인증 없이 원격접속이 가능
            - rsh
            - rexec
            - rlogin 
        * rpc (remote procedure call) 서비스 - 버퍼 오버플로우 공격에 취약
            - rpc.csmd
            - rsuersd
        * finger, tftp 등 취약한 서비스

# TCP Wrapper (접근통제)

- inetd 데몬에 의해 구동되는 네트워크 서비스 (ftp, telnet, rlogin, rsh, exec, tftp, talk, systat, finger 등) 들에 대한 접근 통제와 사용된 서비스별 로그를 기록할 수 있는 보안강화 도구
- 시스템에 존재하는 소프트웨어나 설정 파일을 바꿀 필요가 없음
- 클라이언트와 서버 응용 프로그램 사이의 실질적인 통신에 부하를 주지 않음
- TCP Wrapper 는 /etc/inetd.conf 에 정의되고 inetd 데몬에 의해 통제되는 네트워크 서비스를 통제하기 위하여 inetd 데몬과 네트워크 서비스데몬(in.telnetd, in.ftpd, in.rlogind 등) 사이에 tcpd 데몬을 구동하여 네트워크 서비스의 사용에 대한 접근제어와 로그 기록 기능을 함

- hosts.allow / deny 파일
    + 접근허용 / 접근차단
    + `service_list : client_list [: shell_command]` 형식
        * telnet : 192.168.16.10
        * ALL : 192.168.16.10
        * ALL : ALL
    + 접근순서
        * 예시 1
            - hosts.allow
                + ALL : 192.168.16.10
            - hosts.deny
                + ALL : ALL
            - 192.168.16.10 은 모든 서비스를 허용하고 나머지는 모두 차단
        * 예시 2
            - hosts.allow
                + in.telnetd : 192.168.1.1, 192.168.1.0/255.255.255.0
                + in.ftpd : test.co.kr, 192.168.1.
                + ALL : LOCAL
            - hosts.deny
                + ALL : ALL
            - 지정된 서비스, IP 는 허용, 나머지는 모두 차단
            - test.co.kr 도메인에 해당하는 모든 IP
            - 서브넷 마스크 사용 가능 (192.168.1.0/255.255.255.0) = 192.168.1.
            - LOCAL : 같은 네트워크에 있는 모든 호스트
    + `: shell_command` : 주로 hosts.deny 와 연계하여 사용, 즉시통보, 경고메시지
        * twist : shell_command 명령의 결과를 클라이언트에게 전송
        * spawn : 메일을 보낼 때 사용
        * banners : 특정 디렉터리 내의 문서(데몬명과 동일제목)를 클라이언트에게 출력
        * 예시
            - hosts.deny
                + in.telnetd : 203.241.200.97 : twist /bin/echo "not allowed"
            - hosts.deny
                + in.telnetd : 203.205.205.91 : spawn /bin/mail -s "%a is denied"
                    * %a : TCP Wrapper 환경변수 (client IP 주소)
                        - `%a` : 클라이언트 IP 주소
                        - `%c` : 클라이언트 정보 (user@호스트명 or IP주소)
                        - `%d` : 데몬 프로세스명
                        - `%h` : 클라이언트 호스트명 or IP 주소
                        - `%n` : 클라이언트 호스트명 or unknown
                        - `%p` : 데몬의 PID
                        - `%s` : 서버정보 (데몬명@호스트명)
                        - `%u` : 클라이언트 사용자 이름
                    * 설정한 제목으로 메일을 보냄
- 로그 기록
    + syslogd 사용
    + telnet, ftp, rexec, rsh, rlogin, tftp, talk 등에 대한 접근로그 기록
    + `/var/log/syslog` 파일에 저장 - 변경하려면 `/etc/syslog.conf` 파일 수정

- xinetd 접속 설정
    + `/etc/xinetd.conf` : 글로벌 (Default) 설정
        * cps : (connection per second) - 초당 연결 수
            - cps = 30 10 
                + 30 : 초당 연결 제한 수
                + 10 : 초당 연결 수 초과 시 대기(중지) 시간
        * instances : 동시에 서비스할 수 있는 서버의 최대 개수
            - instances = 100
                + 동시에 서비스할 수 있는 최대 서버 수 100 개
        * per_source : 출발지 IP 주소 기준 서비스 최대 연결 수
            - per_source = 10
                + 동일 IP에서 최대 연결 수를 10개로 제한
        * 설정 변경 후 서비스 재가동
            - `service xinetd restart`
            - `service network restart`
    + `/etc/xinetd.d/서비스설정파일` : 각각의 서비스별 설정

    ```
    service telnet
    {
        flags       = REUSE
        socket_type = stream
        wait        = no
        user        = root
        server      = /usr/sbin/in.telnetd
        log_on_failure += USERID
        disable     = no
        only_from   = 192.168.0.0/16
        no_access   = 192.168.1.137/32 192.168.1.130
        access_times = 09:00-22:00 23:00-01:00
    }
    ```

        * flags : 더이상 사용하지 않음 (inetd 에서는 사용) - 호환성을 위함
        * socket_type : TCP 인 경우 stream, UDP 인 경우 datagram
        * wait : 요청에 대해 즉시 처리 / 이전요청 처리후 처리 여부
        * user : 서비스를 어떤 사용자로 실행할 것인지
        * server : 서비스 실행파일 경로
        * log_on_failure : 서버에 접속 실패했을 경우 로그 파일에 기록할 내용
            - USERID : 원격접속 사용자 ID
            - PID : xinetd 프로세스 ID
            - HOST : 원격 HOST IP
            - EXIT : 프로세스 종료 상태
            - DURATION : 서버 세션 지속시간
        * log_on_success : 서버에 접속 성공했을 경우 로그 파일에 기록할 내용
        * disable : 해당 서비스 실행여부
            - no : 실행
            - yes : 실행안함
        * only_from : 특정 주소(대역) 만 접근 허용
        * no_access : 특정 주소(대역) 의 접근 차단
        * access-times : 접근허용 시간
- 접근 차단 로그
    + `/var/log/message` 확인
    + `cat /var/log/message | grep xinetd | grep FAIL`
