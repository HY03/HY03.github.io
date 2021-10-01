---
title: 정보보안 - DHCP(Dynamic Host Configuration Protocol)
date: '2021-07-08 21:00:00'
tags:
- 정보보안기사
- 어플리케이션
related: true
categories:
- IS_Certification
toc: true
---

# DHCP (Dynamic Host Configuration Protocol)
- 네트워크 통신
    + TCP/IP 기반 네트워크 노드 (컴퓨터, host) 는 자신의 IP를 알아야 함
        * IPv4 클래스 : A, B, C, D
        * Classless 주소체계 : 서브넷 마스크 필요
    + 외부네트워크와 통신하기 위해 디폴트 라우터 주소나 이름과 네임서버 주소를 알고 있어야 함
    + 네트워크에 접속한 모든 컴퓨터가 알아야 할 정보
        * 자신의 IP 주소
        * 라우터 IP 주소
        * 네임서버 IP 주소
        * 서브넷 마스크
- 네트워크 역사
    + RARP => BOOTP (Bootstrap) => DHCP (동적 호스트 설정 프로토콜)
    + RARP (Reverse Address Resolution Protocol)
        * 자신의 IP를 모를 때 이용
        * 인터넷 초창기에 부팅된 컴퓨터의 IP주소 제공
        * 물리주소 (Mac Address) 를 IP 주소로 변환
        * ARP : IP 주소를 물리주소 (Mac Address) 로 바꿔주는 역할
        * 현재 사용하지 않음
            - IP 주소만을 제공 (라우터, 네임서버 IP나 서브넷 마스크를 모름)
            - ARP 서버가 각 네트워크 망에 존재해야 함
    + BOOTP (Bootstrap)
        * RARP의 2가지 단점을 보완한 클라이언트/서버 형태의 프로토콜
        * 인터넷 상에 BOOTP 서버 존재
            - 4가지 정보 항목 (자신, 라우터, 네임서버 IP, 서브넷 마스크) 모두 제공
            - 정적 설정 프로토콜
        * 포트번호 67 (Server), 68 (Client) 사용 => DHCP 로 사용 가능
    + DHCP (Dynamic Host Configuration Protocol)
        * UDP 기반 프로토콜
        * 서버가 네트워크 클라이언트에게 IP주소를 실시간으로 부여
        * IP 주소가 수동으로 설정되는 정적 IP주소와는 다르게, 사용 가능한 IP주소를 자동으로 확인
        * 클라이언트/서버 형태의 응용계층 프로토콜
        * 처음 부팅된 컴퓨터나 디스크가 없는 컴퓨터에게 4개의 정보를 제공
- 장점
    + 네트워크 설계 변경 / 구성 변경이 쉬움
        * DHCP 서버에서 변경
    + IP 절약
    + 관리 용이 (TCP/IP 설정을 따로 할 필요없음)
- 단점
    + IP 없는 클라이언트 부팅 시 네트워크 상의 DHCP 서버에 IP 요청 브로드캐스트
        * 네트워크 성능 저하
    + 전원이 켜져있는 호스트에게 IP 할당
        * IP 낭비
    + IP 임대기간동안 다른 단말기에 해당 IP가 할당되지 않음
        * IP 낭비
    + 호스트가 너무 많은 경우 서버 과부하가 발생
- DHCP 동작과정
    + Client -> Server
        * Client 자신의 IP를 모르는 상태
        * Server : 192.168.172.10 으로 가정
        * DHCPDISCOVER 메시지 전송 (UDP)
            - Client IP : 0.0.0.0
            - 전송지 : 255.255.255.255
    + Client <- Server
        * DHCPOFFER 메시지 전송
            - 여러 개의 IP 주소를 제안
    + Client -> Server
        * Client 는 IP 주소를 선택
        * DHCPREQUEST
    + Client <- Server
        * DHCPACK
    + IP 주소가 마음에 안 들 경우 반복

