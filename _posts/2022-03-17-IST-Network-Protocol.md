---
title: 정보보안기사 실기 - 01.네트워크 - 01.프로토콜
date: '2022-03-17 09:00:00'
tags:
- 정보보안기사 실기
- 네트워크
- 프로토콜
related: true
categories:
- IS_Certification
toc: true
---

# 프로토콜 (Protocol)
1. 프로토콜의 정의
    - 서로 다른 시스템 및 기기 간 데이터 교환을 원할히 하기 위한 표준화된 통신 규약
2. 프로토콜의 기본 요소
    - 구문(Syntax) : 송수신 데이터 포멧 의미
    - 의미(Semantic) : 데이터의 각 항목이 가지는 의미
    - 타이밍(Timing) : 데이터 송수신 동작방식을 정의하는 것
3. 프로토콜의 특징
    - 단편화(Fragmentation) : 송신 측에서는 긴 데이터 블록을 손쉽게 전송할 수 있도록 크기가 똑같은 작은 블록으로 나누어 전송
    - 재조립(Assembly) : 수신 측에서 쪼개진 작은 데이터 블록을 재합성하여 원래의 메시지로 복원하는 기능
    - 캡슐화(Encapsulation) : 각 프로토콜에 적합한 데이터 블록을 만들려고 데이터에 정보를 추가하는 것
    - 연결 제어(Connection Control) : 비연결 데이터 전송(데이터그램)과 연결 위주 데이터 전송(가상회선)을 위한 통신로를 개설, 유지, 종결하는 기능
    - 오류 제어(Error Control) : 데이터 전송 중 발생할 수 있는 오류나 착오 등을 검출하고 정정하는 기능
    - 동기화(Synchronization) : 두 통신 객체의 상태(시작, 종류, 검사 등)를 일치시키는 기능
    - 다중화(Multiplexing) : 하나의 통신로를 여러 개로 나누거나 회선 여러개를 하나의 통신로로 변환시켜 다수의 가입자가 동시에 사용할 수 있도록 하는 기능
    - 주소지정(Addressing) : 발생지, 목적지 등의 주소를 명기하여 데이터를 정확하게 전달하는 기능
    - 전송 서비스(Transmission Service) : 데이터 전송 중 발생할 수 있는 오류나 착오 등을 검출하고 정정하는 기능
    
# 통신 프로토콜
1. 통신 프로토콜의 정의
    - 통신 프로토콜은 컴퓨터나 원거리 통신 장비 사이에서 메시지를 주고 받는 양식의 체계
2. 통신 프로토콜의 종류
    - TCP(Transmission Control Protocol) : 전송 제어 프로토콜, 전송계층
    - IP(Internet Protocol) : 송신 호스트와 수신 호스트가 패킷 교환 네트워크에서 정보를 주고받는데 사용하는 정보 프로토콜