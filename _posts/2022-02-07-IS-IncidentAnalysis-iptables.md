---
title: 정보보안 - iptables
date: '2022-02-07 09:00:00'
tags:
- 정보보안기사
- iptables
related: true
categories:
- IS_Certification
toc: true
---

# iptables
- 정의
    + 리눅스 상에서 방화벽을 설정하는 도구
    + 패킷 필터링 기능 : 지나가는 패킷의 헤더를 보고 해당 패킷에 대한 허용/차단 (ACCEPT/DROP) 결정
    + Rule 기반의 패킷 필터링 기능을 제공
- 기능
    + 상태추적 (Connection Tracking or Stateful Inspection)
        * 방화벽을 통과하는 모든 패킷에 대한 연결상태를 추적, 메모리에 기록하여 상태추적테이블을 생성
        * 모든 송 수신 패킷을 기록하고 검사
        * 기존 연결을 가장하여 접근할 경우 저장되어 있는 상태목록과 비교하여 허용/차단
        * 이미 연결된 상태의 패킷은 검사하지 않음
        * Stateful Inspection : 이스라엘 보안업체인 Checkpoint 사 에서 최초로 개발한 방화벽 구조
    + NAT 기능
        * 네트워크 주소 변환
    + 패킷 레벨에서 로깅
    + 확장 모듈을 이용해 다양한 기능 제공
- 용어
    + Table
        * Filter Table 
            - Chain : 패킷이 이동하는 경로 (3가지 기본 Chain 이 존재-삭제불가)
                + Chain Input : 방화벽을 최종 목적지로 하는 체인
                + Chain Output : 방화벽을 최초 출발지로 정하는 체인
                + Chain Forward : 방화벽을 통과하는 체인 (방화벽을 별도의 서버로 구성해서 서비스할 때 사용하는 체인)
                + -> INPUT -> Linux Server -> Output ->
                + ->->->->->->-> FORWARD ->->->->->->->
                    * Linux Server 를 목적지로 삼는 모든 패킷은 Input Chain 통과
                    * Linux Server 에서 생성되어 외부로 보내지는 모든 패킷은 Output Chain 을 통과
                    * Forward Chain 은 현재의 Linux Server 가 목적지가 아닌 패킷이 통과하는 Chain (방화벽을 별도의 서버로 구성)
            - Rule : 패킷 필터링을 하기 위한 Rule
                + **사용법**
                    * 기본구문 형식
                        - `iptables [테이블] [체인] [룰] [타깃]`
                        - 대 소문자 구분
                    * 테이블 설정
                        - 형식 : `-t [테이블]`
                        - 테이블을 명시하지 않으면 기본값으로 Filter Table 이 적용
                    * 체인(Chain) 설정
                        - 형식 : `iptables -[A|I|D] [체인]`
                        - `-A` : append 모드 (해당 체인의 가장 마지막 줄에 룰을 추가) (4:45)
        * NAT Table
        * Mangle Table