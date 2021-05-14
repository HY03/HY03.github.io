---
title: 정보보안 - 유닉스 시스템 접속
date: '2021-01-08 15:00:00'
tags:
- 정보보안기사
- UNIX
related: true
categories:
- IS_Certification
toc: true
---

# 시스템 접속

시스템 접속하기
- telnet, ssh 등으로 접속
- 로그인 프로그램 으로 로그인 (ID/PW)
    + `/etc/passwd` 와 비교
- 환경변수 값 참조
    + HOME : 홈 디렉토리
    + SHELL : 실행 쉘 경로
    + USER : 로그인 계정
    + LOGNAME : 로그인 계정

# 사용자 기본 명령어
쉘의 종류 확인
- `echo $SHELL`

암호 변경
- `passwd`

사용자 정보
- `/etc/passwd`
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
- uid, gid, Shell 부분의 주기적 확인이 필요

사용자 확인
- `id` : uid, gid 확인
- `id [사용자명]` : 사용자명에 대한 uid, gid 확인

사용자 전환
- `su [-] [user_account] [-c command]`
    + `-c` : 쉘을 실행하지 않고 주어진 명령만을 수행
    + `-s` : 지정된 쉘로 로그인
    + `-`, `-i` : 지정한 사용자의 환경변수를 적용하여 로그인
- $`su ab` -> 사용자 전환 (비밀번호 입력)
- $`su - ab` -> 사용자 전환 + login shell (환경변수설정) (비밀번호 입력)
- #`su ab` -> 사용자 전환 (비밀번호 입력 불필요)
- $`su -c 'cat /etc/shadow' - root`: root 권한으로 명령어 실행
- $`su -s /bin/csh - root` : c쉘로 root 권한 획득

그룹정보
- 그룹명과 그룹 ID
    + 사용자는 하나 이상의 그룹에 소속될 수 있음
- 기본 그룹과 보조 그룹
    + /etc/passwd 파일의 네 번째 필드는 사용자가 소속된 기본 그룹(GID)
    + /etc/group : 보조 그룹
- /etc/group 파일
    + group_name : unavailable : group_ID : user_account_entry_in_group
    + 그룹명 : 패스워드(사용 X) : 그룹 ID : 그룹을 보조그룹으로 사용하는 사용자계정

쉘(SHELL)
- 사용자는 커널에 직접 명령을 내리기 어려움
- 운영체제의 일부인 쉘이 사용자로부터 명령을 입력받아 그것을 해석하고 실행
- 사용자가 운영체제를 사용할 수 있도록 환경을 만들어 줌

쉘의 기능
- 입출력 재지정
    + 표준입력, 표준출력, 표준에러를 사용하지 않고 다른 경로인 파일로 재지정 
        * 키보드 입력 대신 파일로 입력을 받음
        * 모니터 출력 대신 파일로 출력결과를 받음
    + `ls -l > ls.txt` : ls -l 명령의 결과를 ls.txt 파일로 출력 저장
    + `who >> a.txt` : 결과를 해당 파일에 append 시킴
- 파이프
    + 둘 이상의 결과를 함께 묶어 출력 결과를 다른 프로그램의 입력으로 전환
    + `ps -ef | grep telnet` : ps -ef 명령의 결과를 grep 명령의 입력으로 전달
    + `ls -l | more`
- 특수문자 (Meta Character)
    + `~` : 홈 디렉터리
    + `#` : 주석
    + `$` : 쉘 변수
    + `&` : 백그라운드작업
    + `*` : 문자열와일드카드
    + `?` : 문자열와일드카드(1문자)
    + `;` : 쉘명령 구분자
    + `|` : 파이프
    + `<>` : 입출력재지정
    + `''` : 특수문자로서의 사전정의를 제거, 단순 문자로 취급
    + `\` : 바로 다음 문자의 사전적 정의를 제거
    + `\`\`` : 문자열을 명령어로 인식하게 해줌
- 환경변수 (명령)
    + `printenv` : 설정된 환경변수들을 모두 확인
    + `printenv 변수명` : 주어진 환경변수 확인
    + `echo $변수명` : 주어진 환경변수 확인
    + `# export PATH=/bin/local;$PATH` : 환경변수값 설정 / 변경 
- 환경변수 (목록)
    + TERM : 터미널의 종류
    + HOME : 홈디렉토리 지정
    + SHELL : 쉘의 종류
    + PATH : 파일 검색 경로 설정
    + USER, LOGNAME : 사용자 계정
    + HOSTNAME : 호스트이름
    + MAIL : 메일이 저장된 파일 경로
    + MANPATH : 온라인 메뉴얼 경로
    + TZ : 타임 존 설정
    + LD_LIBRARY_PATH : 라이브러리 경로
    + LANG : 언어 지정
- 설정파일 위치
    + 대부분 Home 디렉터리에 있음
    + `.` : 숨김 파일
    + `rc` : Resource Configuration
    + 새로운 사용자 등록 시 /etc/skel 아래의 설정파일을 홈 디렉터리에 복사함
    + `.bashrc` : 서브쉘, 쉘이 실행될 때마다 실행되는 쉘
    + `.bash_profile` : 로그인할 때마다 읽는 설정파일
    + `.bash_logout` : 로그아웃할 때 읽는 설정파일

#유닉스 파일시스템
- 파일시스템은 Partition(물리적) 내의 File system(논리적 구조) 으로 이루어짐.
    + Boot Block : 운영체제 부팅, 부트스트랩 코드
    + Super Block : 전체 파일시스템 관리를 위한 정보
    + i-node List : 각각의 i-node를 표 형태로 보유
    + Data Block : 실제 파일내용
- i-node List
    + i-node 를 가지고 있는 표
- i-node (index-node)
    + 각 파일/디렉터리에 대한 정보를 기억하는 120byte 고정 크기 구조체
    + i-node number
    + 파일타입
    + 접근권한
    + 링크카운트
    + 소유자 / 소유그룹
    + 파일크기
    + MAC Time
        * last Modification Time : 파일의 내용 최종수정시간
        * last Access Time : 최종접근시간
        * last Change Time : 파일의 속성 (i-node) 최종수정시간
        * `stat [파일명]` : MAC Time 확인
    + Block index (인덱스 필드)

    + 인덱스 필드 : Data Block 에 저장되어 있는 파일의 위치정보
    + 파일/디렉터리 생성 시 i-node 생성, i-node List에 등록

#파일링크
- 심볼릭링크
    + 다른 파일로의 경로를 가리키는 파일
    + 심볼릭 링크 파일이 삭제되어도 실제원본 파일에는 영향을 미치지 않음
    + 서로 다른 파일 시스템 내에서도 링크가 가능
    + `ln -s [원본파일] [심볼릭 링크 파일]`
    + i-node number 를 이용하지 않고 파일 경로를 기반
- 하드링크
    + 원본과 하드링크된 파일이 모두 수정되어 항상 같은 파일의 내용을 유지
    + 동일한 i-node number를 갖는 파일을 생성하는 것
    + `ln [원본파일] [하드 링크 파일]`
    + 하드링크는 i-node의 link-count 를 증가시킴
        * link-count 가 0이 되면 해당 파일의 데이터가 삭제
        * stat -> links : 0 -> 삭제
- 비교
    + 하드링크
        * original file -> i-node -> original data
        * hardlink file -> i-node -> original data
    + 심볼릭링크
        * symboliclink file -> i-node2 -> original file pointer (경로기반) -> original file -> i-node -> original data
        * ls 명령어 확인 시 : sl-aaa.dat -> aaa.dat
- i-node 값 확인
    + `stat aaa.dat`
    + `ls -i aaa.dat`

# 디렉토리 관리
- 파일 종류
    + 일반 파일 / 정규 파일
        * 데이터 또는 프로그램 코드에 해당하는 바이트 스트림
        * 표준 파일 입출력 시스템 호출을 통해 참조
    + 디렉터리 파일
        * 특별한 형식으로 디스크에 저장
        * i-node number 를 보유
        * 디렉터리의 명시적인 시스템 호출을 통해 참조 (ls 등)
    + 특수 파일
        * 프린터, 터미널, 디스크와 같은 주변장치
        * 파이프, 소캣 같은 프로세스 간 상호 통신 기법
        * 표준 입출력 시스템 호출을 통해 참조

- ls
    + 디렉터리는 i-node number 정보를 가지고 있음
    + i-node number를 통해 구조체 정보 참조
    + `-l` : 목록 형태로 정보를 자세히 출력
    + `-a` : 도트파일(숨김파일) 을 포함한 모든 파일 출력
    + `-R` : 하위 디렉터리 내용까지 보여줌
    + `-F` : 디렉터리인지 어떤 파일인지 알려줌
    + `-i` : i-node number를 출력
    + `-s` : 크기순 정렬
    + `-t` : 수정 시간 순 정렬
    + `-r` : 출력 결과 역순 정렬

- 파일유형
    + `?rwxrwxrwx` 의 ? 에 들어갈 값
    + d : 디렉터리
    + b : block 장치파일 - 하드디스크, CD-ROM 등 블록으로 Random Access
    + c : 문자 장치파일 - 터미널, 스캐너, 키보드, 프린터 등
    + l : 심볼릭 링크파일
    + p : 파이프 파일
    + - : 일반 / 정규파일
    + s : UNIX 소켓 파일

- 파일 관련 명령어
    + cd : 디렉터리 간 이동
        * / : 절대경로 (루트 디렉터리 기준)
        * . : 상대경로 (현재 디렉터리 기준)
    + pwd : 현재 디렉터리 확인
    + mkdir : 새로운 디렉터리 생성
        * mkdir -p a/b : 계층구조 한번에 생성
    + rmdir : 기존 디렉터리 삭제
        * rmdir -p aa/bb : 계층구조 한번에 삭제, 하위 디렉터리나 파일이 없어야 함.
    + mv : 파일 이동
        * mv text1 data1 : 파일명 변경 (덮어쓰기)
        * mv text1 directory1 : 파일 이동
        * mv a/test.c a/test2.c directory1 : 여러개 파일 이동
    + cp : 파일 복사
    + rm : 파일 삭제
        * -r : 디렉터리 삭제
    + ln : 파일 링크

- 파일 권한 관리 관련 명령어
    + chmod : 접근권한 변경
        * 슈퍼유저 (root), 해당 파일 소유자 만 변경 가능
        * u : user / g : group / o : others / a : all
        * r : read(4) / w : write(2) / x : execute(1)
        * + : add / - : remove / = : assign
        * chmod g+x test.c : test.c 의 그룹에 실행권한 추가
        * chmod a=rw test.c : test.c 의 모든사용자에 읽기/쓰기권한 설정
        * chmod 777 test.c : 모든사용자에게 rwx 권한을 부여
    + chown : 소유자 변경
        * 슈퍼유저 (root) 만 명령어 사용 가능
        * chown root test.c 
        * chown -R 소유자 디렉터리  : 디렉터리 하위 모두 적용
        * -h : 심볼릭링크 자체의 소유자 변경 
    + chgrp : 소유 그룹 변경
        * 슈퍼유저 (root) 만 명령어 사용 가능
    + umask : 접근권한 마스크
        * 앞으로 생성될 파일에 대한 접근권한 지정
        * /etc/profile 에서 umask 지정
        * umask 0022 : 명령어를 통해 0022 로 지정
        * 0022 일 경우
            - 파일 : 666-022 = 644
            - 폴더 : 777-022 = 755
    + touch
        * touch test.c
        * (파일이 없을 때) 비어있는 파일을 생성
        * (파일이 존재할때) 시간이 변경됨
            - touch -m 파일명 : modify + change
            - touch -a 파일명 : access + change
            - touch -c 파일명 : 파일이 없으면 생성하지 않는다.


- 파일 검색
    + `find [찾을 디렉터리 경로] [찾기 옵션]`
    + 디렉터리 경로
        * `/` : 루트에서 검색 (시스템 전체)
        * `.` : 현재 디렉터리를 포함하여 하위 디렉터리까지 검색
        * `/directory` : 지정된 디렉터리에서 검색
    + 옵션
        * `-name [파일이름(정규식)]` : 파일명 또는 확장자 기준 검색
        * `-perm [권한]` : 권한과 일치하는 파일
            - `find / -user level2 -perm -4000` : -(최소한), 4(SetUID) 가 걸려있는 모든 파일을 검색
                + SetUID : 해당 파일을 실행할 때 파일 소유자의 권한으로 실행
                + RUID/RGID : 실제 명령을 수행한 사용자의 UID/GID
                + EUID/EGID : 명령을 실행중인 동안의 UID/GID
            - `find . -perm -1 -print` : 기타 사용자 실행권한이 있는 모든 파일 검색
        * `-user [유저]` : 소유자 일치
        * `-group [그룹]` : 그룹 일치
        * `-empty` : 비어있는 파일이나 디렉터리 검색
        * `-size [+파일크기/-파일크기/파일크기] [bckmgw중 택1]` : 파일크기와 일치하는 파일
            - b : 블록단위 512kb (디폴트)
            - c : byte
            - k : kbyte
            - w : 2byte 워드
        * `-type [파일타입]` : 파일의 타입을 지정하여 검색
            - b : 블록 특수 파일 (block device) - Random Access 가능 (HDD,CD-ROM)
            - c : 캐릭터 특수 파일 (character device) - 프린터 등
            - d : 디렉터리 (directory)
            - f : 일반파일 (file)
            - l : 심볼릭 링크(link)
            - p : 파이프(pipe)
            - s : 소켓(socket)
        * `-print` : 찾은 파일들을 표준출력(stdout-모니터)으로 출력, 기본값
        * `-nouser` : 소유자가 없는 파일을 검색 (`/etc/passwd` 파일에 없는 사용자)
        * `-nogroup` : `/etc/groups` 파일에 없는 그룹
        * `-fprint [임의파일명]` : 검색 결과를 임의 파일에 출력
        * `-exec [command] {} \` : 파일이 검색되었을 경우, 검색된 파일들에 대해 특정 명령 수행
            - `{}` : 검색된 파일을 의미. 여러 개의 파일이 검색되면 하나씩 치환되면서 해당 명령어를 수행
            - `;` : 검색된 결과가 여러개인 경우 하나의 행에 여러 명령을 사용하기 위함
            - `\` : ; 이 특수문자이기 때문에 문자로 ;라는 것을 알려주기 위함
            - `find . -name kkk* -exec rm {} \;` : kkk로 시작하는 파일을 검색하여 삭제
            - `-ok` : -exec와 동일한 작업, 단, 실행할 때마다 실행 의사를 물어봄
            - MAC Time 조회
                + `find -type f -mtime -1 -exec ls -al {} \;`
                + `+` : n일 초과
                + `-` : 1일 미만
            - 용량 조회
                + `find -type f -size +5M -exec ls -al {} \;`
                + `b` : default (block)
                + `c` : bytes
                + `k` : kb
                + `M` : mb
                + `G` : gb