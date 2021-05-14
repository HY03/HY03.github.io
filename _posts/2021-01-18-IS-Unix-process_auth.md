---
title: 정보보안 - 유닉스 프로세스 실행권한
date: '2021-01-18 17:00:00'
tags:
- 정보보안기사
- UNIX
related: true
categories:
- IS_Certification
toc: true
---

# 유닉스 프로세스 실행권한

- 프로세스에 부여되는 번호
    + 프로세스 식별자(PID)
    + 실제 사용자 ID(RUID) 
    + 유효 사용자 ID(EUID) 
    + 실제 사용자 그룹(RGID)
    + 유효 사용자 그룹(EGID)

- 사용 용도
    + 계정 관리에 사용 : RUID, RGID
    + 접근 권한 결정에 사용 : EUID, EGID (보안상 주의를 요함)
    + 일반적으로 실제 번호와 유효 번호는 동일함
    + 동일하지 않은 경우 : setUID, setGID

- 시스템에서 사용자가 명령 실행 시 명령어를 찾는 경로와 절차
    + 현재 디렉터리 확인
    + 명령어 없으면 `$PATH` 에서 명령어 찾기
    + 명령어가 있으면 실행권한 확인
        * 실행권한 있으면 명령어를 실행시킨 사용자 ID로 명령어 실행
        * 실행권한이 없으면 명령파일에 setUID 가 설정되어 있는지 확인
            - 소유주 권한으로 명령어 실행

- UNIX 시스템 파일 접근권한
    + UNIX 시스템은 파일에 대한 접근 권한, 파일 종류를 나타내기 위해 16bit 사용
    + 4bit 는 파일의 종류 표현에 사용
    + 3bit 는 특수권한에 사용
    + user, group, other 에 9bit 사용

- 특수권한
    + setuid : 4, setgid : 2, sticky bit : 1
    + 설정 예
        * `aaa rwxr-xr-x` : 755
            - `chmod 4755 aaa`, `chmod u+s aaa` : setUID
            - `chmod 2755 aaa`, `chmod g+s aaa` : setGID
            - `chmod 6755 aaa`, `chmod ug+s aaa` : setUID + setGID
    + 실행 예
        * `ls -al aaa` : `rwxr-xr-x`
        * `id` 의 결과가 uid : 502, gid : 506
            - `$aaa`
                + RUID:502, EUID:502, RGID:506, EGID:506
        * `#chmod ug+s aaa`
        * `ls -al aaa` : `rwsr-sr-x`
        * aaa 의 소유자가 root 라고 가정
            - `$aaa`
                + RUID:502, EUID:0, RGID:506, EGID:0

- setUID 의 보안상 문제점
    + root 권한이 필요없는 프로그램의 소유주가 root 이고 setUID 가 설정된 경우 취약
    + `find . -user root -perm -4000`
    + `chmod -s aaa` : setUID, setGID 모두 제거

- `passwd` 명령어
    + `ls -l /etc/passwd` : -rw-r--r-- root root
        * 644 권한, 패스워드 보관 파일
    + `ls -l /usr/bin/passwd` : -r-s--x--x root root
        * 4511 권한, 패스워드 변경 명령어 (root권한 이용)
        * `/etc/shadow` 파일 변경

- Sticky-bit
    + 대부분 /tmp 디렉터리에 설정 (공유디렉터리 : `tmp`, `/var/tmp`)
    + `rwxrwxrwx` : 모든 사용자들이 모든 권한을 가지게 됨
        * 다른 사람이 생성한 파일을 수정하거나 삭제할 수 있음
        * 파일을 자유롭게 생성하되, 수정, 삭제는 소유자만 가능하게 하고 싶음
    + 모든 사용자가 write 가능, 생성된 파일은 write 한 사용자의 소유
    + `chmod 1777 /tmp`, `chmod o+t /tmp` (리눅스) / `chmod u+t /tmp` (SunOS)
        * `rwxrwxrwt`
