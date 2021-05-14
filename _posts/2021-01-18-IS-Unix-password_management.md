---
title: 정보보안 - 유닉스 패스워드 관리
date: '2021-01-18 15:00:00'
tags:
- 정보보안기사
- UNIX
related: true
categories:
- IS_Certification
toc: true
---

# 유닉스 사용자 패스워드 관리

- `/etc/passwd`
    + user_account : user_passwd : user_id : group_id : comment : home_directory : login_shell
        * user_passwd : X 는 shadow 파일을 이용한다는 의미
        * user_id : 0 일 경우 root
        * group_id : 0 일 경우 관리자 그룹
        * login_shell : 로그인 계정이 아닌 경우 (시스템계정)
            - `/sbin/nologin`
            - `/bin/false`  

- `/etc/shadow`
    + user_account : encrypted_password : last_change : minlife : maxlife : warn : inactive : expires : 
        * encrypted_password : $1$xdBAee04$z1FE9wTQ0cXiFFU7E0gB
            - `$` + 1 : id
                + 해시 알고리즘의 종류
                + 1 : MD5
                + 2 : BlowFish
                + 5 : SHA256
                + 6 : SHA512
            - `$` + xdBAee04 : salt
                + 같은 비밀번호여도 salt 값이 다르면 다른 패스워드 생성
            - `$` + z1FE9wTQ0cXiFFU7E0gB : encrypted-password
            - 리눅스
                + 정상암호
                + 특수문자 `*` 로 표기 : 패스워드가 잠긴 상태여서 로그인 불가하나 다른 방법으로 로그인할 수 있음 (예: SSH)
                + 특수문자 `!!` 로 표기 : 사용자 계정 생성을 하였으나 패스워드 미생성
                + `!!` + $1$xdBAee04$z1FE9wTQ0cXiFFU7E0gB : 패스워드 잠겨서 로그인불가
                    * `passwd -l [계정명]` : 패스워드 락
                    * `passwd -u [계정명]` : 패스워드 언락
                + 비어있는 경우 : 패스워드 미설정, 패스워드 없이 로그인 가능
            - 유닉스
                + 정상암호
                + NP : No Password > 로그인 불가 계정 (시스템 계정 등)
                + `*LK*` : Lock > 패스워드가 잠긴 상태
                + 비어있는 경우 : 패스워드 미설정, 로그인 시 패스워드 설정작업진행
        * last_change : 마지막 수정일 (1970년 1월 1일부터 경과일 수)
        * minlife : 패스워드 최소 사용기간 (변경 이후 변경불가 일수)
        * maxlife : 패스워드 최대 사용기간 (예: 90일)
        * warn : 패스워드 만료 이전 경고일수
        * inactive : 계정이 잠기기 전까지 비활성화 일수
           - 리눅스 : 비활성화 일수 동안 패스워드 미변경 시 잠김
           - 유닉스 : 비활성화 일수 동안 로그인을 하지 않으면 잠김
        * expires : 계정 만료일 (1970년 1월 1일부터 경과일 수)
