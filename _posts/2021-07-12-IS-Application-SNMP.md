---
title: 정보보안 - SNMP(Simple Network Management Protocol)
date: '2021-07-12 09:00:00'
tags:
- 정보보안기사
- 어플리케이션
related: true
categories:
- IS_Certification
toc: true
---

# SNMP (Simple Network Manage Protocol)
- 개요
    + 간이 망 관리 프로토콜
        * TCP/IP 초창기에는 ICMP 프로토콜의 ping 을 이용해 장비간 연결 상태 파악
            - Host 수의 증가로 네트워크 시스템이 복잡해짐 -> 관리의 어려움
        * 1988년 IAB (인터넷 표준제안 위원회) 에서 표준 작업을 시작
            - SGMP -> SNMP
        * 네트워크 장비를 관리, 감시하기 위한 프로토콜
        * UDP, 응용계층 표준 프로토콜
        * 모니터링 / 컨트롤
        * 네트워크 관리 시스템 (NMS - Network Management System) 에서 사용
            - 정기적으로 관리정보 수집 (SNMP Protocol)
            - 실시간으로 네트워크 상태 모니터링, 설정 (SNMP Protocol)
    + 대부분의 OS 에 탑재
    + 네트워크 관리 정보를 얻기 위해서 관리 프로그램이 필요
        * SNMP 는 Protocol
        * 관리 프로그램 필요
        * 관리시스템, 관리대상 필요
- SNMP 구조
    + 관리시스템 Manager 모듈 (162/UDP)
        * Manager 모듈이 Agent 모듈을 조회
        * Agent 는 Manager 에 응답
    + 관리대상 Agent 모듈 (161/UDP)
        * Agent 는 Manager 에 Trap 전송
    + MRTG (Multiple Router Traffic Grapher)
        * SNMP 기반의 장비 모니터링 프로그램
        * 네트워크 상에 발생하는 트래픽 사용량 모니터링
        * MIB 를 이용해 정보 수집 가능
    + MIB (Management Information Base)
        * 관리장치에 대한 정보가 모인 데이터베이스
        * Agent 각각이 MIB를 가지고 있음
- SNMP 동작 (PDU)
    + Manager -> Agent
        * `Get Request` : MIB 정보 요청
    + Manager <- Agent
        * `Get Response`
    + Manager -> Agent
        * `Get Next Request` : 이미 요청한 정보의 다음 정보 요청
    + Manager <- Agent
        * `Get Response`
    + Manager -> Agent
        * `Set Request` : MIB 설정 요청
    + Manager <- Agent
        * `Get Response`
    + Manager <- Agent
        * `Trap` : Agent 가 Manager 에게 비동기적으로 알림 (Notify)
- SNMP 통신을 위한 3가지 조건
    + SNMP 버전이 일치해야 함
        * v1
            - SGMP (Simple Gateway Management Protocol) 를 발전시킴
            - 보안기능(암호, 인증) 없음
            - community string 만 일치하면 모든 정보 획득가능
            - 평문 전송 (Snipping 노출)
        * v2
            - 보안기능 추가
                + 대칭키 암호화 알고리즘 (DES)
                + 해시 알고리즘 (MD5)
            - 송신처 인증 기능이 없음
            - SNMP v2c
                + 복잡한 보안기능 제거, 보안상 취약
                + 평문 전송 (Snipping 노출)
        * v3
            - Data 인증
            - 암호화
            - 재사용방지
            - 접근통제
    + Community String
        * 송수신 하기 전 미리 인증을 하기 위해 사용하는 P/W
    + PDU (Protocol Data Unit) 이 일치해야 함
        * 통신하기 위한 메시지 유형
            - Get Request <--> Get Response
        * Read 형
            - SNMPv1
                + Get Request
                + Get Next Request
            - SNMPv2 / v3
                + Get Request
                + Get Bulk Request : 요청 객체의 범위를 지정
        * Write 형
            - SNMPv1
                + Set Request
            - SNMPv2 / v3
                + Set Request
        * Response 형
            - SNMPv1
                + Get Response
            - SNMPv2 / v3
                + Response
        * Trap
            - SNMPv1
                + Trap
            - SNMPv2 / v3
                + TrapV2
                + InformRequest : Manager 가 다른 Manager 에 정보를 요청할 때 사용
                + Report : 다른 Manager 에 보고할 때 사용
- (SNMPv3 기준) Message 구조
    + | 버전 | 공통 Header | 보안 매개변수 | PDU 데이터 |
        * 공통 Header 부
            - | 메시지 ID | 메시지 최대크기 | 메시지 Flag | 메시지 보안모델 |
                + 메시지 보안모델
                    * USM (User Security Model)
                    * VACM (View-based Access Control Model) 모델
        * 보안 매개변수 부
            - | 재전송 | 공격 | 방지 | 메시지위변조 | 방지 | 암호 매개변수 |
                + 재전송 공격방지
                    * SNMP 엔진 IP (Authoritative 엔진 ID)
                    * 부트횟수 (Authoritative 엔진부트 횟수)
                    * 엔진 시간정보 (Authoritative 엔진 time)
                + 메시지위변조 방지 (HMAC) - MD5 | SHA
                    * 사용자 인증 매개변수
                + 암호 매개변수 (DES-CBC)
- SNMP 데이터 수집 방식
    + Polling : Manager 가 Agent 에 요청하면 응답
    + Event Report : Agent 가 Manager 에 Trap Message 전송 (Notify)
- SNMP 취약점
    + DoS 공격 취약점
    + 버퍼오버플로우 취약점
    + 비인가접속 취약점
- SNMP 보안
    + SNMP는 관리자 입장에서 매우 중요한 정보를 제공
    + SNMP에 읽기, 쓰기 권한이 있는데 쓰기 권한은 사용 자제
        * Set Request 제한
    + SNMP v3 에서 보안 서비스를 제공
    + community string
        * SNMP 데몬 (manager) 와 클라이언트 (Agent) 간 데이터 송수신 전 인증 필요
        * 인증을 위해 사용하는 패스워드
    + 읽기모드 RO (Read Only) / 쓰기모드 RW (Read Write)
    + MIB : 관리장치에 대한 정보가 모인 데이터베이스
        * 관리되어야 할 정보, 자원 => 객체의 집합체
        * 객체별 트리형식 구조
        * SMI (Structure Management Information)
            - 객체의 구조, 형식
            - ASN.1 (Abstract Syntax Notation) 언어 사용
                + Data 와 Data 속성을 설명하기 위한 공식 언어
                + name (OID:Object Identifier)
                + syntax (객체의 Data 유형)
                + encoding (Message 전송 시 비트변환 규칙)
                    * BER : encoding rule 중 하나