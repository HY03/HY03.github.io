---
title: 02.네트워크 보안 - 03.네트워크 대응 기술 및 응용
date: '2020-10-28 01:00:00'
tags:
- 정보보안기사
- 네트워크 보안
- 네트워크 대응 기술
related: true
categories:
- IS_Certification
toc: true
---
# 침입차단 시스템
## 1. **침입차단 시스템(Firewall)**
+ 개요
    * 접근통제 (Access Control List) 를 적용하여 시스템에 접근이 허용 가능한 사용자, IP, 포트를 결정
    * 인바운드 : 외부 네트워크에서 내부 네트워크로 들어오는 것
    * 아웃바운드 : 내부에서 외부로 나가는 것
        - 리버스 텔넷(Reverse Telnet) : 내부 망에서 공격자의 PC로 연결

## 2. 침입차단 시스템 구현방식에 따른 유형
### 1. 패킷 필터링(Packet Filtering)

|구분|내용|
|:---|:---|
|계층|Network 계층과 Transport 계층에서 작동|
|특징|미리 정해진 규칙에 따라 패킷 출발지 및 목적지 IP 주소 정보와 각 서비스의 port 번호를 이용해 접속 제어|
|장점|{::nomarkdown}<ul><li>다른 방화벽에 비해 속도가 빠름</li><li>사용자에게 투명성을 제공하며, 새로운 서비스에 대해 쉽게 연동이 가능</li></ul>{:/}|
|단점|{::nomarkdown}<ul><li>TCP/IP 구조적인 문제로 인한 패킷의 헤더는 쉽게 조작이 가능</li><li>강력한 Logging 및 사용자 인증 기능을 제공하지 않음</li></ul>{:/}|

### 2. 애플리케이션 게이트웨이(Application Gateway)

|구분|내용|
|:---|:---|
|계층|Application 계층|
|특징|{::nomarkdown}<ul><li>각 프로토콜 별로 Proxy Daemon이 있어 Proxy Gateway 라고도 함</li><li>사용자 및 응용 서비스에서 접근 제어를 제공하여 응용 프로그램 사용을 기록하여 감시 추적에 사용</li></ul>{:/}|
|장점|{::nomarkdown}<ul><li>Proxy 를 통해서만 연결이 허용되므로 내부 IP 주소를 숨길 수 있음</li><li>Packet 필터링에 비해 보안성 우수</li><li>가장 강력한 Logging과 Audit 기능 제공</li></ul>{:/}|
|단점|성능이 떨어지고, 새로운 서비스에 대해 유연성이 결여|

### 3. 회선 게이트웨이(Circuit Gateway)

|구분|내용|
|:---|:---|
|계층|Application ~ Session 계층 사이|
|특징|방화벽을 통해 내부 시스템으로 접속하기 위해서는 Client 측에 Circuit Proxy를 인식할 수 있는 수정된 Client 프로그램(예 : SOCKS) 이 필요하며 설치된 Client만 Circuit 형성이 가능|
|장점|{::nomarkdown}<ul><li>내부의 IP 주소를 숨길 수 있고 투명한 서비스 제공</li><li>Application에 비해 관리가 수월</li></ul>{:/}|
|단점|{::nomarkdown}<ul><li>수정된 Client 프로그램 필요</li><li>비표준 포트로 우회 접근 시 방어 불가</li></ul>{:/}|

### 4. 상태 기반 패킷 검사(Stateful Packet Inspection)

|구분|내용|
|:---|:---|
|계층|전 계층에서 동작|
|특징|{::nomarkdown}<ul><li>패킷 필터링 방식에 세션 추적 기능 추가</li><li>패킷의 헤더 내용 해석하여 순위에 위배되는 패킷 차단</li><li>패킷 필터링의 기술을 사용하여 Client/Server 모델을 유지하면서 모든 계층의 전후 상황에 대한 문맥 데이터를 제공하여 기존 방화벽의 한계 극복</li><li>방화벽 표준으로 자리매김</li></ul>{:/}|
|장점|서비스에 대한 특성 및 통신 상태를 관리할 수 있기 때문에 돌아나가는 패킷에 대해서는 동적으로 접근 규칙을 자동 생성|
|단점|데이터 내부에 악의적인 정보를 포함할 수 있는 프로토콜에 대한 대응이 어려움|

### 5. 혼합형 타입(Hybrid type)

|구분|내용|
|:---|:---|
|특징|서비스의 종류에따라 복합적으로 구성할 수 있는 방화벽|
|장점|서비스의 종류에 따라서 사용자의 편의성, 보안성 등을 고려하여 방화벽 기능을 선택적으로 부여|
|단점|구축 및 관리의 어려움|

### 6. 심층 패킷분석(Deep Packet Inspection)
    * 패킷이 가지고 있는 컨텐츠까지 모두 검사
    * OSI 전 게층에서 동작, 전 계층에 대한 접근 통제

## 3. 침입차단 시스템 구축 유형
### 1. 스크리닝 라우터(Screening Router)

|구분|내용|
|:---|:---|
|개념도||
|내용|IP, TCP, UDP 헤더 부분에 포함된 내용만 분석하여 동작하며 내부 네트워크와 외부 네트워크 사이의 패킷 트래픽을 perm/drop 하는 라우터|
|장점|{::nomarkdown}<ul><li>필터링 속도가 빠르고 비용 적음</li><li>클라이언트와 서버 환경 변화 없이 설치가 가능</li><li>전체 네트워크에 동일한 보호 유지</li></ul>{:/}|
|단점|{::nomarkdown}<ul><li>OSI 3,4 계층만 방어하여 필터링 규칙을 검증하기 어려움</li><li>패킷 내의 데이터는 차단 불가 및 로그 관리가 어려움</li></ul>{:/}|

### 2. 베스천 호스트(Bastion Host)

|구분|내용|
|:---|:---|
|개념도||
|내용|{::nomarkdown}<ul><li>내부 네트워크 전면에서 내부 네트워크 전체를 보호하며 외부 인터넷과 내부 네트워크를 연결하는 라우터 뒤에 위치</li><li>Lock down 된 상태에 있으며 인터넷에서 접근이 가능한 서버</li></ul>{:/}|
|장점|{::nomarkdown}<ul><li>스크리닝 라우터보다 안전</li><li>Logging 정보 생성 관리가 편리</li><li>접근 제어와 인증 및 로그 기능 제공</li></ul>{:/}|
|단점|{::nomarkdown}<ul><li>Bastion Host 손상 시 내부망 손상</li><li>로그인 정보 유출 시 내부망 침해 가능</li></ul>{:/}|        

### 3. 듀얼 홈드 호스트(Dual-Homed Host)

|구분|내용|
|:---|:---|
|개념도||
|내용|{::nomarkdown}<ul><li>2개의 네트워크 인터페이스를 가진 Bastion Host로서 하나의 NIC(Network Interface Card)는 내부 네트워크와 연결하고 다른 NIC는 외부 네트워크와 연결</li><li>방화벽은 하나의 네트워크에서 다른 네트워크로 IP 패킷을 라우팅하지 않기 때문에 Proxy 기능을 부여</li></ul>{:/}|
|장점|{::nomarkdown}<ul><li>정보 지향적인 공격 방어</li><li>Logging 정보 생성 관리가 편리</li><li>설치 및 유지보수가 쉬움</li></ul>{:/}|
|단점|{::nomarkdown}<ul><li>방화벽에서 보안 위반 초래 가능</li><li>서비스가 증가할수록 Proxy 구성 복잡</li></ul>{:/}|

### 4. 스크린드 호스트(Screened Host)

|구분|내용|
|:---|:---|
|개념도||
|내용|{::nomarkdown}<ul><li>Packet Filtering Router와 Bastion Host로 구성되어 있음</li><li>Packet Filtering Router는 외부 및 내부 네트워크에서 발생하는 패킷을 통과시킬 것인지를 검사하고 외부에서 내부로 유입되는 패킷에 대해서는 Bastion Host로 검사된 패킷을 전달</li><li>Bastion Host는 내부 및 외부 네트워크 시스템에 대한 인증을 담당</li></ul>{:/}|
|장점|{::nomarkdown}<ul><li>네트워크 계층과 응용 계층의 2단계 방어이므로 매우 안전</li><li>가장 많이 사용, 융통성 우수</li><li>Dual-Homed 장점 유지</li></ul>{:/}|
|단점|{::nomarkdown}<ul><li>스크리닝 라우터의 정보가 변경되면 방어가 불가능</li><li>구축 비용이 높음</li></ul>{:/}|

### 5. 스크린드 서브넷(Screened Subnet)

|구분|내용|
|:---|:---|
|개념도||
|내용|{::nomarkdown}<ul><li>스크린드 호스트 보안상의 문제점을 보완</li><li>외부 네트워크와 내부 네트워크 사이에 하나 이상의 경계 네트워크를 두어 내부 네트워크를 외부 네트워크로 분리하기 위한 구조</li><li>일반적으로 두 개의 스크리닝 라우터와 한 개의 베스천 호스트를 이용하여 구축</li></ul>{:/}|
|장점|{::nomarkdown}<ul><li>스크린드 호스트 구조의 장점 유지</li><li>가장 안전한 구조</li></ul>{:/}|
|단점|{::nomarkdown}<ul><li>설치 및 관리가 어려움</li><li>구축 비용이 높고, 서비스 속도가 느림</li></ul>{:/}|

# 침입탐지 시스템
## 1. **침입탐지 시스템(Intrusion Detection System)**
### 1. 개요
* 침입 패턴 정보를 데이터베이스에 저장하고 지능형 엔진을 사용하여 네트워크나 시스템의 침입을 실시간 모니터링, 침입탐지 여부를 확인하는 보안시스템
* 조직 IT 시스템의 기밀성, 무결성, 가용성을 침해하고, 보안정책을 위반하는 침입사건을 사전 또는 사후에 감시, 탐지, 대응하는 보안 시스템
* 컴퓨터 시스템의 비정상적인 사용, 오용, 남용 등을 가능하면 실시간으로 탐지하는 시스템

### 2. 침입차단 시스템 차단 방법

|절차|세부 설명|
|:---|:---|
|정보 수집|{::nomarkdown}<ul><li>침입탐지를 하기 위한 근원적인 자료들을 수집</li><li>자료원에 따라 NIDS와 HIDS 로 나누어짐</li></ul>{:/}|
|정보 가공 및 축약|{::nomarkdown}<ul><li>불필요한 정보 제거(침입과 관련 없는 정보 제거)</li><li>침입 판정을 위한 최소한의 정보만 남김(분석의 복잡도를 감소)</li></ul>{:/}|
|침입 분석 및 탐지|{::nomarkdown}<ul><li>축약된 정보를 기반으로 침입 여부를 분석, 탐지</li><li>방식에 따라 오용탐지와 이상탐지로 나누어짐</li></ul>{:/}|
|보고 및 조치|{::nomarkdown}<ul><li>침입탐지 후 적절한 보고 및 대응 조치</li><li>다른 보안장비(방화벽) 등과 연계</li></ul>{:/}|

### 3. 침입탐지 방법

|구분|오용탐지(Misuse)|비정상탐지(Anomaly)|
|:---:|:---|:---|
|동작 방식|시그니처(Signature) 기반,<br/>지식(Knowledge) 기반|프로파일(Profile) 기반, 행동(Behavior) 기반,<br/>통계(Statistical) 기반|
|침입판단 방법|{::nomarkdown}<ul><li>미리 정의된 Rule에 매칭</li><li>이미 정립된 공격패턴을 미리 입력하고 매칭</li></ul>{:/}|{::nomarkdown}<ul><li>미리 학습된 사용자 패턴에 어긋남</li><li>정상적, 평균적 상태를 기준, 급격한 변화 있을 때 침입 판단</li></ul>{:/}|
|사용기술|패턴 비교, 전문가시스템|신경망, 통계적 방법, 특징 추출|
|장점|{::nomarkdown}<ul><li>빠른 속도, 구현이 쉬움, 이해가 쉬움</li><li>False Positive가 낮음</li></ul>{:/}|{::nomarkdown}<ul><li>알려지지 않은 공격(Zero Day Attack) 대응 가능</li><li>사용자가 미리 공격 패턴을 정의할 필요가 없음</li></ul>{:/}|
|단점|{::nomarkdown}<ul><li>False Negative가 큼</li><li>알려지지 않은 공격탐지 불가</li><li>대량의 자료 분석에 부적합</li></ul>{:/}|{::nomarkdown}<ul><li>정상인지, 비정상인지 결정하는 임계치 설정이 어려움</li><li>False Positive가 큼</li><li>구현이 어려움</li></ul>{:/}|

    * False Positive : false(+)로 표현, 공격이 아닌데도 공격이라고 오판하는 것
    * False negative : false(-)로 표현, 공격이지만 공격이 아니라고 오판하는 것

### 4. 침입탐지 시스템 분류

|구분|NIDS(Network based IDS)|HIDS(Host based IDS)|
|:---:|:---|:---|
|동작|{::nomarkdown}<ul><li>네트워크에 흐르는 패킷들을 검사, 침입 판단</li><li>방화벽 외부의 DMZ나 방화벽 내부의 내부 네트워크 모두 배치 가능</li></ul>{:/}|{::nomarkdown}<ul><li>시스템 상에 설치, 사용자가 시스템에서 행하는 행위, 파일의 체크를 통해 침입 판단</li><li>주로 웹 서버, DB 서버 등의 중요 서버에 배치</li></ul>{:/}|
|자료원|Promiscuous 모드로 동작하는 네트워크 카드나 스위치|시스템 로그, 시스템 콜, 이벤트 로그|
|탐지 가능 공격|스캐닝, 서비스 거부 공격(DoS), 해킹|내부자에 의한 공격, 바이러스, 웜, 트로이목마, 백도어|
|장점|{::nomarkdown}<ul><li>네트워크 자원의 손실 및 패킷의 변조가 없음(캡처만 하기 때문)</li><li>거의 실시간으로 탐지가 가능함</li><li>감시 영역이 하나의 네트워크 서브넷으로서 HIDS에 비해 큼</li></ul>{:/}|{::nomarkdown}<ul><li>침입의 성공 여부 식별이 가능함</li><li>실제 해킹 및 해킹시도 판단이 용이</li><li>주로 S/W적으로 서버와 같은 시스템에 인스톨되며, 설치 및 관리가 간단함</li></ul>{:/}|
|동작|{::nomarkdown}<ul><li>부가 장비가 필요함(스위치 등)</li><li>암호화된 패킷은 분석 불가</li><li>False Positive가 높음</li><li>오탐으로 인해 정상적인 세션이 종료</li><li>DoS의 경우 대응이 불가능(탐지만 가능)</li><li>능동적인 대응 기능 미비</li></ul>{:/}|{::nomarkdown}<ul><li>감시 영역이 하나의 시스템으로 한정됨</li><li>탐지 가능한 공격에 한계가 있음(주로 이벤트 로그로만 탐지)</li><li>오탐으로 인해 정상적인 사용자가 자신의 계정을 사용할 수 없는 문제</li></ul>{:/}|

    * Hybrid IDS : NIDS + HIDS, 단일 호스트를 출입하는 네트워크 패킷을 검색 시스템의 이벤트, 데이터, 디렉터리, 레지스트리에서 공격 여부를 감시하여 보호한다.

+ IDS의 지식기반 탐지 기법과 행위기반 탐지기법

    |지식기반 탐지 기법| 행위기반 탐지 기법|
    |:---|:---|
    |{::nomarkdown}<ul><li>전문가 시스템</li><li>시그니처 분석</li><li>페트리넷</li><li>상태전이</li><li>신경망</li><li>유전 알고리즘</li></ul>{:/}|{::nomarkdown}<ul><li>통계적 방법</li><li>전문가 시스템</li><li>신경망</li><li>컴퓨터 면역학</li><li>데이터 마이닝</li><li>기계학습</li></ul>{:/}|

## 2. **Snort**
### 1. 개요
* 패킷을 스니핑해서 지정한 Rule과 동일한 패킷을 탐지하는 침입탐지 시스템
* 침입탐지 시스템을 구현한 공개 소프트웨어, Plug-in 형태로 기능을 추가함

### 2. Snort Rule
* 개요
    - 공격자의 공격을 탐지하기 위한 등록된 규칙(패턴)
    - 수신되는 패킷은 Rule과 비교해서 탐지여부를 결정
    - Rule은 Rule Header와 Rule Option으로 구성되고 Rule Header는 Action Protocol, IP 주소, 포트 등으로 구성되고, Rule Option은 탐지할 조건으로 구성된다.
    - Rule 과 비교해서 탐지가 완료되면 경고 및 로그를 발생
    - 패킷(Packet) -> 스니퍼(Sniffer) -> 전처리기(Preprocessing) -> 탐색엔진-탐지 룰(Detection Engine) -> 경고,로깅(Alert, logging) -> 로그 파일(Log file)
* 스니퍼(Sniffer)
    - 네트워크 인터페이스에 입력되는 패킷을 수신
    - 스니퍼는 Promiscuous Mode 로 동작하여 네트워크에 전송되는 모든 패킷 수신
    - TCP, UDP, IP, ICMP 의 프로토콜을 수신받음
    - Snort 가 실행하는 것이 아니라 WinPcap 라이브러리가 실행
* 전처리기(Preprocessing)
    - 입력되는 패킷에 대해 특정 행위가 탐지될 경우 탐색엔진으로 전송
    - 필요한 Plug-in을 활성화 / 비활성화 시킴
    - 프로토콜의 종류별로 검사 여부를 결정할 수 있음
* 탐색엔진(Detection Engine)
    - 등록된 Rule과 동일한지 규칙 기반 패턴 검색을 실행
    - Rule과 일치하면 로그를 기록하거나 경고(Alert)를 발생
* 경고(Alert) 및 로깅(Logging)
    - 탐지된 정보에 대해 로그파일, 네트워크, SNMP 프로토콜 등으로 결과 전송
    - 로그파일에 기록하거나 등록된 데이터베이스에 입력

### 3. Snort 시그니처 (Signature)

|룰 헤더(Rule Header)|룰 옵션(Rule Option)|
|:---|:---|
|`alert tcp any any -> any any`|`(msg: "ABC TCP" ; sid:100000491;)`|

+ 룰 지정 파일 : `local.rules`
+ 룰 헤더 (Rule Header)
    * 처리 방법(action)

        |종류|내용|
        |:---|:---|
        |alert|alert 를 발생시키고 탐지 정보를 로그파일에 기록한다.|
        |log|패킷에 대해서 로그를 기록한다.|
        |pass|패킷을 무시한다.|
        |dynamic|{::nomarkdown}<ul><li>activate 시그니처에 의해서 유효하게 된 경우는 한쪽의 패킷을 기록한다.</li><li>activate 시그니처가 있어야만 동작이 가능하다.</li></ul>{:/}|

    * 프로토콜 (Protocol)
        - TCP, UDP, IP, ICMP의 프로토콜을 설정
    * IP 주소와 포트번호
        - 서브넷 마스크와 함께 설정이 가능함
        - `211.110.15.33/24`
    * Direction
        - 패킷의 방향을 위미
        - `198.150.10.2/24 1000 -> 211.110.15.33/24 80`
+ 룰 옵션(Rule Option)
    * 탐지 조건을 여러 개 설정할 때는 각 조건마다 ; 를 넣어 구분
    * 간단한 탐지 조건
    
    |룰 옵션| 내용|
    |:---|:---|
    |msg|{::nomarkdown}<ul><li>alert가 발생하면 msg가 설정된 문장을 로그파일에 기록한다.</li><li>예: `msg:"Hack TCP"`</li></ul>{:/}|
    |sid|{::nomarkdown}<ul><li>시그니처 아이디를 지정하는 것으로 0~99는 예약되어 있고 100~1000000은 snort.org에서 공식적으로 배포하는 룰이다.</li><li>1000000 이상의 값으로 사용자가 지정해서 사용한다.</li></ul>{:/}|
    |dsize|{::nomarkdown}<ul><li>버퍼 오버플로우 탐지할 때 자주 사용</li><li>전송되는 패킷의 사이즈를 식별할 수 있어서 dsize보다 큰 값과 작은 값에 대한 설정이 가능하다</li><li>예: `dsize:100<>500` -> 100에서 500바이트의 패킷을 탐지</li></ul>{:/}|
    |nocase|패턴을 매칭할 때 대문자와 소문자를 구분하지 않는다.|

    * content
        - 전송하는 페이로드를 검색해서 지정한 문자열이 있는지 확인 (문자열/바이너리)
        - 패턴 매칭 옵션
        
        |옵션|내용|예제|
        |:---|:---|:---|
        |`!`|입력한 값과 일치하지 않으면 탐지|`content:!"google";`|
        |`|`|Hex 값을 표현할 때 사용|`content:"|30|";`|
        |`\`|특수 기호를 탐지할 때 사용|`content:"\"google\|";`|

    * flags
        - TCP 프로토콜에서 Control Flags (SYN,FIN,URG,ACK)를 지정하는 것
        - Control Flags

        |구분|내용|
        |:---|:---|
        |SYN|TCP 연결 시에 동기화를 요구한다.|
        |ACK|응답에 대한 확인을 요구한다.|
        |PSH|데이터 버퍼링을 하지 않고 수신자에게 송신을 요구한다.|
        |URG|긴급 포인터 Flag이다.|
        |FIN|정상 접속 종료이다.|
        |RST|비정상 종료를 위한 Reset을 한다.|

        - 예 : `flags:SF` - SYN과 FIN 모두 탐지
    * flow
        - established : TCP 연결이 확립된 세션 탐지
        - `alert tcp any any -> any 80 (msg:"TCP Coneection"; flow:established; sid:100000491;)`
    * itype 과 icode
        - ICMP 프로토콜 : 네트워크의 오류를 감시하고 보고하는 프로토콜, 헤더에 Type 값이 0 이면 ICMP Echo Reply 이고 8이면 ICMP Echo Request
        - 예 : `alert icmp any any -> any any (itype:0; msg:"ICMP echo Reply"; sid:10000491;)`
    * sameip
        - 송신자와 수신자의 IP 주소가 동일한 것을 탐지
        - Land Attack 탐지
            + `alert ip any any -> 210.10.111.23/24 any (msg:"Land Attack"; sameip;)`
    * session
        - TCP를 사용하는 POP3 전자우편 프로토콜의 세션 정보를 로그에 기록하는 시그니처를 가정
        - `log tcp any any -> any 110 (session: printable);`
    * threshold
        - 동일한 특정 패킷에 대해 설정한 시간 내에 일정 수가 발생하면 탐지 (무작위 공격 (Brute Force) 을 탐지)
        - `treshold: type [limit, treshold, both], track [by_src, by_dst], count [횟수], seconds [몇 초]`
        - treshold 옵션

        |구분|내용|
        |:---|:---|
        |limit|count 동안에 횟수 번째 트래픽까지 탐지한다.|
        |treshold|횟수마다 계속 탐지한다.|
        |both|count 동안 횟수만큼 트래픽이 탐지 될 시에 한번만 탐지한다.|
        |by_src|출발지의 패킷만 탐지한다.|
        |by_dst|목적지 패킷만 탐지한다.|

        - 사용 예 : ICMP 패킷이 20초 동안 5번 발생하면 한 번씩 경고 메시지를 기록
        - `alert icmp any any -> any any (msg:"ICMP";threshold: type both, track by_src, count 5, seconds 20; sid:10000495;)`
    * 세션절단
        - 탐지 후 세션절단(1)
            + `alert tcp any any -> any any (msg:"Outgoing HTTP Connection";react:block;sid:10000495;)`
        - 탐지 후 세션절단(2)
            + `alert tcp any any -> any any (msg:"Outgoing HTTP Connection";react:warn,msg;sid:10000495;)`

### 4. Snort 탐지
* TCP SYN Flag 탐지
    - `alert tcp any any -> any any (msg:"SYN Attack"; flags:S; threshold:type both, track by_src, count 10, secnods 20; sid:10000495;)`
* TCP ACK Flag 탐지
    - `alert tcp any any -> any any (msg:"ACK Attack"; flags:A; threshold:type both, track by_src, count 10, secnods 20; sid:100000495;)`
* TCP NULL Flag 탐지
    - `alert tcp any any -> any any (msg:"NULL Attack";flags:0; threshold:type both, track by_src, count 10, seconds 20.; sid:10000495;)`
* Ping of Death
    - `alert icmp any any -> any any (msg:"Ping of Death"; fragbits:M; threshold:type both, track by_src, count 10, seconds 20;sid:10000495;)`
* 특정 문자열 탐지
    - `alert tcp any any -> any 80 (msg:"Content Detection"; content:"test";offset:0;nocase;sid:100000495;)`
* Telnet에 대한 탐지(로그인 실패)
    - `alert tcp any any -> any 23 (msg:"Telnet Connection";content:"login failed";nocase;sid:100000495;)`
* Telnet에 대한 탐지(로그인 성공)
    - `alert tcp any any -> any 23 (msg:"Telnet Success"; content:"Documents and Setting";nocase;sid:100000495;)`
* SSH 탐지
    - `alert tcp any any -> any 22 (msg:"SSH Connection"; content:"SSH-2.0";nocase;sid:100000495);`
* SSH 무작위 공격 탐지
    - `alert tcp any any -> any 22 (msg"Brute Force SSH";threshold:type both, track by_src, count 10, seconds 20;content:"SSH-2.0";sid:100000495;)`
* FTP 무작위 공격 탐지
    - `alert tcp any any -> any 21 (msg:"Brute Force FTP";threshold:type both, track by_src, count 10, seconds 20; content:"Login incorrect";sid:100000495;)`
* Telnet 무작위 공격 탐지
    - `alert tcp any any -> any 23 (msg:"Brute Force Telnet";threshold:type both, track by_src, count 10, seconds 20, content:"Login Failed";sid:100000495;)`
* 웹 서버에 접근하는 호스트 탐지
    - `alert tcp any any -> any 80 (msg:"Web Server"; content:"GET/admin/index.html";nocase;sid:100000495;)`

# 침입대응 시스템
## 1. 침입대응 시스템 (Intrusion Prevention System)
+ 공격 시그니처를 찾아내 네트워크에 연결된 기기에서 비정상적인 활동이 이루어지는지 감시, 자동으로 해결조치하는 보안솔루션
+ 침입 경고 이전에 공격을 중단시키는 것이 주요 목적
+ Real Time 대응이 가능한 예방 통제 시스템

## 2. 침입대응 시스템 종류

|구분|설명|
|:---|:---|
|NIPS(Network IPS)|공격 탐지에 기초하여 트래픽 통과 여부를 결정하는 인라인 장치|
|HIPS(Host IPS)|호스트 OS 위에서 수행, 공격 탐지 후 실행 전에 공격 프로세스 차단 기능|

# 허니팟
## 1. 개요
* 해커들의 정보를 얻기 위한 하나의 개별 시스템
* 해커의 행동, 공격 기법 등을 분석하는데 사용
* 합법적이고 윤리적인 유인(Enticement) != Entrapment(유혹,불법/비윤리)
* Zero Day 공격을 탐지하기 위한 수단
* Padded-cell : IDS와 연계하여 IDS에서 탐지 후 Honeypot으로 패킷을 전달하는 것으로 교정통제 효과가 있다.
* 침입사고 대응 및 분석 기술 발전을 위한 인터넷 위협 정보 수집 및 위협에 대한 자산 보호 정보를 제공

## 2. UTM (Unified Threat Management)
* 통합 보안 솔루션 
* Firewall, IDS, IPS, Anti-virus 등의 보안 솔루션을 하나의 하드웨어에 통합
* 구매비용이 적고 일관성 있는 보안정책을 적용

## 3. 허니팟 위치

|구분|설명|
|:---|:---|
|방화벽 앞|IDS처럼 Honeypot 공격으로 인한 내부 네트워크 위험도 증가는 없음|
|방화벽 내부|효율성이 높아 내부 네트워크에 대한 위험도 커짐|
|DMZ 내부|가장 적당한 위치, 설치시간 소요, 관리 불편, 다른 서버와의 연결은 반드시 막아야 함|

## 4. 허니팟 구축 시 고려사항
* 해커에 쉽게 노출되어 해킹 가능한 것처럼 취약해 보여야 한다.
* 시스템의 모든 구성요소를 갖추고 있어야 한다.
* 시스템을 통과하는 모든 패킷을 감시해야 한다.
* 시스템 접속자에 대해 관리자에게 알려야 한다.

# 가상사설망
## 1. 가상사설망 개요
* Virtual Private Network
* 공중망(인터넷)을 이용하여 사설망(기업 전용 네트워크)과 같은 효과를 얻기 위한 컴퓨터 시스템과 프로토코릐 집합
* 보안성이 우수하고 사용자 인증, 주소 및 라우터 체계의 비공개와 데이터 암호화, 사용자 Access 권한을 제어한다.
* 패스워드 인증과 함께 USB를 사용한 2-Factor 인증 제공
* 터널링 : VPN 클라이언트와 VPN 서버 간에 암호화 키 교환 과저을 수행한 후에 암호화를 사용해서 메시지를 주고 받음
* SSL VPN : 별도 소프트웨어 설치 필요 없음 (웹 브라우저 사용)
* IPSEC VPN : 별도의 소프트웨어 설치 필요
* PPTP VPN : 윈도우에서 기본적으로 지원

## 2. SSL VPN
### 1. SSL (Secured Socket Layer) VPN
* 웹브라우저만 있으면 언제 어디에서나 사용이 가능
    - 웹 브라우저와 웹 서버 간에 SSL 협상과정을 한 후 세션키를 생성, 세션키로 암호화 하여 터널링을 구현
* 서버와 클라이언트 간 인증(Certification) : RSA 방식과 X.509 사용
* 실제 암호화된 정보는 새로운 암호화 소켓 채널을 통해 전송
* OSI 4~7계층에서 동작

### 2. SSL VPN 구성
![SSL VPN Topology](/assets/images/posts/SSL_VPN_Topology.png)

[이미지 출처](https://commons.wikimedia.org/wiki/File:SSL_VPN_Topology-en.svg)
+ 브라우저와 웹 서버 사이의 통신을 위해 널리 사용되는 세션 기반 프로토콜
+ 브라우저와 서버 간 전송되는 모든 거래를 위한 안전한 통로를 제공
+ 대칭키 암호화(트래픽 암호)와 비대칭키 암호화(대칭키 암호)를 혼합해서 사용
+ Server 와 Client 사이의 인증, 기밀성, 무결성, 부인방지 서비스 제공
+ SSL은 상호 인증, 무결성을 위한 메시지 인증 코드(MAC), 기밀성을 위한 암호화 등을 제공함으로써 안전한 데이터 통신을 제공

### 3. SSL VPN 특징

|구분|설명|
|:---|:---|
|장점|{::nomarkdown}<ul><li>별도 장비 없이 웹브라우저만으로 VPN 구현 가능, 뛰어난 사용성과 관리의 편의성</li><li>네트워크 레이어의 암호화 방식이기 때문에 HTTP 뿐만 아니라 NNTP(뉴스전송 프로토콜), FTP 등에도 사용</li></ul>{:/}|
|단점|SSL 자체의 부하(암복호화 지연)|

### 4. SSL VPN 보안 서비스

|보안서비스|설명|
|:---|:---|
|인증(Authentication)|{::nomarkdown}<ul><li>Client가 접속하는 서버가 신뢰할 수 있는 서버인지 또는 서버에 접속한 Client가 허가된 사용자인지를 확인</li><li>전자서명과 X.509 공개키 인증서 사용</li></ul>{:/}|
|무결성(Integrity)|함께 키를 사용하는 MAC 기법을 사용하여 데이터 변조 여부 확인|
|기밀성(Confidentiality)|대칭키 암호 사용|
|부인봉쇄(Non-repudiation)|부가적인 SW를 사용하여 응용 계층에서 메시지에 대한 전자서명 허용|

- 주로 클라우드 서비스와 함께 사용
- 공개된 네트워크 망에서 접근할 수 있기 때문에 추가적인 인증 기능인 OTP 단말기를 같이 사용

## 3. IPSEC VPN
### 1. IPSEC(IP Security) VPN
* 보안에 취약한 인터넷에서 안전한 통신을 실현하는 통신 규약
* 가상적인 전용 회선을 구축하여 데이터를 도청당하는 등의 행위를 방지하기 위한 통신 규약
### 2. IPSEC 터널링 모드

* ESP Transport mode
![ESP Transport mode](/assets/images/posts/ESP-transport.png)

[이미지 출처](https://wiki.mikrotik.com/wiki/Manual:IP/IPsec)

* ESP Tunnel mode
![ESP Tunnel mode](/assets/images/posts/ESP-tunnel.png)

[이미지 출처](https://wiki.mikrotik.com/wiki/Manual:IP/IPsec)

* IPSEC의 전송 모드는 데이터에 대해 암호화를 수행하지만, IP 헤더에 대해서는 암호화를 수행하지 않음
* 터널 모드는 보안 IPSEC 헤더를 추가하고 IP 헤더와 데이터 모두를 암호화함.
### 3. IPSEC VPN 전송 모드

|종류|설명|
|:---|:---|
|터널모드|VPN과 같은 구성으로 패킷의 출발지에서 일반 패킷이 보내지면 중간에서 IPSec 을 탑재한 중계 장비가 패킷 전체를 암호화(인증)하고 중계 장비의 IP 주소를 붙여 전송|
|전송모드|패킷의 출발지에서 암호화(인증)를 하고 목적지에서 복호화가 이루어지므로 End-to-End 보안을 제공|

### 4. IPSEC VPN 키 관리 담당

|종류|설명|
|:---|:---|
|ISAKMP|{::nomarkdown}<ul><li>Internet Security Association and Key Management Protocol</li><li>Security Association 설정, 협상, 변경, 삭제 등 SA 관리와 키 교환을 정의했으나 키 교환 메커니즘에 대한 언급은 없음</li></ul>{:/}|
|IKE|{::nomarkdown}<ul><li>Internet Key Exchange, 키 교환 담당</li><li>IKE 메시지는 UDP 프로토콜을 사용해서 전달되며 출발지 및 도착지 주소는 500 port를 사용하게 됨</li></ul>{:/}|

### 5. IPSEC VPN 인증과 암호화를 위한 Header

|종류|설명|
|:---|:---|
|AH|{::nomarkdown}<ul><li>데이터 무결성과 IP 패킷의 인증을 제공, MAC 기반</li><li>Replay Attack으로부터 보호 기능(순서번호 사용)을 제공</li><li>인증 시 MD5, SHA-1 인증 알고리즘을 이용하여 Key 값과 IP 패킷의 데이터를 입력한 인증 값을 계산하여 인증 필드에 기록</li><li>수신자는 같은 키를 이용하여 인증 값을 검증</li></ul>{:/}|
|ESP|{::nomarkdown}<ul><li>전송 자료를 암호화하여 전송하고 수신자가 받은 자료를 복호화하여 수신</li><li>IP 데이터그램에 제공하는 기능으로서 데이터의 선택적 인증, 무결성, 기밀성, Replay Attack 방지를 위해 사용</li><li>AH와 달리 암호화를 제공(대칭키, DES, 3-DES 알고리즘)</li><li>TCP/UDP 등의 Transport 계층까지 암호화할 경우 Transport 모드를 사용</li><li>전체 IP 패킷에 대해 암호화할 경우 터널 모드를 사용</li></ul>{:/}|

### 6. AH(Authentication Header) 세부 구조

|필드|크기|내용|
|:---|:---|:---|
|Next Header|1Byte|다음에 오는 헤더를 연결하기 위해서 헤더의 프로토콜 번호를 담고 있음|
|Payload Length|1Byte|AH 길이를 측정|
|Reservation|2Byte|'0'으로 설정|
|SPI|4Byte|Destination IP Address와 ESP(Encapsulating Security Payload)를 조합하여 데이터그램에 대한 SA(Security Association)를 식별|
|Sequence Number|4Byte|SA(Security Association)이 구성될 때 '0'으로 초기화되는 특성을 지니고 있음. SA를 사용하여 데이터그램이 송신될 때마다 값이 증가하며, 데이터그램을 식별하여 데이터그램의 재전송을 방지함으로써 재생 공격으로부터 IPsec을 방어하는 데 사용|
|Authentication Data|-|무결성 검사 값(ICV; Integrity Check Value)으로 구성되어 있음|

### 7. ESP(Encapsulation Security Payload) 세부 구조

|필드|크기|내용|
|:---|:---:|:---|
|SPI(ESP Header)|4|Destination IP Address와 ESP(Encapsulating Security Payload)를 조합하여 데이터그램에 대한 SA(Security Association)를 식별|
|Sequence Number(ESP Header)|4|SA(Security Association)이 구성될 때 '0'으로 초기화되는 특성을 지니고 있음. SA를 사용하여 데이터그램이 송신될 때마다 값이 증가하며, 데이터그램을 식별하여 데이터그램의 재전송을 방지함으로써 재생 공격으로부터 IPsec을 방어하는 데 사용|
|Payload Data(Payload)|-|다음 헤더 필드에 의해 묘사된 데이터를 포함하는 가변 길이 필드|
|Padding(ESP Trailer)|0~255|암호화 또는 정렬을 위해서 추가적인 패딩 바이트가 포함된 필드|
|Padding Length(ESP Trailer)|1|패딩 필드의 바이트 수|
|Next Header(ESP Trailer)|1|다음 헤더의 프로토콜 번호를 포함하여 헤더를 연결하는 데 사용|
|ESP Authentication Data|-|ESP 인증 알고리즘을 적용하여 계산된 ICV(Integrity Check Value)값으로 구성|

## 4. PPTP VPN
* PPTP(Point-to-Point Tunneling Protocol) VPN
* PPP(Point-to-Point Protocol)의 Packet을 IP Packet으로 Encapsulation 하여 IP 네트워크에 전송하기 위한 터널링 기법으로 2계층에서 동작한다.
* Microsoft의 RAS(Remote Access Service)에 기반한다.

## 5. L2TP VPN
* L2TP(Layer 2 Tunneling Protocol) VPN
* L2F 프로토콜(Cisco에서 제안)과 PPTP 프로토콜과의 호환성을 고려하여 만들어진 터널링 프로토콜
* 2계층 데이터 링크에서 동작한다.

# **NAC(Network Access Control)**
## 1. 개요
+ 엔드 포인트(End Point) 보안 솔루션으로 등록되지 않은 단말기를 식별하여 차단
+ NAC는 네트워크에 연결된 단말기에 대해서 사전에 IP주소, MAC주소를 등록하고 등록되지 않은 단말기의 네트워크 접근을 차단
+ 네트워크에 대한 무결성을 지원
+ NAC 정책 관리 서버는 등록된 단말기를 관리하고 차단 조건을 설정하여 등록되지 않은 단말기가 네트워크 연결을 시도하면 차단하는 기능이 있다.

## 2. NAC 구성요소
+ 정책관리 서버(Policy Management Server)
    * 네트워크에 대한 정책등록 및 에이전트(Agent) 정책 설정, 네트워크 접근 로그관리를 관리한다.
    * 단말기(PC)의 보안상태를 모니터링하고 사용자 역할 기반 정책을 수립한다.
    * 차단 서버 및 에이전트에 대해서 동작을 설정한다.
+ 차단 서버(Policy Enforcement Server)
    * 네트워크에 연결된 단말기를 통제한다.
    * 단말기(PC)의 IP 주소, MAC 주소, 운영체제 정보를 수집하고 분류한다.
    * 웹 기반 사용자 인증을 지원한다.
    * 유해 트래픽을 감지하고 차단한다.
+ 에이전트(Agent)
    * 사용자 단말기에 설치되고 보안정책을 점검한다.
    * 사용자 단말기의 취약점 점검 및 단말기 정보를 확인한다.
    * 무선인증을 지원한다.
+ 콘솔(Console)
    * 웹(Web) 기반으로 네트워크 보안정책 설정, 감사, 모니터링을 한다.
    * 대시보드(Dashboard)를 제공하고 주요 네트워크 이벤트에 대해서 알람을 발생시킨다.

## 3. NAC 주요 기능
+ 네트워크 정보 자동 수집 및 업데이트 지원
    * 네트워크 장비, 사용자 단말기, 네트워크 프린터, 무선 AP(Access Point) 등의 정보를 자동으로 수집하고 분류
    * Agent 방식 : 사용자 단말기의 윈도우 환경, 설정 정보 등의 자세한 정보를 수집
    * Agentless 방식 : ARP 프로토콜을 사용해서 사용자 단말기 정보를 수집 - 정보 수집에 한계
+ IP 관리 및 충돌 보호
    * 특정 IP 주소 및 MAC 주소에 대해서 네트워크 차단 및 해제
    * 사용자 네트워크 사용기간을 등록해서 제한
    * 장기간 미사용 IP 주소를 탐지, IP 주소를 자동회수
    * 사용자가 임의의 IP주소 변경 방지, 충돌 방지
+ 인증 서버 연동
    * 사용자 인증 시 Active Directory, Local Database 인증, RADIUS 인증, LDAP 인증과 연동
        - RADIUS 인증 : 무선 LAN 에서 사용하는 소규모 단방향 인증
    * 즉, NAC 자체 인증 기능을 사용할 수도 있고 다른 인증과 연동하여 서비스를 제공할 수도 있음
+ 네트워크 접속 강제화 및 필수 프로그램 설치유도
    * 사용자 단말기에 설치된 프로그램을 확인해서 필수적으로 설치해야 하는 프로그램을 강제적으로 설치하게 할 수 있음
    * 보안정책 실행을 위해 필요한 프로그램이 설치되지 않은 경우 강제적으로 네트워크를 연결하여 필수 프로그램을 설치함
+ 보안 무결성 확인
    * 사용자 단말기의 보안 패치를 확인하고 운영체제 패스워드 안정성을 확인
    * 윈도우 시스템 설정을 검증하고 웹브라우저 보안 설정, 공유 폴더를 점검
+ 무선AP(Access Point) 정보수집 및 비인가 무선 AP접속 통제
    * 무선 AP 정보를 자동으로 수집해서 NAC 정책 서버로 전송
    * NAC 관리자에 의해서 화이트 리스트(White List) 기반 무선 AP를 관리하고 통제
+ DHCP
    * 정책 서버와 차단 서버 모두 동적으로 IP를 할당하는 DHCP 기능을 지원

## 4. NAC 동작 방식

|구분|Agent 방식|Agentless 방식|
|:---:|:---|:---|
|특징|사용자 단말기 NAC Agent를 설치|사용자 단말기에 별도의 Agent를 설치할 필요가 없음|
|접근제어|{::nomarkdown}<ul><li>IP, MAC, Port, Protocol 별 접근 제어</li><li>사용자별 접근 제어</li></ul>{:/}|{::nomarkdown}<ul><li>IP, MAC, Port, Protocol 별 접근 제어</li><li>사용자별 접근 제어</li></ul>{:/}|
|장점|Patch지원, 하드웨어 및 프로세스 정보, 백신 연동 등|{::nomarkdown}<ul><li>Agent 설치 없이 IP 관리, 사용자 PC에 연결된  스위치 및 포트 정보</li><li>호스트명, 도메인명 관리 등</li></ul>{:/}|

# ESM (Enterprise Security Management)
## 1. 개요
+ 다수 보안 시스템을 통합한 통합 보안관제 시스템
+ 침입차단 시스템, 침입탐지 시스템, VPN 등의 각종 이벤트를 수집하고 분석

## 2. ESM 주요 기능
+ 통합 보안관제 업무
    * 정보보안 정책을 등록하고 자산 및 자원을 관리
    * 실시간 종합관제가 지원되어 침입발생 시 탐지가 가능
    * 각종 보안로그 및 이벤트에 대한 조회, 분석, 대응관리 지원
    * 실시간 보안감사, 위험도 추론 침입탐지, 상관성 분석을 지원
+ ESM Agent 관리
    * ESM Agent에 대해서 정보보안 정책을 실행하게 통제할 수 있음
    * 수집해야 하는 로그 정보, 각종 이벤트 등을 관리
+ 분석 보고서 관리
    * 보안 제품별 보고서 및 통합 보고서를 작성하고 로그를 분석해서 각종 통계 보고서를 자동 생성
    * 스케줄러를 사용해서 자동적으로 보고서를 생성

## 3. ESM 구성요소
+ *그림삽입*
+ ESM Agent
    * ESM Agent는 각종 보안 솔루션의 로그를 수집하는 역할 수행
    * 실시간으로 로그를 수집하여 정규 표현식으로 변환 후 ESM Manager에게 전달
+ ESM Manager
    * ESM Manager는 로그를 데이터베이스에 저장하고 위협분석, 상관성 분석, 위험도 추론 침입탐지 등의 분석을 함
    * SSL을 사용하여 ESM Agent에서 ESM Console로 명령을 전달
+ ESM Console
    * ESM Console은 모든 보안정보를 모니터링하며 침입 발생 시 명령을 ESM Manager 에게 전달한다.
    * 각종 침입에 대한 알람이 발생하며 통합 보안관제 화면을 제공함

## 4. SIEM 과 ESM 차이점
+ SIEM(Security Information Event Management)는 통합 보안관제 부분 측면에서는 ESM과 거의 유사한 통합보안 솔루션
+ SIEM과 ESM 모두 각종 보안 솔루션으로부터 로그를 수집하고 분석하는 기능을 함
+ ESM은 수집된 로그 및 분석정보를 데이터베이스에 저장하고 관리
+ SIEM은 빅데이터를 사용해서 대용량의 로그정보를 보관하고, 각 컬럼별 인덱싱이 가능하여 빠르게 데이터를 분석함
+ SIEM은 대용량의 로그 파일을 삭제하지 않고 빅데이터에 저장
+ 빅데이터는 NoSQL을 기반으로 하여 칼럼에 대해 모두 인덱스를 만들고 MapReduce, Mahout등을 사용해서 분석
    
|구분|SIEM|ESM|
|:---:|:---|:---|
|특징|기업의 모든 자원의 정보 및 로그를 통합해서 수집한다.|보안 솔루션 위주의 로그를 수집하고 통합한다.|
|수집정보|보안 시스템, 보안 소프트웨어, 네트워크 장비, 애플리케이션 로드, 이벤트, 구성 정보, 시스템 정보, 웹 로그 등|보안 시스템, 서버 시스템 로그, 이벤트 등으로 수집되는 로그가 한정적|
|분석|{::nomarkdown}<ul><li>사용자, Port, IP, 애플리케이션 등의 작은 단위로 분석이 가능</li><li>APT 공격 및 알려지지 않은 패턴에 대해서 분석</li></ul>{:/}|{::nomarkdown}<ul><li>시그니처 중심으로 분석</li><li>IP, Port 단위 분석과 알려진 패턴 위주의 분석</li></ul>{:/}|
|기반기술|{::nomarkdown}<ul><li>빅데이터 기술인 MapReduce, Indexing 등을 사용</li><li>고성능의 데이터 분석이 가능</li></ul>{:/}|{::nomarkdown}<ul><li>Agent 혹은 API를 사용해서 로그를 수집</li><li>관계형 데이터베이스의 테이블에 데이터를 입력하고 분석</li></ul>{:/}|

# 무선 LAN 보안 기법
## 1. 무선 LAN(Wireless Local Area Network)
+ LAN 기반 망과 단말기 사이를 무선주파수(RF: Radio Frequency)를 이용하여 전송하는 근거리 통신 기술
+ 무선 LAN 표준 IEEE 802 위원회에서 진행
    * WWAN : CDMA, LTE (100~200Km) - IEEE 802.16
    * WMAN : Mobile WIMAX (20~30Km) - IEEE 802.16
    * WLAN : IEEE 802.11a, IEEE 802.11.b/g/n (50m) - IEEE 802.11
    * WPAN : Zigbee, Bluetooth, uwb (3~5m) - IEEE 802.15
+ 무선 LAN 은 모바일 노드(노트북, 센서)가 AP(Access Point)에 연결되고 AP는 인터넷과 유선으로 연결된 구조
+ 전송되는 무선 신호를 쉽게 도청할 수 있으며 DDoS와 같은 형태의 공격에도 취약

## 2. SDR (Software Defined Radio)
+ 무선네트워크를 수신받기 위한 안테나와 소프트웨어로 구성
+ 무선 네트워크를 수신받아서 처리
    * 주파수를 수신받아 재생공격(Replay Attack) 가능
    * 신호를 소프트웨어로 변환하고 변조해서 IoT 서비스를 공격

## 3. 무선 LAN(Local Area Network) 보안 요소

|기능|내용|
|:---|:---|
|인증(Authentication)|{::nomarkdown}<ul><li>무선 LAN 사용을 위한 AP(Access Point) 인증</li><li>무선 LAN 단말에 대한 인증 기능</li></ul>{:/}|
|암호화(Encryption)|무선 LAN 구간에서 안전한 데이터 통신을 위한 기밀성, 무결성을 지원|

## 4. 무선 LAN 보안 기술
### 1. SSID(Service Set ID)
* 무선 LAN 서비스 영역을 구분하기 위한 식별자로 AP(Access Point)는 동일한 SSID를 가진 클라이언트만 접속을 허용하는 방법

### 2. WEP(Wired Equivalent Privacy)

![WEP](/assets/images/posts/wep.png)

[이미지 출처](https://ko.wikipedia.org/wiki/%EC%9C%A0%EC%84%A0_%EB%8F%99%EB%93%B1_%ED%94%84%EB%9D%BC%EC%9D%B4%EB%B2%84%EC%8B%9C)

* IEEE 802.11b 에서 표준화한 데이터 암호화 방식
* RC4 대칭형 암호화 알고리즘을 사용한 40bit키를 사용
* 24bit 길이의 초기화 벡터(IV : Initialization Vector)에 연결되어 64bit 혹은 128bit WEP 키열을 생성하여 해당 키를 통해 암호화를 수행
* 키열을 활용하여 정적으로 키를 사용하므로 무작위 공격과 같은 방법으로 키를 유추할 수 있음
    - Dynamic WEP : 세션 별로 키를 변경할 수 있음

### 3. WPA(Wi-Fi Protected Access) : IEEE 802.1x/EAP
* WEP의 정적 키 관리에 대한 문제점을 해소할 수 있다.
* 128bit 동적 암호화 및 복호화를 수행
* TKIP(Temporal Key Integrity Protocol)방식으로 사용자, 네트워크 세션, 전송 프레임 별로 키를 동적으로 생성

### 4. WPA2(Wi-Fi Protected Access)
* 2세대 WPA로 보안 기능이 개선됨
* AES(Advanced Encryption Standard) 암호화, 사전 인증 및 캐시로 구성

### 5. IEEE 802.11i
* WPA 방식의 암호화 알고리즘은 AES(Advanced Encryption Standard)를 사용한다.
* CCMP(Counter Mode Encryption Protocol)는 RC4 암호화 알고리즘 대신 TKIP 블록 암호화 방법을 사용하고 AES 암호화 사용, 즉 블록 암호화 방식에 인증을 강화함

### 6. 무선 LAN 공격 기법
* WPA는 인증 시 4-Way Handshaking을 사용하고, 이 과정 중에 사전파일을 대입하여 패스워드를 복호화함

|구분|설명|
|:---|:---|
|iwconfig|컴퓨터 시스템에 설치되어 있는 무선 LAN 카드를 확인|
|Monitor 모드|{::nomarkdown}<ul><li>`airmon-ng start <WLAN 명>` 을 실행하여 모니터 모드로 변경</li><li>iwconfig 명령을 실행하여 등록된 mon0 을 확인</li></ul>{:/}|
|스니핑(Sniffing)|`airodump-ng mon0` 를 실행하여 무선 LAN 패킷(Packet)을 스니핑함|
|DDoS 공격|{::nomarkdown}<ul><li>Handshaking을 다시 발생하게 하여 피해자를 대상으로 DDoS 공격을 수행</li><li>`aireplay-ng -3 -b <AP MAC> -h <LAN MAC> mon0`</li><li>3은 30,000의 트래픽을 발생시킴</li></ul>{:/}|
|크랙(Crack)|{::nomarkdown}<ul><li>`aircrack-ng`를 실행하여 크랙을 수행함</li><li>`aircrack-ng -b <AP MAC> -w <사전파일> <패킷파일>`</li></ul>{:/}|

# RFID 보안
## 1. RFID/USN(Radio Frequency Identification & Ubiquitous Sensor Network)
+ Micro-chip을 내장한 Tag, Label, Card 등에 저장된 Data를 무선 주파수를 이용하여 Reader기가 자동으로 인식하는 기술
    * ID 바코드 : 바코드 번호 Read Only 기반 식별, 유통관련 서비스
    * RFID : 무선주파수 R/W Tag, 컨테이너,우편,도서
    * USN : Ad-hoc Net (별도의 베이스 스테이션 없음) 기반 센서간 서비스, Zigbee(IEEE802.15.4), 상시접속
## 2. RFID 보안 기술

|보안 기술|내용|
|:---|:---|
|Kill Tag|사용자 요청에 따른 태그 무효화|
|Faraday Cage 원리|주파수를 차단할 수 있는 차폐망을 이용|
|방해전파|RFID 태그의 신호를 인식할 수 없도록 방해전파 전송|
|Blocker Tag|외부침입을 막기 위한 차단 태그|
|재 암호화 방법|암호화 된 RFID 태그 정보를 다시 정기적으로 암호화|
|Active Jamming|태그와 판독기 간 사전 약속된 의미없는 전자신호 발생, 부당한 판독기를 정상적으로 통신하지 못하도록 방해|
