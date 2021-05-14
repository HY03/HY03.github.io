---
title: 정보보안 - 유닉스 사용자 관리
date: '2021-01-15 15:00:00'
tags:
- 정보보안기사
- UNIX
related: true
categories:
- IS_Certification
toc: true
---

# 유닉스 사용자 관리

- 사용자 계정 관리
    + `useradd`, `adduser` : 사용자 계정 추가
        * `useradd [계정명]` : root 권한 수행
        * `passwd` : 비밀번호 설정
        * 홈 디렉터리 생성
        * 접근권한 추가
        * 사용자 로그인 시 필요한 시작파일을 홈디렉토리에 생성
    + `usermod` : 사용자 계정 변경
    + `userdel` : 사용자 계정 삭제
        * `userdel -r [유저명]` : 사용자 계정 삭제 + 홈디렉터리 삭제
    + `/etc/passwd` : 사용자 계정정보
    + `/etc/shadow` : 암호화된 비밀번호

- 그룹 관리
    + `groupadd` : 그룹 추가
        * `groupadd -g 520 test` : gid 520인 test 그룹 생성
    + `groupdel` : 그룹 삭제
    + `/etc/group` : 그룹 정보
