---
title: 정보보안 - 네트워크 IP 프로토콜
date: '2021-01-29 14:00:00'
tags:
- 정보보안기사
- 네트워크
related: true
categories:
- IS_Certification
toc: true
---

- IP 프로토콜 (Internet Protocol)

    + 헤더구조, IP 프로토콜 동작방식, IP 단편화
    + 비연결형/비신뢰적 프로토콜 (연결상태를 유지하지 않음)
    + 패킷 전송 순서를 보장하지 않음
        * 라우터간 경로가 만들어 졌을 때 중간의 라우터에 의해 경로가 바뀔 수 있음
        * 전송 순서가 변경되거나 중간의 손실 등이 있을 수 있음

- IP Header


    ![IPv4 Packet](/assets/images/posts/IPv4_Packet_-en.svg.png)

    [이미지 출처](https://en.wikipedia.org/wiki/IPv4)

    + IPv4 : 기본 헤더 20 Byte, 옵션 헤더 40 Byte
    + Version (4bit) : 버전정보, 4.0
    + IHL (4bit) : 헤더의 길이, HLEN
    + Type of service (8bit) : QoS(서비스 품질), 현재 사용 X
    + Total length (16bit) : 전체 IP 패킷 길이
    + Identification (16bit) : 단편화 되기 전 식별 값
        * 단편화 (Fragmentation) / 재조합 (Reassembly) 관련 필드
    + Flag 필드 : 단편화, 재조합과 관련된 필드
        * 첫번째 Bit : 사용 X (예약)
        * 두번째 Bit : DF (Don't Fragment Bit)
            - 1 설정 시 단편화 X 
        * 세번쨰 Bit : MF (More Fragments Bit)
            - 1 설정 시 재조합할 조각이 더 있음
            - 0 설정 시 더 이상 단편화된 조각이 없음
    + Fragment offset : 단편화, 재조합과 관련된 필드
        * IP는 전송의 순서를 보장하지 않음
        * 각각의 단편에 대한 상대위치값 (offset) 표기
        * Bit 표기 = 8 Byte 단위로 표기
    + Time to live (8bit) : IP 패킷 생존시간
        * 네트워크에 패킷이 계속 떠돌면 안됨.
        * L3 스위치, 라우터 를 건너 뛸 때 (hop) 통과 가능 횟수
        * 리눅스 : 64
        * 윈도우 : 128
        * 유닉스 : 255
        * TTL 값을 이용해 시스템 정보를 얻는 취약점이 있음 (OS Fingerprint)
    + Protocol (8bit) : 상위 프로토콜과의 다중화, 역다중화를 위한 식별자
        * 상위 프로토콜을 식별하기 위한 번호
        * HTTP, telnet 등 응용계층 프로토콜 -> TCP
            - TCP 하나로 여러 가지 프로토콜을 사용하는 것 : 다중화
        * TCP -> HTTP, telnet 등으로 분기
            - 역다중화
    + Header checksum : 헤더 부분의 오류 검사 (Data 부 제외)
    + Source address
    + Destination address
    + Options : 경로설정, 타이밍 관리 등 패킷의 부가정보
        * 잘 사용하지 않음 (악용, 공격에 활용 가능)
        * Loose Source Route : 느슨한 소스 라우팅
            - 출발지에서 지정하는 옵션
        * Strict Source Route : 엄격한 소스 라우팅
            - 출발지에서 지정한 경로로 무조건 라우팅하는 옵션

- 단편화 (Fragmentation)
    + 하나의 파일을 이루는 데이터가 분산되어 있는 것
    + 최대 전달 단위 : MTU (Maximum Transmission Unit)
        * 프레임의 최대 크기
        * 단편화 되는 데이터그램의 크기는 MTU 보다 작아야 함
        * (클 시 전송 불가능)
    + Ethernet 프로토콜 일 경우 1500 Byte
    + X.25 : 576 Byte
    + FDDI : 4352 Byte
    + 예:
        * MTU 가 1500 byte 일 경우
        * 2800 byte 전송 시 쪼개야 함
            - 2 개의 프레임 생성 
            - IP Header + IP payload (쪼갠 데이터) : 1500 byte 이하여야 함
    + 과정
        * 출발부터 단편화가 이루어짐 (예: Ethernet 1500byte MTU)
        * 중계구간의 L3 / Router 등을 거침
        * 중계구간의 MTU 는 값이 다를 수도 있음
            - 더 작은 MTU일 경우 분할이 이루어져야 함 (예: 576byte MTU : 3분할)
            - 분할이 이루어진 후 MTU가 커질 경우 다시 합쳐지거나 하지는 않음.
        * 중간 과정에 손실이 발생할 경우
            - 최종 목적지에서는 일정시간 대기
            - 최종 목적지에서 재조합 실패 시 모든 단편을 폐기
            - ICMP 프로토콜로 에러메시지를 출발지로 보냄
                + Type 11:Time Exceeded > 시간초과
                    * Code 1 : Fragment reassembly time exceeded
    + 동작방식
        * 가정
            - Original IP Datagram
                + IP Header 20Byte
                    * 기본 Header 20Byte, 옵션 Header 40Byte (거의 사용안함)
                    * ID : 32000
                + Payload 3100Byte
            - Ethernet 환경
                + MTU 1500Byte
        * 단편화 과정
            - frag #1
                + 기본 IP Header 20Byte
                    * IPv4 | HLEN=20 | -(QoS) | Total Length=1500
                    * ID=32000 | FLAG=001 `(X_DF_MF)` | offset = 0 (0~1479)
                + Payload 1480Byte
                + 합계 1500Byte
            - frag #2
                + IP Header 20Byte
                    * IPv4 | HLEN=20 | -(QoS) | Total Length=1500
                    * ID=32000 | FLAG=001 `(X_DF_MF)` | offset = 1480 (1480~2959)
                + Payload 1480Byte
                + 합계 1500Byte
            - frag #3
                + IP Header 20Byte
                    * IPv4 | HLEN=20 | -(QoS) | Total Length=1500
                    * ID=32000 | FLAG=000 `(X_DF_MF)` | offset = 2960 (2960~3099)
                + Payload 140Byte
                + 합계 160Byte
    + 확인방법
        * tcpdump 를 이용해서 단편화 동작방식을 분석
            - (`frag` `단편ID` : `단편사이즈 (헤더제외, Payload size)` `@오프셋` `[+|-]`)
                + `+` : More fragments bit : 1 (추가단편이 있음)
                + `-` : More fragments bit : 0 (추가단편이 없음)
        * 예시
            - frag # 1 : (frag 32000 : 1480 @0 +)
            - frag # 2 : (frag 32000 : 1480 @1480 +)
            - frag # 3 : (frag 32000 : 140 @2960)

- IP 라우팅 규칙
    + IP 데이터그램이 네트워크 목적지를 찾는 두 가지 방법
        * 직접 또는 로컬 라우팅
            - 송신자와 같은 물리 네트워크에 있는 목적지에는 데이터그램이 직접 전달됨
        * 간접 라우팅
            - 목적지가 송신자와 다른 물리네트워크에 있을 경우
            - IP는 그 데이터그램을 다른 장치에게 보내야만 함
        * IP는 송신자 IP 주소와 목적지 IP 주소를 이용하여 목적지가 로컬 네트워크에 있는지를 판단
    + Router 혹은 Gateway 경우지
        * Router 와 Gateway 의 겅계가 모호해짐
        * 라우팅 테이블을 참조 : 목적지로 가기 위한 여러가지 경로가 저장되어 있는 자료구조
        * `netstat -rn` : 해당 호스트에서 라우팅 테이블이 어떻게 구성되어있는지 확인
        * netmask / genmask 
    + 검색 방법
        * IP 패킷의 목적지 IP와 라우팅 테이블의 netmask/genmask 를 `bit&` 연산한다.
            - `& 연산` : 두 bit 가 모두 1일 때만 1
            - 같은 네트워크에 있는지 판단
        * 목적지 IP(호스트) 와 일치하는 값이 있을 경우 일치하는 경로 : `255.255.255.255`
        * 테이블에 목적지 IP(호스트) 와 일치하는 경로가 없으면 일치하는 네트워크 주소를 검색
        * 검색결과 경로가 없을 경우 Default Gateway 이용 : `0.0.0.0` 의 값
    + 명령어
        * `netstat -rn` 해석
            - Destination
                + 목적지 호스트 or 목적지 네트워크 주소
            - Gateway
                + 목적지로 전송하기 위한 게이트웨이 주소
            - Genmask
                + 범용 목적의 마스크 (목적지 호스트 or 목적지 네트워크 주소 or 디폴트 게이트웨이 식별용)
            - Flags
                + U : Route is Up (라우트 활성화)
                + G : Use Gateway (게이트웨이 사용중)
                + H : Target is a Host (목적지가 Host를 의미)
            - Interface
                + Eth0
        * Class 개념 (목적지 호스트, 네트워크 주소)
            - Class A : 0~126 (127.0.0.1 : Loopback)
                + 0+7bit(네트워크주소)+3Byte(호스트주소)
                + 사설 IP : 10.0.0.0 ~ 10.255.255.255
            - Class B : 128~191
                + 10 + 14bit (네트워크주소) + 2Byte(호스트주소)
                + 사설 IP : 172.16.0.0 ~ 172.31.255.255
            - Class C : 192~223
                + 110 + 21bit (네트워크주소) + 1Byte(호스트주소)
                + 사설 IP : 192.168.1.1 ~ 192.168.255.255
            - Class D
            - Class E
        * `netstat -rn` 결과 예시

            |No|Destination|Gateway|Genmask|Flags|Interface|
            |:---|:---|:---|:---|:---|:---|
            |1|10.0.96.100|10.0.120.1|255.255.255.255|UGH|eth0|
            |2|10.0.64.0|10.0.120.2|255.255.224.0|UG|eth0|
            |3|10.0.64.0|10.0.120.3|255.255.192.0|UG|eth0|
            |4|0.0.0.0|10.0.120.5|0.0.0.0|UG|eth0|

            - IP Routing 계산
                + 목적지 주소 `10.0.96.100`
                    * 일치 항목 있음
                    * Genmask 와 &연산
                        - `00001010 00000000 01100000 01100100`
                        - `11111111 11111111 11111111 11111111`
                        - `-----------------------------------`
                        - `00001010 00000000 01100000 01100100`
                        - `10. 0. 120. 1` 로 패킷 전송
                + 목적지 주소 `10.0.122.100`
                    * 일치 항목 없음
                    * IP 가 10으로 시작 : class A
                    * Genmask 와 &연산
                        - `00001010 00000000 01111010 01100100`
                        - `11111111 11111111 11111110 00000000`
                        - `-----------------------------------`
                        - `00001010 00000000 01111010 00000000`
                        - 불일치
                        - `00001010 00000000 01111010 01100100`
                        - `11111111 11111111 11111110 00000000`
                        - `-----------------------------------`
                        - `00001010 00000000 01111010 00000000`
                        - 불일치
                        - `00001010 00000000 01111010 01100100`
                        - `11111111 11111111 11000000 00000000`
                        - `-----------------------------------`
                        - `00001010 00000000 01000000 00000000`
                        - `10.0.120.3` 로 패킷 전송
                        
                         
# IP Spoofing

- 개요
    + IP를 속여서 공격하는 기법
    + 시스템 간의 신뢰(trust) 관계를 이용 : ID/PW 인증 없이 접속
    + 신뢰관계에 있는 클라이언트를 연결 불가능 상태(DoS) 로 만든다
    + 공격자가 클라이언트 IP 주소로 위조하여 서버에 접속
- 리눅스의 신뢰관계 설정
    + `/etc/host.equiv` : 시스템 전체에 영향
        * `[+|-] [host_name(IP/호스트명)] [user_name(호스트별 사용자명)]`
        * 호스트에 접속하는 사용자의 접근을 허용
        * `+` : 모든 호스트에 대한 접근 허용 (ID, PW 인증필요)
            - `+ user_name` : 해당하는 유저에게 모든 호스트 접근 허용
            - `+ @group` : 모든 호스트에 대해 해당 group 사용자로 접근 허용
            - `+ +` : 모든 호스트에 대해서 모든 계정을 신뢰 (매우 취약)
        * `-` : 차단
            - `- host_name` : 해당하는 사용자의 접근을 차단
        * `host_name` : 특정 호스트에 대한 접근 허용
            - `host_name -user_name` : 해당 호스트에서 특정 사용자만 접근 차단
            - `host_name user_name` : 해당 호스트에서 특정 사용자의 접근 허용
    + 홈 디렉토리 내 `.rhost` : 각 사용자별로 영향
    + 취약점 대응책
        * 되도록 `$HOME.rhost`, `/etc/host.equiv` 사용 X
        * 파일 소유자 root, 권한 600 변경
        * `+` 설정 제거, 반드시 필요한 host와 계정만 등록
- r 계열 서비스 (신뢰관계 서비스 - 인증없이 원격접속) : `rlogin`, `rexec`, `rsh`
    + cat /etc/xinetd.d/rlogin
    + service xinetd restart
    + netstat -antp | grep 513
    + 취약점 대응책
        * Trust 관계 설정 없애기
        * Trust 관계 유지해야 한다면 MAC 주소 정적(Static) 구성
        * r 계열 서비스 사용 X

# IPv6 주소

- 개요
    + IPv4 한계점 해결을 위해 등장
        * 주소 공간의 고갈
        * 최소 지연과 자원의 예약 불가
        * 암호화와 인증기능 미제공
    + IPv6 장점
        * 주소 공간 확대
        * 헤더 단순화
        * 주소 설정 간편
        * 보안성 강화
        * 모바일 IP 지원 개선
    + IPv6 전환기술
        * Dual Stack : IPv4 와 IPv6 를 동시 사용
        * 터널링 : IPSec 기능 기본 제공
            - IPv6 를 IPv4 로 캡슐화 후 IPv4 네트워크 통과
        * 주소변환/헤더변환
            - IPv4, IPv6 간 전환기능
    + IPv4 : 32bit, IPv6 : 128bit
    + 네트워크 주소 : 64bit
    + 인터페이스 주소 : 64 bit

