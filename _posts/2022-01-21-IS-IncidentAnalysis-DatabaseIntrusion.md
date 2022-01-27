---
title: 정보보안 - 데이터베이스 침해사고
date: '2022-01-21 09:00:00'
tags:
- 정보보안기사
- 데이터베이스 침해사고
related: true
categories:
- IS_Certification
toc: true
---

# 데이터베이스 침해사고
- Data, 데이터베이스, DBMS
    + 데이터와 정보
        * 데이터 : 정보작성을 위한 필요자료, 특정목적에 대해 평가되지 않은 상태, 단순한 사실
        * 정보 : 데이터를 추출, 분석, 비교 등의 가공절차를 통해 의사결정, 행동을 할 수 있는 의미있는 데이터
    + 데이터베이스 : 데이터의 집합, 여러 응용시스템이 공유. Integrated, Stored, Operational
    + DBMS : 데이터를 통합적으로 생성, 관리, 저장하는 소프트웨어. MySQL, MSSQL, ORACLE, DB2 등
        * MySQL 서버가 침해사고의 대상이 되는 경우가 많음. (PHP)
- MySQL 점검 항목
    + 관리자 아이디를 default 로 사용하는지 점검
        * 기본 패스워드 값은 누구나 알고 있음
        * 무차별대입 / 사전대입 공격 - 추측 어려운 패스워드를 사용해야 함
        * `mysql -uroot -p` 
    + 패스워드 설정 안된 계정 존재하는지 점검
        * `select user, password from user`
        * `update user set password = [] where user = []` 
        * `flush privileges;` : 변경사항을 즉시 반영
    + Remote 에서 MySQL 서버로 접속이 가능한지 점검
        * 기본 포트 : 3306/tcp
            - `my.cnf` 파일의 `[mysqld]` 에 `skip-networking` 추가 : 로컬 접속만 가능
    + MySQL 계정을 이용한 접속 가능한지 점검
        * `/etc/passwd` 의 쉘 부분을 `/bin/sh` 대신 `/bin/false` 로 변경하여 원격 접속을 차단
    + 시스템 사용자들의 DB에 대한 권한 설정이 올바른지 점검
        * 필요 이상의 권한을 부여하지 않아야 함
        * `select * from DB;` 
        * 불필요한 권한을 `Revoke` 시킴
        * `update db set update_priv = 'N' where db=[]`
        * `flush privileges;` 
    + 데이터베이스 내의 사용자별 접속/권한 설정이 올바른지 점검
        * dba 가 아닌 일반 user 가 중요 table 을 조작할 수 있음
        * `select * from user;`
            - Host | User | Password | Select_priv | ... | Process_priv | File_priv |
            - File_priv : 파일에 대한 권한
            - Process_priv : 쓰레드 정보 확인 (쓰레드 중지 권한 포함)
            - Shutdown_priv : MySQL 서버의 실행을 중지시키는 권한
        * localhost 의 root 계정을 사용하지 않고 다른 DBA 계정을 만들어 사용하는 것이 바람직함
        * 원격 Host 에서 root 계정등의 접근설정 확인
            - `update user set process_priv="N" where host = [ip] and user = "root";`
    + 안정적인 MySQL 버전을 사용하고 있는지 점검
    + MySQL 의 데이터 디렉터리는 안전하게 보호되고 있는지 점검
        * `my.cnf` 파일에 대한 접근통제도 필요
- MS-SQL 점검 항목
    + 계정의 패스워드 중 빈 패스워드나 약한 패스워드가 존재 하는가?
        * `select name, password from syslogins;` : 결과 NULL 일 경우 패스워드가 없음. 단 Builtin 계정은 NULL 이 정상
    + guest 계정이 비활성화 되어 있는가?
        * SQL 서버의 Enterprise Manager 를 실행하여 guest 계정 확인, 삭제
    + public 데이터베이스 역할이 부여되어 있는가?
        * public 역할 : 모든 데이터베이스 사용자들의 표준에 해당되는 역할. 인가를 받지 않은 사용자도 권한을 행사할 수 있는 취약점
    + SYSADMIN 그룹의 사용자를 인증된 사용자만으로 제한하고 있는가?
        * System Administrator 권한
    + 방화벽에서 SQL Server 포트를 차단했는지?
        * TCP/1433 or 1434
    + 가장 최신의 서비스 팩을 설치했는가?
        * 서버 보안을 위함
    + Microsoft Baseline Security Analyzer(MBSA) 로 서버를 점검했는가?
        * 보안패치가 최신으로 유지되는지 확인
        * 보안상 약점이 될만한 설정이 있는지 확인
        * 기본 취약점 점검하는 도구
    + SQL Server 연결에 대한 감사를 수행하는가?
        * 이벤트 정보 로그 기록
    + 웹서버 확인 필요 항목
        * DB 접속 파일 확인
            - 예 : conn.asp 파일 침해
                + DB 서버의 IP 주소, Host Name
                + DB 접근 게정 (ID)
                + DB 서버 접근 패스워드 (PW)
            - sqlpoke.exe 프로그램을 업로드, 이용, 위에 획득한 정보로 접속
        * MS-SQL 서버 의 디폴트 계정 중 sa (system admin) 계정 이용
            - 서버 내부의 Administrator 계정으로 명령어를 전달할 수 있는 계정
        * net user 명령어 사용
            - remote 에서 DB 서버에 계정을 생성 가능 (우회적으로 DB서버에 접근)
        * SQL Injection 공격
            - IIS 웹로그 등을 통해 침입 흔적을 확인해야 함