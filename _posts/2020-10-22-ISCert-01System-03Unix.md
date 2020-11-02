---
title: 01.시스템보안 - 03.유닉스&리눅스 서버 보안
date: '2020-10-24 16:00:00'
tags:
- 정보보안기사
- 시스템 보안
- 유닉스 서버 보안
- 리눅스 서버 보안
related: true
categories:
- IS_Certification
toc: true
---

# **권한 설정**

## 1. umask : 디폴트 권한
+ 0022
    * 666 - 022 : 644 (파일)
    * 777 - 022 : 755 (디렉터리)
+ 0077
    * 666 - 077 : 600 (파일)
    * 777 - 077 : 700 (디렉터리)

## 2. rwxrwxrwx : 소유자, 그룹, 다른 사용자
+ `chmod 644 abc.txt`
    * 유저 : 읽기, 쓰기, 실행
    * 그룹 : 읽기, 실행
    * Others : 읽기, 실행
    * `chmod u+x, g+x, o-r abc.txt` : 750
+ `chown root:group abc.txt`
    * abc.txt 의 owner 를 root 로 설정
    * abc.txt 의 group 을 group 로 설정
+ `chgrp group abc.txt`
    * abc.txt의 group을 group 로 설정

## 3. 특수 권한 관리
+ setuid : 4000, 실행 시에 소유자의 권한으로 실행
    * -rwsr-xr-x /usr/bin/passwd
    * 일반 사용자가 패스워드 변경시 owner(root) 권한으로 /usr/bin/passwd 파일을 실행
    * `chmod 4744 abc.txt`
    * `chmod u+s abc.txt`
    * 실행가능 파일 rws, 실행불가능 파일 rwS
+ setgid : 2000, 실행 시에 그룹의 권한으로 실행
    * Mbox 의 경우 `/var/mail` 디렉터리의 소유자는 root, 그룹은 mail, setgid
    * 일반 사용자는 메일 계정을 생성할 때 mail 디렉터리에 자신의 디렉터리를 생성하고 메일을 보관할 수 있게 됨
+ sticky bit : 1000, /tmp, 임시 디렉터리
    * 공용 디렉터리를 만들어 누구나 자유롭게 사용할 수 있도록 함
    * 해당 디렉터리의 삭제는 소유자, root만 가능
    * drwxrwxrwt tmp
+ 검색법
    * find / -perm -4000 -print 2>/dev/null
    * find / -perm -2000 -print 2>/dev/null
    * find / -perm -1000 -print 2>/dev/null
        - `2>/dev/null` : 에러, /dev/null 에 버리기 (1은 표준출력)
        - `-` : 해당 권한을 포함한 모든 권한
        - ` ` : 정확히 일치하는 권한
        - `+` : 해당 권한중 하나라도 포함하는 모든 권한

# **계정 정보**

## 1. `/etc/passwd`
- 해시로 암호화된 패스워드
- `/etc/shadow` -> `/etc/passwd` : `pwunconv`
- `root: x: 0: 0: root: /root: /bin/bash`
    + Login Name
    + Password : x -> `/etc/shadow`
    + User ID : root = 0
    + User Group ID : root = 0
    + Comments
    + Home Directory
    + Shell : 만약 `/bin/false` 이면 셸을 실행하지 않음.

## 2. `/etc/shadow`
- `/etc/passwd` -> `/etc/shadow` : `pwconv`
- `root: $1$Fz4q1GjE$G/: 14806: 0: 99999: 7: : : `
    + Login Name
    + Encrypted : password
    + Last Changed : 1970년 1월 1일 부터 일수 계산
    + Minimum : 패스워드 변경 전 최소 사용기간 (일 수)
    + Maximum : 패스워드 변경 전 최대 사용기간 (일 수)
    + Warn : 패스워드 사용 만기일 전에 경고 메시지를 제공하는 일 수
    + Inactive : 로그인 접속차단 일 수
    + Expire : 로그인 사용을 금지하는 일 수(월/일/연도)
    + Reserved : 사용하지 않음 
    + `chage -l <사용자명>` : 패스워드 만료날짜 확인

## 3. `/usr/bin/passwd`
- `-rwsr-xr-x` : setuid > 실행시 소유자의 권한으로 실행
- `passwd`, `passwd <사용자명>`

## 4. John the ripper
- `john --format=raw-MD5 /root/passwd.txt --crack-status`
- Cracking : 패스워드를 알아내기 위한 공격
- 어떤 입력 값 X 에 대해 암호문 Y가 나오는 것을 알고 있을 때, 입력 값과 암호문을 파일에 저장하고 계속 입력해보면서 패스워드를 알아냄

## 5.  `/etc/motd`
- 로그인 시에 출력하는 배너 정보 -> 확인필요

# 로그 파일 (책임 추적성) 및 주요 명령어 사용

## 1. **로그파일**
+ WORM(Write Once Read Many) Storage : 한번만 기록하고 그 뒤로 읽기만 가능
    * 로그파일 삭제,변조 불가 / 고비용
+ utmp : 현재 사용자 정보를 가진 로그파일
    * `/var/run/utmp`
    * `w`, `who` : 현재 사용자 정보를 utmp 파일을 읽어 출력
+ btmp : 로그인 실패 정보
    * `/var/log/btmp`
    * `lastb` : 해당 파일은 바이너리로 되어있기 때문에 `lastb` 로 확인
+ wtmp : 로그인과 로그아웃 정보를 가진 로그파일
    * 로그인/로그아웃 정보, 시스템 종료(shutdown) 및 부팅(booting) 정보, 재부팅(reboot) 정보, telnet/ftp 로그인 정보
    * `/var/log/wtmp`
    * `last` : 로그인, 로그아웃 정보를 wtmp 파일을 읽어 출력
    * `last reboot | more` : 재부팅 시간 정보 확인하기
+ lastlog : 마지막 로그인
+ secure : 원격 접속 정보
+ syslog : 로그를 남기는 프로그램
    * `/var/log/` 디렉터리에 로그가 기록됨
    * syslogd 에 의해 로그가 기록됨.
    * `/etc/syslogd.conf` 파일을 읽어 로그 기록 수준을 결정함
    * syslog 주체별(Facility) 분류
        - kern : kernel 에서 요청하는 경우
        - mail : mail subsystem 에서 요청하는 경우
        - lpr : printing subsystem 에서 요청하는 경우
        - daemon : system server processes 에서 요청하는 경우
        - auth : login authentication system 에서 요청하는 경우
    * emerg > alert > crit > err > warn > notice > info > debug
        - kern.* : 커널 수준의 모든 로그를 기록하라.
        - mail.err : 메일에서 발생하는 err 수준의 로그를 기록하라.
+ xferlog : ftp 사용 정보
+ sulog : su 명령로그

## 2. 명령어 관련
+ cron : 작업 스케줄 관리
    * 일정한 날짜와 시간에 지정된 작업을 실행하는 프로세스
    * `/etc/crontab` 에 설정된 것을 읽어서 작업을 수행
    * 분 시 일 월 요일(0\~6/일\~토) 사용자 실행명령
        - `30 3 * * 2 root /home/clean.sh` : 매주 화요일 3시 30분에 /home/clean/sh 명령을 실행
        - `*/10 2-5 * * * root /home/user/abc` : 매일 2-5시 까지 10분마다 실행
        - `0 7 1 1-10/2 * /bin/vipcheck.sh` : 1-10월 두 달 주기로 1일 7시에 실행
    * crontab 옵션
        - `-l` : 현재 로그인 된 계정에 잡혀있는 작업 스케줄 확인
        - `-e` : 현재 로그인 중인 계정에 작업을 등록
        - `-r` : 현재 crontab에 등록된 작업을 삭제
        - `-u <USER명>` : 작업을 실행시킬 유저명 설정
        - 주의사항 : `crontab` 명령 실행 시 현재 입력되는 내용으로 덮어씀.
+ at : 예약한 명령어를 정해진 시간에 한 번만 실행
    * `at 18:00`
        - `hping --rand-source 127.0.0.1 -p 80 -S -flood > log.txt`
        - 18시에 TCP Syn DDos 공격을 하고 그 로그를 log.txt에 저장
    * at 옵션
        - `-l` : 현재 실행 대기 중인 명령의 목록을 출력
        - `-r` : 실행 대기 중인 명령에서 해당 작업번호를 삭제
        - `-f` : 표준 입력 대신 실행할 명령을 파일로 지정
+ 리다이렉션
    * `>` : 결과를 파일로 저장
    * `>>` : 해당 파일에 결과를 추가
+ 파이프
    * `명령어1 | 명령어2` : 명령어 1의 결과를 명령어 2의 입력으로 전달
+ find
    * `find /var/www -name "[a-z][a-z][0-9]*` : var/www 의 웹디렉터리에서 첫 번째 글자와 두 번째 글자는 영문자 소문자이고 세 번째 글자는 숫자로 되어있는 파일 찾기
    * `find . -name "* *" -exec rm -f { }\;` : 현재 디렉터리에서 파일 이름에 공백이 들어간 모든 문자를 삭제
    * `find / -perm +6000 2>/dev/null` : setuid, setgid 중 하나라도 부여된 것을 찾음
    * `find / - mtime -1` : 24시간 동안 변경된 파일 찾기
        - `-atime` : access time 으로 파일을 열거나 접근한 시간을 기준
        - `-mtime` : modify time 으로 파일이 변경된 시간을 기준
        - `-ctime` : change time 으로 파일속성이 변경된 시간을 기준
    * `find / -nouser -o -nogroup` : 소유자가 없거나 그룹이 없는 파일
+ 파일 무결성 검사
    * 파일 시간은 얼마든지 변조가 가능
    * 해시 값을 사용해서 파일의 무결성 검사 : tripwire
        - `tripwire --init` : 해시 값을 저장한 데이터베이스를 초기화
        - `tripwire --check` : 무결성 검사를 실행

# Run Level (부팅 단계)

## 1. Loader
+  보조기억장치에 있는 운영체제의 커널을 주기억장치에 올리는 작업
+  LILO (Linux Loader)
+  GRUB
    *  `/boot/grub/grub.conf`

    |설정 값|설명|
    |:---|:---|
    |`default=0`|부트 순서를 지정하는 번호로 멀티부트의 경우 운영체제 레이블 번호를 선택|
    |`timeout=0`|지정된 시간이 경과되면 default로 지정된 운영체제로 부팅|
    |`splashimg=(hd0,0)`</br>`/grub/splash.xpm.gz`|부팅 이미지를 지정하는 부분|
    |hiddenmenu|부트로더를 보여주지 않음|
    |title Fedora|부팅되는 부트 엔트리를 의미|

    *  부트정보를 사용자가 임의로 변경할 수 있음
    *  여러 운영체제를 사용할 수 있는 멀티부팅 지원
    *  커널 경로 및 파일명만 알고 있다면 부팅이 가능함

## 2. 리눅스 Booting 순서
1. 전원을 켜면 ROM BIOS를 읽고 디스크의 MBR(Master Boot Record)에 있는 부트로더(Boot Loader)가 실행된다.
2. 부트로더는 보조장치에 저장되어 있는 리눅스 커널을 찾고 실행한다.
3. 리눅스 커널은 하드웨어를 확인하고 root의 읽기전용으로 마운트를 수행한다.
4. 디스크를 검사하고 root의 쓰기전용으로 다시 마운트를 수행한다.
5. 리눅스 커널은 init 프로세스를 실행하고 PID 1번을 할당한다. init 프로세스는 자신의 설정파일인 /etc/initab 파일을 읽어서 디바이스 및 프로세스를 활성화 한다. init 프로세스의 작업 수행은 리눅스의 Run 레벨에 따라 다르게 부팅한다.

## 3. Run Level
+ `init [Run Level]`

    |실행단계|내용|
    |:---|:---|
    |0|PROM 감사 단계|
    |1|{::nomarkdown}<ul><li>관리상태 단계. 사용자 로그인의 접근이 불가능한 단일 사용자 단계. 여러 개의 파일 시스템이 로드되어 있음.</li><li>암호를 변경할 때 사용</li></ul>{:/}|
    |2|공유 자원을 갖지 않은 다중 사용자 단계|
    |3|기본 실행단계로 자원을 가진 다중 사용자 단계, 텍스트 유저모드|
    |4|현재 사용되지 않음|
    |5|Run Level 3로 기동 후에 그래픽 모드인 X-Windows 를 실행|
    |6|재부팅 단계로 실행단계 3의 상태로 재부팅|

## 4. 3.20 사이버테러
+ 2013년 3월 20일 방송국과 금융사를 대상으로 전산망을 마비시킨 사건
+ 악성코드는 kbs.exe, imbc.exe, sbs.exe 등 형태로 배포
+ 윈도우 계열 : MBR(Master Boot Record), VBR(Volume Boot Record)삭제
+ 리눅스(유닉스) : dd 및 rm 명령을 원격 전송해 디스크를 삭제

# **리눅스 방화벽**

## 1. iptables
+ 리눅스에서 특정 패킷을 분석해 패킷을 차단하거나 허용할 수 있다.
+ 패킷 필터링 정책
    * 모든 것을 허용한 후에 일부 패킷을 제한한다.
    * 모든 것을 거부한 후에 일부 패킷을 허용한다.
    * 패킷 필터링 chain 종류
        - INPUT : 외부에서 방화벽 서버로 들어오는 패킷 제어
        - FORWARD : 외부 패킷이 방화벽을 통과해 다른 네트워크로 가는 것 제어
        - OUTPUT : 방화벽에서 외부로 나가는 패킷 제어
+ 사용법
    * `-A` : `--append` : 규칙을 추가한다.
    * `-N` : `--new-chain` : 새로운 체인 생성
    * `-X` : `--delete-chain` : 체인 제거
    * `-P` : `--policy` : 체인 기본정책 변경
    * `-L` : `--list` : 체인의 규칙상태 보기
    * `-F` : `--flush` : 체인 내의 모든 규칙 제거 (방화벽 초기화)
    * `-Z` : `--zero` : 체인내의 모든 규칙의 패킷과 바이트의 카운트를 0으로 초기화
    * `-D` : `--delete` : 규칙을 삭제
    * `-R` : `--replace` : 새로운 규칙으로 대체
    * `-I` : `--insert` : 체인 가장 처음에 규칙을 추가
    * `-E` : `--renmae-chain` : 체인의 이름을 변경
+ `ifconfig`
    * 네트워크의 인터페이스 정보를 확인하거나 시작, 종료할 수 있다.
    * eth0
    * lo : Loopback 주소 (127.0.0.1)
        - `iptables -A OUTPUT -o lo -j DROP` : 루프백 차단
+ `iptables -L` : 등록된 접근 제어를 확인
+ `iptables -A INPUT -s 192.168.0.3 -j DROP` : 특정 소스 차단
+ `iptables -A FORWARD -p udp -m udp --dport 53 -j DROP` : DNS 차단

# 유닉스 구조

## 1. 파일 시스템 구조

|구성 항목|세부 내용|
|:---|:---|
|VTOC 디스크 레이블|각 파티션의 기본 정보|
|부트블록|부트스트랩에 필요한 파일들|
|프라이머리 슈퍼블록|데이터 블록의 개수, 실린더 그룹의 개수, 마운트 정보|
|백업 슈퍼블록|각 실린더마다 슈퍼블록에 대한 복사본을 가짐|
|실린더 그룹|슈퍼블록, 실린더 그룹 블록, i-node 테이블, 데이터 블록을 포함|
|슈퍼블록|파일 시스템 크기, i-node 테이블의 크기, 빈 블록 리스트 등 파일 시스템 관리 정보|
|실린더 그룹 블록|실린더 그룹 내의 유효 블록들의 비트맵 정보나 통계 정보|
|i-node 테이블|파일에 대한 중요한 정보, 파일 크기, 위치, 유형, 사용 허가권, 날짜 정보|
|데이터 블록|실제 데이터가 저장되는 공간|

### 1. **리눅스 파일 시스템 구조**
* 부트 블록 (Boot Block)
    - 파일 시스템으로부터 리눅스 커널을 적재시키기 위한 프로그램
* 슈퍼 블록 (Super Block)
    1. 파일 시스템의 크기, 블록 수 등 이용 가능한 빈 블록 리스트(list)
    2. 빈 블록 리스트에서 그 다음의 빈 블록을 가리키는 인덱스
    3. inode 목록의 크기, 파일 시스템에 있는 빈 inode의 수와 목록
    4. 빈 inode 목록에서 그 다음의 빈 inode의 수와 목록
    5. 슈퍼 블록들이 수정되었는지 나타내는 플래그
    6. 파일 시스템 이름과 파일 시스템 디스크의 이름
* 아이노드(inode)
    - 파일이나 디렉터리에 대한 모든 정보를 가지고 있는 구조
    
* 데이터 블록(Data block)
    - 실제 데이터가 저장되어 있는 파일 형태

### 2. **아이노드(inode)**
* inode Number : 유일한 일련번호 값, 파일 관리를 위함
* inode 가 가진 정보
    - 파일 소유자의 사용자 ID
    - 파일 소유자의 그룹 ID
    - 파일 크기
    - 파일이 생성된 시간
    - 최근 파일이 사용된 시간
    - 최근 파일이 변경된 시간
    - 파일이 링크된 수
    - 접근모드 (퍼미션)
    - 데이터 블록 주소


![Symbolic Link vs Hard Link](/assets/images/posts/sym_hard.png)

[이미지 출처](https://dev.to/robogeek/reflinks-vs-symlinks-vs-hard-links-and-how-they-can-help-machine-learning-projects-1cj4)

* 심볼릭 링크 :  
    - 링크정보만 가진 새 inode 가 만들어지고 inode는 원래 파일의 포인터(주소)정보만을 가짐.
    - 원본 파일 삭제시 삭제됨.
    - `ln -s 대상파일 심볼릭링크`
* 하드 링크 :  
    - 원본 파일을 복사해서 동일한 inode를 만듦.
    - 원본 파일을 삭제하거나 이동해도 하드링크는 존재
    - `ln 대상파일 하드링크`
    - 원본 파일과 링크 정보는 다른 개념으로 하드 링크 시 원본파일은 하나만 존재.
* `ls -il` 로 inode number 를 확인할 수 있음.


## 2. 파일 시스템 종류
+ Ext(Extended File System)
    * 최대 2GB
    * 255byte 파일명
    * 접근제어, inode 수정, 타임스템프 수정 등의 기능이 불가
    * 사용할 수록 단편화가 심해짐
+ Ext2(Second Extended File System)
    * 파일 시스템은 2GB까지, 볼륨 크기는 32TB까지 지원, 서브 디렉터리 개수 제한이 대폭 증가(32768개)
    * FSCK를 사용한 파일 시스템 오류 수정을 지원
    * 캐시의 데이터를 디스크에 저장 중 오류 발생 시 파일 시스템에 손상이 올 수 있다. (Sync 이전 데이터 손실)
    * FSCK 이용한 파일 복구 시간에 많은 시간이 소요됨(전체 섹터 검사)
+ Ext3(Third Extended File System)
    * EXT2에 저널링 기능 추가 및 온라인 파일 시스템이 증대
    * 파일 시스템 변경 시 저널에 먼저 수정 내용을 기록함(갑작스런 다운 시 빠르게 오류 복구)
    * 온라인 조각 모음이 불필요(장시간 사용 시 조각화 발생)
    * 디스크 조각화를 최소화
+ Ext4(Fourth Extended File System)
    * 16TB까지 파일 시스템을 지원, 볼륨은 1엑사바이트까지 지원
    * Block Mapping 방식 및 Extends 방식을 지원
    * 저널 Checksum 기능이 추가되어 안전성 강화
    * 하위 호환성 지원 : ext3, ext2와 호환
    * Delayed allocation : 디스크에 쓰이기 전까지 블록 할당을 미루는 기술로 조각화 방지에 효과적
    * 온라인 조각 모음 : 조각화 방지를 위한 커널 레벨의 기술
    * Persistent pre-allocation : 파일 전체만큼의 공간을 사전 할당. 스트리밍, 데이터베이스 등에 유용

## 3. **운영체제 핵심 구성요소**

|구성요소|내용|
|:---|:---|
|커널(Kernel)|{::nomarkdown}<ul><li>주기억장치(Main Memory)에 상주하면서 사용자 프로그램을 관리하는 운영체제의 핵심 역할</li><li>커널은 프로세스, 메모리, 입출력(I/O), 파일 관리 등을 수행</li></ul>{:/}|
|셸(Shell)|{::nomarkdown}<ul><li>명령어 해석기/번역기로 사용자 명령의 입출력을 수행하며 프로그램을 실행</li><li>셸의 종류에는 Bourne 셸, C 셸, Korn 셸 등이 있고 리눅스 표준 셸 인 bash 셸이 있음</li></ul>{:/}|
|파일 시스템(File System)|여러 가지 정보를 저장하는 기본적인 구조이며, 시스템 관리를 위한 기본 환경을 제공하고, 계층적인 트리 구조 형태(디렉터리, 서브 디렉터리, 파일)|

## 4. bash 셸 환경 설정

|전역 설정파일|설명|
|:---|:---|
|`.bash_profile`|{::nomarkdown}<ul><li>사용자 홈 디렉터리에 있는 파일</li><li>개별적인 셸 환경을 설정</li></ul>{:/}|
|`.bashrc`|사용자 정의 변수, 함수 alias 를 정의|
|`.bash_logout`|사용자가 로그아웃할 때 실행되는 파일을 정의|

+ `/etc` : 리눅스를 사용하는 모든 사용자에게 적용되는 환경파일
+ `Home Directory` : 사용자별 설정파일
+ 셸 환경변수
    * `env` : 현재 환경에 설정되어 있는 모든 변수를 확인할 수 있음
    * `echo $[환경변수명]` : 환경변수명에 해당하는 변수값을 확인
+ Shell Shock
    * 악의적 명령실행, 관리자 권한 획득 등
    * 리눅스 환경변수에 빈 함수를 넣으면 그 뒤에 오는 코드는 무조건 실행됨
    * `env x='(){ :;}; echo vulnerable' bash -c "echo print this"`

## 5. 리눅스 디렉터리 구조

|디렉터리 구조|설명|
|:---|:---|
|`/`|루트 디렉터리|
|`/bin`|기본적인 실행 명령|
|`/boot`|LILO 등 부팅에 관련된 파일|
|`/dev`|장치 파일 모음|
|`/etc`|시스템 설정 파일|
|`/home`|사용자 홈 디렉터리|
|`/lib`|C 라이브러리|
|`/mnt`|임시 마운트용 디렉터리|
|`/proc`|시스템 정보를 가진 가상 디렉터리|
|`/root`|루트 사용자의 홈 디렉터리|
|`/sbin`|시스템 관리용 실행파일|
|`/tmp`|임시파일 디렉터리|
|`/usr`|애플리케이션이 설치되는 디렉터리|
|`/var`|시스템에서 운영되는 임시파일 및 로그파일|

|usr 디렉터리 구조|설명|
|:---|:---|
|`/usr/X11`|Link|
|`/usr/bin`|Common Programs|
|`/usr/doc`|Documentation|
|`/usr/etc`|Configuration files|
|`/usr/lib`|Shared libraries|
|`/usr/local`|Other applications|
|`/usr/man`|Man page|
|`/usr/sbin`|System administration programs|
|`/usr/share`|Shared information|
|`/usr/src`|Source code|

|Device 파일 시스템|설명|
|:---|:---|
|`/dev/fd`|플로피 디스크|
|`/dev/hda`|마스트 IDE 하드 디스크|
|`/dev/sda`|SCSI 및 SATA 하드 디스크|
|`/dev/cdrom`|CD ROM 드라이버|
|`/dev/mouse`|마우스|
|`/dev/hdb`|슬레이브 IDE 하드 디스크|
|`/dev/hd`|하드 디스크|

|proc 파일 시스템|설명|
|:---|:---|
|`/proc/buddyinfo`|버디 할당자 정보|
|`/proc/cmdline`|시스템 부팅 중 커널에 제출된 명령 내용|
|`/proc/cpuinfo`|시스템 cpu 정보|
|`/proc/devices`|디바이스들의 목록|
|`/proc/diskstats`|디스크의 상태 및 파티션 정보|
|`/proc/dma`|DMA 채널|
|`/proc/filesystems`|파일 시스템의 목록|
|`/proc/interrupts`|인터럽트에 대한 통계|
|`/proc/iomem`|할당된 메모리 영역|
|`/proc/ioport`|입출력 포트 정보|
|`/proc/kallsyms`|심볼들의 목록|
|`/proc/loadavg`|CPU 부하와 관련된 정보
|`/proc/locks`|잠금 파일|
|`/proc/mdstat`|RAID 시스템의 정보를 제공|
|`/proc/meminfo`|시스템 메모리 정보|
|`/proc/modules`|사용중인 모듈들|
|`/proc/partitions`|파티션 정보|
|`/proc/slabinfo`|슬랩 할당기 통계 정보|
|`/proc/swaps`|스왑 파티션에 대한 정보|
|`/proc/uptime`|시스템이 부팅된 이후의 시간|
