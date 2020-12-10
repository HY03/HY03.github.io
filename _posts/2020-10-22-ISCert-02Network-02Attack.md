---
title: 02.네트워크 보안 - 02.네트워크 공격기법
date: '2020-10-27 14:00:00'
tags:
- 정보보안기사
- 네트워크 보안
- 네트워크 공격기법
related: true
categories:
- IS_Certification
toc: true
---

# 서비스 거부 공격(DoS : Denial of Service)
- 특정 서비스를 계속적으로 호출하여 CPU, Memory, Network 자원을 고갈시킴

## 1. 공격 방식
+ 소프트웨어 취약점을 이용
+ IP Header를 변조하는 로직 공격
+ 무작위로 패킷을 발생시키는 플러딩 공격
+ DDoS(Distributed Denial of Service) 공격은 여러 대의 공격자 서버가 분산되어 있고 특정 시스템을 집중적으로 공격하는 방법

## 2. Dos 및 DDoS 공격 도구

|도구|Trinoo|TFN|Stacheldraht|
|:---|:---|:---|:---|
|공격방법|UDP Flood|UDP, ICMP, SYN Flood, Smurf|UDP, ICMP, SYN Flood, Smurf|
|암호 기능|없음|없음|가능|
|Attacker<->Master|27665/tcp|Telnet 등 방법(별도의 연결 없음)|1660/tcp(암호화)|
|Master<->Agent|27444/udp|ICMP echo Reply|ICMP echo Reply, 65000/tcp|
|Agent<->Master|313335/udp|ICMP echo Reply|ICMP echo Reply|

# 분산 서비스 거부 공격(DDoS)

## 1. TCP SYN Flooding

### 1. 공격방법
* TCP 패킷의 SYN 비트를 이용한 공격 방법으로 많은 연결 요청을 전송해서 대상 시스템이 Flooding(범람)하게 만들어 서비스를 중단시키는 공격
* Hacker가 의미없는 발신주소로 변조한 TCP SYN packet 를 Victim에게 전송
* Victim은 의미없는 발신주소에게 TCP SYN/ACK packet 전송
* 대상이 없기 때문에 ACK packet 은 돌아오지 않음
* Victim의 TCP 연결 대기 큐가 overflow 될 때까지 Hacker가 계속 연결 요청


### 2. 대응 방법

|대응책|내용|
|:---|:---|
|방화벽에서 대응|IP당 SYN 요청에 대한 PPS(Packet Per Second) 임계치를 단계적으로 조정|
|First SYN Drop(Spoofed) 설정|⦁ SYN 패킷을 보낸 클라이언트의 존재 여부를 파악하여 차단하는 방법<br/>⦁ 클라이언트에서 전송된 첫 번째 SYN을 DROP하여 재요청 여부를 확인 후 Spoofing 여부를 판단함|
|TCP 세션 연결 차단|트래픽 유형별 임계치를 조정하여 TCP 세션 연결에 대한 차단|
|Back queue 증가|⦁ 임시적 방법으로 서버의 Queue 사이즈를 증가시킴<br/>⦁ `sysctl -w net.ipv4.tcp_max_syn_backlog = 1024`|
|L4/방화벽 SYN cookies 설정|⦁ L4/방화벽은 ACK에 필요한 Seq값을 인코딩하여 같이 전달하고 잊어버림<br/>⦁ `sysctl -w net.ipv4.tcp_syncookies=1`|

+ netstat 명령으로 TCP SYN Flooding 공격을 확인할 때 Source IP 주소가 변조되어 있을 수 있으므로 ISP 사업자와 협조를 통해서 IP를 추적해야 한다.
+ 라우터(Router) 에서 대응 방법

    |구분|내용|
    |:---|:---|
    |**Watch Mode**|SYN 패킷을 통과시키고 **일정 시간 동안 연결이 이루어지지 않으면 라우터가 SYN 패킷을 차단함**|
    |**Intercept Mode**|라우터에 유입되는 SYN 패킷 요청을 서버로 전송하지 않고, **라우터에서 가로채어 SYN 패킷을 요청한 클라이언트와 서버를 대신 연결**하는 것|

## 2. DRDoS
+ 공격방법
    * **별도의 Agent를 설치하지 않고 TCP Half Open의 취약점을 이용하는 공격기법**
    * **Victim IP로 출발지 IP를 변조하여 반사서버 (정상 TCP 서버 또는 라우터) 에 SYN packet 전송**
    * **서버들은 변조된 공격대상 IP 로 SYN/ACK packet 전송**

## 3. ICMP Flooding

### 1. 개요
* **IP특징(Broadcast 주소 방식)과 ICMP 패킷을 이용한 공격 방법**
* 통신을 위해 **서비스 및 포트가 필요 없는 유일한 프로토콜**
* **Smurfing Attack** 이라고도 함.

### 2. 공격 방법
* 공격자는 **다수의 호스트가 존재하는 서브 네트워크에 변조된 ICMP Echo 패킷을 Broadcast 전송 (Source IP를 피해자의 IP로 변조함)**
* 다량의 응답 패킷이 공격대상 서버로 집중됨

### 3. ICMP 공격에 사용되는 메시지

|메시지|내용|
|:---|:---|
|Source Quench(Type-4)|사용 중 전송자에게 패킷 전송 속도를 줄여 줄 것을 요구하는 메시지로 전송 속도의 지연 발생|
|Time to live exceeded in Transit<br/>(Type-11, Code-0)|시간 초과로 패킷이 폐기되었기 때문에 재전송함|
|Destination unreachable<br/>(Type-3, Code-0,1,2,3)|ICMP 트래픽 처리에 자원을 사용하게 되므로 시스템이 조금씩 느려지는 현상 발생|

### 4. ICMP 및 UDP Flooding 대응 방법

|대응 방법|내용|
|:---|:---|
|ACL(Access Control List)을 이용한 차단|웹 서버 혹은 운영 장비에 대한 접근 제어목록을 이용해 차단|
|Inbound 패킷 임계치 설정|⦁ 운영 장비로 유입되는 Inbound 패킷을 기준으로 PPS 수치를 유입되는 수치보다 낮게 설정<br/>⦁ 임계치 이상의 ICMP 및 UDP를 차단|

## 4. Tear Drop : IP Fragmentation (Ping of Death)

### 1. 개요
* 네트워크 패킷은 MTU(Maximum Transmission Unit)보다 큰 패킷이 오면 분할(Fragmentation)하고 분할 된 정보를 flags와 offset이 가지고 있다.
* 이 때 offset을 임의로 조작하여 다시 조립될 수 없도록 하는 공격
* Fragment를 조작하여 **패킷 필터링 장비나 IDS를 우회하여 서비스 거부 공격을 유발**

### 2. Tear drop 공격 종류

|종류|설명|
|:---|:---|
|**Tiny Fragment**|**최초의 Fragment를 아주 작게 만들어서 네트워크 침입탐지 시스템이나 패킷 필터링 장비를 우회**하는 공격|
|**Fragment Overlap**|⦁ Tiny Fragment 공격 기법에 비해 더욱 정교한 방법<br/>⦁ **IDS의 Fragment 처리 방법과 패킷 필터링의 재조합과 Overwrite 처리를 이용**|
|IP Fragmentation을 이용한<br/> 서비스 거부 공격|⦁ **Ping of Death** : Ping을 이용하여 ICMP 패킷을 규정된 길이 이상으로 큰 IP 패킷을 전송, 수신 받은 OS에서 처리하지 못함으로써 시스템을 마비시키는 공격<br/>⦁ **Tear Drop** : fragment 재조합 과정의 취약점을 이용한 공격으로 목표시스템 정지나 재부팅을 유발하는 공격. **TCP Header 부분의 offset field 값이 중첩되는 데이터 패킷을 대상 시스템에 전송**<br/>⦁ **Targra Attack** : 작업 중에 저장 되지 않는 데이터를 모두 삭제하는 공격방법. IP 단편화가 발생하면, 수신자는 재 조립시에 정확한 조립을 위하여 오프셋을 더하게 되는데, 이 때 더 큰 오프셋을 더해서 오버플로우를 발생시키는 공격 방법. New Tear, Nestea Attack|

### 3. Ping of Death 공격 방법
* **하나의 큰 패킷을 전송**하면 패킷은 **MTU(Maximun Transmission Unit)의 크기를 넘을 수 없기 때문에 분할**되어서 응답을 받음
* 5000 Byte 의 패킷을 보냈을 경우 1500 MTU의 네트워크 환경에서 offset:0 1500 bytes, offset 1480 1500 bytes, offset 2960 1500 bytes, offset 4440, 580 bytes

## 4. Land Attack
+ 개요
    * **송신자와 수신자의 IP 주소와 Port 주소를 동일하게 설정**하여 네트워크 장비에 부하를 일으킴
+ 공격 방법
    * IP Header를 변조, 송신자 IP 주소 및 Port 주소를 수신자(희생자)의 IP 주소와 Port 주소로 설정하여 트래픽을 전송
+ 대응 방법
    * 송신자와 수신자의 IP 주소가 동일한 패킷을 삭제

## 5. HTTP Get Flooding
+ 개요
    * 정상적인 TCP 연결 이후 정상적으로 보이는 HTTP Transaction 과정을 수행하는 Dos/DDos 공격 방법
    * **HTTP Get을 지속적으로 요청**하여 HTTP 연결 및 HTTP 처리 로직의 과부하를 유발
    * TCP 3-Way Handshaking 이후 공격을 수행하기에 IP를 변조하지 않음
+ 대응 방법
    
    |대응 방법|내용|
    |:---|:---|
    |선별적 IP 차단|⦁ TCP 연결요청 임계치 값과 HTTP Get의 임계치 값을 모니터링한 후 비정상적인 트래픽은 차단함<br/>⦁ 연결기반 공격이므로 IP를 변조할 수 없는 특성을 이용|
    |컨텐츠 요청횟수에 의한 임계치 설정|특정 컨텐츠를 다량으로 요청하는 것이므로 IP마다 컨텐츠 요청 횟수의 임계치를 설정|
    |시간대별 웹 페이지 URL 접속 임계치 설정 차단|시간대별 임계치를 설정하여 임의의 시간 안에 설정한 임계치 이상의 요청이 들어온 경우 해당 IP를 탐지하여 방화벽 차단 목록에 등록|
    |Web Scraping 기법을 이용한 차단|L7 스위치를 운영하는 경우 웹 스크래핑 기능을 사용하여 요청 패킷에 대한 쿠키 값이나 자바 스크립트를 보내어 클라이언트로부터 원하는 값에 재요청 패킷이 없는 경우 해당 패킷을 차단|

+ 탐지
    * `ngrep -l Serverl.pcap -twbyline | grep GET | sort | uniq -c | sort -rn`

## 6. Cache Control
+ 개요
    * no cache : 임시 인터넷 파일의 저장된 페이지 새 버전 확인을 웹 페이지를 열때마다 로 설정
+ Cache Control Attack 공격 방법
    * **Cache-Control Header 옵션 값을 사용**
    * **no-cache 로 설정**하면 항상 최신의 페이지를 요청하여 부하를 발생시킴
    * 웹 서버 호출
+ 대응 방법
    * 임계치 기반 대응을 실시

## 7. Slow HTTP Get/Post Attack

### 1. Slow HTTP Get 방식
* TCP/UDP 기반 공격 : **변조 IP가 아닌 정상 IP 기반 공격, 탐지 어려움**
* 소량의 트래픽을 사용한 공격 : 소량의 트래픽과 세션 연결을 통해서 공격
* 애플리케이션 대상 : 서비스의 취약점을 이용한 공격

### 2. Slow HTTP Post 방식
* **HTTP의 Post 지시자를 사용하여 서버에게 전달할 대량의 데이터를 장시간에 걸쳐 분할 전송**
* Post 데이터가 모두 수신되지 않으면 연결을 장시간 유지
* 공격 방법
    - **HTTP Header의 Content-Length 필드에 임의의 큰 값을 입력**
    - **공격자는 소량의 데이터를 느린 속도로 전송하여 장기간 동안 세션을 유지**

### 3. Slow HTTP Get/Post 공격 대응 방법

|대응 방법|내용|
|:---|:---|
|접속 임계치 설정|특정 발신지에서 IP로 연결할 수 있는 최대값 설정|
|방화벽 설정 도구인<br/>iptables로 차단|⦁ iptables -A INPUT -p tcp -dport 80 -m connlimit-above 30 -j DROP<br/>⦁ 30개 이상의 Concurrent Connection 에 대한 차단|
|Connection Timeout과<br/>Keepalivetime 설정|⦁ Connection Timeout 설정으로 클라이언트와 서버 간에 데이터 전송이 없을 경우 연결 종료<br/>⦁ 웹 서버의 Keepalivetime을 설정하여 차단|
|RequestReadTimeout<br/>설정으로 차단|⦁ Apache 2.2.15버전 이후에서 사용<br/>⦁ Slow Attack를 차단하기 위해서 RequestReadTimeout header=5 body=8 설정<br/>⦁ 5초 내에 연결이 안 되면 연결 종료, POST 요청 이후 8초 내에 데이터가 오지 않으면 연결 종료|
|그 외|⦁ POST 메시지의 크기를 제한(POST_MAX_SIZE) 한다.<br/>⦁ 최저 데이터 전송 속도를 제한한다.<br/>⦁ TCP 상태를 모니터링한다.|

### 4. Slow HTTP Read DoS
* 공격자가 웹 서버와 TCP 연결 시 **TCP 윈도우 크기 및 데이터 처리율을 감소**시킨 후 HTTP 데이터를 송신하여 웹 서버가 정상적으로 응답하지 못하도록 하는 Dos/DDoS 기법
* TCP 윈도우 크기 및 데이터 처리율을 감소시키면 서버는 정상상태로 회복될 때까지 대기상태에 빠지게 되어 부하를 유발함
* 공격 방법
    - 공격자는 자신의 TCP 윈도우 크기를 0Byte로 만든 후 서버로 전달
    - 서버는 윈도우 크기가 0 Byte인 것을 확인하고 데이터를 전송하지 않고 Pending 상태를 유지함
    - 공격자는 윈도우 크기를 점검하는 Probe 패킷을 ACK로 전송하여 서버는 대기 상태로 빠지게 됨

### 5. Slow HTTP Header DoS(Slowloris)
* HTTP Header 를 비정상적으로 조작해서 웹 서버가 헤더 정보를 구분할 수 없도록 하는 방법
* 웹 서버에 HTTP Header 정보가 모두 전달되지 않은 것으로 판단하여 연결을 장시간 유지
* 웹 서버는 클라이언트로부터 요청이 끝나지않은 것으로 판단하기 때문에 웹 로그에 기록하지 않음
* 공격 방법
    - HTTP Header 와 Body 는 개행문자 **`(\r\n\r\n)`** 로 구분됨.
    - Slow HTTP Header DoS 는 **`\r\n`** 만 전송하여 불완전 Header 를 전송

## 8. Hash DoS

### 1. 개요
* 클라이언트에서 전달되는 각종 파라미터 값을 관리하는 **해시테이블의 인덱스 정보가 중복되도록 유도** -> 사전에 저장된 정보 조회 시 **많은 CPU 자원을 소모**하도록 하는 공격
* HTTP Request 요청 시 Get, Post 방식으로 전송되는 변수를 Hash 구조로 관리함. (Get 방식은 길이에 제한이 있으나 Post 전송 방식은 파라미터 개수의 제한이 없음)
* 많은 수의 매개변수를 전달하면 매개변수를 저장하는 해시테이블에서 해시 충돌이 발생하여 해시테이블에 접근하는 시간이 증가함.
* 동일한 Hash Bucket 에 element 가 삽입될 경우 Hash Table은 이를 Linked List 로 구성하게 되는데 이 때 O(n^2) 의 시간이 소요됨.
+ 해시충돌(Hash Collision)
    * 서로 다른 키 값이 같은 인덱스 값으로 매핑되는 현상

### 2. Hash DoS 대응방안

|대응 방법|내용|
|:---|:---|
|HTTP Post 파라미터 수 제한|TOMCAT,PHP,Ruby 등의 최신 버전은 파라미터의 수를 제한할 수 있음. 즉, 개수 제한을 적용<br/>예)tomcat 은 TOMCAT_HOME/conf/server.xml 에 maxParameterCount = "XX"|
|Post 메시지 크기 제한|POST 메시지의 사이즈를 제한하는 서비스 설정<br/>예)tomcat 의 TOMCAT_HOME/conf/server.xml 에 MaxPostSize = "XX"|
|PHP에서 Hash DoS 차단|php.ini 파일에서 max_input_var 로 최대 HTTP POST Parameter 개수 설정|

## 10. Hulk DoS

### 1. 개요
* 웹 서버의 가용량을 모두 사용하여 정상적인 서비스가 불가능하도록 하는 Get Flooding 의 공격 유형이다.
* **공격 대상 URL을 지속적으로 변경**하여 DDoS 차단정책을 우회하는 특성을 가진다.
* 특정 URL이 계속 변경되면 임계치 설정 기반 방어가 불가능하게 된다. 즉, 임계치는 고정된 URL에만 설정이 가능하다는 특성을 이용, 우회한다.

### 2. 대응 방안

|대응 방안|내용|
|:---|:---|
|접속 임계치 설정을 통한 차단|발신 IP에서 연결할 수 있는 동시 접속 수에 대한 최댓값을 설정하여 한 개의 IP에서 대량의 연결 시도를 차단<br/>`iptables -A INPUT -p tcp -dport 80 -m connlimit-above 30 -j DROP`<br/># 30개 이상의 Concurrent Connection에 대한 차단|
|HTTP Request HOST 필드 값에<br/>대한 임계치 설정을 통한 차단|Hulk DoS는 URL을 계속 변경하기 때문에 URL이 아닌 HTTP Request에 포함된<br/>HOST 필드 값을 카운트하여 임계치 이상인 경우 차단함|
|302-Redirect를 이용한 차단|⦁ 대부분의 DDoS 공격 툴은 302-Redirect 요청에 대해 반응하지 않는 것이 특징임<br/>⦁ URL 중에서 공격 당하기 쉬운 웹 사이트에 대한 Redirect 처리를 통해서 자동화된 DDoS 공격 툴을 이용한 공격을 사전에 차단|

# 스캐닝

## 1. 포트 스캐닝(Port Scanning)

### 1. 개요
* 포트 스캐닝은 서버에 열려 있는 포트를 확인하기 위한 방법으로 NMAP 이라는 도구를 사용해서 스캐닝을 수행한다.
* 포트 스캐닝을 사용하면 서버에 열려 있는 포트를 확인하고 해당 포트의 취약점을 이용하여 공격할 수 있다.
* **NMAP 포트 스캐닝**

    |NMAP Port Scan|설명|
    |:---|:---|
    |**TCP connect() Scan**|3-Way Handshaking 를 수립하고 Target System 에서 쉽게 탐지가 가능하다.|
    |**TCP SYN Scan**|⦁ SYN 패킷을 대상 포트로 발송하여 SYN/ACK 패킷을 수신 받으면 Open 상태이다.<br/>⦁ SYN 패킷을 대상 포트로 발송하여 RST/ACK을 수신 받으면 Close 상태이다.<br/>⦁ Half Open 또는 Stealth Scan이라고 한다.|
    |**TCP FIN Scan**|⦁ 대상 포트로 FIN 패킷을 전송하고 닫혀 있는 포트는 RST를 전송한다.<br/>⦁ 포트가 개방되어 있으면 패킷을 무시한다.|
    |**TCP Null Scan**|⦁ 모든 플래그를 지운다.<br/>⦁ 대상 포트가 닫혀 있으면 RST를 돌려보내고 개방 상태이면 패킷을 무시한다.|
    |**TCP X-MAS Tree Scan**|⦁ 대상 포트로 FIN, URG, PSH 패킷을 전송한다.<br/>⦁ 대상 시스템에서 포트가 닫혀 있으면 RST를 되돌려 보낸다.<br/>⦁ 포트가 개방되어 있으면 패킷을 무시한다.|

    * **`nmap -O`** : TTL 값을 사용해서 해당 시스템의 운영체제를 식별하기 위함

### 2. 포트 스캐닝 기법

#### 1. NMAP 옵션
- SCAN Type
    + `-sS` : TCP SYN Scan
    + `-sT` : TCP Connection Scan
    + `-sU` : UDP Scan
    + `-sF` : TCP FIN Scan
    + `-sX` : TCP Xmas Scan
    + `-sN` : TCP NULL Scan
    + `-sA` : TCP ACK Scan
    + `-sW` : TCP Windows Scan
    + `-sM` : TCP Maimon Scan
    + `-sI` : TCP IDLE Scan
    + `-sO` : IP Protocol Scan
    + `-b` : FTP Bounce Scan
- Port Option
    + `-p #` : 특정 포트번호만 검색
    + `-p ssh` : 특정 이름의 포트만 검색
    + `-p 1,2,3` : 여러 개의 특정 포트만 검색
    + `-p 1-1023` : 특점 범위의 포트만 검색
    + `-p -1023` : 처음부터 특정 범위까지 포트만 검색
    + `-p 49152-` : 특정 포트부터 끝까지 검색
    + `-p-` : 0번을 제외한 모든 포트 검색
    + `-pT:1,2,U:1,2 ` : T는 TCP 포트 U는 UDP 포트를 의미
    + `-p http` : http라는 이름을 가진 모든 포트를 스캔

#### 2. NMAP 포트 스캐닝
* **UDP SCAN**
    - 공격자는 UDP Packet을 전송해서 스캐닝하는 것으로 UDP 특성상 신뢰성이 떨어짐
        + Port Open : UDP Packet -> 응답 X
        + Port Close : UDP Packet -> ICMP Unreachable
* TCP Open Scan
    - 공격자는 TCP의 3-Way handshaking 과정을 진행해서 오픈된 포트를 확인
    - 서버에 로그가 기록되고 스캔 속도가 느림
        + Port Open : SYN -> SYN+ACK -> ACK
        + Port Close : SYN -> RST+ACK
* Stealth Scan
    - 스캔하는 대상에 로그를 남기지 않는 스캐닝 기법
        + TCP Half Open Scan
        + FIN Scan
        + Xmas Scan
        + Null Scan
    - 로그가 남지 않음
        + Port Open : 응답 X
        + Port Close : RST
* TCP Half Open Scan
    - TCP 연결 시에 SYN 패킷만 전송하고 응답 정보로 포트 오픈을 확인
    - 완전한 세션을 성립하지 않고 포트의 활성화를 확인, 로그가 남지 않음
        + Port Open : SYN -> SYN+ACK -> RST
        + Port Close : SYN -> RST+ACK
* FIN Scan
    - TCP에서 FIN은 연결 종료를 의미
        + Port Open : FIN -> 응답 X
        + Port Close : FIN -> RST
* X-MAS Scan
    - 공격자는 TCP FIN, PSH, URG Packet 전송하여 포트 오픈을 확인
        + Port Open : FIN, PSH, URG -> 응답 X
        + Port Close : FIN, PSH, URG -> RST
* NULL Scan
    - 공격자는 TCP NULL 패킷을 전송하여 포트 오픈을 확인
        + Port Open : NULL -> 응답 X
        + Port Close : NULL -> RST
* ACK Scan
    - 공격자는 TCP ACK 패킷을 전송하여 방화벽 필터링 여부를 알 수 있음 (unfiltered)
        + Port Open : ACK -> RST
        + Port Close : ACK -> RST
* **TCP Fragmentation**
    - 20Byte의 헤더를 2개로 분할하여 보안장비의 탐지를 우회하는 방법
    - 첫 번째 패킷은 IP 주소 정보가 있고 두 번째 패킷은 Port 정보만 있음

# 스니핑 공격

## 1. 스니핑(Sniffing)
+ 개요
    * 네트워크로 전송되는 패킷을 훔쳐보는 도구
    * 송신자와 수신자의 IP주소, 포트번호 및 송수신 메시지 확인
    * **Normal Mode** : 자신의 컴퓨터에 전송되는 패킷만 수신받고 나머지 패킷은 Drop 한다.
    * **Promiscuous Mode** : 네트워크에 흐르는 모든 패킷을 모니터링 한다.
        - `#> ifconfig eth0 promisc`
    * tcpdump, wireshark
    * 소극적 공격은 적발이 어렵지만 예방이 가능하다. (예: 암호화)

## 2. 세션 하이재킹(Session Hijacking)
+ 개요
    * 로그인 사용자에게 웹 서버가 세션 값이라는 문자열을 생성해서 전송할 때 **세션 값이라는 문자열만 획득하면 로그인 과정없이 홈페이지에 접근할 수 있음**
    * 이미 인증을 받아 세션을 생성, 유지하고 있는 연결을 빼앗는 공격
    * 인증을 위한 모든 검증을 우회
    * TCP를 이용해 통신하고 있을 때 RST 패킷을 보내 일시적으로 TCP 세션을 끊고 시퀀스 넘버를 새로 생성하여 세션을 빼앗고 인증을 회피
    * 세션을 스니핑 추측(Brute-Force Guessing)을 통해 도용하거나 가로채어 자신이 원하는 데이터를 보낼 수 있는 공격방법
    * 원인 : 암호화되지 않은 프로토콜에서 정보를 평문으로 전송, 길이가 짧은 Session ID, 세션 타임아웃 부재
    * 세션 하이재킹은 어떻게 세션 값을 훔칠 것인지를 의미하는 것이다
        - 공격자 웹 서버에 세션 문자열을 기록하는 악성코드를 하나 생성
        - XSS를 사용해서 피해자가 악성코드를 호출하게 유도
        - 공격자는 웹프록시를 사용하여 세션값을 피해자의 세션 값으로 변경후 사이트 접속
    * 세션 하이재킹 도구

        |도구|내용|
        |:---|:---|
        |Hunt|네트워크상의 감시, 가로채기|
        |Arpspoof|공격자의 주소로 속이는 행위|
        |IP Watcher|네트워크상의 연결, 감시 및 세션을 가로채기 위한 다양한 기능|
        |Ferret|세션 정보를 가로채는 도구|
        |Hamster|Proxy 서버 상태로 만들어 주는 도구|
        |Paros|웹 Proxy 서버로서 쓸 수 있는 도구|
        |Cain & Abel|스푸핑과 스캐닝 등 다양한 기능|
        |WireShark|네트워크 패킷 분석, 다양한 패킷 정보를 볼 수 있음|

# 스푸핑 공격

## 1. IP Spoofing

### 1. 개요
* **자신의 IP를 속이는 행위**
* TCP/IP의 취약점을 이용
    - 순서 제어 번호 추측 (Sequence Number Guessing)
    - SYN flooding
    - Connect Hijacking
    - RST/FIN 을 이용한 접속끊기
    - SYN/RST 패킷 생성 공격
    - IP주소 인증(rlogin, rsh등)

### 2. 대응 방법

|대응 방법|내용|
|:---|:---|
|Router에서 Source Routing 차단|⦁ 외부에서 유입되는 패킷 중 출발지 IP(Source)에 내부망 IP 주소를 가지고 있는 패킷을 라우터 등에서 차단함<br/>⦁ 내부에서 발생한 IP Spoofing은 차단하지 못함|
|Sequence Number를 Random하게 발생시키도록 함|⦁ 일부 운영체제 중에서 Sequence Number를 일정하게 증가시키면서 사용함<br/>⦁ Random하게 발생하도록 서버 설정을 변경|
|R-Command 취약점 제거|IP로 인증하는 서비스들을 가능하면 차단함|
|암호화된 프로토콜|IP Spoofing 공격을 효과적으로 차단하지만 속도가 느려짐|

### 3. DNS Spoofing 및 Web Spoofing

|기법|내용|
|:---|:---|
|DNS Spoofing|⦁ DNS(Domain Name System) : www.abc.com 등의 도메인 이름을 IP 주소로 바꾸는 역할<br/>⦁ www.abc.com의 IP 주소를 바꾸어 엉뚱한 사이트로 접속하게 하는 공격 (예:위장된 금융사이트 접속)|
|Web Spoofing|⦁ 공격자가 다른 컴퓨터(공격대상)로 전송되는 웹 페이지를 보거나 바꿀 수 있는 방법<br/>⦁ 가짜 홈페이지를 만들어 두고 로그인을 유도하여 정보 획득(ID/PW, 신용카드 정보 등)|

## 2. ARP Spoofing
+ 개요
    * 로컬 통신 과정에서 서버와 클라이언트는 IP와 MAC 주소로 통신
    * 클라이언트의 MAC 주소를 중간에 공격자가 자신의 MAC 주소로 변조하여 마치 서버와 클라이언트가 통신하는 것처럼 속이는 공격
    * 이러한 공격은 **fragrouter를 통하여 연결이 끊어지지 않도록 Release**를 해줘야 함
+ 특징
    * ARP는 인증을 하지 않기 때문에 **ARP Reply 패킷을 각 호스트에 보내서 쉽게 ARP Cache를 업데이트**한다.
    * 즉, 변조된 ARP Reply 를 지속적으로 보내서 각 호스트들이 ARP Cache에 변조된 MAC 주소정보를 계속 유지시켜야 한다.
+ **방법**
    * `arp` : 현재 IP 주소와 MAC 주소를 확인 (linux)
    * `arp -a` : 현재 IP 주소와 MAC 주소를 확인 (windows)
    * `arpspoof -i eth0 -t [victim ip] [gateway ip]`
+ **정적 설정법**
    * `arp -s [IP 주소] [MAC 주소]`

## 3. 원격접속 공격
+ 개요
    * RDP, Teamviewer, VNC, NetCat 등의 프로그램 사용
    * 원격으로 윈도우 시스템에 연결하여 윈도우 시스템을 모니터링하거나 명령을 실행하는 공격
+ 윈도우 원격접속 프로그램

    |구분|내용|
    |:---|:---|
    |**RDP**|Remote Desktop Protocol, RDP는 MS에서 개발한 원격 데스크톱 연결 프로그램이 사용하는 프로토콜|
    |**Teamviewer**|원격으로 시스템에 연결하는 프로그램|
    |**VNC**|Virtual Networking Computing, 원격으로 대상 시스템을 모니터링하거나 관리할 수 있는 프로그램|
    |**NetCat(NC)**|원격으로 연결하여 명령을 실행할 수 있는 프로그램|

+ 대응 방법
    * 윈도우 원격 데스크톱 프로토콜(RDP)
        - RDP 서비스에 취약한 패스워드 사용 -> 무작위 공격(Brute Force Attack)대상
        - RDP 에서 사용하는 기본포트 (3389/TCP)
        - RDP 포트 변경 : HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TerminalServer\WinStations\RDP-Tcp 의 PortNumber 키 값 변경
        - 시스템 속성 -> 원격 -> 이 컴퓨터에 대한 원격 지원 연결 허용 해제
    * VNC
        - 패스워드 복잡도를 준수하여 3~6개월 단위로 패스워드 변경
        - VNC가 사용하는 포트번호(5800/tcp, 5900/tcp)를 다른 포트번호로 변경
