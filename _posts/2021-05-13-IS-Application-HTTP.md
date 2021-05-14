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
- 세션(Session) 방식
- HTTP 쿠키(Cookie) 보안속성
