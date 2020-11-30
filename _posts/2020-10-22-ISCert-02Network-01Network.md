---
title: 02.네트워크 보안 - 01.네트워크 개념
date: '2020-10-26 16:00:00'
tags:
- 정보보안기사
- 네트워크 보안
- 네트워크 개념
related: true
categories:
- IS_Certification
toc: true
---

# 네트워크와 프로토콜
- **프로토콜**
    + 메시지의 포맷, 언제 데이터를 보낼 것인지, 한번 전송하고 한번 응답할지 등.
    + OSI7 계층, TCP/IP 4계층
    + 다른 약속을 한 송 수신자 간 통신 : 게이트웨이 장비 필요
- 데이터 전송방식 (OSI7 -> 세션계층)
    + 단방향 (Simplex) : 키보드, 모니터
    + 반이중 (Half Duplex) : 송수신을 동시에 하는 것이 불가능
    + 전이중(Full Duplex) : 동시에 양방향 송수신

# 네트워크 토폴로지
- **계층형**
    + Tree 구조 / 최상위 노드가 네트워크 제어
    + 통제 및 유지보수 용이
    + 중앙 컴퓨터 장애 시 전체 통신망 영향
- **버스형**
    + 중앙 통신 회선 하나에 여러 대의 노드 연결
    + 하나의 컴퓨터가 다운되어도 나머지에 지장 없음
    + 우선순위 제어가 어렵고 충돌이 자주 발생
- **스타형**
    + 중앙 컴퓨터를 중심으로 터미널이 연결된 중앙집중형
    + 유지보수 및 관리가 용이
    + 중앙 컴퓨터 고장 시 네트워크 전체 마비
- **링형**
    + 인접해 있는 노드들을 연결하는 단방향 전송 형태
    + 단말기 추가 및 삭제가 어려움
- **망형**
    + 모든 노드들이 상호 연결
    + 가장 많은 통신 선로가 필요

# **OSI 7계층**

## 1. OSI 7계층 구조

|OSI 7계층|주요 내용|주요 프로토콜(매체)|
|:---|:---|:---|
|**7. Application**<br/>**(Gateway)**|⦁ 사용자 소프트웨어를 네트워크에 접근 가능하도록 함<br/>⦁ 사용자에게 최종 서비스를 제공|FTP, SNMP, HTTP, Mail, Telnet 등|
|**6. Presentation**|⦁ 포맷기능, 압축, 암호화<br/>⦁ 텍스트 및 그래픽 정보를 컴퓨터가 이해할 수 있는 16진수 데이터로 변환|⦁ 압축, 암호, 코드 변환<br/>⦁ GIF, ASCII, EBCDIC|
|**5. Session**|⦁ 세션 연결 및 동기화 수행, 통신 방식 결정<br/>⦁ 가상 연결을 제공하여 Login/Logout 수행|반이중, 전이중, 완전이중 결정|
|**4. Transport**|⦁ 가상연결, 에러 제어, Data 흐름 제어, Segment 단위<br/>⦁ 두 개의 종단 간 End-to-End 데이터 흐름이 가능하도록 논리적 주소 연결<br/>⦁ 신뢰도, 품질보증, 오류탐지 및 교정 기능 제공<br/>⦁ 다중화(Multiplexing) 발생|TCP, UDP|
|**3. Network**<br/>**(Router)**|⦁ 경로선택, 라우팅 수행, 논리적 주소 연결(IP)<br/>⦁ 데이터 흐름 조절, 주소 지정 메커니즘 구현<br/>⦁ 네트워크에서 노드에 전송되는 패킷 흐름을 통제하고, 상태 메시지가 네트워크 상에서 어떻게 노드로 전송되는가를 정의, Datagram(Packet) 단위|⦁ IP, ICMP, IGMP, ARP, RAPR<br/>⦁ 라우팅 프로토콜(RIP, OSPF, BGP)|
|**2. Data Link**<br/>**(Switch, Bridge)**|⦁ 물리주소 결정, 에러 제어, 흐름 제어, 데이터 전송<br/>⦁ Frame 단위, 전송 오류를 처리하는 최초의 계층|⦁ 흐름 제어, 오류 제어(ARQ)<br/>⦁ 브릿지, PPTP, L2TP, HDLC<br/>⦁ Frame Relay|
|**1. Physical**<br/>**(Repeater)**|⦁ 전기적, 기계적 연결 정의, 실제 Data Bit 전송<br/>⦁ Bit 단위, 전기적 신호, 전압구성, 케이블, 인터페이스 등을 구성<br/>⦁ Data Rates, line noise control, 동기화 기능 수행|매체: 동축케이블, 광섬유, Twisted Pair Cable|
    
    - 다중화(Multiplexing) : 여러 개의 소캣으로부터 데이터를 수집하여 헤더를 붙여 전송
    - End-to-End : 7~4 계층, 송수신자 간의 에러 Control
    - Point-to-Point : 3~1 계층, 각 구간에 대해 에러 Control

## 2. OSI 계층별 하드웨어 장비

|계층|장비명|설명|
|:---|:---|:---|
|Physical|**Cable**|Twisted Pair Cable, Coaxial, Fiber-Optic Cable|
||**Repeater**|⦁ 네트워크 구간 케이블의 전기적 신호를 재생하고 증폭하는 장치<br/>⦁ 디지털 신호를 제공, 아날로그 신호 증폭 시 잡음과 왜곡까지 증폭|
|Data Link|**Bridge**|⦁ 서로 다른 LAN Segment를 연결, 관리자에게 MAC 주소 기반 필터링 제공하여 더 나은 대역폭(Bandwidth) 사용과 트래픽을 통제<br/>⦁ 리피터와 같이 데이터 신호를 증폭하지만, MAC 기반에서 동작|
||**Switch**|⦁ 목적지의 MAC 주소를 알고 있는 지정된 포트로 데이터를 전송<br/>⦁ Repeater 와 Bridge의 기능을 결합<br/>⦁ 네트워크의 속도 및 효율적 운영, Data Link 계층에서도 작동|
|Network|**Router**|⦁ 패킷을 받아 경로를 설정하고 패킷을 전달<br/>⦁ Bridge는 MAC 주소를 참조하지만, Router는 네트워크 주소까지 참조하여 경로를 설정<br/>⦁ 패킷 헤더 정보에서 IP 주소를 확인하여 목적지 네트워크로만 전달하며 Broadcasting을 차단|
|Application|**Gateway**|⦁ 서로 다른 네트워크망과의 연결(PSTN, Internet, Wireless Network 등)<br/>⦁ 패킷 헤더의 주소 및 포트 외 거의 모든 정보를 참조|

# TCP/IP 4계층

## 1. **TCP/IP 구조**

|OSI 7계층|TCP/IP 4계층|주요 기능|
|:---|:---|:---|
|⦁ Application<br/>⦁ Presentaion<br/>⦁ Session|Application|⦁ 네트워크를 실제로 사용하는 응용 프로그램으로 구성<br/>⦁ FTP, TELNET, SMTP 등이 있음|
|Transport|Transport|⦁ 도착하고자 하는 시스템까지 데이터를 전송<br/>⦁ Port를 가지고 있으며, 프로세스를 연결 또는 비연결해서 통신함<br/>⦁ TCP,UDP|
|Network|Internet|⦁ Datagram을 정의하고 Routing하는 일을 담당<br/>⦁ IP, ARP, RARP, ICMP, IGMP|
|⦁ Data Link<br/>⦁ Physical|Network Access|케이블, 송수신기, 링크 프로토콜, LAN 접속과 같은 물리적 연결 구성을 정의|

## 2. TCP/IP 프로토콜의 구성 : TCP, UDP, IP, ICMP, ARP, RARP

![TCP IP Protocols](/assets/images/posts/tcp_ip_protocols.png)

[이미지 출처](http://www.tcpipguide.com/free/t_TCPIPProtocols.htm)

- **TCP** : Connection Oriented Protocol (연결지향)로 사용자에게 신뢰성 있는 서비스를 지원. 신뢰성 있는 서비스를 지원하기 위해서 Error Control 기법을 포함하고 있으며, 송신자가 보낸 메시지를 수신자가 전송받았는지 확인하기 위해서 수신자를 ACK 를 송신자에게 전송. 만약 ACK가 오지 않거나 동일한 ACK 번호가 오면 다시 전송
- **UDP** : Connectionless Protocol(비연결)로 데이터 전송을 보장하지 않는 비신뢰성 서비스를 제공하지만, TCP에 비해서 전송 속도가 빠른 특징을 가진다.
- **ARP** : IP Address 를 LAN 카드의 물리적 주소인 MAC 주소로 변환한다.
- **RARP** : MAC 주소를 IP Address 로 변환하는 역할을 수행한다.
- **ICMP** : 네트워크 오류와 상태를 점검하기 위해서 사용된다.
- **IP** : 네트워크 주소와 호스트 주소 정의에 의한 네트워크의 논리적 관리를 담당하는 것으로 송신자와 수신자의 주소를 지정한다.

# 네트워크 활용(TCP/IP 구조)

## 1. 애플리케이션 계층
- 일반 사용자들이 사용하는 프로그램이 있는 계층
    
    |서비스|내용|
    |:---|:---|
    |FTP|⦁ File Transfer Protocol<br/>⦁ 사용자 파일의 업로드 혹은 다운로드를 수행하는 프로그램<br/>⦁ 파일 전송을 위한 인터넷 표준으로 제어 접속과 데이터 접속을 위한 분리된 포트를 사용함|
    |DNS|⦁ Domain Name Service<br/>⦁ DNS Query를 사용해서 DNS Server에 URL을 전송하고 해당 URL에 매핑되는 IP 주소를 제공하는 서비스|
    |HTTP|⦁ Hyper Text Transfer Protocol<br/>⦁ 웹브라우저와 웹 서버 사이에 웹 페이지의 Request 와 Response 를 수행하는 프로토콜|
    |Telnet|⦁ 특정 지역의 사용자가 지역적으로 다른 곳에 위치한 컴퓨터를 온라인으로 연결하여 사용하는 서비스|
    |SMTP|⦁ Simple Mail Transfer Protocol<br/>⦁ RFC 821에 명시된 인터넷 전자우편을 위한 프로토콜로 메시지 전달을 위해서 **Store and Forward** 방식을 사용<br/>⦁ 암호화 및 인증 기능 없이 사용자의 e-Mail을 전송하는 프로토콜|
    |SNMP|⦁ Simple Network Management Protocol<br/>⦁ 네트워크에 대한 트래픽, 세션 등의 네트워크 상태를 모니터링하고 정보를 전달할 때 사용되는 프로토콜|

### 1. HTTP(Hyper Text Transfer Protocol)
+ HTTP 프로토콜 개요
    * WWW(World Wide Web)로 시작되는 인터넷에서 웹 서버와 사용자의 인터넷 브라우저 사이에 문서를 전송하기 위해 사용되는 통신 프로토콜
    * **TCP 기반 프로토콜의 80번 Port 를 사용하고 Request 및 Response 구조를 가짐**
    * **State-less 로 프로토콜을 구성함 (요청 / 응답 후 연결종료)w**
+ **HTTP Version 1.0**
    * 연결을 수행할 때마다 3-Way handshaking 기법을 사용
    * HTML 페이지를 수신 받고 완전히 연결을 종료시킴
    * 필요 시 다시 연결하고 페이지를 수신받음
+ **HTTP Version 1.1**
    * 연결을 종료하지 않고 유지되는 Keep Alive Connection 을 지원
    * 연속된 연결 요청이 있을 경우 HTML 페이지를 수신받고 연결을 종료하지 않은 상태에서 이미지 등을 요청하는 형태의 서비스
+ **HTTP Version 2.0**
    * 멀티플랙싱(Multiplexing), 헤더압축(Header Compression), 서버 푸시(Server Push) 등의 기술이 추가됨
        - 멀티플랙싱 : 순차적 연결이 아니라 동시 다발적인 양방향 통신 지원
        - 헤더압축 : 헤더정보를 1/3 수준으로 압축, 보다 빠르게 메시지 처리
        - 서버푸시 : 웹 서버가 웹브라우저에게 필요한 데이터를 알아서 미리 전송할 수 있는 기술로 요청횟수와 전송해야 하는 데이터의 양을 줄임
+ **HTTP 세션 연결 과정 (3-Way handshaking)**

![TLS Handshaking](/assets/images/posts/tls_handshake.gif)

[이미지 출처](https://www.ibm.com/support/knowledgecenter/SSFKSJ_9.0.0/com.ibm.mq.sec.doc/q009930_.htm)


+ **HTTP Request 시 Header 구조**
    * HTTP Header 와 Body 의 구분은 `\r\n\r\n`

    |구조|설명|
    |:---|:---|
    |**Request Method**|호출되는 메소드로 GET 혹은 POST, OPTIONS, PUT, DELETE, TRACE, CONNECT 인지를 나타냄|
    |**Accept**|웹 브라우저에서 사용 가능한 미디어 타입을 의미, text/*, text/html 등|
    |**Accept-Language**|웹브라우저가 인식할 수 있는 언어를 의미|
    |**User-Agent**|웹브라우저 정보를 의미|
    |**Accept-Encoding**|웹브라우저에서 제공되는 인코딩 방식을 의미|
    |**Host**|웹 서버의 기본 URL을 의미|
    |**Connection**|연결 지속 및 연결 끊기를 나타내며 Keep Alive 혹은 close|

* HTTP 요청 방식

    |요청 방식|내용|
    |:---|:---|
    |**GET 방식**|⦁ 서버에 전달할 때 데이터를 URL에 포함시켜서 요청<br/>⦁ 전송할 수 있는 데이터 양이 제한됨(2Kbyte 또는 4Kbyte)<br/>⦁ 예) aa.php?userid=a&password=a|
    |**POST 방식**|⦁ 서버에 전달할 때 데이터를 Request Body에 포함시킴<br/>⦁ 데이터 전송량의 제한이 없음<br/>⦁ 예)aa.php<br/>userid=a;password=a|

- HTTP Response 시 Header 구조

    |구조|설명|
    |:---|:---|
    |Status Code|HTTP 응답 코드를 의미하고 200은 성공적으로 처리한 것을 의미|
    |Server|서버 프로그램 이름과 버전 전송을 의미 예)`Apache/2.2.22 (Debian)\r\n`|
    |Expires|자원의 만기 일자를 의미|
    |Cache Control|캐시 사용 여부를 나타내며 no-cache는 캐시를 사용하지 않음|
    |Pragma|캐시 사용 여부를 나타냄. HTTP/1.0 에서 쓰이며 HTTP/1.1에서는 Cache-Control 이 쓰임|
    |Content-Encoding|응답되는 메시지의 인코딩 방식을 의미|
    |Content-Length|바이트 단위로 응답되는 리소스의 크기|
    |Keep-Alive|연결 유지 시간을 의미하며 위의 예는 timeout 5초, 최대 100초로 설정되어 있음|
    |Connection|Keep-Alive 사용 여부를 의미|
    |Content-Type|응답되는 미디어 타입|

* Cookie 와 Session

    |방식|Cookie|Session|
    |:---|:---|:---|
    |저장 위치|클라이언트|서버|
    |저장 형태|Text 형식|Object 형식|
    |종료 시점|쿠키 저장 시에 종료 시점을 설정하며, <br/>설정하지 않으면 브라우저 종료 시점이 됨|정확한 시점을 알 수 없음|
    |자원|클라이원트 자원|서버 자원|
    |용량|한 도메인당 20개, 쿠키 하나에 4Kb, 총 300개|용량 제한 없음|

### 2. SMTP (Simple Mail Transfer Protocol)

![SMTP](/assets/images/posts/how-does-email-work-mta-mua-msa-mda-smtp.svg)

[이미지 출처](https://afreshcloud.com/sysadmin/mail-terminology-mta-mua-msa-mda-smtp-dkim-spf-dmarc)

+ SMTP 개요
    * **RFC 821에 명시되어 있는 인터넷 전자우편 표준 프로토콜**
    * **Store-and-Forward 방식으로 메시지를 전달**
    * **송신자 메일 -> Store -> Mbox -> Forward -> 수신자 메일**
    * **TCP 25**
+ SMTP 기본 동작 방식
    * 송신자가 보낸 전자우편은 전자우편을 관리하는 메일서버에 전달됨
    * 메일 서버는 수신자의 전자우편 주소를 분석, 최단 경로를 찾아 근접한 메일 서버에게 편지를 전달
    * 최종 수신자 측 메일 서버에 도착하기까지 연속적으로 전달하는 중계 작업을 수행
    
    |구성요소|내용|
    |:---|:---|
    |MTA(Mail Transfer Agent)|메일을 전송하는 서버|
    |MDA(Mail Delivery Agent)|수신측에 고용된 우체부의 역할, MTA에게 받은 메일을 사용자에게 전달|
    |MUA(Mail User Agent)|사용자들이 사용하는 클라이언트 애플리케이션|

+ POP3와 IMAP 및 IMAP3

    |POP3|IMAP 및 IMAP3|
    |:---|:---|
    |⦁ **TCP 110번**으로 메일서버에 접속하여 저장된 메일을 내려받는 MDA 프로그램<br/>⦁ **메시지를 읽은 후 메일 서버에서 해당 메일을 삭제함**|⦁ **POP과 달리 메일을 내려받아도 메일 서버에 원본을 계속 저장**<br/>⦁ **IMAP 143 Port**|

### 3. FTP (File Transfer Protocol)
+ FTP 개요
    * 인터넷 표준으로 파일 전송을 위한 프로토콜
    * 해당 컴퓨터에 등록된 사용자만 전송 가능, 그 외에는 익명(Anonymous)으로 사용
    * ftpusers 에 등록된 사용자는 FTP 접근이 허용되지 않음
    * 제어 접속과 데이터 접속 포트가 분리되어 있음
+ FTP 전송모드
    * Active Mode
        ![FTP Active](/assets/images/posts/ftp_active.png)

        [이미지 출처](https://infosys.beckhoff.com/english.php?content=../content/1033/cx8190_hw/27021604587584011.html&id=)

        - **서버 TCP/21번 포트로 접속 시도**
        - **서버 TCP/20번 포트로 데이터를 송수신**
    * Passive Mode
        ![FTP Passive](/assets/images/posts/ftp_passive.png)

        [이미지 출처](https://infosys.beckhoff.com/english.php?content=../content/1033/cx8190_hw/27021604587584011.html&id=)

        - **서버 TCP/21번 포트로 접속한 후 두 번쨰 포트를 질의**
        - **서버는 클라이언트에게 데이터 연결을 위한 두 번째 포트(TCP/1024이상)을 알려줌**
        - **클라이언트는 서버가 알려준 두 번째 포트로 접속**

### 4. SNMP  와 NMS 
+ NMS (Network Management System) : 운영되는 네트워크의 안정성, 효율성을 높이기 위해서 구성,장애,통계,상태정보를 실시간 수집 및 분석하는 네트워크 관리시스템
+ SNMP (Simple Network Management Protocol) : 이 떄 사용되는 프로토콜

## 2. 전송 계층(Transport Layer)
- 수신측에 전달되는 데이터에 오류가 없고 데이터의 순서가 그대로 보존되도록 보장하는 연결 지향 서비스(Connection Oriented Service)의 역할을 하는 종단 간(End-to-End) 서비스 계층
- 신뢰성 있는 전송을 하는 TCP / 비신뢰성 전송을 하는 UDP 프로토콜이 존재
- **애플리케이션 계층의 메시지에 TCP 혹은 UDP의 Header 를 붙인 것을 Segment 라 함.**

### 1. **TCP** (Transmission Control Protocol)
+ 전송 계층의 프로토콜
+ **연결지향(Connection Oriented)**
    * 가상 연결을 수행
        - 실제 물리적으로 연결된 것이 아니어서 주기적으로 메시지(ICMP 프로토콜)를 송수신하여 송수신 가능 여부를 확인함.
+ **3-Way Handshaking**
    * SYN -> SYN+ACK -> ACK
+ **신뢰성 있는 데이터 전송**
    * 송신자가 메시지를 전송하면 수신자는 ACK를 되돌려서 수신 여부를 확인해줌
    * 동일한 ACK 번호를 반복적으로 전송한다면 어떤 이유로든 데이터를 받지 못하는 것이고, 반복적인 ACK가 되돌아오면 TCP는 에러 제어기법을 통해 재전송을 수행함
+ **순서 제어(Sequence Control)**
    * Sequence 번호를 가지고 메시지의 순서를 파악
        - 비동기 방식으로 데이터 전송 시 다른 경로로 보내질 수 있기 때문에 송신자의 메시지가 꼭 순서에 따라 도착하지 않음
+ **완전 이중(Full Duplex)**
    * 송신과 수신을 동시에 함
+ **에러 제어**
    * FEC(Forward Error Correction)
        - 송신 측이 특정한 정보 비트를 함께 전송하여 수신측에서 이 정보 비트로 에러 발생 시 수정하는 방식 (수신측이 에러 처리)
        - 재전송 요구가 없음
        - 오류 검출 및 수정을 위한 잉여 비트 추가 -> 전송 효율 감소
        - 해밍 코드, 상승 코드 등
    * BEC(Backward Error Correction)
        - 수신 측이 에러 검출 후 송신측에게 에러가 발생한 데이터 블록을 다시 전송 요청
        - 패리티 검사, CRC 등 Checksum 을 이용해 오류 검출 후 오류 제어는 ARQ(Auto Repeat Request) 가 처리
            + Checksum 은 TCP, UDP 모두에 존재, 송신중에 메시지의 변조를 파악하기 위한 송/수신자 간 에러 체크 방법
        - BEC 기법 특징

        |기법|Stop-and-Wait|Go-Back-N(TCP)|Selective Repeat|
        |:---|:---|:---|:---|
        |재전송 요청 방법|에러발생 즉시 재전송|오류 발생 또는 잃어버린 프레임 이후의 모든 프레임을 재요청하거나 타임아웃으로 자동 재송신 됨|오류 발생 또는 잃어버린 프레임에 대해서만 재요청 또는 타임아웃으로 인한 자동 재송신|
        |수신 방법|순차적으로 수신|프레임의 송신 순서와 수신 순서가 동일해야 수신|순서와 상관없이 윈도우 크기만큼의 범위 내에서 자유롭게 수신|
        |장단점|⦁ 가장 단순한 구현<br/>⦁ 신뢰성 있는 전송<br/>⦁ 대기시간 존재로 전송 효율 저하|⦁ 간단한 구현<br/>⦁ 적은 수신측 버퍼 사용량|⦁ 구현이 복잡<br/>⦁ 버퍼 사용량이 많음<br/>⦁ 보다 적은 재전송<br/>⦁ 대역폭|

+ **흐름 제어 (Flow Control)**
    * 송수신측 사이의 전송 패킷의 양, 속도를 조절하여 네트워크를 효율적으로 사용
    * 수신자가 메시지를 제대로 받지 못하면 송신자가 전송 속도를 낮춤
    * 수신자의 Buffer Overflow 를 방지
    * 슬라이딩 윈도우(Sliding Window) (TCP)
        - TCP 호스트 간 효율적 데이터전송을 위해 송수신 할 수 있는 Size 정보를 제공, Size를 맞춤 / Ack 를 묶어서 처리
        - Stop-and-Wait의 단점을 보완한 방식으로 수신측의 확인 신호를 받지 않더라도 미리 정해진 프레임의 수만큼 연속적으로 전송
+ **혼잡제어 (Congestion Control)** 
    * Router 가 처리할 수 있는 packet 양보다 더 많은 packet 전송 시 비효율
    * TCP Slow Start
        - TCP가 시작될 때 전송속도를 초기값부터 지속적으로 올리는 방법
        - Receive Windows : 수신자의 윈도우 크기 (메모리 버퍼). 수신자의 버퍼 상태를 보고 전송속도를 조절
        - Sender 에서 packet을 전송하는 비율과 Receiver 에서 수신된 ACK를 통해 congestion window(cwnd) 를 지수의 크기로 증가시키는 방법
+ TCP 상태전이
    ![TCP Status Diagram](/assets/images/posts/tcp_status_diagram.gif)

    [이미지 출처](https://www.ibm.com/support/knowledgecenter/en/SSLTBW_2.1.0/com.ibm.zos.v2r1.halu101/constatus.htm)
    * **`netstat`** 명령어로 TCP 상태 확인
        - **`netstat -b`** : 어떤 프로세스가 네트워크를 사용하는지 확인
+ TCP 헤더 구조

    ![TCP Header](/assets/images/posts/Ntwk_tcp_header.jpg)

    [이미지 출처](https://commons.wikimedia.org/wiki/File:Ntwk_tcp_header.jpg)

* **Control Flag**
    - SYN : 연결 동기화
    - ACK : 응답 확인
    - PSH : 버퍼링 미 수행, 송신요구
    - URG : 긴급 포인터
    - FIN : 정상 접속 종료
    - RST : 비정상 종료를 위한 Reset

### 2. UDP (User Datagram Protocol)
+ 비연결성, 비신뢰성의 특성으로 Packet을 빠르게 전달
+ 송수신의 여부에 대한 책임을 Application이 가진다.

    |특징|내용|
    |:---|:---|
    |**비신뢰성(Unreliable)**|Packet을 목적지에 성공적으로 전송한다는 것을 보장하지 않음|
    |**비접속형(Connectionless)**|전달되는 Packet에 대한 상태 정보를 유지하지 않음|
    |**간단한 Header 구조**|TCP에 비해 헤더 구조가 간단하여 처리가 단순함 (TCP 세그먼트의 헤더 20byte, UDP 세그먼트의 헤더 8byte)|
    |**빠른 전송**|TCP에 비해 전송속도가 빠름|

+ UDP 프로토콜의 Header 구조

    ![UDP Header](/assets/images/posts/UDP_header.png)

    [이미지 출처](https://commons.wikimedia.org/wiki/File:UDP_header.png)
    * 가상 선로(Virtual Circuit)의 개념이 없는 비연결성
    * 블록 단위로 데이터 전송
    * 블록 재전송 및 흐름 제어 등이 없음 (데이터 신뢰성이 없음)
    * CRC 무결성 검사
    * 슬라이딩 윈도우 등의 복잡한 기술을 사용하지 않음
    * UDP의 각 사용자는 16bit의 포트 번호를 할당 받음

+ **UDP 를 사용하는 VoIP(Voice Over IP)**
    * 빠르게 데이터를 전송해야 하는 서비스
    * 내부적으로 2개의 프로토콜을 사용
        - RTP(Real Time Protocol) - 음성을 전송
        - RTCP(Real Time Control Protocol) - 에러처리
    * 스니핑 노출로 인한 사생활 침해 취약점
    * DDoS 보안 취약점

## 3. 인터넷 계층(Internet Layer)
- 송신자의 IP 주소와 수신자의 IP 주소를 읽어 경로 결정, 전송하는 역할
- 다중 네트워크 링크를 통해 패킷의 발신지 대 목적지 전달에 대한 책임을 가짐 (DataLink 층은 노드간 전달 책임) (Point to Point)
- 인터넷 계층은 IP, ICMP 의 TCP/IP 프로토콜 군이 존재하고 멀티캐스팅을 위한 IGMP(Internet Group Management Protocol), 라우팅을 위한 BGP, OSPF, RIP 등이 존재
- 경로결정(Routing) 은 수신자의 IP 주소를 읽어서 어떻게 목적지까지 가는 것이 최적의 경로인지를 판단하는 것이며, 이러한 작업을 하는 것은 라우터(Router)라는 네트워크 장비이다.

- 기능
    + 경로설정(Routing) : 경로를 결정
    + Point to Point packet 전달
    + 논리 주소 지정 (Logical Addressing) : IP 주소로 변환해서 사용
    + 주소 변환 (Adress Transformation)
    + 데이터그램(Datagram) : 기존 패킷(Packet)에 IP Header 를 붙이는 것을 의미

### 1. 라우팅

#### 1. 개념
+ 정적 경로 (Static Routing) 
    * 관리자가 최적의 경로로 설정
    * 라우터의 직접적인 처리 부하 감소
    * 환경변화가 적은 형태의 네트워크에 적합
    * Floating static routing
+ 동적 경로 (Dynamic Routing)
    * 네트워크 환경변화에 능동적인 대처가 가능
    * 라우터 간에 자동으로 정보를 교환, 경로 설정
    * 수시로 환경이 변화되는 형태의 네트워크에 적합
    * Distance Vector routing, Linked state routing
+ 라우팅 범위에 따른 라우팅 프로토콜 종류
    * IGP(Internal Gateway routing protocol) : 동일 그룹(기업 또는 ISP) 내에서 라우팅 정보를 교환
    * EGP(Exterior Gateway routing protocol) : 다른 그룹과의 라우팅 정보를 교환
+ **라우팅 프로토콜 분류**

    |구분|Distance Vector|Link State|
    |:---|:---|:---|
    |알고리즘|최단경로(Shorter Path)를 구하는 벨만 포드(Bellman-Ford) 알고리즘 기반|최소 신장 트리(Shortest Path Tree)를 구하는 다익스트라(Dijkstra) 알고리즘 기반|
    |동작 원리|네트워크 변화 발생 시 해당 정보를 인접한 라우터에 정기적으로 전달 하고, 인접 라우터에서는 라우팅 테이블에 정보 갱신|⦁ 라우터와 라우터를 연결하는 Link 상태에 따라 최적의 경로 설정<br/>⦁ 라우터가 Network 변화 감지 시 링크 상태변경 정보를 인접한 라우터에게 즉각 전달하고 이를 저장함|
    |라우팅 정보|모든 라우터까지의 거리 정보 보관|인접 라우터까지의 Link Cost 계산|
    |정보전송 시점|일정주기(30초, 이웃 라우터와 공유)|변화 발생 시에만|
    |대표 프로토콜|RIP, IGRP, EIGRP(내부 라우팅), BGP|OSPF(가장 많이 사용됨), IS-IS(소규모 N/W에서 사용)|
    |단점|⦁ 변화되는 라우팅 정보를 모든 라우터에 주기적으로 갱신하므로 망 자체의 트래픽을 유발<br/>⦁ 라우팅 정보를 변경하는 문제 발생 시 Routing Loop가 발생할 가능성이 있음|⦁ 네트워크 전체 정보 유지를 위한 많은 메모리 소요<br/>⦁ 라우터의 CPU 사용량이 많아짐<br/>⦁ 라우터간 회선의 대역차이로 동기화 실패할 가능성이 있음|

    * Distance Vector
        - 통과해야 하는 라우터의 수가 적은 쪽으로 경로를 결정
        - 홉 카운터(Hop count) / TTL (Time to Live)
        - RIP, IGRP
    * Link State
        - 네트워크 대역폭, 지연 정보 등을 종합적으로 고려해 Cost 산정
        - 주기적으로 지연과 같은 정보를 라우터 간에 공유해야 하고 라우터 브로드캐스트를 통해서 공유함
        - OSPF, EIGRP(애매함)
    * Path Vector
        - Distance Vector 와 Link State 의 Hybrid 형태
        - Hop count 및 Bandwidth, Delay 등의 다양한 정보로 경로를 설정
        - 정책기반으로 라우팅 정보 업데이트
        - BGP

+ 스패닝 트리(Spanning Tree)
    * 스위치나 브리지에서 발생하는 루핑을 막는 프로토콜 
    * 출발지부터 목적지까지의 경로가 두 개 이상 있는 경우 한 개의 경로만 남겨두고 나머지 경로는 끊어서 루핑을 방지

#### 2. RIP (Routing Information Protocol)

|구분|내용|
|:---|:---|
|개념|RFC 1058에 정의되어 있고 대표적인 거리 벡터 라우팅 프로토콜|
|동작 원리|⦁ 라우터 간 거리 계산을 위한 척도로 홉수(Hop count) 사용<br/>⦁ 16Hop 이상이면 패킷을 폐기<br/>⦁ 180초 이내에 새로운 라우팅 정보가 수신되지 않으면 해당 경로를 이상 상태로 간주<br/>⦁ 수신된 목적지의 거리 값과 현재 거리 값을 비교하여 작을 것을 기준으로 라우팅 테이블을 변경|
|라우팅 정보|라우팅 정보 변경 시 모든 망에 적용하므로 큰 규모의 망에는 적합하지 않음|

#### 3. OSPF (Open Shortest Path First)

|구분|내용|
|:---|:---|
|개념 및 특징|⦁ RFC 1247에 정의되어 있는 IP 라우팅 프로토콜<br/>⦁ 대규모 IP 망에서 사용되며 Link State Routing Protocol<br/>⦁ 링크에서의 전송 시간을 링크 비용으로 사용하여 각 목적지별 최단경로를 구함<br/>⦁ 네트워크에 변화가 발생했을 때 상대적으로 짧고 간단한 링크 상태 정보를 교환|
|동작 원리|⦁ Link의 delay, throughput, reliability 를 이용하여 기본적은 throughput 만 이용하고, Hop 수에 대한 제약이 없음<br/>⦁ 네트워크를 Area로 구분하여 많은 라우팅 정보의 교환으로 인한 라우터의 성능저하를 예방. 대역폭을 절약함<br/>⦁ Link 변화 감지 시 해당 Link에 대한 정보만을 즉시 모든 라우터에 전달하여 Convergence가 매우 빠름<br/>⦁ Supernetting 된 형태로 Routing Information 을 전달할 수 있어 라우터 메모리 절약, 상능 향상 및 대역폭 절약 가능|

- OSPF의 동작 원리 구성도의 예
    ![OSPF](/assets/images/posts/ospf.gif)

    [이미지 출처](https://wiki.mikrotik.com/wiki/Testwiki/IP_routing)
    + ABR (Area Border Router, 영역 경계 라우터) : Area에 백본망을 연결해주는 라우터
    + ASBR (Autonomous System Boundary Router, 자율 시스템 경계 라우터) : 다른 AS(Autonomous System)에 속한 라우터와 경로정보 교환
    + IR (Internal Router, 내부 라우터) : Area에 접속한 라우터
    + BR (Backbone Router, 백본 라우터) : 백본망에 접속한 모든 라우터

#### 4. BGP (Border Gateway Protocol)

|구분|내용|
|:---|:---|
|개념|AS(Autonomous System) 상호 간의 routing 에 적용되는 routing protocol 로 ISP(Internet Service Provider) 사업자들 상호 간에 주로 사용되는 routing protocol|
|특징|⦁ EGP라고 하는 Inter-domain routing protocol<br/>⦁ 인터넷을 AS 상호 간 연결 Path로 이뤄진 방향성 그래프의 집단으로 인식 : 경로 벡터 라우팅(path vector routing) 방법을 적용하며, 경로 벡터 라우팅 테이블을 유지<br/>⦁ Looping free routing<br/>⦁ TCP 기반 routing : BGP 라우팅 정보는 router 간에 179번 포트를 통하여 TCP에 의해 신뢰성 있게 전달<br/>⦁ Routing 정보의 점진적 갱신 : BGP 는 주기적으로 라우팅 정보를 갱신하지 않고, 변화가 있을 때 neighbor router에게 갱신 정보를 advertising 함<br/>⦁ 다양한 routing metric 사용 : 최상위 경로 선택을 위해 우선순위가 있는 각 metric을 참조<br/>⦁ Class 없는 주소체계 지원 : CIDR(Classless Inter-Domain Routing) 지원<br/>⦁ IBGP(Internal BGP) : 동일한 AS에 속한 BGP 라우터 간의 동작<br/>⦁ EBGP(External BGP) : 서로 다른 AS에서 동작하는 BGP 프로토콜|

### 2. IP(Internet Protocol)

#### 1.개념
+ TCP/IP 의 네트워크 계층(IP계층) 은 **주소화, 데이터그램 포맷, 패킷 핸들링** 등을 정의해놓은 인터넷 규약이다.
+ IPv4 : **32비트** 주소체계를 사용하고 있고 이러한 주소체계는 네트워크 ID와 호스트 ID로 구분된다.
+ IPv6 : IP 주소의 부족 문제를 해결하기 위해서 주소 비트 수를 **128비트**로 늘린 것
+ 네트워크 계층 구조
    ![IPv4 Packet](/assets/images/posts/IPv4_Packet_-en.svg.png)

    [이미지 출처](https://en.wikipedia.org/wiki/IPv4)
+ **IP Header 구조**
    * **Version** : **IPv4 버전**
    * **Header Length** : **Header의 전체 길이**
    * **Type of Service** : **서비스 유형**
    * **Total Length** : **IP Datagram의 byte 수**
    * **Identification** : **Host에서 보낸 Datagram 식별**
    * **Flags & Offset** : **IP Datagram 단편화 정보**
        - **MTU(Maximum Transmission Unit)** : **네트워크 상 한 번에 통과할 수 있는 패킷의 최대 크기**
        - **패킷의 크기가 너무 크면  (MTU 보다 크면) 분할되고, 패킷이 분할된 경우 패킷 수신자가 다시 조립을 해야 하는데 이와 관계된 정보**
    * **Time to Live(TTL)** : **Datagram이 통과할 수 있는 라우터 수**
        - 0이 되면 자동 폐기
    * **Protocol** : **ICMP, TCP, UDP**
        - IP Header 위의 상위 프로토콜의 종류
    * **Header Checksum** : **IP Header Checksum 계산**
        - Header 의 무결성을 검사하기 위함
+ **IPv4의 단편화(Fragmentation) 와 재결합(Reassembly)**
    * `ifconfig` 로 MTU 확인 (1500 = 국제표준)
+ IP 주소의 구조
    * 클래스(Class)
        - 클래스의구조 / 네트워크ID / 호스트ID
        - 네트워크 ID : 네트워크에 부여될 수 있는 것
        - 호스트 ID : 하나의 네트워크에 부여될 수 있는 호스트 IP 주소 자릿수

    * **클래스(Class) 구조**

        |Class|Message|
        |:---|:---|
        |Class A|⦁ 첫 바이트 7Bit가 네트워크 식별자<br/>⦁ 한 네트워크에 가장 많은 호스트를 가짐<br/>⦁ 최상위 비트 0|
        |Class B|⦁ 14Bit의 네트워크 식별자<br/>⦁ 한 네트워크에 약 2^16대의 호스트 수용<br/>⦁ 최상위 비트 10|
        |Class C|⦁ 세 번째 바이트까지 네트워크 식별자<br/>⦁ 한 네트워크에 254대까지 수용<br/>⦁ 최상위 비트 110|
        |Class D|멀티캐스트 주소로 사용 <br/>⦁ 최상위 비트 1110|

    * 서브넷팅(Subnetting)
        - 주어진 네트워크 주소를 작게 나누어 여러 개의 서브넷(논리적)으로 구성
        - 네트워크 식별자 부분을 구분하기 위한 mask를 서브넷 마스크(Subnet Mask)라고 함
        - 예) 1.0.0.1 / 24 : 서브넷마스크를 2진수 표현 시 1의 개수(CIDR 표기법)
        - 예) 255.255.255.192/26 : 4개의 네트워크, 네트워크당 IP 64개
        
            |클래스 종류|네트워크 ID|||호스트 ID|
            |:---:|:---:|:---:|:---:|:---:|
            |일반적인 Class C|11111111|11111111|11111111|00000000|
            |subnet mask|255|255|255|0|
            |2비트를 사용하여 서브넷팅|11111111|11111111|11111111|11000000|
            |subnet mask|255|255|255|192|

    * 수퍼넷팅(Supernetting) : ISP 사업자가 많은 네트워크 망을 관리하기 위해 묶은 것
    * VLSM(Variable Length Subnet Mask) :서로 다른 크기의 Subnet을 지원, 필요한 호스트의 수가 많은 Subnet을 먼저 계산
    * CIDR : IP 주소와 서브넷 마스크를 이진 표기법으로 표현하여 기존의 고정크기 네트워크를 다양하고 세부적으로 분할. 기존 클래스 A,B,C 네트워크 주소의 개념을 무시 (IPv4 주소 공간의 효율적 할당)

#### 2. ICMP (Internet Control Message Protocol)
- TCP/IP 에서 오류 제어 프로토콜
- 호스트 및 라우터는 다른 호스트나 라우터가 현재 도달 가능한지 여부를 결정함
- 라우터는 특정 목적지 네트워크로 후속 IP 데이터그램을 보냈는데, 사용할 수 있는 더 좋은 경로가 있을 때 근원지 호스트에게 통지함.
- 호스트나 라우터는 그들이 처리하기에 너무 빠른 IP 데이터그램이 도착하면 다른 시스템에게 통보함.
- ICMP 주요 기능
    + 오류 보고 메시지 : IP 패킷 처리 도중 발견된 문제를 보고
    + 질의 메시지 : 다른 호스트로부터 특정 정보를 획득하기 위해 사용
- ICMP 메시지 구조
    + Type : ICMP 메시지 유형 표시
    + Code : Type과 같이 사용되며 세부적인 유형을 표현
    + Checksum : IP Datagram Checksum
- **ICMP 메시지**

    |Type|Message|설명|
    |:---:|:---|:---|
    |3|**Destination unreachable**|Router가 목적지를 찾지 못할 경우 보내는 메시지|
    |4|**Source quench**|패킷을 너무 빨리 보내 Network에 무리를 주는 호스트를 제지할 떄 사용|
    |5|**Redirection**|패킷 Routing 경로를 수정, Smurf 공격에서 사용|
    |8 or 0|**Echo request or reply**|Host의 존재를 확인|
    |11|**Time exceeded**|패킷을 보냈으나 시간이 경과하여 Packet이 삭제되었을 때 보내는 메시지|
    |12|**Parameter problem**|IP Header field에 잘못된 정보가 있다는 것을 알림|
    |13 or 14|**Timestamp request and reply**|Echo와 비슷하나 시간에 대한 정보 추가|

- **ping 명령어 실행**
    + **윈도우 기본 TTL** : 128
    + **리눅스 기본 TTL** : 64

#### 3. 데이터 전송 방식과 멀티캐스트

- 데이터 전송방식

    |전송방식|내용|
    |:---|:---|
    |**Unicast**|1:1 전송 방식|
    |**Broadcast (IPv4)**|1:N 전송방식 (동일한 서브넷 상의 모든 수신자에게 전송)|
    |**Multicast**|M:N 전송방식 (하나 이상의 송신자들이 특정 그룹의 수신자에게 전송)|
    |**Anycast (IPv6)**|그룹에 등록된 노드 중에서 최단 경로 노드 한개에만 전송|

#### 4. 멀티캐스트와 IGMP (Internet Group Management Protocol)
- 멀티캐스트는 그룹에 등록된 사용자에게만 데이터를 전송하는 것
- IGMP 는 그룹에 등록된 사용자를 관리하는 프로토콜
- IGMP 메시지 구조

    <table>
        <tr><td>14bytes</td><td>20bytes</td><td colspan=4>8bytes</td></tr>
        <tr><td>이더넷 헤더</td><td>IP 헤더</td><td colspan=4>IGMP 메시지</td></tr>
        <tr><td colspan=2></td><td>Version(=0)</td><td>Type(=1-2)</td><td>(unused)</td><td>Checksum</td></tr>
        <tr><td colspan=2></td><td colspan=4>32bit 클래스 D 주소</td></tr>
    </table>

    + 8Byte 구성
    + Version : IGMP 프로토콜의 버전 표시, 현재 IGMP Version 2
    + Type : 메시지 유형, 1 = 보고, 2 = 질의메시지
    + Group id
        * 보고 메시지 : 호스트에서 신규 가입하고자 하는 멀티캐스트 서비스 group id / 계속 받아보기를 원하는 멀티캐스트 서비스의 group id
        * 질의 메시지 : 0

#### 5. ARP (Address Resolution Protocol)
- **IP 주소를 물리적 하드웨어 주소인 MAC 주소로 변경하는 프로토콜**
- ARP Request를 보내고 인접에 있는 컴퓨터가 ARP Reply 로 응답한 것을 통해서 **ARP Cache Table**을 유지한 후 인접 컴퓨터의 IP 주소와 MAC 주소를 가지고 있게 됨
- ARP Cache Table : MAC 주소와 IP 주소 매핑 테이블
- ARP Operation Code (1-9)
    + 1 : ARP Request
    + 2 : ARP Reply
    + 3 : RARP Request
    + 4 : RARP Reply
- `arp` : 리눅스에서 ARP Cache Table 확인
- `arp -a` : 윈도우에서 ARP Cache Table 확인

#### 6. RARP (Reverse Address Resolution Protocol)
- **물리적인 주소 MAC을 기반으로 논리적인 주소 IP를 알아오는 프로토콜**
- 하나의 호스트를 RARP 서버로 지정
- 디스크 없는 워크스테이션은 RARP Request 패킷을 전송 (브로드캐스트)
- RARP 서버는 인터넷 주소를 RARP Response 패킷으로 응답 (유니캐스트)

## 3. 네트워크 접근(Network Access) 계층

### 1. 네트워크 접근(Network Access) 계층
+ 논리적 주소인 IP 주소를 물리적 MAC 주소로 변환
+ Physical Layer 가 이해할 수 있는 헤더를 붙여주는 Layer
+ Frame 단위, Mac Adress를 사용하는 계층
+ 통신기기 사이의 연결, 데이터 전송 기능
+ 에러제어, 흐름 제어
+ OSI 7 계층의 데이터 링크 및 물리 계층에 해당

### 2. 네트워크 접근 주요 기능
+ Point-to-Point 간 신뢰성 있는 전송으로 흐름 제어(Flow Control) 담당
+ 에러 제어(Error Control) : Error Detection(에러검출), Error Correction(에러정정)
+ MAC(Media Access Control) : Lan 카드의 물리적 주소
+ Frame 으로 변환

### 3. CSMA/CD (Carrier Sense Multiple Access/Collision Detection)
+ **유선 LAN(Local Area Network) 에서 메시지를 송수신하기 위한 접근 방법**
+ 충돌이 발생하지 않는 경우
    * 전송을 원하는 호스트는 네트워크에 캐리어를 감지해 전송이 가능한지 검사 (데이터 프레임 전송)
    * 발생한 프레임은 네트워크 내에서 Broadcast 됨
    * 다른 호스트는 목적지 IP 주소가 자기가 아니라는 것을 알면 프레임을 바로 폐기
    * 목적지 호스트는 전송 호스트에게 Unicast 로 응답 (Shared Device Hub 네트워크에서는 유니캐스트와 브로드캐스트의 차이가 없음)
+ 충돌이 발생한 경우
    * 각각의 호스트에서 발생한 프레임이 공유 매체에서 Collision을 발생시킴
    * Collision 이 발생되면 Jam Signal을 모든 호스트로 전송
    * JamSignal을 받은 호스트들은 일정 시간 후에 다시 전송을 시작 (Back off 알고리즘을 이용하여 재전송)
    * 리피터와 허브를 이용하면 Collision이 발생하지만, Switch 는 Collision을 막을 수 있음.
    * Switch는 브로드캐스트를 막을 수 없고, Router 가 브로드캐스트를 막을 수 있음.

### 4. CSMA/CA (Carrier Sense Multiple Access/Collision Avoidance)
+ **무선 LAN(Local Area Network) 에서 메시지를 송수신하기 위한 접근 방법**
+ 프레임 전송 이전에 제어 메시지를 이용하여 수신자로부터 간단한 전송을 유발
+ 제어 신호에 대한 응답이 도착하면 프레임을 전송

### 5. LAN Switch

|LAN Swtich 방식|설명|
|:---|:---|
|**Cut through**|목적지의 MAC Address만 확인 후 해당 포트로 전송|
|**Store and Forward**|전채 Frame을 모두 저장하여 Error Check를 수행 후 전송|
|**Fragment Free**|⦁ Modify Cut Through<br/>⦁ Frame의 64 Bit를 검사, Header의 Error를 검사 후 전송<br/>⦁ 512Bit가 수신 될 때까지 대기 후 에러가 존재하지 않으면 전송하는 방식|


