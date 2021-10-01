---
title: 정보보안 - HTTP
date: '2021-06-25 18:00:00'
tags:
- 정보보안기사
- 어플리케이션
related: true
categories:
- IS_Certification
toc: true
---

# Web 보안

## HTTPS
- 개요
    + SSL을 이용한 HTTP (`https://...`)
    + http 는 80 포트, https 는 443 포트
    + 기밀성, 클라이언트와 서버간 인증, 무결성 기능 제공
    + 암호화
        * 요청문서 URL
        * 문서내용
        * 브라우저 입력 양식 내용
        * 쿠키
        * HTTP 헤더 내용
    + 스니핑이 어렵지만, 사용자 입력 값에 대한 검증이 어렵다.

## S-HTTP
- 개요
    + Secure-HTTP : EIT 에 의해 1994년 정의된 프로토콜
    + SSL과 다르게 HTTP 메시지가 암호화되어 TCP/IP 망을 통해 전송 (`shttp://`)
    + HTTP 메시지 캡슐화 (암호화), 헤더 추가 (메시지형식, 부호화 방식)
- 과정
    + 웹 클라이언트에서 HTTP 메시지를 암호화하여 SHTTP 메시지로 변환
    + 웹 클라이언트에서 서버로 전송
    + 웹 서버에서 SHTTP 메시지를 복호화 하여 HTTP 메시지로 변환
- 특징
    + HTTP 트랜잭션의 특징 (요청-응답) 을 유지
    + 협상을 통해 암호화 방식 등을 결정 가능

## IIS
- 자원 권한설정 (파일, 폴더)
    + 읽기, 쓰기, 실행 기능 부여 / 회수
        * 예 : Upload 폴더에 실행권한을 부여 -> Webshell 실행 위험
- 관리자 페이지 접근통제
    + 무작위 공격 (Brute Force) 에 노출될 수 있음
    + 관리자 권한 획득 관련 취약점 노출될 가능성이 있음
    + 관리자 페이지 접근 IP 설정 필요
- 메소드 제한 (GET, POST) 설정
    + DELETE, HEAD, PUT 등등의 메소드는 제한하는 것이 좋음
    + 확장 매핑 메뉴 -> 제한 입력창에 기록
- 헤더정보 숨김
    + 서버의 헤더정보를 이용해 취약점 공격이 가능
    + 어드민 등록정보에 http 헤더를 입력하여 설정
    
## httpd.conf (Apache 보안 설정 파일)
- 자원 권한설정 (파일, 폴더)
- 관리자 페이지 접근통제
    + `<Directory "/var/www/html/admin">`
- 메소드 제한 (GET, POST) 설정
    + `<LimitExcept GET POST>`
        * `order allow, deny`
        * `Deny from all`
    + `</LimitExcept>`
- 헤더정보 숨김
- 옵션 설명
    + ServerType : (standalone, inetd)
        * standalone : 사용자 요청을 웹데몬이 받음
        * inetd, xinetd : 사용자 요청을 수퍼데몬이 받음
    + User nobody, Group nobody
        * User, Group 설정 지시자는 홈페이지 서비스를 직접 담당하는 Apache 의 자식 프로세스들의 실행소유자, 소유그룹을 어떤 게정에 부여할 것인지를 결정
        * 보안을 위해 nobody 로 설정
    + Options : 지정한 디렉토리 이하에 모든 파일, 디렉토리에 적용할 접근제어 설정
        * 디렉토리 목록을 보여줄지, CGI를 허용할 것인지 등등의 것들을 설정
        * `-index` : 디렉토리 목록을 보여주지 않음
        * `-FollowSymlinks` : 심볼릭링크를 허용하지 않음 : 링크파일의 경로까지 확인할 가능성이 있음
    + ServerSignature : (On, Off)
        * 서버 배너 정보의 노출 여부
        * Off 로 설정하여 서버 정보를 숨김
    + ServerTokens : (Prod, Min, OS, Full)
        * 서버 정보표시 제한
        * Prod : ProductOnly -> 웹서버의 종류만을 표시
        * Min : Minimal -> 웹서버 종류와 버전정보 표시
        * OS : 웹서버 종류, 버전, OS 정보 표시
        * Full : 웹서버 종류, 버전, OS 정보, 설치 모듈 표시




