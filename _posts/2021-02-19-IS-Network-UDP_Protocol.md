---
title: 정보보안 - 네트워크 UDP 프로토콜
date: '2021-02-19 16:00:00'
tags:
- 정보보안기사
- 네트워크
related: true
categories:
- IS_Certification
toc: true
---

# UDP 프로토콜
- 개요
    + 전송계층, TCP 와 함께 대표되는 프로토콜
    + 비연결형 프로토콜 : 연결이 수립되지 않음 (Connectionless Protocol)
        * 연결설정 과정 (예: 3 Way Handshake) 이 없음
        * Datagram 전송시마다 주소정보를 설정해서 전송
        * 데이터 순서를 보장하지 않음
    + 비신뢰형 프로토콜 : 전송을 신뢰할 수 없음 (Unreliable Protocol)
        * TCP의 제어기능 등이 없음
            - Flow control
            - Error control
            - Congestion control
        * 상위 어플리케이션에 대한 식별기능 (IP, Port)
    + Datagram 전송기반
        * 데이터를 정해진 크기로 전송
- 장점
    + 전송속도가 빠름 (단순, 가벼움)
    + 대용량 송수신 부적절하나 한번의 패킷전송으로 송수신 완료되는 서비스에 적합
        * DNS (53/UDP)
        * DHCP (67,68/UDP)
- 헤더 구조

    ![UDP Header](/assets/images/posts/UDP_header.png)

    [이미지 출처](https://commons.wikimedia.org/wiki/File:UDP_header.png)

    + Total Length : Header 와 Data 를 합친 길이
    + Checksum : 전체 Datagram 오류검사 필드
