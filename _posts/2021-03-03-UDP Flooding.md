---
title: 정보보안 - UDP Flooding
date: '2021-03-03 15:00:00'
tags:
- 정보보안기사
- 네트워크
related: true
categories:
- IS_Certification
toc: true
---

# DDoS (Distributed Denial of Service) 공격
- 개요
    + 분산서비스공격
        * 네트워크 자원 소진 : BandWidth 소진
        * 시스템 자원 소진 : 서비스/서버 자원 소진
    + 분산된 다수의 좀비 PC에 의해 공격대상 시스템의 서비스를 마비시키는 공격
- 공격 구조
    + Attacker(공격자) : C&C 서버에 공격 명령을 전달하는 해커 (Bot Master)
    + Botnet(봇넷) : 악의적 의도를 갖는 불법 네트워크
        * C&C (명령제어) 서버 : 공격 명령을 전달받고 좀비 PC에 명령을 전달하는 시스템 (Master)
            - 중앙집중형 (C&C 서버만 발견되면 전체 무효화)
                + IRC 봇넷 (Rbot) (초창기)
                    * IRC (Internet Replay Chat) : 인터넷에서 채팅을 할 수 있는 프로토콜
                    * IRC 서버간에 연결 (전 세계 모든 서버)
                    * IRC 서버를 C&C 서버로 주로 활용
                + HTTP 봇넷 (Robax) (탐지, 대응이 어려움)
            - 분산형 (탐지, 차단 어려움)
                + P2P 봇넷 (Storm, Peacomm)
                    * 공격자 (Bot Master) 가 P2P 중 하나에 명령 전송
                    * 봇넷 내 Host 끼리 명령을 전파시킴l
        * 좀비 PC (악성 봇) : C&C 서버로부터 전달받은 명령을 수행(실제 공격) 하는 시스템 (Bot, Slave, Agent)
            - 봇 프로그램에 감염된 PC
            - 봇(Bot)
                + 웜/바이러스, 백도어, 스파이웨어, 루트킷
                + 원격에서 해당 System을 제어하는 프로그램
                + 정보유출, 스팸메일발송, DDoS 공격
                + 타 PC 감염
    + 공격대상 : 피해 시스템 (Target, Victim)

# DNS 싱크홀 서비스
- 개요
    + 한국인터넷진흥원 (KISA) 에서 싱크홀 서비스 운영
    + DDoS 공격 구조
        * Zombie PC : C&C 서버 도메인으로 DNS 서버에 질의 (C&C 서버 IP 필요)
        * DNS 서버 : C&C 서버 IP 주소 전송
        * Zombie PC : C&C 서버 접속
        * C&C 서버 : 명령
    + DNS 싱크홀 (Sinkhole) 서비스
        * C&C 서버 도메인 목록 제공 서버 : DNS 서버에 목록 제공
        * Zombie PC : C&C 서버 도메인으로 DNS 서버에 질의 (C&C 서버 IP 필요)
        * DNS 서버 : 싱크홀 서버 IP 주소 전송
        * Zombie PC : 싱크홀 서버 접속

# DDoS 공격 유형
- 유형별 분류
    + 네트워크 대역폭 소진 공격 (네트워크 계층, 전송 계층) -> 네트워크 인프라 장애, 다른 정상 Client 접속 X
        * UDP Flooding
        * ICMP Flooding 
        * DNS Query Flooding : DNS Query Data 를 서버에 대량전송, DNS 서버에서 서비스를 받는 호스트에 타격
        * IP Flooding
        * TCP SYN Flooding : 대량의 SYN 패킷을 서버에 전송, 대기큐 (Backlog Queue)
        * TCP Flag Flooding : Flag값 (UAPRSF) 을 재멋대로 설정하여 전송, 서버에서 검증에 자원을 소모
        * TCP SYN + ACK Flooding
    + 서비스(어플리케이션) 마비 공격 -> 공격대상 시스템만 피해
        * HTTP Traffic Flooding
            - HTTP Get Flooding : 웹서버 자원 소모
            - HTTP Get Flooding with CC Attack : 캐싱 서버 무력화, 웹서버에 부하
        * HTTP Header/Option Spoofing
            - HTTP Continuation : HTTP Header 없이 Data 만 채워 웹서버에 전송 > 웹서버는 데이터가 더 있다고 생각하고 지속적으로 TCP 자원을 사용
        * TCP Traffic Flooding (TCP Session, SYN Flooding, TCP Slow Read)
            - TCP Session Flooding : 3Way-Handshake 과정을 지나치게 많이 발생시킴 
        * L7 Service Flooding (Hash DoS, Hulk DoS, FTP/SMTP Attack)