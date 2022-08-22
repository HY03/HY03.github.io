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
        + NAT Table
        * Mangle Table
    + Chain : 패킷이 이동하는 경로 (3가지 기본 Chain 이 존재-삭제불가)
        * Chain Input : 방화벽을 최종 목적지로 하는 체인
        * Chain Output : 방화벽을 최초 출발지로 정하는 체인
        * Chain Forward : 방화벽을 통과하는 체인 (방화벽을 별도의 서버로 구성해서 서비스할 때 사용하는 체인)
        * -> INPUT -> Linux Server -> Output ->
        * ->->->->->->-> FORWARD ->->->->->->->
            - Linux Server 를 목적지로 삼는 모든 패킷은 Input Chain 통과
            - Linux Server 에서 생성되어 외부로 보내지는 모든 패킷은 Output Chain 을 통과
            - Forward Chain 은 현재의 Linux Server 가 목적지가 아닌 패킷이 통과하는 Chain (방화벽을 별도의 서버로 구성)
    + **Rule** : 패킷 필터링을 하기 위한 Rule    
        * 기본구문 형식
            - `iptables [테이블] [체인] [룰] [타깃]`
            - 대 소문자 구분
        * 테이블 설정
            - 형식 : `-t [테이블]`
            - 테이블을 명시하지 않으면 기본값으로 Filter Table 이 적용
        * 체인(Chain) 설정
            - 형식 : `iptables -[A|I|D] [체인]`
            - `-A` : append 모드 (해당 체인의 가장 마지막 줄에 룰을 추가)
            - `-I` : insert 모드 (해당 체인의 첫 줄에 룰을 추가)
                + 행번호 지정시 : `-I 체인 n(행번호)` (n 행에 롤 추가)
            - `-D` (delete) : delete 모드
                + `-D 체인 룰` : 해당 룰 삭제
                + `-D 체인 n(행번호)` : n행의 룰 삭제
                    * `iptables -D INPUT 1`
            - `-P` : 해당 체인의 기본 정책을 설정, 변경
                + 기본 정책 : 해당 체인의 모든 룰에 매치가 되지 않으면 적용되는 정책
                + `iptables -P [체인명] [ACCPET|DROP|REJECT|LOG]`
            - `-R` (replace) : 위치 선택 후 정책 교환
            - `-N` (new chain) : 새로운 체인을 생성
            - `-Z` : 체인의 모든 규칙들의 패킷과 바이트 카운트를 0으로 만듬
            - `-X` (delete chain) : 비어있는 체인 제거
            - `-C` (check) : 패킷을 테스트
        * 룰 설정
            - 형식 : `iptables -A INPUT -s [발신지] --sport [발신지포트] -d [목적지] --dport [목적지포트] -j [정책]`
            - 제어 옵션 명령어
                + `-s` (source) : 패킷의 송신지를 제어, IP 또는 IP 대역을 명시 가능
                    * `-s 192.168.159.0/24`
                + `-d` (destination) : 패킷의 수신지를 제어, IP 또는 IP 대역을 명시 가능
                + `-j` (jump) : 방화벽을 지난 후 패킷의 상태를 제어
                    * `-j [정책]` : 룰에 매치될 때 적용할 정책을 지정
                    * ACCEPT : 허용
                    * DROP : 해당 패킷을 차단한 후 아무런 응답 메시지를 전송하지 않음
                    * REJECT : 해당 패킷을 차단한 후 ICMP 에러 메시지로 응답 (불필요한 네트워크 트래픽 발생)
                    * LOG : 탐지 로그를 남김. 주로 `/var/log/message` 에 남김
                + `-p` (protocol) : 프로토콜을 제어
                + `-i` (inbound interface) : 패킷이 들어오는 네트워크 인터페이스
                + `-o` (outbound interface) : 패킷이 나가는 네트워크 인터페이스
                + `-f` (fragment) : 분절(단편화)된 패킷
                + `--sport` : 송신지 포트 제어 (대역 명시 가능)
                    * `--sport 1024:` : 1024 이상의 포트
                    * `--sport :65535` : 65535 이하의 포트
                    * `--sport 1:1023` : 1 ~ 1023 대역의 포트
                + `--dport` : 수신지 포트 제어
                + `--icmp-type [메시지타입]` : ICMP 의 경우 타입과 코드를 이용한 메시지 식별
                    * `--icmp-type echo-request` (ICMP Echo Request 를 의미)
            - 예시
                + 기본 정책을 ACCPET 로 설정
                    * `iptables -P INPUT ACCEPT`
                    * `iptables -P OUTPUT ACCEPT`
                    * `iptables -P FORWARD ACCEPT`
                    * `-P` (policy) : 미리 만들어진 체인의 정책을 바꾸기
                + 현재 자신의 방화벽 규칙을 보는 명령
                    * `iptables --list`
                    * `iptables -L` (list) : 적용되어 있는 정책 목록을 출력
                + 규칙 추가 후에 저장하기
                    * `service iptables save -> /etc/sysconfig/iptables`
                + 현재 iptables 규칙을 초기화 하기
                    * `iptables -F` (flush) : 체인의 모든 규칙을 삭제
                    * `iptables -F INPUT`
                + 특정 출발지의 패킷을 모두 차단
                    * `iptables -A INPUT -s 192.168.0.5 -j DROP`
                + 특정 목적지로 가는 패킷이 아니라면 허용
                    * `iptables -A INPUT -d !192.168.0.10 -j ACCPET`
                + 특정 프로토콜, 특정 포트일 경우 허용
                    * `iptables -A INPUT -p TCP --dport 80 -j ACCEPT`
                + ICMP 의 경우 타입과 코드를 이용한 메시지 식별
- 상태 추적 (Connection Tracking)
    + 상태 유형
        * New 
            - 상태추적 테이블에 연결 정보를 가지고 있지 않은 패킷
            - 최초로 들어온 패킷의 상태
            - 예) TCP 의 SYN 패킷
        * ESTABLISHED
            - 상태추적 테이블에 연결 정보를 가지고 있는 패킷의 상태
            - 연결된 이후에 송수신되는 패킷들의 상태
        * RELATED
            - 상태추적 테이블에 연관된 연결정보를 가지고 있는 패킷의 상태
                + 연관 : 연결 시에 포트의 정보가 서비스 수행중에 새롭게 추가될 수 있는데 이러한 서비스 패킷의 상태를 말함
                    * 예) FTP 프로토콜 (21/TCP 제어채널 -> 20/TCP 데이터채널) 
        * INVALID
            - 어떤 상태에도 해당되지 않는 패킷상태
            - 연결된 상태를 알 수 없거나 잘못된 헤더를 가지고 있는 경우
    + 프로토콜 상태 유형
        * TCP 프로토콜 상태
            - 방화벽 기준
                + inbound SYN 패킷 : NEW
                + outbound SYN + ACK 패킷 : ESTABLISHED
                + 연결종료 마지막 ACK 패킷이 보이기 전까지 ESTABLISHED 상태
        * UDP 프로토콜 상태
            - 비연결 (Connectionless) 프로토콜 : 연결 상태를 유지하지 않음
                + 프로토콜 수준에서 연결상태 정보가 없음
            - 최초 inbound : NEW 상태
            - 상태추적 테이블에 UDP 는 inbound 될 때마다 임의의 시간동안 정보를 유지 (ESTABLISHED 상태)
    + 룰 설정 예시
        * 위조된 TCP SYN 패킷을 차단
            - 상태 추적 테이블 상 NEW 이면서 SYN 플래그로 설정되지 않은 패킷 -> DROP
            - `iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT` 
                + 상태추적 테이블에서 ESTABLISEHD, RELATED 상태의 패킷은 모두 허용
                    * 이미 허용된 트래픽에 대해서는 룰 검사를 다시 하지 않음 (성능 향상)
                + `-m state` : 상태추적기능을 사용하기 위한 옵션 (state 모듈)
                + `--state` : state 옵션
            - `iptables -A INPUT -p tcp !--syn -m state --state NEW -j LOG --log-prefix "[Forged SYN Packet]"`
                + 패킷의 상태가 NEW 이면서, SYN 플래그가 설정되지 않은 비정상 패킷을 로그에 남김
                + `--log-prefix "접두어"` : 로그에 접두어를 만들어 표시
            - `iptables -A INPUT -p tcp !--syn -m state --state NEW -j DROP`
                + 패킷의 상태가 NEW 이면서, SYN 플래그가 설정되지 않은 비정상 패킷을 차단
            - 설정 예시
                ```
                    iptables -F INPUT
                    iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
                    iptables -A INPUT -p tcp !--syn -m state --state NEW -j LOG --log-prefix "[Forged SYN Packet]"
                    iptables -A INPUT -p tcp !--syn -m state --state NEW -j DROP
                ```
        * FTP ACTIVE MODE 상태추적 (Passive 모드일 경우 포트 지정을 1024 이상으로 하면 됌)
            ``` 
                iptables -F INPUT 
                iptables -P INPUT DROP 
                iptables -P OUTPUT DROP
                iptables -A INPUT -m state --state ESTABLISHED -j LOG --log-prefix "[INPUT ESTABLISHED]"
                iptables -A INPUT -m state --state RELATED -j LOG --log-prefix "[INPUT RELATED]"
                iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
                iptables -A INPUT -m state --state NEW -p tcp --dport 21 -j LOG --log-prefix "[FTP INPUT_NEW]"
                iptables -A INPUT -m state --state NEW -p tcp --dport 21 -j ACCEPT
                iptables -A OUTPUT -m state --state ESTABLISHED -j LOG --log-prefix "[OUTPUT ESTABLISHED]"
                iptables -A OUTPUT -m state --state RELATED -j LOG --log-prefix "[OUTPUT RELATED]"
                iptables -A OUTPUT -m state --state ESTABLISHED, RELATED -j ACCEPT
            ``` 
- 확장 모듈
    + connlimit 모듈
        * 개요
            - 동일한 ip 대역에 대한 동시 연결 개수 제한 모듈
            - 디도스 공격에 효과적 대응
        * 주요옵션
            - `--connlimit-above n` : n을 초과하는 동시 연결을 제한
            - `--connlimit-mask mask` : 0~32 범위의 mask 값을 지정, IP 대역에 대한 동시 연결 제한
        * 예시
            - 웹서버 80 포트에 대한 TCP SYN Flooding 공격 대응룰
                + TCP 80포트에 동일 src IP 로 동시연결개수가 5개 초과시 이를 차단
                + 로그 저장 위치 : `/var/log/messages`
                ```
                    iptables -F INPUT
                    iptables -A INPUT -m state --state ESTABLISHED, RELATED -j ACCEPT
                    iptables -A INPUT -p tcp --syn --dport 80 -m connlimit --connlimit-above 5 -j LOG --log-prefix "[CONNLIMIT]"
                    iptables -A INPUT -p tcp --syn --dport 80 -m connlimit --connlimit-above 5 -j DROP
                ```
            - 웹서버 (80/tcp) 에 대한 TCP SYN Flooding 공격에 대응하기 위한 룰 설정
                ```
                    iptables -F INPUT
                    iptables -A INPUT -m state --state ESTABLISHED, RELATED -j ACCEPT
                    iptables -A INPUT -p tcp --syn --dport 80 -m connlimit --connlimit-above 10 --connlimit-mask 24 -j LOG --log-prefix "[CONNLIMIT]"
                    iptables -A
                ```
            - 
    + limit 모듈
        * 룰에 매치되는 비율을 제한