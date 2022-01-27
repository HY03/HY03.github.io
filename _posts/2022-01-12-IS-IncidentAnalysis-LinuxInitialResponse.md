---
title: 정보보안 - 리눅스 사고 분석
date: '2022-01-12 09:00:00'
tags:
- 정보보안기사
- 리눅스 침해사고
- 초기 분석
- 상세 분석
related: true
categories:
- IS_Certification
toc: true
---

# 리눅스 침해사고 분석
- 기본 정보 수집
    + OS 종류, 커널 버전
    + 사용 용도
    + 운영중인 서비스
    + 네트워크 접속 현황
    + 보안 패치 적용 현황
    + 네트워크 구성 형태 및 보안 장비 운영 현황
- 휘발성 정보 수집
    + 프로세스 확인
        * `ps -ef`
            - process 실행자
            - PID
            - 실행 일시
            - 프로세스명
        * `lsof` : List open file
            - System 에서 동작중인 Process 에 의해서 열린 파일들에 대한 정보를 보여주는 명령
            - `ps` 명령어가 변조된 경우 공격자가 구동시킨 프로세스를 볼 수 없음
    + 네트워크 상태
        * `netstat -an`
            - 현재 시스템의 네트워크 연결 상태 확인
            - 발신지 주소 확인
        * `nmap` : network mapper
            - 네트워크 보안 유틸리티
            - 대규모 네트워크를 고속으로 스캔
                + `-sT` : tcp 스캔
                + `-p` : 특정 포트 점검
                + `nmap -sT -p [포트번호] [ip주소]`
        * `fuser`
            - 어떤 프로세스에서 포트를 열었는지 확인
            - 현재 사용중인 파일, 소켓이 사용하는 프로세스 확인
            - `fuser 6001/tcp`
                + 위 명령어의 결과 (예:pid 6333) 를 활용해 `ps [pid]` 명령어 사용
    + 사용자 로그인 상태
        * `w`
            - 현재 시스템에 성공적으로 로그인한 사용자에 대한 Snapshot 제공
                + 해당 사용자 목록 중에 `w` 명령어에 없는 (성공적으로 로그인) 사용자는 백도어를 이용했을 확률이 있음
            - 사용자들이 어디에서 로그인을 했는지, 어떤 작업을 하는지 알 수 있음
            - `utmp` : 정보 파일
    + 공격자를 규정하기 위해 자세히 봐야할 정보
        * 접속 계정이 정상적인지 확인
        * 접속출처가 정상 위치인지 확인 (내부 IP 주소 이외에 접속, 국외 IP 주소 접속 등)
        * 접속 사용자의 정상적인 행위인지 확인 (Scan 도구 사용, 다른 서버에 서비스거부공격 등)
    + 프로세스 확인, 네트워크 상태를 확인해 시스템의 상황을 파악하고 공격자의 단서를 찾음, 이후 세부사항 확인
- 상세분석
    + 패스워드 파일 분석
        * **`/etc/passwd`**
            - 관리자 권한을 가진 계정이 있는지 확인 : uid=0
                + `user1:x:0:0::/home/user1:/bin/bash`
            - 새로 생성된 계정이나 패스워드가 없는 계정 확인
    + **로그 파일 분석**
        * 사고가 일어난 뒤의 로그는 100% 신뢰할 수는 없다. 하지만 흔적은 어딘가에 남아있다.
        * 로그파일 생성일자, 변경일자 확인
        * 공격자는 공격을 통해 발생하는 시스템로그, 접속로그등을 삭제/변경함
        * 주요 로그 파일
            - `utmp` : 현재 시스템에 로그인한 사용자의 정보
            - `wtmp` : 과거에 로그인했던 사용자의 정보
            - `messages` : 시스템 장애 정보 + 공격으로 인한 정보
        * 웹로그 파일
            - `access_log`
                + Host (도메인,IP주소) : 웹서버에 접속한 IP 주소
                + Identification : 사용자의 이름, - 로 표시
                + User Authentication : (사용자 인증이 된 경우) 패스워드가 표시되는 부분
                + Time Stamp : 방문자가 접속한 시간 (GMT - 그리니치 표준시 포함)
                + HTTP Request
                    * 사용자 접속한 방식 (POST, GET)
                    * 접속파일
                    * HTTP 버전
                + Status : 사용자가 요청한 내용을 처리한 결과
                    * 200 : Success
                    * 3xx : Redirection
                    * 4xx : Client Error
                        - 404 : NotFound
                    * 5xx : Server Error
                + Transfer Volume : 요청한 파일의 용량, 없는 경우 0 또는 - 로 표시
            - `access`
    + 루트킷 확인
        * Rootkit : 정상적 프로그램을 변조시킴 (주로 바이너리 파일)
            - ls, ps, netstat, login, top, dir, du, ifconfig, find, tcpd ...
            - `ls` 명령어를 변조시켜 공격자가 만든 파일이 보이지 않도록 함
        * 시스템 프로그램 파일크기, 생성시간, 변경시간 확인
        * `/bin`, `/usr/bin` 디렉토리 에서 `ls -alct | more` 명령을 통해 다른 프로그램이 기본적으로 설치된 시간과 다른 것이 있는지 확인 -> 파일사이즈 비교
            - `ls`
                + `-a` : all - 숨겨진 파일, 디렉토리를 보고자 할때
                + `-l` : long - 자세한 내용을 출력. 권한, 파일 수, 소유자, 그룹, 파일크기, 수정일자 등
                + `-c` : ctime - 변경시간
                    * MAC : mtime (수정), atime (접근), ctime (변경)
                    * 기본값 mtime 으로 출력
                    * `-u` : atime
                + `-t` : 시간순 정렬
                + `-S` : 파일 사이즈 순 정렬
        * 무결성 검사 (리눅스) : `rpm -Va`
            - `s.5.....GT /bin/df`
                + s : 프로그램 사이즈 변경
                + 5 : md5 checksum 값 변경
                + T : vkdlfdml mtime 값 변경
                + G : group 변경
                + u : user 변경
                + c : 설정파일 변경
        * 무결성 검사 (솔라리스) : `fingerprint`
            - 검사하고자 하는 파일의 checksum 값을 만들어 비교
        * `strace` : System 콜 추적
            - 예) ps 명령어 추적 : `strace -e trace=open ps|more`
                + 결과가 `open("/usr/lib/locale/ro_RO/u~/procre)` 이라고 나왔을 경우 ps 가 해당 파일을 참조
                    * 공격자가 숨기고 싶은 파일을 해당 파일에 등록
    + 해킹 관련 파일 조사
        * 피해 시스템에 공격자가 어떤 작업을 했는지 알아보기 위한 절차
            - 타 시스템 스캔
            - 공격 프로그램 설치
            - IRC 서버 설치
            - 로그 삭제
            - 스니퍼 프로그램 설치
        * 새롭게 생긴 파일 찾기
            - `find` 명령어를 이용해 최근 수정된 파일, 생성된 파일 찾기
                + 변조를 감추기 위해 시간을 수정하는 경우 inode 변경시간 체크
                + 수정일 7일 이내인 파일 찾기
                    * `find [폴더] -type f -mtime 일수 -print` 
                + 최근 5일동안 수정되거나 새롭게 생성된 파일을 찾아서 `/var/aaa/test.out` 파일에 저장
                    * `find / -ctime -5 -print > /var/aaa/test.out` 
                + setuid, setgid
                    * `find / -user root -perm -4000 -print > suidlist` 
                    * `find / -user root -perm -2000 -print > sgidlist` 
        * `/usr, /var, /dev, /tmp` 디렉토리 안 파일 검사
            - `/dev` 폴더 안 디바이스파일 이외의 일반파일이 있는 경우
                + `find /dev -type f -print` 
        * `. , ..` 으로 시작하는 디렉토리
            - `ls` 명령어를 옵션 없이 사용시 비노출 (숨겨진 디렉토리)