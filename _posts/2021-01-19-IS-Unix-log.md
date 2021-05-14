---
title: 정보보안 - 유닉스 로그
date: '2021-01-19 18:00:00'
tags:
- 정보보안기사
- UNIX
related: true
categories:
- IS_Certification
toc: true
---

# 로그관리

- 윈도우 : 중앙에서 집중적으로 로그 수집, 저장
    + 관리 쉬움
    + 공격에 치명적
- 유닉스 : syslogd (중앙 관리 데몬) 가 있으나, 서비스 데몬들이 산발적으로 저장
    + 관리 어려움
        * syslogd
        * maild
        * httpd
        * xinetd
    + 공격에 강함

- OS에 따른 차이
    + 초기 유닉스 BSD 계열 : /usr/adm
        * HP-UX 9.X
        * SunOS 4.X 
    + 최근 유닉스 SVR 계열 : **/var/adm**
        * 솔라리스
        * HP-UX 10.X 이후
        * AIX
    + 일부 BSD 계열 : **/var/log**
        * BSD
        * FreeBSD
        * 오라클 솔라리스
        * 리눅스
    + 일부 리눅스 : /var/run

- 로그관리
    + `/var/log` 디렉토리에서 시스템의 로그를 기록/관리
    + `/etc/syslog.conf` 파일에서 로그파일 위치 지정
    + 로그 시스템 흐름
        * init 데몬 -> syslogd 실행 -> /var/log 디렉토리 지정된 파일에 로그 기록 -> crond에 의한 주기적인 logrotated 데몬 실행
    + 로그파일
        * `/var/log/dmesg` : 부팅될 당시에 각종 메시지들을 저장
            - 텍스트 형식
            - `dmesg -c` : 모든 메시지 삭제, 새로운 로그 기록
            - `dmesg -k` : 커널 메시지 출력
            - `dmesg | grep [hda|sda]` : 하드디스크 관련 정보 출력
            - `dmesg | grep eth` : NIC 정보
            - `dmesg | grep usb`
        * `/var/log/cron` : crond 데몬 로그파일
            - crond 가 동작하면서 실행한 프로세스, 응용프로그램에 대한 정보
            - `/var/run/crond.pid` 에 실행중인 crond 의 pid 저장
            - `/etc/cron.[hourly|daily|weekly|monthly]/*`
                + 주기별로 실행할 스크립트 파일이 존재
        * `/var/log/message` : 모든 데몬의 시스템 로그
            - 텍스트 형식
            - 시스템의 가장 기본적인 시스템 로그 파일
            - 시스템관리자가 중요하게 다루는 파일
                + TCP Wrapper의 접근 제어 정보 - xinetd : ...
                + 사용자 접속 정보
                + 시스템 데몬 실행 내역
            - 형태 : timestamp / 호스트명 / 데몬명 / 메시지 내용
        * `/var/log/secure` : 보안인증 관련 메시지
            - 사용자/그룹 생성, 삭제
            - 사용자 인증, PW 변경
            - 원격접속 내역
            - su (switch user) 명령 수행 내역
        * `/var/log/xferlog` : ftp(proftpd, vsftpd) 로그
            - `Fri Nov 8 12:33:43 2013 1 61.75.198.144 36 /Q00/mac.txt b _ o a chrome@example.com ftp 0 * c`
                + `Fri Nov 8 12:33:43 2013` : current_time
                + `1` : transfer_time (초단위) - 전송시간
                + `61.75.198.144` : remote host
                + `36` : file_size (kb)
                + `/Q00/mac.txt` : file_name
                + `b` : transfer_type (전송파일 종류)
                    * a : ascii
                    * b : binary
                + `_` : special action_flag (FTP 서비스 내 행동)
                    * _ : no action
                    * c : 압축
                    * u : 압축하지 않음
                    * t : tar (묶여있는 파일)
                + `o` : direction (전송방향)
                    * i : incoming (서버로 들어오는 것 - 업로드)
                    * o : outgoing (서버에서 나가는 것 - 다운로드)
                    * d : delete
                + `a` : access mode (사용자 접근 방식)
                    * a : anonymous (익명성)
                    * r : real (인증)
                + `chrome@example.com` : user_name (로그인한 사용자명)
                + `ftp` : service name (서비스명)
                + `0` : 사용자 인증 방법
                    * 0 : none (인증방법 없음)
                    * 1 : RFC931 (인증있음)
                + `*` : 인증을 통해 리턴된 사용자 ID
                    * * : 인증 성공
                    *   : 인증 실패
                + `c` : 파일 전송 상태
                    * c : Complete
                    * i : Incomplete
                 
        * `/var/log/maillog` : sendmail, pop 등의 메일관련 데몬 로그
        * `/var/spool/mail` : 디렉터리, ID 와 동일한 파일이 저장됨
            - mail 명령어로 확인, `&[숫자]` 로 메일에 접근
    + 기타 로그파일
        * `/var/log/wtmp` : 시스템 전체 로그인 기록을 저장
            - 사용자의 성공한 로그인/로그아웃 정보
            - 시스템의 Boot, Shutdown 정보
            - binary 파일, `last` 명령어로 확인 : 모든 계정 로그인/로그아웃 정보
            - 유닉스(SunOS) : `/var/adm/wtmpx`
            - 리눅스 : `/var/log/wtmp`
            - 확인 시 `last [계정명]`
        * `/var/log/utmp` : 현재 로그인 사용자에 대한 기록, 사용자 IP 저장
            - binary 파일 형태
            - 확인 시 `w`, `who`, `finger` 명령어 사용
                + `w [유저명]`

                    |USER|TTY|FROM|LOGIN@|IDLE|JCPU|PCPU|WHAT|
                    |:---|:---|:---|:---|:---|:---|:---|:---|
                    |root|tty1|-|23:24|0.00s|0.31s|0.02s|w|

                    * USER : 유저 계정
                    * TTY : 터미널 (TTY-콘솔, PTS- xwindows 가상 콘솔, PTY - 외부연결 가상 콘솔)
                    * FROM : 접속위치
                    * LOGIN@ : 로그인한 시간
                    * IDLE : 유휴시간 (입력 X)
                    * JCPU
                    * PCPU
                    * WHAT : 수행하고 있는 작업명령어

                + `who [유저명]` : USER, TTY, LOGIN@

            - 유닉스(SunOS) : `/var/adm/utmpx`
            - 일부 리눅스 : `/var/run/utmp`
        * `/var/log/lastlog` : 가장 최근에 로그인 성공한 기록
            - binary 파일
            - 확인 시 `lastlog` (리눅스), `finger` (리눅스, 유닉스)
                + `lastlog -u 계정명`
                + `lastlog -t 일수`
                + `finger 계정명` : 해당 계정의 마지막 로그인 정보
            - 유닉스(SunOS) : `/var/adm/lastlog`
            - 리눅스 : `/var/log/lastlog`
        * `/var/log/btmp` : 실패한 로그인 시도 기록
            - 리눅스 : btmp - `/var/log/btmp` (binary)
                + `lastb` 명령으로 확인
            - 유닉스 : loginlog - `/var/adm/loginlog` (text)
                + `vi` 또는 `cat` 으로 확인
                + 5회 이상 실패시 기록을 남김
        * `/etc/init.d/acct -> /var/adm/pacct` : accounting (통계)
            - 사용자가 로그인~로그아웃 동안 입력한 명령어 내역
            - binary 형태 파일
            - `lastcomm`, `acctcom` 으로 확인
                + `lastcomm [계정명]`
                + 내용 : 명령어, 플래그, 사용자명, 터미널, 프로세스 시작시간
                    * 플래그
                        - F (fork 에 의해 수행)
                        - S (superuser)
                        - X (SIGTERM 종료)
            - 명령어로 초기 로그파일 생성 (자동생성 X)
                + 리눅스 : `accton /var/account/pacct`
                + 유닉스 : `accton /var/adm/pacct`
                + free BSD
                    * 생성 : `touch /var/account/acct`
                    * 실행 : `/usr/sbin/accton /var/account/acct`
                + 명령어 경로
                    * 유닉스, SunOS, HP : `/usr/lib/acct/accton`
                    * 리눅스 : `/sbin/accton`
                    * SunOS : `/sbin/acct.sh`
            - 파일경로
                + 유닉스 : `/var/adm/pacct`
                + 리눅스 : `/var/account/pacct`
        * `/$HOME/.bash_history` : .쉘이름_history, 계정별 실행 명령
            - 텍스트 형태 파일
            - `history` 명령어로 확인 가능
        * `/var/adm/sulog` : 유닉스 로그파일, 리눅스는 secure 파일에 저장
            - 텍스트 형태 파일
            - 형태 : su 시간 + pts/5 test-root
                + `+` : 성공 (+) / 실패 (-)
                + `test-root` : test 계정에서 root 계정으로 시도
        * `/var/log/boot.log` : 리눅스에서 부팅될 때 성공/실패 여부 표시
            - 파일시스템 체크
            - 서비스데몬 실행상태


- `/etc/syslog.conf` 파일 분석
    + syslogd : syslog 표준 api에 의해서 로그 생성, 관리
    + syslogd 는 운영체제에 의해 자동 시작되며 syslog.conf 파일을 참조
    + syslog 표준 api를 준용해 구현한 Application 은 syslogd 에 의해 로그 기록
    + syslog 는 기밀성, 무결성, 가용성을 고려하지 않고 개발됨
        * UDP 프로토콜로 원격지 전송 시 스니핑 가능성
        * 보완책 (RFC3195) : rsyslog
            - TCP 이용 : 로그 전송의 신뢰성 보장
            - syslog msg 의 기밀성 보장을 위해 syslog 서버와 로그수집 대상 서버의 IP를 제외한 payload 보호를 위해 BEEF 사용 권고
                + the Blocks Extensible Exchange Protocol
                + 연결지향적, 신뢰성 보장 프로토콜 (내부인증, 재전송)
                + 응용프로그램 프로토콜 Framework
    + 기본 구성 형식
        * `facility.priority; facility.priority; ... action(logfile location)`
            - 서비스데몬(facility) 에 대해서 로그레벨(priority) 로 기록
        * selector field(선택자 필드) : 어떤 것을 기록할 것인지를 설정       
            - 메시지 종류(facility)
                + `*` : 모든 서비스를 의미
                + auth : login과 같이 인증프로그램 유형이 발생한 메시지
                + authpriv : 개인 인증을 요하는 프로그램 유형이 발생한 메시지
                + cron : cron, at 과 같은 프로그램이 발생한 메시지
                + daemon : telnetd, ftpd와 같이 daemon이 발생한 메시지
                + kern : 커널이 발생한 메시지
                + lpr : 프린터 유형의 프로그램이 발생한 메시지
                + mail : mail 시스템이 발생한 메시지
                + mark : syslogd 에 의해 만들어지는 날짜 유형 메시지
                + news : 유즈넷 뉴스 프로그램 유형이 발생한 메시지
                + syslog : syslogd 에서 발생한 메시지
                + user : 사용자 프로세스
                + uucp : UUCP 시스템이 발생한 메시지
                    * unix-to-unix copy protocol 
                    * 유닉스 시스템을 연결하는 광역 연결망
                + local0 ~ loacl7 : 여분으로 남겨둔 유형(시스템부팅 메시지 기록) 
            - 메시지 우선 순위(priority)
                + `*` : 발생하는 모든 상황에 대한 메시지
                + debug : 프로그램을 디버깅할 때 발생하는 메시지
                + info : 통계, 기본정보 메시지
                + notice : 특별한 주의를 요하나 에러는 아닌 메시지 (관리자조치필요)
                + warning : 주의를 요하는 경고 메시지
                + err : 에러가 발생하는 경우의 메시지
                + crit : 크게 급하지는 않지만 시스템에 문제가 생기는 단계의 메시지
                + alert: 즉각적인 조정을 해야 하는 상황 (예:DB 오류)
                + emerg : 모든 사용자에게 전달되어야 할 위험한 상황 (예:시스템중지)
                + none : 어떠한 경우라도 메시지를 저장하지 않음
        * action field(액션 필드) : 어느파일에 로그를 기록할 것인지를 설정
            - 콘솔, 파일명, 사용자 또는 원격시스템
            - file : 해당 file 에 내용을 추가
                + `/var/log/secure`
                + 콘솔 : `/dev/console`
            - @host : 지정된 호스트로 메시지를 보냄
                + 원격로그서버. `@호스트주소` (예:`@192.168.190.10`)
            - user : 지정된 사용자의 스크린으로 메시지를 보냄 (예:`root`)
            - `*` : 현재 로그인되어 있는 모든 사용자의 스크린으로 메시지를 보냄


- syslog.conf(rsyslog.conf) 설정
    + `*.alert; kern.err; daemon.err; operator`
        * 모든 서비스의 alert 이상 로그는 operator 계정으로 출력
        * 커널의 error 이상 로그는 operator 계정으로 출력
        * 데몬의 error 이상 로그는 operator 계정으로 출력
    + `kern.* /dev/console`
        * 커널의 모든 로그는 콘솔에 출력
    + `*.info; mail.none; authpriv.none; cron.none /var/log/messages`
        * 모든 서비스의 info 이상 로그는 `/var/log/messages` 에 로그 기록
        * mail, authpriv, cron 서비스의 로그는 기록하지 않음
        * `/var/log/messages` 파일이 존재하지 않는 경우 새로 생성하지 않음
    + `authpriv.* /var/log/secure`
        * xinetd, tcp_wrapper, ftp, telnet, finger ... 등의 서비스의 모든 수준 로그는 `/var/log/secure` 에 로그 기록
    + `mail.* /var/log/maillog`
        * 메일서비스(sendmail, ipop, imap, qmail) 서비스의 모든 수준 로그는 `/var/log/maillog` 에 로그 기록
    + `cron.* /var/log/cron`
        * crond, atd 의 모든 수준 로그는 `/var/log/cron` 에 로그 기록
    + `*.emerg *`
        * 모든 서비스의 emerg 수준의 로그는 모든 사용자에게 전송
    + `uucp,news.crit /var/log/spooler`
        * uucp, news 서비스의 crit 수준 이상의 로그는 `/var/log/spooler` 에 기록
    + `local7.* /var/log/boot.log`
        * 시스템 부팅 시 모든 로그는 `/var/log/boot.log` 에 기록
    + 적용 시 rsyslogd 데몬 재시작 필요

- 실시간으로 로그 보기
    + `tail [option] ... [file] ...` : 텍스트 파일 형식의 로그파일 확인
    + `tail -f /var/log/secure` : 파일의 마지막 10라인을 실시간으로 계속 출력

- Logrotate 리눅스 로그 순환 관리
    + syslog 나 데몬프로세스가 출력하는 로그파일을 그대로 방치하면 사이즈가 커지면서 관리하기 어려워지고 디스크 사용률이 100% 가 되어 시스템 장애가 발생할 수 있음
    + logrotate 는 리눅스 설치시 패키지형태로 기본적으로 설치되는 시스템 로그파일 관리도구, 로그파일 순환, 압축 등의 기능을 가지고 있음
    + 경로 : `/usr/sbin/logrotate`
    + 설정파일 : `/etc/logrotate.conf`
        * 순환주기 : daily, weekly, monthly
        * 로그파일의 순환 개수 : rotate n
        * 압축여부 : compress, uncompress
        * 로그파일의 순환 사이즈 지정 : size n
        * 확장자에 날짜를 붙여 저장 : dateext
    + 설정디렉토리 : `/etc/logrotate.d` logrotate 를 적용하기 위한 프로세스, 데몬에 관련된 설정파일
    + 상황파일 : `/var/lib/logrotate.status` logrotate 의 작업내역이 보관됨
    + cron : `/etc/cron.daily/logrotate` 주기적으로 실행되어야 할 내용

# 아파치 웹서버 로그
- 액세스 로그 (접속 로그)
    + `/var/log/httpd/access_log`

- 에러 로그
    + CentOS : `/var/log/httpd/error_log`
    + 우분투 : `/var/log/apache2/error.log`

- 로그 포맷
    + httpd.conf 파일에 기록
    + CLF(Common Log Format)
        * `LogFormat " %h %l %u %t \ " %r\ " %>s %b " common`
            - `%h` : Host (클라이언트 호스트명, IP Address)
            - `%H` : 요청 프로토콜의 종류
            - `%I` : Ident (아이덴티티 체크), 클라이언트 로그인명
                + 대부분 `-` 로 표기 (신뢰할 수 없는 item)
            - `%u` : Auth user (클라이언트 사용자명)
                + 대부분 `-` 로 표기 (신뢰할 수 없는 item)
            - `%U` : 요청된 경로 (자료의 경우 r을 사용)
            - `%t` : time (접속시간, 날짜)
            - `%T` : 웹서버가 요청을 처리하는데 걸린 시간
            - `%r` : request (HTTP 접근 방식, Client가 요청한 자료, HTTP 버전)
            - `%c` : 응답이 완료 되었을 때 연결상태
                + `x` : 응답완료 전 연결 끊김
                + `+` : 응답완료 후 연결 지속
                + `-` : 응답완료 후 연결 끊김
            - `%s` : status (요청에 대한 서버처리 상태코드)
            - `%b` : bytes (서버에서 클라이언트로 전송한 데이터크기)
            - `%i` : 클라이언트의 웹브라우저 정보
                + `LogFormat "{user-agent}i" agent`
                + `LogFormat "{Referer}i->%U" referer` : 현재 아파치서버에 접속하기 전에 있었던 URL 을 기록
        * 웹 서버 로그 예
            - `203.251.189.47 - - [03/Aug/2000:21:56:55 +0900] "GET /doc/images/sub.gif HTTP/1.1" 200 6083`
    + CustomLog
        * `LogFormat " %h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{user-agent}i\"" combined`
