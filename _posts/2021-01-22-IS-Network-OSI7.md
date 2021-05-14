---
title: 정보보안 - 네트워크 OSI 계층모델
date: '2021-01-22 19:00:00'
tags:
- 정보보안기사
- 네트워크
related: true
categories:
- IS_Certification
toc: true
---

# OSI 계층 모델

- 개념
    + 개방형 시스템 상호연결(Open System Interconnection) 모델 (ISO 표준)
    + 상호 이질적인 네트워크간 연결에 호환성 확보를 위함
    + TCP/IP 는 OSI참조 모델을 기반으로 단순화, 현실화의 과정에서 채택된 모형
    + 거대한 하나의 프로토콜이 아닌 계층별 분할을 통해 접근하는 방식

- OSI 7계층
    + 사용자 지원 계층 (상위 계층)
        * 7. 응용 계층
            - 사용자가 네트워크에 접근할 수 있도록 해주는 계층
            - 사용자 인터페이스, 저자우편, 데이터베이스 관리 등의 서비스 제공
            - 텔넷, HTTP, SSH, SMTP, FTP
            - 데이터 단위 : 데이터
        * 6. 표현 계층
            - 운영체계의 한 부분
            - 입력 또는 출력되는 데이터를 하나의 표현형태로 변환
            - 필요한 번역을 수행하여 두 장치가 일관되게 전송 데이터를 서로 이해할 수 있도록 한다.
            - 제어코드나 문자 및 그래픽 등의 확장자(jpg, gif, mpg)
            - 인코딩/디코딩, 압축/압축해제, 암호화/복호화
            - 데이터 단위 : 데이터
        * 5. 세션 계층
            - 통신장치 간의 세션을 설정, 유지, 동기화
            - Application 간의 논리적인 연결 (포트연결, 세션) 생성, 관리
            - 데이터 단위 : 데이터
    + 전송 계층
        * 4. 전송 계층 (L4 계층)
            - 프로세스 간 데이터 전송
                + Process - to - Process Communication
                + 논리적인 주소 (IP) 필요
                + 프로세스 포트 번호 필요
            - 전체 메시지를 발신지 대 목적지(종단 대 종단)간 제어와 에러 관리
                + End - to - End Reliable Delivery
            - 신뢰성 있는 통신을 보장
                + 패킷들의 전송이 유효한지 확인
                + 실패한 패킷은 재전송
            - 데이터 단위 : 세그먼트
            - 프로토콜
                + TCP
                    * 연결지향
                    * 연결제어
                    * 흐름제어
                        - 손실 방지를 위해 사용할 수 있는 용량만큼 속도 조절
                        - 종단 간 제어
                    * 오류제어
                    * 혼잡제어
                        - 네트워크의 혼잡도를 계산, 전송량 조절
                    * 분할, 재조합
                        - 원본 -> 데이터 분할 -> Segment -> 재조합 -> 원본
                + UDP
                    * 비연결지향
            - 장비
                + L4 스위치
                    * 서버 로드 밸런싱 (트래픽 부하 분산)
                    * Failover 기능
    + 네트워크 계층 (하위 계층)
        * 3. 네트워크 계층 (L3 계층)
            - 다중 네트워크 링크에서 패킷을 발신지로부터 목적지로 전달할 책임
            - 각 패킷이 시작 지점에서 최종 목적지까지 성공적이고 효과적으로 전달되도록 함
                + End - to - End Delivery
                + Routing 알고리즘
            - 데이터 단위 : 패킷
            - 장비  
                + 라우터 (L3 스위치) : 최적의 경로를 찾음
                    * 데이터링크 계층의 Broadcast, Multicast 를 전달하지 않음
                    * VLAN 기능
                    * 보안기능
                    * QoS 관련 기능
            - 프로토콜
                + IP
        * 2. 데이터링크 계층 (L2 계층)
            - MAC 주소 (물리적 주소) 가 데이터 링크 계층에 해당
            - 1계층을 통해 받은 비트들이 제대로 전송되었는지 확인 (프레임 전송 신뢰성)
            - 1계층을 통해 받은 비트들을 어떻게 보내야 할지 결정
                + Node - to - Node Delivery
            - LAN(Local Area Network), NIC 해당
            - 프로토콜
                + 이더넷 (Ethernet)
                + HDLC
                + LCC
            - 데이터 단위 : frame
            - 장비
                + 스위칭 허브
                + L2 스위치
                    * 내부적으로 MAC Address Table 을 내장
                    * 정확히 수신해야 할 노드에 전송 (목적지 정보가 존재)
                        - 스니핑을 하기 위해 아래의 방법을 사용
                        - Switch Jamming, ARP Spoofing, ARP Redirect, ICMP redirect, SPAN(모니터링 포트) 활용
                + 스위칭 환경 : 관리목적의 패킷 복제 (악용의 여지가 있음)
                    * Monitoring Port : (SPAN : Switch Port Analyzer)
                        - 특정 포트를 모니터링
                        - 스위치를 통과하는 패킷을 복제
                    * TAP : 트래픽을 모니터링하는 장비
                + 브릿지
                    * LAN 연결장비
            - IEEE 802 위원회 (미국 전기전자 기술자 협회) 에서는 2개의 계층으로 세분화
                + LLC (Logical Link Control : 논리적 연결 제어)
                    * 네트워크 계층과의 연결을 담당
                + MAC (Media Access Control : 물리적 연결 담당)
            - 신뢰성 있는 전송
                + 데이터 전송의 안전성 보장 : Data Link Control
                    * 흐름 제어 : 수신 노드의 처리속도를 감안, 속도 조절
                        - 노드 간 제어
                        - Stop and Wait
                            + 송신측에서 전송 후 수신측 확인(Ack) 응답 대기 후 전송
                        - Sliding Window
                            + 수신측의 확인응답을 받기 전에 수신 가능한 범위에서 여러 패킷을 전송
                    * 오류 제어 (Error Control) : 전송 중 여러 원인으로 인한 데이터 손실, 오류 발생 시 제어 기능
                        - BEC ( Backward Error Correction : 후진 오류 수정방식)
                            + 송신 측에서 오류를 검출할 수 있는 추가정보를 같이 전송
                            + 수신측에서 점검, 오류 발생 시 송신 측에 재전송 요청(ARQ : Automatic Repeat Request)
                        - FEC ( Forward Error Correction : 전진 오류 수정방식)
                            + 재전송을 하지 않는 방식 (ARQ 불필요)
                            + 송신 측에서 오류 검출, 수정도 가능한 추가정보를 전송
                    * 회선제어 : 한 회선에 여러 장치가 연결된 경우 전송방식을 제어
                        - 전송방식
                            + 단방향 : 한 방향으로 전송
                            + 반이중 : 동시에 전송 불가
                            + 전이중 : 양쪽으로 동시전송 가능
        * 1. 물리 계층 (L1 계층)
            - 전기적 신호만 전송
            - 데이터 단위 : Bit
            - 동축케이블, 광케이블, 랜선
            - 장비
                + 리피터 : 증폭기, 재생중계장치
                + Dummy Hub : 전체포트로 전달
                    * 목적지 정보 없음
                    * 연결된 장비의 NIC의 MAC 주소로 직접 폐기
                    * 스니핑에 취약

- OSI 모델 데이터 교환 방식
    + 캡슐화 (Encapsulation) : 헤더추가
        * 응용계층 -> 물리계층
    + 역캡슐화 (Decapsulation) : 헤더 제거
        * 물리계층 -> 응용계층

- OSI7 과 TCP/IP 비교

    |OSI 7계층|TCP/IP 4계층|주요 기능|
    |:---|:---|:---|
    |⦁ Application<br/>⦁ Presentaion<br/>⦁ Session|Application|⦁ 네트워크를 실제로 사용하는 응용 프로그램으로 구성<br/>⦁ FTP, TELNET, SMTP 등이 있음|
    |Transport|Transport|⦁ 도착하고자 하는 시스템까지 데이터를 전송<br/>⦁ Port를 가지고 있으며, 프로세스를 연결 또는 비연결해서 통신함<br/>⦁ TCP,UDP|
    |Network|Internet|⦁ Datagram을 정의하고 Routing하는 일을 담당<br/>⦁ IP, ARP, RARP, ICMP, IGMP|
    |⦁ Data Link<br/>⦁ Physical|Network Access|케이블, 송수신기, 링크 프로토콜, LAN 접속과 같은 물리적 연결 구성을 정의|

    + TCP/IP 프로토콜
        * 애플리케이션 계층
            - TCP
                + HTTP : 80
                + Telnet : 23
                + FTP : 20 / 21
                + SSH : 22
                    * sFTP : 22
                + SMTP : 25
                + IMAP : 143
                + POP3 : 110
            - UDP
                + DNS (Domain - IP) : 53
                + tFTP : 69
                + SNMP (네트워크 관리) : 161
        * 트랜스포트 계층 (Process to Process Communication)
            - TCP
            - UDP
            - SCTP (TCP + UDP)
        * 인터넷 계층 (Host to Host Communication)
            - IP (비연결형 데이터그램 프로토콜)
                + IPv4
                + IPv6
            - ICMP (에러, 진단)
            - ARP (논리 -> 물리)
            - RARP (물리 -> 논리)
            - IGMP (멀티캐스트)
        * 네트워크 인터페이스 계층 (Node to Node Delivery)
            - MAC 주소 사용
                + 총 48Bit 사용 
                    * 상위 24Bit : 제조사 
                    * 하위 24Bit : 시리얼번호
            - Ethernet : LAN
            - TokenRing : LAN
            - Frame Relay : WAN 
            - ATM : WAN

- ARP / RARP 프로토콜
    + ARP
        * IP (논리주소) -> MAC (물리주소) 로 변환
        * 과정
            - 질의자가 IP 를 브로드캐스트 하여 MAC 주소를 질의
            - 해당하는 IP를 가진 응답자가 자신의 MAC 주소를 유니캐스트
            - 해당하지 않는 Host 는 해당 프레임 폐기
        * 각 Host 는 ARP Cache 를 가지고 있음
            - 일시적으로 보관
        * ARP Cache 테이블
            - 각각의 MAC/IP 정보를 일시적으로 보관
            - IP에 대한 MAC 질의 결과를 보관
            - 윈도우
                + `arp -a` 로 확인
                + Type
                    * dynamic : 동적 생성 (1~2분 유지)
                    * static : 관리자 할당 (재부팅시 삭제)
                + `arp -d [ip]` 로 삭제
                    * incomplete : 삭제된 직후 MAC 주소란에 표기됨
                + `arp -s [ip] [mac]` 로 static 할당
            - 리눅스
                + `arp -a` 로 확인
                    * PERM : 관리자 할당 (static)
                + `arp -d [ip]` 로 삭제
                + `arp -s [ip] [mac]` 로 static 할당
    + RARP
        * MAC (물리주소) -> IP (논리주소) 로 변환
        * 과정
            - Host 는 본인의 MAC 주소만 알고 IP를 모름 (하드디스크 없음)
            - 질의자가 MAC 주소를 브로드캐스트 하여 IP를 질의
            - MAC / IP 정보를 가진 RARP 서버가 IP 주소를 유니캐스트
            - 해당하지 않는 Host 는 해당 프레임 폐기

- ARP Spoofing 공격 = ARP Cache poisoning
    + 과정 (MAC 주소 변조, 트래픽 스니핑, 2계층 공격)
        * 2계층은 서로 다른 네트워크끼리 라우팅하지 않음
            - 공격 대상은 같은 네트워크 대역
            - 라우터 바깥으로 나갈 때는 물리적 주소가 아닌 IP 주소로 사용
            - Router
                + 라우터의 논리적인 주소를 r.ip, 물리적 주소를 r.mac 으로 가정
                + Alice : a.ip, a.mac, a.cache
                    * H : b.ip h.mac (공격결과)
                    * H : h.ip h.mac (정상결과)
                + Bob : b.ip, b.mac, b.cache
                    * H : a.ip h.mac (공격결과)
                    * H : h.ip h.mac (정상결과)
                + Hacker : h.ip, h.mac, h.cache
                    * A : a.ip a.mac (정상결과)
                    * B : b.ip b.mac (정상결과)
                + Hacker는 가운데에서 스니핑하며 정상 중계
        * Hacker, Alice (Victim), Bob (Victim), Router
        * Hacker는 Alice 와 Bob 의 MAC 주소를 공격자의 MAC 주소로 위조한 Reply 를 만들어서 지속적으로 전송
        * Alice 와 Bob 의 Cache Table 에는 Hacker 의 MAC 주소가 유지됨
        * Alice 와 Bob 간의 통신이 Hacker 에게 넘어감 (스니핑)
        * Hacker 는 Alice 와 Bob 이 스니핑을 눈치채지 못하도록 IP 포워딩 (전송기능 활성화)
            - Arp Cache 가 저장된 Router 주소를 자신의 주소로 변조시킴
            - 외부로 나가는 데이터를 전부 스니핑
            - Arp Redirect 공격
    + ARP 의 취약점을 완전히 없애는 방법은 없음. 정적인 설정으로 캐시정보 갱신 X
        * 시스템이 종료되면 전부 삭제
        * 재가동 될 때마다 ARP 캐시를 정적으로 구성
        * ARP Watch : ARP 트래픽을 실시간으로 모니터링
    + GARP (Gratuitous - 불필요한)
        * Sender IP 와 Target IP 를 동일하게 함
        * 목적
            - IP 주소 충돌 감지
                + 자신 이외의 Reply 를 받아서는 안됨
                + Reply 가 있을 경우 자신과 같은 IP를 가진 Host 가 존재함을 인지
            - ARP Table 갱신
                + MAC 주소 변경 시 자신의 MAC 주소가 바뀌었음을 알려줌
                + ARP 문제점 : 인증을 하지 않고 수정함 (취약점 존재)