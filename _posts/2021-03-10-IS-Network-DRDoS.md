---
title: 정보보안 - 네트워크 DDoS
date: '2021-03-10 15:00:00'
tags:
- 정보보안기사
- 네트워크
related: true
categories:
- IS_Certification
toc: true
---

# DRDoS (Distributed Reflection Denial of Service) 공격
- 개요
    + 공격자는 출발지 IP를 공격대상의 IP 로 위조 (IP Spoofing) 함.
    + 다수의 반사서버(Reflector) 로 요청정보를 전송
        * DDoS 의 Zombie PC 이용이 아닌 정상적인 서버를 이용
    + 공격대상은 반사서버로부터 다수의 응답을 받아 서비스 거부 상태가 됨.
- DoS 공격과의 차이점
    + 공격 근원지 파악이 어려움
    + 좀비 PC 이용에 비해 공격 효율성이 좋음
        * Victim으로부터 SYN+ACK 에 대한 응답이 없을 경우 일정 횟수 재전송을 함.
- DRDoS 의 공격 형태
    + 3 way-handshake 의 취약점 이용
        * 정상적인 TCP서버들 또는 라우터를 활용
        * 소스 IP를 Victim IP 로 위조하여 SYN 패킷 전송
        * 정상적인 TCP 서버에서 SYN+ACK 을 Victim 에게 전송 
    + ICMP 프로토콜의 Echo Request 와 Echo Reply 를 이용
        * 정상적인 반사 서버를 이용
        * 소스 IP를 Victim IP 로 위조하여 Echo Request 패킷 전송
        * 정상적인 서버에서 Echo Reply 을 Victim 에게 전송
    + UDP 프로토콜 서비스를 제공하는 서버를 반사서버로 이용하는 방법
        * 대표적인 방법, 응답메시지 증폭 (대량의 트래픽 생성)
        * 유형
            - DNS : DNS 질의 시 타입:ANY,TXT 등과 같이 많은 양의 레코드 정보 제공을 요구하여 공격대상자에게 대형 트랙픽 유발
            - NTP : NTP (Network Time Protocol) 서버에 최근 접속한 클라이언트 목록을 요청 (monlist) 하여 공격대상자에게 대량 트래픽 유발
            - SNMP : SNMP Agent 에 MIB와 같은 정보를 대량 요청(GetBulkRequest) 하여 공격대상자에게 대량 트래픽 유발
            - CHARGEN : CHARGEN (Character Generate) 서버에 접속시 대량의 문자열(abcd...) 전송을 유도하여 공격대상자에게 대량 트래픽 유발
- 대응책
    + Ingress Filtering : IP 가 위조된 패킷 차단
    + 반사서버에서 ICMP 프로토콜 차단
    + DNS (Resolving)
        * 내부사용자 주소만 쿼리
        * 동일 IP에 대한 초당 요청개수 제한
        * 응답 정보량을 제한 (방화벽 또는 네트워크 보안장비)
    + NTP 서버의 경우 monlist 명령 해제