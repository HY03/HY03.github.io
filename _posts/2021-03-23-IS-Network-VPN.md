---
title: 정보보안 - 가상 사설망
date: '2021-03-23 18:00:00'
tags:
- 정보보안기사
- 네트워크
related: true
categories:
- IS_Certification
toc: true
---

# 가상사설망(Virtual Private Network)
- 개요
    + 공중 네트워크를 이용하여 전용선처럼 자신만의 고유 네트워크 효과를 누릴 수 있도록 하는 기술
    + 데이터 전송 중 전송망에서의 침입자에 의한 데이터 왜곡, 노출을 터널링 기술(프로토콜의 한 종류)을 이용하여 방지함
    + 터널링 : 보안성을 위함, 터널링을 통해 암호화되는 부분 : Payload
- 터널링
    + PPTP (Point to Point Tunnel Protocol)
        * 개발사 : MS
        * 형성층 : Link (2계층)
        * 최적 적용방식 : 독자적 망구성가능
        * PPP (Point to Point Protocol) 패킷 (2계층) 을 IP Packet 으로 캡슐화 -> 3계층에서 전송
        * 1:1 통신만 가능
    + L2F (Layer 2 Forwarding)
        * 개발사 : 시스코
        * 형성층 : Link (2계층)
        * 최적 적용방식 : ISP
        * 원격지 ISP 장비에서 접근서버로 L2F 터널을 생성
        * 위 가상터널을 통해 세션을 생성 (PPP, RAS)
        * 위 세션을 이용해서 원격지의 사용자가 홈사이트에서 주소를 할당하면 홈사이트 게이트웨이에서 사용자 인증을 거침
        * 하나의 터널에 여러개 연결을 할 수 있음 : 다자간 통신이 가능
        * PPP 사용
        * Radius 와 같은 인증서버를 사용
    + L2TP (Layer2 Tunneling Protocol)
        * 개발사 : MS 와 시스코가 함께 제안
        * 형성층 : Link (2계층)
        * 최적 적용방식 : ISP
        * L2F 에 기반, PPTP와 호환가능
        * 지원 프로토콜 : IP, IPX, AppleTalk
        * WAN 기술에 지원됨
    + **IPSec** (IP Security)
        * 개발사 : IETF (국제인터넷기술위원회)
        * 형성층 : Network (3계층)
        * 최적 적용방식 : 독자적 망구성가능
        * 3계층 터널링 프로토콜
        * VPN 구현에 가장 널리 사용되는 터널링 프로토콜
            - 네트워크 전송 간 보안을 강화시킴
            - TCP/IP 통신보다 더 안전하게 IP Datagram의 보호를 수행
            - 상위 계층에 대한 프로그램의 변경이 필요없음 (IP 계층 그대로 서비스)
            - 암호화된 패킷이 일반 패킷과 동일한 구조 > 네트워크 장비의 변경이 필요없음
            - 라우팅을 쉽게 할 수 있음

# IPSEC (Internet Protocol Security)
- 개요
    + network layer에서의 보안을 위한 표준
    + 인터넷 상에서 VPN을 구현하는데 사용될 수 있도록 IETF 에서 개발된 프로토콜셋
    + IPv6 에서 기본적으로 IPSec 을 지원
        * IPSec 지원 방화벽, 라우터, VPN : 보안/터널 게이트웨이
