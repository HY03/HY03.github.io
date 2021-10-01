---
title: 정보보안 - DNS(Domain Name System)
date: '2021-07-08 23:00:00'
tags:
- 정보보안기사
- 어플리케이션
related: true
categories:
- IS_Certification
toc: true
---

# DNS (Domain Name System)
- 개요
    + DNS 는 프로토콜이라기 보다는 인터넷 네임체계를 규정하는 거대한 시스템
        * `www.google.com` : 사람은 문자형태로 입력
        * `192.168.0.1` : 컴퓨터가 알아볼 수 있는 형태
        * DNS : 사람과 컴퓨터가 알아볼 수 있는 형태로 변환해주는 체계
- 과정
    + User -> DNS    Client PC    WebServer
        * User : 네임 입력
    + User    DNS -> Client PC    WebServer
        * DNS 역할
        * DNS : 네임 요청처리
        * DNS : 주소 변환
    + User    DNS    Client PC -> WebServer
        * Client PC : 자원요청
        * WebServer : 요청처리
    + User    DNS    Client PC <- WebServer
        * WebServer : 응답생성
        * Client PC : 응답처리
        * User : 자원접근
- 기능
    + 네임 공간 정의 (네임구조)
        * 계층구조 예시 : `www.google.com` (계층구조)
            - `com` : 최상위 도메인 (TLDs) 공간
                + `google` : 2단계 도메인
        * 루트서버 (전 세계 13개정도가 있음)
            - 도메인에 대한 정보를 가지고 있지 않음
            - 자신의 권한을 다른 서버들에게 위임, 서버만 참조
            - `.com` : TLDs
                + `.google` : 2단계 도메인
                + `.naver` : 2단계 도메인
                    * `blog` : 하위도메인
                    * `cafe` : 하위도메인
                    * `mail` : 하위도메인
            - `.edu` : TLDs
                + `.skku` : 2단계 도메인
            - `.net` : TLDs
                + `.daum` : 2단계 도메인
        * DNS 서버
            - 도메인을 담당하는 호스트
            - 저장되는 데이터
                + 자원 레코드 (Resource Record, RR)
                    * `www.google.com = 192.168.xx.xx`
                + 마스터 파일 (Zone 파일)
                    * 도메인에 대한 정보를 가지고 있음
                    * RR(복수개) 들을 관리
                    * RR의 추가, 변경, 삭제
                        - `music.google.com = 192.168.xx.xx`
        * Zone : 도메인을 관리하는 단위 (영역)
            - DNS 서버(네임서버) 하나가 책임이나 권한을 가지는 영역
            - 도메인은 위임을 통해 관리가 가능한 단위로 분할 가능
        * 자원 레코드 (Resource Record, RR)
            - DNS 서버는 모든 데이터를 RR로 정의해서 저장
                + 네임 (0~32)
                    * 도메인 혹은 Zone Name
                + 유형(Type) (0~16) | 클래스 (16~32)
                    * 유형(Type)
                        - |  유형값 | 문자코드 | RR유형 |
                        - | 1 | A | 주소 (IPv4) |
                        - | 28 | AAAA | 주소 (IPv6) |
                        - | 2 | NS | 네임서버 (이름) |
                        - | 5 | CNAME | 정규네임 (Canonical) |
                        - | 6 | SoA | 권한개시정보 (DNS Zone 정보) |
                            + 모든 Zone 은 하나의 SoA 레코드를 가지고 있음
                        - | 12 | PTR | 포인터 (다른 위치를 가리킴, 역방향 변환 이용) |
                        - | 15 | MX | 메일 교환 (도메인으로 들어오는 이메일 처리를 위한 장비 이름 명시) | 
                        - | 16 | TXT | 문자열 |
                    * 클래스
                        - | 1 | IN (Internet, 가장많이 사용) |
                        - | 2 | Unassigned (할당되지 않음) |
                        - | 3 | CH (Chaos) |
                        - | 4 | HS (Hesiod) |
                + 유지시간 (0~32)
                    * 레코드를 읽은 장비가 캐시에 얼마동안 정보를 유지해야 하는지 명시
                + 데이터 길이 (0~32)
                    * 자원에 대한 데이터 크기
                + 데이터 (0~32)
                    * 실제 데이터
                ```
                    이름 : www.google.com
                    유형 : 1
                    TTL (Time To Live) :
                    Size :
                    호스트 : 192.168.170.10
                    ...
                ```
        * DNS 서버
            - Master
            - Slave
        * DNS 메시지 구조
            - 사용자가 DNS 서버에 질의했을 때 받는 메시지
                + Header (12 Bytes)
                    * 메시지 유형 설명
                + Question (가변)
                    * 질의 정보
                + Answer (가변)
                    * 질문에 대한 답변 RR
                + Authority (가변)
                    * 권한 네임서버를 가리키는 하나 이상의 RR 포함
                + Additional (가변)
                    * 질의와 관련된 부가정보를 나타내는 하나 이상의 RR
    + 네임 등록
        * IP 주소 (예:192.168.0.10) 에 웹서버를 구축한 경우
            - 이름 `www.abc.com` 으로 접근시키기 위해서 네임 등록
            - `abc.com` 도메인 공간 안에서는 마음대로 등록 가능
                + `cafe.abc.com`
                + `blog.abc.com`
                + ...
    + 네임 변환
        * 사용자가 `www.abc.com` 으로 접근했을 경우
            - IP 주소 192.168.0.10 으로 변환시켜줌
        * DNS 네임 변환 과정
            - DNS => Client / Server 가 존재
                + 주소 => 이름
                + 이름 => 주소
                + Resolver (변환기) : 네임서버에 질의를 수행하는 SW / 모듈
            - 반복적 해석 (변환) : Iterative Query
                + Authoritative 서버에 질의할 경우
                + User -> 루트 NS    .Com 담당 NS    google 담당 NS
                    * google.com 변환 요청
                + User <- 루트 NS    .Com 담당 NS    google 담당 NS
                    * com 담당 네임서버 주소
                + User        ->     .Com 담당 NS    google 담당 NS
                    * google.com 변환 요청
                + User        <-     .Com 담당 NS    google 담당 NS
                    * google.com 담당 NS 주소
                + User                     ->         google 담당 NS
                    * google.com 변환 요청
                + User                     <-         google 담당 NS
                    * google.com IP 주소
            - 재귀적 해석 (변환) : Recursive Query
                + Recursive 서버에 질의할 경우
                + 중간 과정의 네임서버들이 모두 재귀적 변환을 허용해야 함
                    * 실제로는 그렇지 않음.
                + User -> 루트 NS    .Com 담당 NS    google 담당 NS
                    * google.com 변환 요청
                + User    루트 NS -> .Com 담당 NS    google 담당 NS
                    * google.com 변환 요청
                + User    루트 NS    .Com 담당 NS -> google 담당 NS
                    * google.com 변환 요청
                + User    루트 NS    .Com 담당 NS <- google 담당 NS
                    * google.com IP 주소
                + User    루트 NS <- .Com 담당 NS    google 담당 NS
                    * google.com IP 주소
                + User <- 루트 NS    .Com 담당 NS    google 담당 NS
                    * google.com IP 주소
            - 일반적인 DNS 네임 변환 과정
                + 반복적(Iterative) 변환과 재귀적(Recursive) 변환을 함께 사용함
                + DNS 서버의 종류
                    * Recursive DNS Server : 조건이 만족될 때까지 반복적으로 처리
                    * Authoritative DNS Server (권한서버)
                        - Root Server, .Com NS, google NS 등 특정 도메인 영역 (Zone) 과 권한을 가지고 있는 서버
                    * Cache : 캐시에 저장된 정보를 제공
                    * ISP 업체 (KT, SKT ... ) 가 제공하는 서버
                + Recursive Query 순서
                    * 1. DNS Cache 검색
                        - 자신에게 도메인 정보가 없을 시 DB를 검색하거나 타 서버에 요청해야 하나 시간이 오래 걸림
                            + 캐시로 저장
                                * 속도는 빠르나 잘못된 정보를 매핑할 수 있음
                            +  양성 캐싱(positive caching) : 한 번 질의 된 도메인 네임과 해당 IP 주소를 캐시에 유지하여 같은 질의가 올 때 캐시에서 응답해 주는 것
                            +  음성 캐싱(negative caching) : 잘못된 도메인에 관한 요청을 캐싱하여 불필요한 트래픽과 지연을 줄이는 역할 
                    * 2. hosts 파일 검색
                        - host 명 : IP 주소 정보를 담고 있는 파일
                        - `/etc/hosts` (리눅스) / `hosts.ics` (Internet Connection Sharing 기능 - 윈도우)
                            + 파밍사이트 접속유도를 위해 공격자가 목표로 하는 파일
                            + 변조 위험성이 큼
                    * 3. DNS 서버 질의
                        - Recursive DNS Server
                        - Authoritative DNS Server
                + 일반적인 과정
                    * 재귀적 질의
                        - 사용자가 브라우저에 URL 입력 : `comps.googleex.edu`
                            + Resolver : 사용자 DNS 클라이언트 소프트웨어
                        - PC 내 Resolver 가 PC 내 Cache 확인
                        - Resolver 가 로컬네임서버에 (Recursive DNS Server) 재귀적 질의
                        - 로컬네임서버가 Cache 확인
                    * 반복적 질의
                        - 로컬네임서버가 루트서버에 질의
                        - 루트서버가 .edu 네임서버 주소를 전달
                        - 로컬네임서버가 .edu 네임서버에 질의
                        - .edu 네임서버가 .googleex 네임서버 주소를 전달
                        - 로컬네임서버가 .googleex 네임서버에 질의
                        - .googleex 네임서버가 comps.googleex.edu 네임서버 주소를 전달
                        - 로컬네임서버가 comps.googleex.edu 네임서버에 질의
                        - comps.googleex.edu IP 주소를 전달
                    * 재귀적 질의
                        - 로컬네임서버가 Cache 갱신
                        - 로컬네임서버가 Resolver 에게 IP 전달
                        - Resolver 가 Cache 갱신
                        - Resolver 가 사용자에게 IP 주소를 전달
                        - 사용자 브라우저가 IP 주소로 HTTP 요청

# 리눅스 네임서버 설정파일
- `/etc/resolv.conf` : 기본 네임 서버 설정정보
    + resolver : 질의 변환기, 재귀적 질의
    + `cat /etc/resolv.conf`
        ```
            domain localdomain
            search localdomain
            nameserver 192.168.172.1 // 기본 네임서버 주소
        ```
- `/etc/hosts` : 도메인/호스트명과 IP주소 매핑 정보
    + hosts 파일은 대부분의 운영체제에 존재한다.
    + `cat /etc/hosts`
        ```
            192.168.10.100 www.abc.com
            ...
        ```
- `/etc/host.conf` : DNS 질의 순서를 지정
    + `cat /etc/host.conf`
        ```
            order hosts, bind // hosts 파일에 먼저 질의하고, bind(nameserver) 에 질의
        ```

# DNS 패킷 분석
- 개요
    + DNS Message 는 Query 패킷, Response 패킷이 있다.
    + DNS Message 구조는 아래와 같다.
        * Header
            - ID : 패킷 식별자 (요청-응답 쌍)
            - QR | OPcode | AA | TC | RD | RA | Z | RCODE : Flag
                + QR (Query/Response)
                    * DNS 메세지가 `DNS 질의(0)`,`DNS 응답(1)` 인지를 구분
                + Op code (Operation code) : 4 비트
                    * 0  :  `표준 질의` 또는 `표준 질의에 대한 응답`
                    * 1  :  역 질의 (Inverse Query)
                    * 2  :  서버의 상태 요구
                    * 4  :  통지
                    * 5  :  갱신
                + AA (Authoritative Answer, 책임 플래그)
                    * DNS 응답 메세지에 만 사용됨
                    * 1  :  네임서버 권한이 인정된 서버일 때
                + TC (Truncated)
                    * 512 바이트 초과 여부
                    * 1  :  응답메세지가 512 바이트 이상이라서  512로 짤리어짐
                        - 이 경우에 클라이언트는 TCP 포트를 통해 질의를 재전송하게 됨
                + RD (Recursion Desired, 재귀 요구 플래그)
                    * 1  :  클라이언트가 재귀 질의를 원함
                        - 질의메세지에서 지정되며, 응답메세지에서 이를 반복함
                        - 재귀질의가 요청되지 않으면, 반복질의로 간주하게됨
                + RA (Recursion Available, 재귀 유효 플래그)
                    * 네임서버가 재귀 질의가 이용 가능한지를 나타냄
                + 예약 (Reserved) : 통상 000 으로 셋팅
                + rCode (response code, 응답/오류 코드)               
                    * 0 => No Error, 1 => Format Error(질의를 이해할 수 없음), 2 => ServFail(Server Failure), 3 => 도메인네임 존재 않음 등
            - QDCOUNT (질의 카운트)
                + 질의의 수
            - ANCOUNT (응답 카운트)
                + 응답 RR(Resource Record)의 수
            - NSCOUNT (네임서버 카운트)
                + 책임 RR(Resource Record)의 수
            - ARCOUNT (추가정보 카운트)
                + 추가 RR(Resource Reocrd)의 수
        * Question
        * Answer
        * Authority
        * Additional
    + DNS 는 일반적으로 UDP 53번 포트를 사용 (512 Bytes 미만 패킷)
    + DNS 는 TCP 53번 포트로도 사용 가능 (512 Bytes 이상 패킷)
        * Zone Transfer (Zone 전달)
            - Master 서버에서 Slave 서버에 Zonefile(Masterfile) 전달
                + 주기적 전달
            - 신뢰할 수 있는 연결 필요
        * 큰 DNS 쿼리를 보낼 때 이용
        * UDP 패킷으로 질의 (query) 했으나 응답패킷 (Response) 이 512Bytes 를 초과할 경우 Response 가 Truncated 되고 TCP 로 요청하라는 메시지 전송

# DNS 관련 명령어
- DNS Cache 조회
    + 윈도우
        * `ipconfig /displaydns` : DNS 캐시 정보 조회
            - DNS 스푸핑 등 확인
        * `ipconfig /flushdns` : DNS 캐시 정보 초기화
            - 결과메시지 : `successfully flushed the DNS Resolver Cache`
- DNS 조회
    + 윈도우
        * `nslookup` : DNS 진단 유틸리티, 네임서버 검색, 대화형모드
            - `nslookup [호스트(도메인 이름/IP주소)] ([네임서버])` : 질의형
                + 정방향조회 (Forward DNS Lookup) : 도메인 -> IP
                + 역방향조회 (Reverse DNS Lookup) : IP -> 도메인
    + 리눅스 / 유닉스
        * `dig` : Domain Information Groper : nslookup 과 기능 유사하나 더 다양한 정보를 획득
            - `dig ([@네임서버]) [도메인] ([쿼리유형]) ([쿼리옵션])`
                + 쿼리유형
                    * `A` : 도메인 IP 주소 질의
                + 쿼리옵션
                    * `+norecurse` : Authoritative NS 에 반복적 질의 수행, 정상 응답이 되는지 여부를 체크
                        - `dig @ns.test.com www.daum.net +norecurse`
                    * `+tcp` : 53/tcp 포트를 허용하는지 체크
                    * `+trace` : 계층 구조의 쿼리 (Root -> .com -> ...) 위임의 상태를 추적
                        - `dig @ns.test.com www.daum.net +trace`
        * `whois` : whois 서버를 활용, 도메인 등록정보(소유정보) 확인
            - `whois www.test.com`
                + 등록인, 주소, 변경일, IP 주소 (ISP 주소), 네트워크 할당정보 등...
        * `host 호스트 [서버]` : nslookup 과 유사

# DNS 스푸핑
- 개요
    + 공격대상자에게 전달되는 DNS 응답을 위조
    + DNS서버에 위조된 IP 주소를 지정
    + 피해자가 의도하지 않은 주소로 접속하게 하는 공격 기법
    + DNS 프로토콜은 Query/Response 를 UDP 프로토콜을 사용하여 주고받음
        * UDP : 비연결성에서 생기는 취약점 이용
            - Transaction ID 값이 일치하면 응답을 신뢰하게 됨
            - 나중에 도착한 값은 폐기됨
- 방식
    + 스니핑을 이용한 스푸핑 공격
        * 희생자에게 전달되는 DNS 응답 위조
    + DNS 캐시 포이즈닝
        * DNS 서버의 캐시정보 조작
- DNS 스푸핑 과정
    + Client -> (Attacker) -> Name Server : Attacker 는 중간에서 Snipping
        * Transaction ID : 1000
        * Src Port : 4500
        * Q : www.test.com
        * A :
    + Client <-  Attacker     Name Server : Attacker 가 Spoofing 응답값 전달
        * 먼저 수신된 DNS 응답을 신뢰
        * Transaction ID : 1000
        * Dst Port : 4500
        * Q : www.test.com
        * A : 위조된 IP
    + Client <- (Attacker) <- Name Server : 정상응답은 폐기됨
        * Transaction ID : 1000
        * Dst Port : 4500
        * Q : www.test.com
        * A : xxx.xxx.xxx.xxx
- DNS 스푸핑 대응책
    + 스니핑을 탐지, 차단
    + DNS 서버 질의 전 우선순위가 높은 hosts 에 중요한 IP 주소를 등록관리
- DNS 캐시 포이즈닝 과정
    + Attacker -> Recursive Server
        * 여러 개의 질의를 한꺼번에 보냄
            - `www.test.com` 질의
            - Recursive Server 는 Authoritative 서버에 여러 번 질의 (반복적 질의에 시간 소요됨)
    + Attacker -> Recursive Server
        * 여러 개의 응답을 한꺼번에 보냄
            -  `www.test.com` 응답
                +  Transaction ID 무작위 입력
                +  Dst Port 무작위 입력
                +  IP 주소 조작
            -  Recursive Server 가 Authoritative 서버에 응답을 받기 전에 Transaction ID 와 Dst Port 가 일치하는 응답값을 전송
                +  뒤에 도착하는 실제 응답은 폐기됨
            -  TTL 지속 시간동안 조작된 IP 로 응답하게 됨

# Primary(Master) / Secondary(Slave) 네임 서버의 이해
- 주서버(Master) : 1차 네임서버
    + 원본 존 데이터를 관리하는 서버
- 보조서버(Slave) : 2차 네임서버
    + 마스터서버에 있는 존 데이터를 복사하여 가지고 있음
    + Master 와 동일한 기능을 함
        * 부하 분산을 통해 안정성을 높임
        * 마스터 장애시 대신 역할을 수행
- 구조
    + Master NS <-> Slave NS
        * Zone transfer : TCP 53
            - Master 서버와 Slave 서버 간 ZoneData 동기화
                + Zone File(MasterFile)
                + Reverse Zone File

# Recursive Query의 제한 방법
- 필요성
    + 대량의 재귀적 질의 요청이 발생하면 DoS 형태의 공격으로 악용될 수 있음
    + DNS Cache Poisoning 공격
    + 사내 DNS 서버의 경우 재귀적 질의 접근제어 설정이 필요
        * 설정파일 : `/etc/named.conf`
- `/etc/named.conf`
    ```
        acl "mynetwork" { 192.168.0.0/24;};
        options {
            listen-on port 53 {127.0.0.1; 192.168.0.254;};
            directory "/var/named";
            allow-query {mynetwork;};
            allow-recursion {none;}; // 재귀적 질의 제한 설정
            allow-recursion {127.0.0.1; 192.168.170.10/30;}; // 재귀적 질의 제한 설정
            recursion no; // // 재귀적 질의 제한 설정
            // allow-transfer {any;}; // 존전송 제한 설정
            allow-transfer {none;}; // 존전송 제한 설정
            //forwarders {0.0.0.0;};
        };
    ```
