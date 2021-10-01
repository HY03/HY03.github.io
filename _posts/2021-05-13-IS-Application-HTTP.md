---
title: 정보보안 - HTTP
date: '2021-05-13 18:00:00'
tags:
- 정보보안기사
- 어플리케이션
related: true
categories:
- IS_Certification
toc: true
---

# Web 보안
- 웹프로토콜인 HTTP, TCP/IP 가 데이터에 대한 보안서비스를 제공하지 않음

## 웹트래픽 보안
- IPSec
    + 네트워크 계층에서 사용
- SSL/TLS
    + 범용 해결방안
    + 전송계층에서 사용
- S/MIME, Kerveros
    + 응용계층에서 사용

## HTTP 프로토콜
- 비연결형 프로토콜 (Connectionless Protocol)
    + Connection : Close 방식의 프로토콜 (1.0)
        * 과정
            - 웹브라우저에서 연결 요청 - 예 : index.php
            - TCP 연결설정 (3Way handshake)
            - HTTP 요청 Msg 전송
            - HTTP 응답 MSg 수신
            - TCP 연결종료 (4Way handshake)
            - 반복...
        * 제한된 서버의 연결자원을 최대한 활용하기 위함
    + Connection : Keep Alive 방식의 프로토콜 (1.1)
        * Request 헤더에 Connection:Keep-Alive 옵션 추가
        * 웹 서버에 연결을 유지할 시간을 설정
        * 과정
            - 웹브라우저에서 연결 요청 - 예 : index.php
            - TCP 연결설정 (3Way handshake)
            - HTTP 요청 Msg 전송
            - HTTP 응답 MSg 수신
            - 반복...
            - 일정 시간 경과 후
            - TCP 연결종료 (4Way handshake)
        * TCP 연결설정 / 연결종료 의 서버부하를 줄이기 위함
- Stateless 프로토콜
    + 상태정보를 유지하지 않는 프로토콜
        * 요청 상태정보를 가지고 있지 않음
        * 현재 요청 상태정보도 가지고 있지 않음
        * 현재 요청과 이전 요청을 구분하여 식별하지 않음

## HTTP 상태정보 유지기술
- 쿠키(Cookie) 방식
    + 과정
        * Client : (Web Browser) 웹서버에 HTTP 요청
        * Server : 클라이언트 식별을 위한 쿠키 생성
        * Server : HTTP 응답 (Response Header 내 Set-Cookie: id:test1234)
        * Client : HTTP 요청 (Request Header 내 Cookie: id:test1234)
    + 쿠키 지속시간에 따른 분류
        * 영속쿠키 : 파일에 쿠키정보 저장
        * 세션쿠키 : 클라이언트 메모리 상에 세션 (연결)이 유지되는 동안 저장, 웹브라우저 종료시 쿠키 소멸
- 세션(Session) 방식
    + 과정
        * Client : (Web Browser) 로그인 요청 (test1234)
        * Server : test1234의 세션객체 (#235@$r324) 생성 (timeout 값 설정 - 일정시간 요청없으면 소멸)
        * Server : HTTP 응답 (Response Header 내 Set-Cookie : session-id:#235@$r324)
        * Client : 웹브라우저 메모리에 세션쿠키값 저장
        * Client : Action (예: A상품 2개 선택) > (Request Header 내 Cookie:session-id:#235@$r324)
        * Client : Action (예: B상품 10개 선택) > (Request Header 내 Cookie:session-id:#235@$r324)
        * Server
            - SessionID : #235@$r324
                + ID : test1234
                + name : ABC
                + ...
                + cart list
                    * A : 2
                    * B : 10
    + 쿠키 방식보다 안전
    + Session Hijacking 공격 : SessionID 탈취
- HTTP 쿠키(Cookie) 보안속성
    + 서버에서 응답할 때 사용
    + httponly : XSS (Cross Site Script) 공격 대비
        * 세션쿠키 탈취 방지
        * Web Server 에서 Client (Web Browser) 에 Header (Set-cookie: {sessionID}; HttpOnly;) 를 전송
            - 웹브라우저 스크립트에서 Set-cookie 에 접근하는 것을 금지
    + secure
        * Set-cookie 에 설정
        * 평문값 대신 암호화된 값을 전송
        * Header (Set-cookie:{sessionID}; secure;)

## HTTP 메시지 구성
- HTTP 요청 메시지
    + 구문형식 : 요청라인, 요청헤더, 빈라인(Empty line), 요청 메시지 바디
        * Request Line : {Method} {URI} {HTTP버전} CRLF
            - `HEAD0x20/index.jsp0x20HTTP/1.00x0d0a`
        * Request Header : {헤더명}:{헤더값}; ...; CRLF
            - Host : 요청 대상 서버의 호스트/도메인명, 포트정보
            - User-Agent : 클라이언트의 어플리케이션/OS 정보
            - Refer : 현재 요청한 URL 정보를 가지고 있는 이전 문서(URL)의 정보
        * Empty Line : CRLF
        * Body : {Msg 내용}
            - GET 인 경우 Message Body 없음
    + 헥사값
        * Space : 0x20
        * CRLF : 0x0d0a
    + 요청메소드 : GET, POST, HEAD, OPTIONS, CONNECT, PUT (PUSH X), TRACE, DELETE
        * GET
            - Query String
                + GET 은 Query String 을 이용하여 제한된 Data 를 전송 (URL 길이 한계)
                + `http://server[:port]/path/program?query_string`
                    * `?` : 쿼리스트링 식별자
                    * query string : `param1=value1&param2=value2&param3=value3`
                        - `&` : 파라미터 구분자
                        - `%` : 문자열을 16진수로 표현
            - GET 방식은 Query String 이 모두 노출되어 POST 에 비해 취약
            - access_log에 query string 기록됨
        * POST
            - Body 부분을 이용하여 긴 Data 를 전송
        * HEAD
            - GET 과 유사, 서버 응답 시 Body 제외하고 Header 부분만 전송
            - 요청에 대한 처리는 서버에서 진행이 됨
            - URL 링크 유효성 검증을 위해 사용
                + Req : `HEAD index.jsp HTTP/1.0`
                + Res :
                    * `Date:Thu 20 June 2018` 
                    * `Server:Apache/... PHP` 
                    * `Content-Type:text/html`
                    * `Connection:close`
        * OPTIONS
            - 서버가 지원하는 메소드를 확인하는 용도
                + Req : `OPTIONS * HTTP/1.0` : URI를 지정하지 않는다.
                + Res :
                    * `HTTP/1.1 200 OK` 
                    * `Date:...`
                    * `Server:Apache...` 
                    * `Content-Length:0`
                    * `Allow:GET,HEAD,TRACE` : 지원하는 메소드
                    * `Connection:close`
        * CONNECT
            - 클라이언트와 서버 간의 터널링 목적
            - 웹 서버가 Proxy 역할을 함
        * PUT
            - Body 에 Data 를 담아 전송
            - 요청 URI 에 저장
        * TRACE
            - 클라이언트로부터 수신한 메세지를 서버에서 그대로 리턴
            - 루프백 테스트 용도
        * DELETE
            - 요청 URI 자원 삭제
- HTTP 응답 메시지
    + 구문형식
        * 상태라인
            - `HTTP/1.1 200 OK` 
                + HTTP 버전
                + 상태코드
                + 응답구문
                + CRLF
        * 응답 헤더
            - `Date:Thu 20 June 2018` 
            - `Server:Apache/... PHP` 
            - `Cache-Control:...`
            - `Content-Type:text/html` : Msg Body 의 데이터 형식
            - `Content-Length:30` : Msg Body 의 데이터 길이
                + 헤더명:헤더값;속성;속성CRLF
                + ...
                + 헤더명:헤더값CRLF
        * 빈 라인 (Empty)
            - CRLF
        * 응답 메시지 바디
            - 메시지 내용
    + 상태코드 : 1xx, 2xx, 3xx, 4xx, 5xx
        * 1xx : 정보 제공
            - 100 : continue (첫 메시지 도착, 클라이언트는 계속 요청이 가능)
            - 101 : switching (서버의 업그레이드 헤더에 정의되어 있는 프로토콜을 변환시키기 위한 클라이언트의 요청을 수락)
        * 2xx : 성공
            - 200 : Success 
            - 201 : Created (PUT 메소드에 의해서 서버에 파일이 정상 생성됨)
            - 202 : Accepted (요청을 수락함, 바로 실행시키지는 않음)
            - 204 : No Content (Msg Body 에 내용이 없음)
        * 3xx : Redirection - 요청자원의 위치 재지정
            - 301 : Moved Permanently (요청된 URL 이 영구적으로 변경됨)
            - 302 : Moved Temporarity = Found (요청된 URL 이 임시적으로 변경됨)
            - 304 : Not Modified (변경이 되지 않았으니 캐시를 사용해 접속)
        * 4xx : 클라이언트 에러
            - 400 : Bad Request (요청 메시지 문법 오류)
            - 401 : Unauthorized (요청 메시지 인증 부족)
            - 403 : Forbbiden (요청 자원에 대한 접근 차단)
            - 404 : Not Found (요청 자원이 없음)
        * 5xx : 서버 에러
            - 500 : Internal Server Error (내부적 서버 오류-프로그램장애 or 메시지손상)
            - 501 : Not implemented (요청된 메소드 수행 불가)
            - 503 : Service Unavailable (서비스 거부상태)
- 예시
    + 요청
        * `GET /test/sample.php HTTP/1.1`
        * `Host:www.test.com`
    + 응답
        * `HTTP/1.1 302 Found` : 요청 자원 위치가 임시적 변경 (Moved Temporarity)
        * `Location:http://www.test.com/test/example.php` : 클라이언트 재요청수행