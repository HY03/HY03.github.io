---
title: 정보보안 - 네트워크 DDoS
date: '2021-02-23 19:00:00'
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
        * UDP Flooding : SRC IP 위조, 전송 계층
        * ICMP Flooding : SRC IP 위조, 네트워크 계층, Echo Request (Ping)
        * DNS Query Flooding : DNS Query Data 를 서버에 대량전송, DNS 서버에서 서비스를 받는 호스트에 타격
        * IP Flooding
        * TCP SYN Flooding : 3-Way Handshake 의 취약점 이용, 대량의 SYN 패킷을 서버에 전송, 대기큐 (Backlog Queue) 를 소진
            - 출발지 주소를 위조하여 SYN 전송, SYN + ACK 를 서버에서 보내도 ACK 가 오지 않기 때문에 Incomplete Connection Queue 가 소진됨
            - Listen : Backlog Queue 에 메세지를 받는 상태
                + Backlog Queue ( Incomplete Connection Queue + Complete Connection Queue )
                    * Incomplete Connection Queue : 서버에 SYN 요청이 들어오면 쌓이게 됨 (SYN-RECV)
                    * Complete Connection Queue : 연결요청이 완료 (3Way Handshake) 되면 또 다른 Queue 에 쌓임
                    * Complete Connection Queue 의 대기 Queue를 System Call (accept()) 하여 Connected Socket이 되고, Queue 에서 삭제
            - 현재 소켓 상태를 파악하기 위한 명령어
                + `netstat -antp | grep SYN RECV` : InComplete Connection Queue 의 리스트를 확인
            - 해결책
                + Syn Cookie 설정
                    * 리눅스 # `sysctl -w net.ipv4.tcp_syncookies=1` : 1=true
                    * Backlog Queue (Incomplete Connection Queue) 가 소진되지 않도록 설정
                    * 유효한 클라이언트인지 판단
                    * 연결 설정 과정에서 서버가 특정값 (쿠키값) 을 설정 - ISN
                        - SYN+ACK 에 설정
                    * ACK 에 ISN 이 실리지 않으면 Backlog Queue 에서 제거
                + 방화벽 이용
                    * 연결 요청 (동일 Client IP에서 발생) 에 대한 임계치 값 (SYN 요청 Threshold) 설정
                    * `iptables –A INPUT –p tcp —syn —dport 80 –m connlimit —connlimit –above 5 –j DROP`
                + 첫번째 SYN DROP
                    * 첫번째 SYN 패킷을 보낸 SRC IP가 존재하는지 확인
                        - DROP 을 시켜 확인 : 재요청 패킷이 도착하는지 확인(실제 IP 존재)
                + Backlog Queue 크기 늘리기 (임시적 조치)
                    * 리눅스 # `sysctl -w net.ipv4.tcp_max_syn_backlog=1024` 
                    * 솔라리스 # `ndd -set /dev/tcp tcp_conn_req_max_q0 1024;`
                + SYN + ACK 에 대한 대기시간 줄이기
        * TCP Flag Flooding : Flag값 (UAPRSF) 을 재멋대로 설정하여 전송, 서버에서 검증에 자원을 소모
        * TCP SYN + ACK Flooding
    + 서비스(어플리케이션) 마비 공격 -> 공격대상 시스템만 피해
        * HTTP Traffic Flooding
            - HTTP Get Flooding
                + 동일한 HTTP Get 요청을 다량으로 발생시킴 > 웹서버 자원 소모
                + Botnet의 ZombiePC 들이 동일 URL 로 GET 요청을 전송
            - HTTP Get Flooding with CC Attack : 캐싱 서버 무력화, 웹서버에 부하
            - Hulk DoS
                + URL 을 변경해가며 (차단-임계치기반 을 피해가며) 공격
                    * 임계치 우회 : 동일 URL에 대한 접근횟수 제한
        * HTTP Header/Option Spoofing
            - HTTP Continuation : HTTP Header 없이 Data 만 채워 웹서버에 전송 > 웹서버는 데이터가 더 있다고 생각하고 지속적으로 TCP 자원을 사용
            - Slow HTTP POST DoS : HTTP POST 메소드 이용, 대량의 데이터를 장시간에 걸쳐 분할 전송 > 서버는 수신이 다 될때까지 세션을 유지, 자원소모
                + RUDY 공격
                + Content-Length 헤더 필드의 값을 비정상적으로 크게 설정
                + 소량의 데이터를 지속적으로 천처히 보냄
                + 웹서버는 정해진 크기만큼 데이터를 모두 수신하기 위해 연결상태 유지
                + 웹 서버의 연결자원을 모두 소진
                + 대응책
                    * 동시연결 임계치 설정 (Concurrent Connection Threshold)
                        - `iptables`
                    * Connection timeout 설정
                        - Apache httpd.conf => timeout=120초 (기본값) -> 5초
                    * Read Timeout 설정 (Apache 2.2 이상)
                        - Apache httpd.conf => RequestReadTimeout body=10
                            + body 부가 10초 이내에 수신되지 않으면 오류
            - Slow HTTP Header DoS : 헤더와 데이터부의 구분 라인을 모호하게 함. > 서버는 아직 헤더정보가 남아있다고 판단, Header 의 나머지 부분 전송 대기, 연결 유지
                + Slowloris 공격
                + 헤더의 마지막을 알리는 Empty line을 보내지 않아 웹서버를 대기시킴
                + 웹서버와의 연결상태를 지속, 저대역폭 공격 (소량의 트래픽을 이용)
                + HTTP 헤더의 구조
                    * Request Line : 요청라인
                        - Method (GET, POST ...) 지정
                        - URL (/home/aa.php)
                        - HTTP Version (HTTP/1.1)
                        - 개행값 (CRLF - Carriage Return ( 행 맨 앞으로 커서 이동) Line Feed (다음 행으로 이동) : 0x0d0a < 0d0a)
                    * Request Header : 요청헤더
                        - Host : 호스트명
                        - 개행값 (CRLF)
                        - User-Agent : 사용 브라우저 정보
                        - 개행값 (CRLF)
                        - ...
                        - 개행값 (CRLF)
                        - 헤더 필드
                        - 개행값 (CRLF)
                    * Empty Line : 공백라인
                        - 헤더를 구분하기 위해 빈 라인 추가
                        - 개행값 (CRLF)
                    * Body (Data) : 데이터
                + 대응책
                    * 동시연결 임계치 설정 (Concurrent Connection Threshold)
                        - `iptables`
                    * Connection timeout 설정
                        - Apache httpd.conf => timeout=120초 (기본값) -> 5초
                    * Read Timeout 설정 (Apache 2.2 이상)
                        - Apache httpd.conf => RequestReadTimeout header=5
                            + header 부가 5초 이내에 수신되지 않으면 오류
            - Slow HTTP Read DoS : 서버와 연결할 때 TCP 윈도우 크기, 데이터 처리율을 감소 시킨 후 HTTP 송신
                + TCP 흐름제어 (Flow Control) 특성을 이용
                + HTTP 요청을 보냔 후 Windows 크기를 조작하여 Zero Window Packet을 지속적으로 서버에 전달
                + 연결 상태를 지속적으로 유지시키는 공격
                + TCP Flow Control (흐름제어)
                    * 3 Way-handshake 완료 후
                    * Client : ESTABLISHED
                    * Server : ESTABLISHED
                    * Data 전송
                    * Client 수신버퍼 / Server 송신버퍼
                    * Flow Control : 수신 측이 받을 수 있는 용량만큼 크기 조절
                    * Client 수신버퍼가 Full : 필드 window=0 설정, Zero Window Packet 송신
                    * 일정시간 경과 ( Server : Slow Read )
                    * Server 에서 ACK, Zero Window Probe Packet 전송 (상태확인용)
                    * Free 상태일 경우
                        - Client : 필드 window=200 설정, Window Update Packet 송신
                        - Server : 송신버퍼에 있는 데이터를 수신 window size에 맞춰 전송
                    * Full 상태일 경우
                        - Client : 필드 window=0 설정, Zero Window Packet 송신
                + 공격자는 window 크기를 조작, Zero Window Packet 을 지속적으로 전송
            - Hash DoS : 웹서버는 파라미터 관리를 위해 해시테이블 사용. 조작된 파라미터 정보를 포함한 다량의 정보를 전송해 해시값의 충돌을 유도 > 웹서버는 해시값 검색에 CPU 자원을 소모하게 됨
                + Hash Table (Key:Value 자료구조) > 속도에 유리
                    * Hash Function : 평문 => Function => 짧은 Msg
                    * Hashing : Hash Table + Hash Function 
                    * Key (예:aaabbb) 를 Hash Function 으로 짧은 값으로 만든 뒤 (예:00) > 00에 해당하는 Bucket 공간에 값을 저장
                    * 문제점 : 입력 값은 무한한데, Hash 값은 유한함 > 충돌 발생
                        - Hash 충돌을 최소화 시키는 Hash 함수를 이용해야 함
                    * Bucket 에는 주소값이 들어가 있음 (Linked List)
                        - 해당 주소에 변수명과 값이 저장되며 충돌이 발생했을 경우 또 다른 Linked List 에 연결됨 > 체이닝 기법
                        - CPU 자원소모
                    * 파라미터 (매개변수): `GET index.php?aaa=123&bbb=100` > aaa, bbb...
                        - GET 방식은 길이 제한이 있기 때문에 HashDos 공격 어려움
                        - POST 방식은 많은 매개변수를 한꺼번에 전달 가능
                            + Hash 충돌 발생
                + 대응책
                    * 최신 패치
        * TCP Traffic Flooding (TCP Session, SYN Flooding, TCP Slow Read)
            - TCP Session Flooding : 3Way-Handshake 과정을 지나치게 많이 발생시킴 
        * L7 Service Flooding (Hash DoS, Hulk DoS, FTP/SMTP Attack)
- UDP Flooding 공격
    + 다량의 UDP 패킷을 서버로 전송
    + 서버가 보유한 네트워크 대역폭을 소진
    + 정상적인 클라이언트의 접속을 하지 못하도록 함
- ICMP Flooding 공격
    + 다량의 ICMP 패킷을 서버로 전송
    + 서버가 보유한 네트워크 대역폭을 소진
    + 정상적인 클라이언트의 접속을 하지 못하도록 함



