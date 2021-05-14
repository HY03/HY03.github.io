---
title: 정보보안 - 네트워크 명령어
date: '2021-02-19 17:00:00'
tags:
- 정보보안기사
- 네트워크
related: true
categories:
- IS_Certification
toc: true
---

# ping 명령어
- 개요
    + ICMP 프로토콜 사용
        * Echo Request (Type:8)
        * Echo Reply (Type:0)
    + 반응이 있을 경우 통신연결이 된다 (포트가 열려있다) 는 의미
    + 보안상의 이유로 destination 에서 서비스를 하지 않을 수 있음
- 윈도우
    + `ping -n 5 -l 128 www.korea.com`
        * `-n` : 패킷 전송 횟수 (Default : 4)
        * `-l` : 패킷 크기 설정 (Default : 32Bytes)
- 리눅스 / 유닉스
    + `ping -c 5 -s 128 www.korea.com`
        * `-c` : 패킷 전송 횟수 (Default : 무한대)
        * `-s` : 패킷 크기 설정 (Default : 56Bytes)


# traceroute 명령어
- 개요
    + 경로를 추적하고, 상태 및 흐름을 파악하기 위함 (라우터의 문제점을 찾아내기 위함)
    + ICMP 프로토콜을 사용
    + 네트워크 계층 (L3/Router) 를 거치면서 패킷을 전송
    + 각 구간별 중계속도, 연결 상태 등을 알 수 있음
    + TTL 값을 1씩 증가시켜 진단함
    + 라우터는 TTL 값을 감소시키고, TTL 값이 0이 되면 패킷을 폐기
        * Type 11 : ICMP Time Exceeded 응답값 () 
            - Code 0 : TTL exceeded
    + 최종목적지 도달
        * Type 0 : ICMP Echo Reply
    + 포트가 닫혀있을 경우
        * Type 3 : ICMP Destination Unreachable
            - Code 3 : Port Unreachable
    + 응답시간 * 로 표기
        * 보안상의 이유로 패킷을 무시할 수 있음
        * 실제로 구간에 문제가 발생한 경우
- 윈도우
    + `tracert`
    + ICMP Request 패킷 이용
        * ICMP Echo Request (Type 8)
    + TTL 값을 1씩 증가시켜 진단함
- 리눅스
    + `traceroute`
    + UDP 패킷 (33434/UDP) 이용
    + TTL 값을 1씩 증가시켜 진단함

# netstat 명령어
- 개요
    + 확인 정보
        * 열려져 있는 포트
        * 서비스 중인 프로세스들의 상태정보
        * 네트워크 연결 상태
    + 옵션
        * No Option : ESTABLISHED(연결상태) 소켓 정보
        * `-a` : 모든 소켓의 상태 정보
        * `-i` : 네트워크 인터페이스 정보
        * `-r` : 시스템 라우팅 테이블 정보
        * `-s` : 각 프로토콜별 (TCP, UDP, ICMP ...) 통계정보
        * `-n` : 네트워크 주소를 숫자로 표현
        * `-u` : UDP
        * `-t` : TCP
    + 결과값
        * Recv-Q : 수신버퍼에 저장된 데이터 크기
        * Send-Q : 송신버퍼에 저장된 데이터 크기
        * Local Address : 로컬호스트의 소캣 주소
        * Foreign Address : 원격호스트의 IP:포트
        * Status : 소켓 상태
        * i 옵션 결과값
            - RX : Received (수신받음)
            - TX : Transmitted (전송)
            - OK : 오류없는 패킷
            - ERR : 에러 패킷
            - DRP : 폐기 패킷
            - OVR : 오버플로우로 폐기된 패킷

# ifconfig 명령어
- 개요
    + ifconfig : interface config (리눅스/유닉스)
        * 인터페이스 설정 정보
        * ip 정보
        * 서브넷 마스크
    + ipconfig (윈도우)
- 내용
    + Link encap : Ethernet
    + HWaddr 00:0c:29:3c:73:32 -> MAC Address
    + inet : IP주소
    + UP : 현재 인터페이스 활성화
    + Metric : 라우팅 경로의 효율성/우선순위, 클수록 비용이 큼
    + RX : Received (수신받음)
    + TX : Transmitted (전송)
- Promiscuous 모드
    + 설정 시 : `ifconfig eth0 promisc`
        * `UP BROADCAST RUNNING PROMISC MULTICAST` 
    + 로그파일
        * `/var/log/message` 
            - 시간 localhost kernel : device eth0 entered promiscuous mode
    + 해제 시 : `ifconfig eth0 -promisc` 
        * `UP BROADCAST RUNNING MULTICAST`
    + 로그파일 
        * `/var/log/message` 
            - 시간 localhost kernel : device eth0 left promiscuous mode

# nmap
- 개요
    + 포트스캐닝 툴, 호스트나 네트워크를 스캐닝
    + 시스템 보안 툴인 동시에 해킹 툴로 사용될 수 있음
- 예시
    + `nmap -sP xxx.xxx.xxx.xxx` : Ping Scan (ICMP) 
        * 활성화 되어있으면 응답 받음
    + `nmap -sP -p 80 xxx.xxx.xxx.xxx` : Port 옵션
        * `-pT:21,23,110,U:53` : TCP 21,23,110, UDP 53번 포트 스캔
    + `nmap -sT xxx.xxx.xxx.xxx` : TCP Connection Open
- 옵션
    + `-sT` : **일반적 TCP 포트 스캐닝 (connect(open))**
    + `-sS` : **half open 스캔 (추적 어려움)**
    + `-sP` : **ping을 이용한 일반적인 스캔**
    + `-sU` : **UDP 포트 스캐닝**
    + `-sA` : **TCP ACK Scan**
    + `-sN` : **TCP Null Scan**
    + `-sF` : **TCP FIN Scan**
    + `-sX` : **TCP XMAS Scan**
    + `-PO` : 대상 호스트에 대한 ping 응답을 요청하지 않음 (log 기록, filtering 회피)
    + `-PT` : 일반적인 ICMP ping이 아닌 ACK 패킷으로 ping을 보내고 RST 패킷으로 응답을 받음
    + `-PI` : 일반적인 ICMP ping 으로 방화벽이나 필터링에 의해 걸러짐
    + `-PB` : ping을 할 때 ICMP ping 과 TCP ping 을 동시에 이용
    + `-PS` : ping을 할 때 ACK 패킷 대신 SYN 패킷을 보내 스캔
    + `-O` : **대상 호스트의 OS 판별**
    + `-p` : **대상 호스트의 특정 포트를 스캔하거나 스캔할 포트의 범위 지정 (1-1024)**
    + `-D` : **Decoy 기능으로 대상 호스트에게 스캔을 실행한 호스트의 주소를 속임**
    + `-F` : /etc/services 파일 내에 기술된 포트만 스캔
    + `-I` : TCP 프로세서의 identd 정보를 가져옴
    + `-n` : IP 주소를 DNS 호스트명으로 바꾸지 않는다. 속도가 빠르다.
    + `-R` : IP 주소를 DNS 호스트명으로 바꿔서 스캔. 속도가 느리다.
    + `-o` : 스캔 결과를 텍스트 파일로 저장
        * `-oN` : 스캔결과 파일로 출력
        * `-oX` : 스캔결과 XML로 출력
        * `-oG` : Grepable (grep, awk) 파일 형식으로 출력
    + `-i` : 스캔 대상 호스트의 정보를 지정한 파일에서 읽어서 스캔
    + `-h` : 도움말 보기

# TCP Connect(Open) 스캔
- 개요
    + connect() 함수를 이용하여 각 포트별로 접속, 스캔하는 방식
    + 포트가 열린 상태이면 연결완료 후 종료
    + 포트가 닫혀있으면 RST+ACT 패킷이 옴
    + 시스템 로그가 남게 됨
- 과정
    + 열려있는 경우
        * Attacker : SYN 전송
        * Target : SYN+ACK 전송
        * Attacker : ACK 전송
        * Attacker : RST+ACK 전송
    + 닫혀있는 경우
        * Attacker : SYN 전송
        * Target : RST+ACK 전송
    + 방화벽에 의한 차단
        * Attacker : SYN 전송
        * Target
            - 무응답
            - ICMP 메시지 (Destination Unreachable)
            - filtered 메시지
- nmap
    + telnet, http 포트 확인 : `nmap -sT -p 23,80 xxx.xxx.xxx.xxx`

# TCP SYN(Half-Open) 스캔
- 개요
    + 관리자 권한을 가지고 있어야 수행 가능
    + 완전 연결 설정과정을 수행하지 않기에 Half-Open(반 열림) 스캔이라 함
    + 시스템 로그가 남지 않기 때문에 Stealth Scan 이라 함
- 과정
    + 열려있는 경우
        * Attacker : SYN 전송
        * Target : SYN+ACK 전송
        * Attacker : RST 전송 
            - 원칙은 ACK 전송, 강제로 Control Flag 를 변경-관리자권한
            - UAPRSF (000100)
    + 닫혀있는 경우와 방화벽 차단은 위와 동일
- nmap
    + telnet, http 포트 확인 : `nmap -sS -p 23,80 xxx.xxx.xxx.xxx`


# TCP FIN 스캔
- 개요
    + 스텔스 스캔 방식의 하나
    + TCP 헤더의 flag 비트를 비정상적으로 설정해서 스캔하는 방식 (관리자권한)
    + TCP 표준의 허점을 이용
        * 포트 상태가 CLOSED 이면 요청 세그먼트에 대한 응답으로 RST 를 보낸다.
        * OPEN 상태이면 RST 가 오지 않음
- 과정
    + 열려있는 경우
        * Attacker : FIN 전송
        * Target : 응답없음
    + 닫혀있는 경우
        * Attacker : FIN 전송
        * Target : RST+ACK 전송
- nmap
    + telnet, http 포트 확인 : `nmap -sF -p 23,80 xxx.xxx.xxx.xxx`

# TCP NULL 스캔
- 개요
    + 스텔스 스캔 방식의 하나
    + 연결되어 있지 않은 포트에 아무 플래그 비트를 설정하지 않은 탐지패킷 전송
    + 열린 포트 : 응답이 없음
    + 닫힌 포트 : RST+ACK 전송
- 과정
    + 열려있는 경우
        * Attacker : 패킷 전송 (No Flag) - UAPRSF (000000)
        * Target : 응답 없음
    + 닫혀있는 경우
        * Attacker : 패킷 전송 (No Flag) - UAPRSF (000000)
        * Target : RST+ACK 전송
- nmap
    + telnet, http 포트 확인 : `nmap -sN -p 23,80 xxx.xxx.xxx.xxx`

# TCP Xmas 스캔
- 개요
    + 스텔스 스캔방식의 하나
    + 여러가지 플래그를 활성화 해 전송
    + 연결되어 있지 않은 포트에 FIN, URG, PSH 플래그 비트를 설정하여 탐지패킷 전송
        * UAPRSF : 101001
    + 열린 포트 : 응답이 없음
    + 닫힌 포트 : RST+ACK 전송
- 과정
    + 열려있는 경우
        * Attacker : FIN+URG+PSH 플래그 전송 (UAPRSF - 101001)
        * Target : 응답 없음
    + 닫혀있는 경우
        * Attacker : FIN+URG+PSH 플래그 전송 (UAPRSF - 101001)
        * Target : RST+ACK 전송
- nmap
    + telnet, http 포트 확인 : `nmap -sX -p 23,80 xxx.xxx.xxx.xxx`

# TCP ACK 스캔
- 개요
    + 포트의 Open 여부보다는 방화벽의 필터링 방식을 확인하기 위함
- 방화벽 필터링 방식
    + Packet Filtering (패킷 필터링) 방화벽
        * deny, allow
            - IP 주소 (소스, 목적지)
            - 포트번호 (소스, 목적지)
            - 트래픽 방향
            - 프로토콜의 형태
        * 전송 / 네트워크 계층 차단 방식
            - TCP/IP 헤더 부분을 이용한 침입차단
            - 수동적 방화벽
        * 응용 계층 차단 (응용 게이트웨이) 방식
            - 프록시 (소프트웨어) 기능을 이용 
                + 패킷의 IP 주소와 서비스 종류에 따라 내부 네트워크 보호 
            - 능동적 방화벽 
    + Stateful Inspection (상태기반) 방화벽
        * 패킷 필터링 방화벽과 마찬가지로 동일한 패킷정보 검토
            - SYN 패킷인 경우
        * 상태 테이블 관리
            - TCP 연결에 관한 정보를 기록
            - TCP 번호 추적
        * DataLink 계층에서 상태 테이블을 확인하고, 허용된 패킷만 통과시킴 
            - SYN 패킷이 아닌 경우
    + NAT (Network Address Translation) 를 이용한 방화벽
- 과정
    + 필터링 하는 경우
        * 응답 없음 (Stateful Inspection 방화벽 가능성이 높음)
        * ICMP 메시지 
    + 필터링 하지 않는 경우
        * RST + ACK 패킷

# UDP 스캔
- 개요
    + ICMP Unreachable 메시지를 이용하여 UDP 포트 오픈 여부 확인
    + 열린 포트 : UDP 응답 or 응답 없음
    + 닫힌 포트 : ICMP 메시지 응답
- 과정
    + 열려있는 경우
        * Attacker : UDP
        * Target : UDP 응답 or 응답 없음
    + 닫혀있는 경우
        * Attacker : UDP
        * Target : ICMP Unreachable (Type : 3, Code : 3)
- nmap
    + DNS 서비스 UDP 스캔 : `nmap -sU -p 53 xxx.xxx.xxx.xxx` 
    + NTP 서비스 UDP 스캔 : `nmap -sU -p 123 xxx.xxx.xxx.xxx`

# Decoy 스캔
- 개요
    + 위조된 주소로 스캔하는 방식
    + 다양한 IP로 스캐너 주소를 위조, 관리자가 누가 스캔하는지 알아채기 어려움
- nmap
    + `nmap -sS -p 23,80 192.168.120.100 -D 192.168.150.1, 192.168.150.2` 
        * src -- SYN --> 192.168.120.100:23/80
        * 192.168.150.1 -- SYN --> 192.168.120.100:23/80 (Decoy)
        * 192.168.150.2 -- SYN --> 192.168.120.100:23/80 (Decoy)