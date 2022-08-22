---
title: 정보보안기사 실기 - 01.네트워크 - 02.OSI7 Layer
date: '2022-03-17 12:00:00'
tags:
- 정보보안기사 실기
- 네트워크
- OSI7 Layer
related: true
categories:
- IS_Certification
toc: true
---

# OSI7 Layer
1. OSI Layer
    - 개방시스템(Open System)의 기반 구조와 관계없이 시스템 간의 통신을 제공하는 계층형 프로토콜 집합
    - 7계층
        + 사용자 계층
            * Application
            * Presentation
            * Session
        + 전송 계층
            * Transport
        + 네트워크 계층
            * Network
            * Data link
            * Physical
2. 주요 용어
    - Node(노드) : 네트워크에 연결된 장치(device)를 의미
    - HOST(호스트) : 일반적으로 Node(노드) 중 컴퓨터(PC, Server)노드를 가리켜 호스트라고 표현
    - End-Node(종단 노드) : 통신의 양 끝단에 해당하는 노드로 최초 송신 노드(최초 출발지)와 최종 수신 노드(최종 목적지)
    - Intermediate Node(중계 노드) : End-Node 사이에 패킷 중계를 해주는 노드
    - Link(링크) : 노드 사이의 패킷을 전달하기 위한 물리적인 통신경로
3. OSI7 Layer 의 계층 구조
    - 1계층 (물리 계층, Physical Layer)
        + 전송을 위해 필요한 물리적 링크의 설정, 유지, 해제를 담당
    - 2계층 (데이터링크 계층, Data link Layer)
        + 논리적 연결제어를 담당하는 LLC(Logical Link Control) 또는 DLC(Data Link Control)과 장비와 장비간의 물리적인 접속을 담당하는 MAC(Media Access Control) 의 두개 sub 계층이 존재 - **MAC 주소**
    - 3계층 (네트워크 계층, Network Layer)
        + 논리적 링크를 설정하고 상위 계층 데이터를 작은 크기의 패킷으로 분할하여 전송하는 역할
    - 4계층 (전송 계층, Transport Layer)
    - 5계층 (세션 계층, Session Layer)
    - 6계층 (표현 계층, Presentation Layer)