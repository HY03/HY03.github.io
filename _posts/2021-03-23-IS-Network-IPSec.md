---
title: 정보보안 - IPSEC
date: '2021-03-23 18:00:00'
tags:
- 정보보안기사
- 네트워크
related: true
categories:
- IS_Certification
toc: true
---

# IPSEC(Internet Protocol Security)

## 보안서비스 (RFC2401 표준)
- 기밀성 : 대칭 암호화를 통해 기밀성 제공
    + AH : 암호화 X
    + ESP : 암호화 O
- 데이터 근원 인증 : 올바른 송신처로 온 것임을 보장
    + MAC (Message Authentication Code) 이용
- 비연결형 무결성 : 위조, 변조 되지 않음을 보장
- 재전송 공격 방지 : 일련번호(Sequence Number)를 유지하여 재전송 여부를 판단
    + SA (Security Assosication, 보안연관) 순서번호 유지, 검증
- 제한된 트래픽 흐름 기밀성 : 최초 출발지, 최종 수신지 정보에 대한 기밀성 보장 (터널모드)
    + 터널 / 보안 게이트웨이란?
        * IPSEC을 지원하는 라우터, 방화벽, VPN 장비
    + 종단과 보안 게이트웨이 사이 원본 IP Header 의 기밀성을 보장
    + 보안 게이트 사이의 New IP Header 구간은 트래픽 흐름이 노출됨
- 접근 제어 : 중요한 정보 시스템에 대한 접근을 제어
    + SP (Security Policy, 보안정책)
    + IP 패킷의 허용(Bypass) / 폐기(Discard) / 보호(Protect)

## IPSEC Architecture
- 개요
    + 양 종단 간의 안전한 통신을 지원하기 위함
    + IP 계층을 기반
    + 개방 구조의 프레임워크
    + IP의 취약점을 보완하기 위한 보안 기능을 제공
- 주요 구성 요소
    + 프로토콜
        * 인증 프로토콜(AH - Authentication Header)
        * 암호화 프로토콜(ESP - Encapsulation Security Payload)
    + 데이터베이스
        * 보안 연계 데이터베이스 (SAD)
        * 보안 정책 데이터베이스 (SPD)
    + 키 관리 매커니즘 (IKE : Internet Key Exchange Protocol)

## IPSEC 의 동작 모드
- 전송모드
    + 원본 IP Header + IPSEC Header + 원본 IP Payload + IPSEC Tailor
        * 보호대상 : IP Payload (캡슐화)
        * 보호구간 (Secure Channel) : End to End 
- 터널모드
    + New IP Header + IPSEC Hader + 원본 IP Header + 원본 IP Payload + IPSEC Tailor
        * 보호대상 : IP Hader + IP Payload (캡슐화)
        * IP 패킷 전체를 보호 > 네트워크상에서 전송을 위한 New IP Header 필요
        * 원본 출발지와 목적지의 트래픽 기밀성을 보장
- 4 가지 형태의 보안성을 제공
    + 터널 모드 AH
    + 전송 모드 AH
    + 터널 모드 ESP
    + 전송 모드 ESP

## IPSEC 프로토콜
### 인증 헤더(Authentication Header)
- 개요
    + 인증 헤더는 IP 데이터그램을 인증하기 위해 필요한 정보를 포함
    + 데이터의 인증과 무결성을 보장
        * 데이터의 무결성
            - MAC(Message Authentication Code) 에 의해 계산된 각 필드의 합산 값을 수신자가 확인
        * 데이터의 인증
            - 인증시 필요한 키와 인증 알고리즘을 SA 와 연계하여 지정하고 지정된 알고리즘을 수행
        * 리플레이 방지
            - 인증 헤더에 있는 Sequence Number 필드의 값을 일련 번호화 함으로써 보장
- 프로토콜
    + IP Header | AH | IP Payload
    + AH 프로토콜 => 인증 (MAC 무결성, 송신처 인증)
        * Security Parameter Index(32) : SA 식별자 (보안과 연관)
        * Sequence Number(32) : 재전송 공격 방지
        * Authentication Data : Mutable 필드를 제외한 IP Packet 전체에 대한 MAC값 (Integrity Check Value)
            - Mutable 필드 : TTL, Header Checksum, NAT 의 SrcIP
- AH 프로토콜 동작모드
    + 전송모드 (Transport) : Mutable 필드 제외한 IP Packet 전체 인증
        * IP Header | Authentication Header | IP Payload
    + 터널모드 (Tunnel) : Mutable 필드 제외한 New IP 패킷 전체를 인증
        * New IP Header | Authentication Header | IP Header | IP Payload

### ESP
- 개요
    + 암호화 기법을 사용
        * 데이터의 무결성
        * 리플레이 방지
        * 비밀성
- 프로토콜
    + IP Header | ESP Header | IP Payload | ESP Tailor | ESP Auth
    + ESP 프로토콜 => 인증 (무결성, 송신처 인증), 기밀성
        * IP Header 는 인증하지 않는다.
        * ESP Header
            - Security Parameter Index(32)
            - Sequence Number(32)
        * ESP Tailor
            - Padding : 블록암호를 위한 패딩정보
            - Pad Length(8)
            - Next Header(8) : Payload 프로토콜 타입
        * ESP Auth : 인증데이터
- ESP 프로토콜 동작모드
    + 전송모드 (Transport)
        * IP Header | ESP Header | IP Payload | ESP Tailor | ESP Auth
        * IP Payload ~ ESP Tailor 암호화
        * ESP Header ~ ESP Tailor 인증
    + 터널모드 (Tunnel)
        * New IP Header | ESP Header | IP Header | IP Payload | ESP Tailor | ESP Auth
        * IP Header ~ ESP Tailor 암호화
        * ESP Header ~ ESP Tailor 인증

## 용어정리
- 보안연관 (SA, Security Association)
    + 한 장비와 다른 장비 사이 논리적 연결을 유지하기 위한 보안설정값
    + 단방향성 특징 (A->B SA, B->A SA)
    + 주요항목
        * SPI : 보안 연관 식별자
        * Sequence Number Counter : 패킷의 순서번호 카운터
        * Anti Replay Window : 재전송 공격 방어 window 값
        * AH / ESP 정보
        * Life time : 세션 만료 시간
        * Mode : IPSec 동작모드 (전송, 터널)
        * Path MTU : 경로의 MTU 값
- SAD
    + 보안 연관을 모은 데이터베이스
    + 일치 항목이 없을 경우 인터넷 키교환 (IKE)
- 보안정책 (SP, Security Policy)
    + 패킷을 송/수신시 적용할 보안의 종류
    + Bypass, Discard, Protect (IPSec 적용) 정책
- SPD
    + 보안 정책을 모은 데이터베이스
- IPSec 송신/수신 과정
    + Outbound 패킷(송신) -> SPD 검색 -> 정책 결정 -> SAD 검색 -> 보안설정 적용
    + Inbound 패킷(수신) -> 패킷유형(IP, IPSec) 탐지 -> 상위계층 패킷 전달
        * IP 일 경우 SPD 검색하여 ByPass, Discard, Protect(패킷삭제) 룰 검색/결정
        * IPSec 일 경우 SAD 검색 -> 일치하면 IPSec 처리 / 일치하지 않으면 패킷삭제

## IKE (Internet Key Exchange)
- 과정
    + IKE-SA 설정/생성 (1단계)
    + IPSec-SA 설정/생성 (2단계)
- IKE 1단계 과정 - Mode 중 하나를 사용해 IKE-SA 생성
    + IKE-SA : IPSec-SA 생성을 위한 데이터 보호를 위함
    + Main Mode : 3쌍의 Message 를 교환하는 방식
        * Session ID 암호화 O
    + Aggressive Mode : 3개의 Message 를 교환하는 방식
        * Session ID 암호화 X
- IKE 2단계 과정 - IPSec-SA 생성
    + IPSec-SA : 데이터 교환 시 데이터 보호를 위함
- IPSec 절차 : (링크설정 -> VPN 생성)(1,2단계) -> Data 교환
