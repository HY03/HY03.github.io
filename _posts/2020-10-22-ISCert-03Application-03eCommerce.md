---
title: 03.애플리케이션 보안 - 03.전자상거래 보안
date: '2020-10-28 05:00:00'
tags:
- 정보보안기사
- 애플리케이션 보안
- 전자상거래 보안
related: true
categories:
- IS_Certification
toc: true
---

# 전자화폐
## 1. 전자화폐
+ 디지털 서명이 있는 금액 가치 정보
+ IC칩 내장 카드나 공중정보통신망과 연결된 PC 등의 전자기기에 전자기호 형태로 화폐적 가치를 저장하였다가 상품 등의 구매에 사용할 수 있는 전자적 지급수단

## 2. 전자화폐 요구조건
+ **불추적성**(사생활 보호, **익명성**)
+ 오프라인성 : 은행에 접속하지 않아도 여러 가지 암호 프로토콜을 통해 검사가능
+ **가치이전성(양도성)** : 다른 사람에게 즉시 이전 가능
+ **분할성** : 그 가치만큼 자유롭게 분할 사용
+ **독립성(완전 정보화)** : 다른 물리적 매체에 의존해서는 안 되며, 디지털 데이터 자체로서 완벽한 화폐가치를 가져야 함
+ **이중사용 방지**(복사 및 위조 방지)
+ **익명성 취소** : 돈 세탁, 돈 약탈, 불법 구매자금 등 부정한 방법으로 그 기능이 전용될 수 있으므로 법원과 같은 공정한 기관의 명령에 의해 사용자의 식별 값이 노출될 수 있어야 함

## 3. IC 카드형 전자화폐 종류

|전자화폐 종류|주요 내용|
|:---|:---|
|**몬덱스**|⦁ 가장 대표적인 전자화폐 시스템<br/>⦁ 현금 지불의 장점과 카드 지불의 편리함을 결합<br/>⦁ 5개국 통화로 가치를 저장할 수 있음<br/>⦁ **해외 사용 및 송금과 외환거래 가능**|
|**비자캐시**|⦁ **소액 지불**을 위한 지불수단<br/>⦁ 11개국에서 사용|
|**PC Pay**|**스마트카드와 카드 리더기로 구성**된 PC Pay Device와 Interface Software로 구성|
|**Ecash**|Dig Cash사에서 개발된 전자화폐 시스템으로 **은닉서명 기술을 사용**하여 온라인상에서 완전한 익명성을 제공|
|**Net Cash**|**전자수표** 등의 금융도구와 교환이 가능한 분산 Currency Server를 기반으로 하며, 전자화폐로 바꾸어 사용 가능|

+ **FDS(Fraud Detection System)**
    * 전자금융거래에서 사용되는 단말정보, 접속로그, 거래정보 등을 분석
    * **각종 부정 거래행위를 탐지, 예방 (탐지통제, 예방통제)**
+ **가상화폐 비트코인(Bitcoin)**
    * 2009년 나카모토 사토시가 만든 디지털 통화
    * 중앙관리 장치가 없으며 P2P(Peer to Peer) 기반의 분산 데이터로 이루어져 있음
    * 공개 키 암호방식을 사용해서 거래
    * 지갑파일의 형태로 저장 관리되고 지갑에는 고유의 ID가 부여됨

# SET
## 1. SET(Secure Electronic Transaction)
* 인터넷에서 신용카드 사용 촉진을 위해 **VISA와 MASTER CARD 사에서 공동 개발한 프로토콜**
* 전자 상거래 인증의 상호 작용을 보장, **SSL에 비해 상대적으로 느림**
* **전자 서명**과 **인증서**를 통한 안전한 거래가 가능
* 신용카드의 지급 결제 처리 절차에 한해서 정의, **시스템 구축 및 인증 절차가 복잡**함
* **기밀성, 무결성, 인증, 부인봉쇄를 지원**
* **이중서명으로 가맹점과 카드 소유자 정보를 분리해서 서명**

## 2. SET 거래 절차

* **거래절차**

![SET Architecture](/assets/images/posts/set_architect.png)

[이미지 출처](http://www.maithean.com/docs/set_bk2.pdf)

## 3. 세부 절차

* 전자봉투에 대해

![Digital Envelope](/assets/images/posts/Digital_envelope.png)

[이미지 출처](http://www.maithean.com/docs/set_bk1.pdf)


* 구매자가 웹 사이트에서 물건을 구매하며 검증 정보를 준비

    ![SET 01](/assets/images/posts/set_01.png)

    [이미지 출처](https://blog.coledeman.com/2017/04/secure-electronic-transaction-set/)

    ![SET 02](/assets/images/posts/set_02.png)

    [이미지 출처](https://blog.coledeman.com/2017/04/secure-electronic-transaction-set/)

    - 구매자(Card Holder)가 상점(Merchant)의 웹 사이트서 상품을 선택
    - 구매자가 상점의 인증서를 수신, 상점 검증
    - 구매자가 아래의 정보를 상점에게 전송
        + 자신의 인증서
        + 암호화된 구매정보 = 상점의 공개키로 암호화된 구매정보
        + 암호화된 결제정보 = 대칭키로 암호화된 결제정보
        + 검증해시값 = hash(구매정보), hash(결제정보)
        + 전자 서명 = 검증해시값 을 고객의 개인키로 암호화한 정보
        + 대칭키를 담은 전자봉투 = 결제정보를 암호화한 대칭키를 PG사의 공개키로 암호화한 정보

* 상점의 검증

    ![SET 03](/assets/images/posts/set_03.png)

    [이미지 출처](https://blog.coledeman.com/2017/04/secure-electronic-transaction-set/)

    - 상점은 구매자의 인증서를 수신, 구매자 검증
    - 상점은 구매 정보 복호화
    - 구매 정보에 해시를 생성
    - 새로 생성한 해시와 전달받은 hash(구매 정보)의 일치 확인
    - 고객의 공개키로 전자서명을 풀어봄
    - 전자서명을 풀어 나온 값이 검증 해시값과 같은지 확인

* PG사의 검증

    ![SET 04](/assets/images/posts/set_04.png)

    [이미지 출처](https://blog.coledeman.com/2017/04/secure-electronic-transaction-set/)

    - 상점으로부터 검증 정보 중 구매정보를 제외한 나머지 정보를 받음
    - PG사는 전자봉투를 자신의 개인키로 복호화
    - 대칭키를 받아서 암호화된 결제 정보를 복호화
    - 결제정보에 hash를 적용
    - 새로 생성한 해시와 전달받은 hash(결제 정보)의 일치를 확인
    - 고객의 공개키로 전자서명을 풀어봄
    - 전자서명을 풀어서 나온 값이 검증 해시값과 같은지 확인
    - 은행에 대금지급을 요청

## 4. SET 구성요소

|구성요소|세부 내용|
|:---|:---|
|**구매자(카드 소지자)**|전자상거래를 수행하는 전자지갑(사용자 신분을 확인하는 SET 인증서 포함)을 얻음|
|**판매자(상점 소유자)**|웹상의 상품 운영자, SET를 이용하여 상품 판매를 제공|
|**PG(Payment Gateway)**|기존의 카드 지불 네트워크의 통로|
|**발급기관(Issuer)**|⦁ 사용자 계좌가 있는 재정 기관으로 신용카드를 발행<br/>⦁ CA 운영하여 사용자에게 인증서를 발행|
|**지불처리은행(Acquirer)**|⦁ 상점의 계좌가 있는 재정 기관으로 신용카드 인가 여부와 지불을 처리<br/>⦁ 지불 Gateway를 운영하고 CA를 운영하여 상인에게 인증서를 발행|
|**인증기관(Certification Authority)**|SET에 참여하는 사용자, 상점, PG의 정당성을 보증하는 기관|

+ SET 사용 기술
    * 대칭키, 공개키, 전자서명, 해시함수, 전자봉투, 공개키 인증(X.509), 이중서명
    * 알고리즘 : DES, RSA, SHA-1

## 5. **SET 이중서명 (Dual Signature)**
+ **SET 이중서명 사용 이유**
    * 사용자는 판매자에게 지불정보(계좌 정보)를 숨기고 싶다.
    * 사용자는 PG로부터 주문정보(물품명세서 등)를 숨기고 싶다.
    * PG(Payment Gateway)는 판매자가 전송한 결제요청이 실제 고객이 의뢰한 정보인지 확인하고 싶다.

    ![SET 01](/assets/images/posts/set_01.png)

    [이미지 출처](https://blog.coledeman.com/2017/04/secure-electronic-transaction-set/)

    * 주문정보와 지불정보를 각각 해시하고 생성된 두 개의 해시 다이제스트를 하나로 합치는 연접 과정 후에 또 다시 해시를 진행하고, 해시 다이제스트를 송신자의 개인키로 암호화한다.
+ SET 장점과 단점
    * 장점
        - 전자상거래의 사기 방지, 기존의 신용카드 기반을 그대로 활용
    * 단점
        - 암호 프로토콜이 너무 복잡함
        - RSA 속도 저하, 카드 소지자에게 전자지갑 소프트웨어를 요구

# SSL
## 1. SSL(Secure Socket Layer) 개요
### 1. 개요
* 개인정보를 전송하는 네트워크 구간은 보안서버를 사용해서 송신 및 수신되는 데이터를 암호화해야함 - 스니퍼(Sniffer) 와 같은 네트워크 패킷 모니터링 도구를 통한 스니핑 방지
* 보안서버의 구축은 SSL 및 SSO 등으로 구축할 수 있으며 공개용 인터넷에서 사용하는 방식은 SSL임

### 2. SSL(Secure Socket Layer)
* **인터넷과 같은 개방환경에서 Client와 Server의 안전한 통신을 목적으로 Netscape 사에서 개발**
* 암호문 전송을 위해 RSA 공개키 알고리즘을 사용
* **X.509 인증서** 지원
* **443 포트** 사용
* Transport ~ Application 계층에서 동작 (http, ftp, telnet, mail)
* 기밀성, 무결성, 인증의 세 가지 보안 서비스를 제공

### 3. SSL 보안 서비스

|보안 서비스|세부 내용|
|:---|:---|
|**인증(Authentication)**|거래하고자 하는 사이트가 신뢰할 수 있으며 검증된 사이트인지 개인정보를 송신하기 전에 먼저 상대 사이트를 인증하는 기능|
|**무결성(Integrity)**|송신자 측의 웹브라우저에서 상대편 웹 서버까지 송신 중 공격자나 제 3자에 의해 무단으로 데이터가 위변조 되는 것을 방지하는 기능|
|**기밀성(Confidentiality)**|앞서 나온 DES, 3DES, IDEA 등 여러 가지 암호화 방식을 사용하여 데이터의 송 수신 중에 인가되지 않은 사용자의 데이터에 대한 불법적인 접근을 통제하고 만일의 경우 데이터가 공격에 의하여 유출되었다 하여도 쉽게 읽힐 수 없는 형태로 변환시키는 기능|

### 4. SSL Handshaking 과정
* 웹 브라우저는 SSL 요청 전에 웹 서버 포트인 443 포트를 호출하여 TCP 3-Way Handshaking을 수행
* 웹 브라우저는 TCP 연결이 완료되면 웹 서버에게 "Client Hello" 를 전송
    - 웹브라우저에서 지원하는 SSL 버전 (예:TLS 1.0)
    - 지원하는 암호화 알고리즘(예:AES 대칭키 및 CBC블록 암호화 등)
    - **Random** : 재생공격(Replay Attack) 방지를 의한 임의적 숫자
* 서버는 웹 브라우저에게 "Server Hello" 를 전송
    - 웹 서버가 암호화 알고리즘을 결정해서 웹 브라우저에게 전송
* 웹브라우저는 최종적으로 "Change Cipher Spec" 을 서버에 전송
    - 웹브라우저가 협의된 암호화 알고리즘을 사용한다는 것을 알림

### 5. **SSL 구성요소**

<table>
    <tr>
        <th colspan=4 align="center">SSL 아키텍처 구조</th>
    </tr>
    <tr>
        <td align="center">SSL Handshake Protocol</td>
        <td align="center">SSL change Cipher Spec Protocol</td>
        <td align="center">SSL Alert Protocol</td>
        <td align="center">Application Protocol(ex.HTTP)</td>
    </tr>
    <tr>
        <td colspan=4 align="center">SSL Record Protocol</td>
    </tr>
    <tr>
        <td colspan=4  align="center">TCP</td>
    </tr>
    <tr>
        <td colspan=4  align="center">IP</td>
    </tr>
</table>

|구성요소|세부 내용|
|:---:|:---|
|**Change Cipher Spec Protocol**|Handshake Protocol 에서 협의된 암호 알고리즘, 키 교환 알고리즘, MAC 암호화, HASH 알고리즘이 사용될 것을 클라이언트와 웹 서버에게 공지|
|**Alert Protocol**|⦁ SSL 통신을 하는 도중 클라이언트와 웹 서버 중 누군가의 에러나 세션의 종료, 비정상적인 동작이 발생할 때 사용되는 프로토콜<br/>⦁ 첫 번째 바이트에 위험도 수준을 결정하는 Level 필드가 있음. 1인 경우 Warning의 의미로 통신의 중단은 없고 2인 경우 Fatal로 Alert 즉시 클라이언트와 서버의 통신을 중단하게 됨<br/>⦁ 두 번째 바이트에는 어떠한 이유로 Alert Protocol이 발생하였는지 나타내는 Description 필드가 있음|
|**Record Protocol**|⦁ 상위 계층에서 전달받은 데이터를 Handshake Protocol에서 협의가 이루어진 암호 알고리즘, MAC 알고리즘, HASH 알고리즘을 사용해 데이터를 암호화<br/>⦁ 산출된 데이터를 SSL에서 처리가 가능한 크기의 블록으로 나누고 압축<br/>⦁ 선택적으로 MAC(Message Authentication Code)를 덧붙여 전송<br/>⦁ 수신한 데이터는 복호화, MAC 유효성 검사, 압축 해제, 재결합의 과정을 거쳐 상위 계층에 전달|

## 2. SSL Handshaking Protocol 과정
### 1. SSL Handshaking Protocol 세부 과정

|진행 과정|세부 내용|
|:---|:---|
|**Client Hello**|Handshake Protocl 의 첫 단계로 클라이언트의 브라우저에서 지원하는 암호 알고리즘, 키 교환 알고리즘, MAC 암호화, HASH 알고리즘을 서버에 전송|
|**Server Hello**|Client Hello 메시지 내용 중 서버가 지원할 수 있는 알고리즘들을 클라이언트에게 전송|
|**Server Hello Done**|클라이언트에게 서버의 요청이 완료되었음을 공지|
|**Client 인증서**|서버에서 클라이언트의 인증 요청 발생 시 클라이언트의 인증서를 전달|
|**Premaster Key 전송**|전달받은 서버의 인증서를 통해 신뢰할 수 있는 서버인지 확인 후 암호 통신에 사용할 **Session Key를 생성**하고 이것을 서버의 공개키로 암호화해 Premaster Key를 만들어 서버로 전송|
|**Change Cipher Spec**|앞의 단계에서 협의된 암호 알고리즘들을 이후부터 사용한다는 것을 서버에게 알림|
|**Finished**|서버에게 협의 종료를 전달|
|**Change Cipher Spec**|서버 또한 클라이언트의 응답에 동의하고 협의된 알고리즘의 적용을 공지|
|**Finished**|클라이언트에게 협의에 대한 종료를 선언|

* SSL 버전은 SSL 3.0 이상, TLS 1.1 이상을 사용해야 보안 취약점에 문제가 없음 


### 2. OpenSSL 사용
* 웹서버에 OpenSSL 패키지를 설치하고 개인키와 인증서를 생성한다.
    - 웹브라우저는 변경 없이 SSL 사용 가능
* 웹서버는 개인키를 최소 길이 2048 이상으로 생성한다.

### 3. OpenSSL 보안 취약점
* **하트블리드(HeartBleed) 취약점**
    - **OpenSSL 암호화 라이브러리(Library)의 하트비트(Heartbeat) 라는 확장모듈에서 발생한 문제**
    - **웹 브라우저가 요청(Request)했을 때 데이터 길이를 검증하지 않아 메모리에 저장되어 있는 평문의 64KB 가 노출되는 현상**
* 해결법
    - OpenSSL 버전을 1.0.1g 버전으로 업데이트
    - Heartbeat를 사용하지 않도록 재컴파일
        ```
        ./config -DOPENSSL_NO_HEARTBEATS
        make depend
        make
        make install
        ```
    - 취약점 공격 탐지 및 차단 패턴 적용
    - 서버 측 SSL 비밀키(Secret Key)가 유출되었을 가능성이 있기 때문에 인증서 재발급을 검토
    - 취약점 조치 완료 후, 사용자들의 비밀번호 재설정을 유도하여 탈취된 계정을 악용한 추가 피해 방지 방안 고려

# sHTTP
## 1. sHTTP 개요
+ 메시지를 안전하게 전송하기 위해 사용
+ 웹 상의 파일들이 안전하게 교환될 수 있도록 해주는 HTTP의 확장판 (HTTP만 지원)
+ HTTP를 캡슐화, HTTP와 같은 message base 프로토콜
+ HTTP와 동일한 요청과 응답 구조를 이용
+ SSL이 전송 계층에 작동하는 것에 비해 S-HTTP는 응용 계층에서 보안 기능을 제공하므로 더 효율적임

## 2. sHTTP 기능
+ **전용 웹브라우저와 웹서버를 사용**
+ 기밀성, 무결성, 전자서명 기능 지원
+ **sHTTP는 웹브라우저와 웹서버 사이 암호화 방식**, CA 선택 등의 기능을 제공

# IPSEC
## 1. IPSEC(IP Security) 개요
+ **IPSEC(IP Security)**
    * 인터넷상에 전용 회선처럼 이용 가능한 가상 전용 회선을 구축, 안전한 통신을 실현하는 통신 규약
+ **IPSEC 전송 방법**

    |종류|설명|
    |:---|:---|
    |**터널 모드**|VPN과 같은 구성으로 패킷의 출발지에서 일반 패킷이 보내지면 중간에서 **IPSec을 탑재한 중계 장비가 패킷 전체를 암호화(인증)**하고 중계 장비의 IP 주소를 붙여 전송, IP 헤더까지 암호화|
    |**전송 모드**|패킷의 출발지에서 암호화(인증)를 하고 목적지에서 복호화가 이루어지므로 End-to-End 보안을 제공, 메시지만 암호화|

## 2. IPSEC Header 구조
+ **IPSEC 인증 및 암호화를 위한 헤더**

    |종류|설명|
    |:---|:---|
    |**AH(Authentication Header**)|⦁ **데이터 무결성과 IP 패킷의 인증을 제공, MAC 기반**<br/>⦁ Replay Attack으로부터 보호 기능(순서번호 사용)을 제공<br/>⦁ 인증 시 MD5, SHA-1 인증 알고리즘을 이용하여 Key 값과 IP 패킷의 데이터를 입력한 인증 값을 계산하여 인증 필드에 기록<br/>⦁ 수신자는 같은 키를 이용하여 인증 값을 검증|
    |**ESP(Encapsulating Security Payload)**|⦁ **전송 자료를 암호화하여 전송하고 수신자가 받으 자료를 복호화하여 수신**<br/>⦁ IP 데이터그램에서 제공하는 기능이며 데이터의 선택적 인증, 무결성, 기밀성, Replay Attack 방지를 위해 사용<br/>⦁ AH와 달리 암호화를 제공(대칭키, DES, 3-DES 알고리즘)<br/>⦁ TCP/UDP 등의 Transport 계층까지 암호화할 경우 Transport 모드<br/>⦁ 전체 IP 패킷에 대해 암호화할 경우 터널 모드를 사용|

+ **IPSEC 키 관리**

    |종류|설명|
    |:---|:---|
    |**ISAKMP**|⦁ Internet Security Assiociation and Key Management Protocol<br/>⦁ **Security Association 설정, 협상, 변경, 삭제 등 SA 관리와 키 교환을 정의했으나 키 교환 메커니즘에 대한 언급은 없음**|
    |**IKE**|⦁ IKE(Internet Key Exchange) 메시지는 UDP 프로토콜을 사용해서 전달되면 출발지 및 도착지 주소는 500port를 사용하게 됨<br/>⦁ **키 교환 담당**|

# OTP
## 1. OTP(One Time Password) 개요
+ **OTP(One Time Password)**
    * OTP 생성 매체에 의해 필요한 시점에 발생하고 매번 다른 번호로 생성되는 높은 보안수준을 가진 사용자 동적 비밀번호
    * 사용된 비밀번호는 일회성으로서 다시 생성되지 않음
+ **OTP의 동기 및 비동기식 방식**

    |구분|방식|단계|
    |:---|:---|:---|
    |**동기식**|시간, 이벤트|⦁ Time 동기화 Token은 정해진 고정된 시간 간격 주기로 난수 값 생성<br/>⦁ 난수 값 생성을 위한 특별한 암호화 알고리즘과 비밀키가 필요<br/>⦁ 토큰 장치로부터 새로 생성된 난수와 개인의 PIN 번호를 입력하면 인증시스템 내의 사용자 개인정보와 생성된 패스워드를 검증하여 인증|
    |**비동기식**|질의응답|⦁ 사용자가 인증 요구와 함께 PIN을 전송하면 인증서버는 난수를 발생하여 Challenge로 사용자에게 전달<br/>⦁ 사용자는 다시 Challenge 값을 암호화하여 Response를 반환하면 인증 서버는 자신의 결과값과 비교하여 인증<br/>⦁ 단점 : 느림, 복잡<br/>⦁ 장점 : 안정성이 매우 우수|

## 2. 동기 방식과 비동기 방식 인증 방법
+ **동기화 방식**

    ![OTP Time Based](/assets/images/posts/TOTP-algorithm.png)

    [이미지 출처](https://www.protectimus.com/blog/totp-algorithm-explained/)

    * 사용자의 OTP 생성 매체와 은행의 OTP 인증서버 사이에 동기화된 기준값에 따라 OTP가 생성되는 방식
    * 동기화된 기준값에 따라 **시간 동기화방식**과 **이벤트 동기화방식**으로 분류됨
    * 시간 동기화 방식은 OTP 생성 매체가 매시간 비밀번호를 자동으로 생성하는 형태로 시간을 기준값으로 하여 OTP 생성 매체와 OTP 인증서버가 동기화됨
    * 시간을 입력값으로 동기화하기 때문에 간편한 장점을 가지지만, 일정 시간 동안 은행에 OTP를 전송하지 못하면 다시 새로운 OTP가 생성될 때까지 기다려야 함
    * 이벤트 동기화 방식은 OTP 생성 매체와 인증서버의 동기화된 인증횟수를 기준값으로 생성, OTP 생성 매체에서 생성된 비밀번호 횟수와 인증서버가 생성한 비밀번호 횟수가 자동으로 동기화되기 때문에 시간 동기화의 불편성을 완화
+ **비동기 방식 : 질의응답(Challenge-Response)**

    ![OTP Challenge-Response](/assets/images/posts/otp-challenge-response.png)

    [이미지 출처](https://cpl.thalesgroup.com/access-management/authenticators/one-time-password-otp/gold-challenge-response-token)

    * 사용자의 OTP 생성 매체와 은행의 OTP 인증 서버 사이에 동기화되는 기준값이 없으며 사용자가 직접 임의의 난수(질의 값)를 OTP 생성 매체에 입력하여 OTP를 생성하는 방식
    * 사용자가 은행의 OTP 인증서버로부터 받은 질의 값(Challenge)을 OTP 생성 매체에 직접 입력하면 응답 값(Response)이 생성됨
    * 사용자가 직접 OTP 생성 매체에 질의 값을 입력해야 하며, 응답 값인 OTP가 생성되기 때문에 전자금융 사고 발생 시 명백한 책임소재를 가릴 수 있고 보안성도 높은 방식
    * 직접 질의 값을 확인하여 OTP 생성 매체에 입력해야 하므로 은행이 별도의 질의 값을 관리해야 함

# 전자문서와 ebXML
## 1. 전자문서(Electronic Documents) 개요
+ **전자문서(Electronic Documents)**
    * 전자상거래 시 비즈니스 거래를 위해 활용되는 문서로 비즈니스 정보 개체의 집합
+ 전자문서 유형

    |유형|세부 내용|
    |:---|:---|
    |EDI(Electronic Document Interchange)|⦁ 기업 간의 전자상거래 시 전자문서를 교환하기 위한 문서화 표준<br/>⦁ UN/EDIFACT 표준을 준수|
    |XML/EDI|XML 문서를 인터넷을 활용해서 전자문서를 교환하는 개방형 표준|
    |XMI(eXtensible Markup Interchange)|W3C에서 제안한 것으로 웹에서 구조화된 문서를 교환하기 위한 웹 표준|
    |ebXML(e-business Extensible Markup Language)|UN/CEFACT와 OASIS에서 표준화한 기업 간의 전자상거래 프레임워크|

## 2. ebXML(e-business Extensible Markup Language) 개요
+ ebXML 전자상거래 방법

    ![ebXML](/assets/images/posts/ebXML.gif)

    [이미지 출처](https://www.ibm.com/developerworks/library/x-ebxml/)

    * 기업 A가 기업 B와 전자상거래를 위해 ebXML 저장소에 비즈니스 상세항목을 요청
    * 비즈니스 상세항목은 기술적 요소와 비즈니스적 요소로 이루어짐
    * 기업 A는 ebXML로부터 상세항목을 받고 기업 A의 정보시스템을 개발
    * 구현된 상세항목과 기업 A의 기업정보를 다시 ebXML 저장소에 저장
    * 기업 B와 전자상거래를 위한 계약을 체결
    * 기업 B는 기업 A의 정보를 얻어 전자상거래가 이루어짐
    * 비슷한 예 : Biztalk
+ **ebXML 구성요소**

    |구성요소|세부 내용|
    |:---|:---|
    |**비즈니스 프로세스**<br/>**(Business Process)**|⦁ 비즈니스 거래 절차에 대한 표준화된 방법<br/>⦁ 모델링을 통해서 비즈니스 프로세스를 표현하는 방법을 정의|
    |**핵심 컴포넌트**<br/>**(Core Components)**|비즈니스에서 교환되는 전자문서를 재사용할 수 있도록 표준화 작업을 수행|
    |**등록저장소**<br/>**(Registry Repository)**|⦁ 거래 당사자들에 의해 제출된 정보를 저장<br/>⦁ 메타 데이터 저장소|
    |**거래 당사자**<br/>**(Trading Partners)**|⦁ 비즈니스 거래 당사자에 대한 정보 및 협업을 위한 프로파일<br/>⦁ 협업 규약 프로파일, 협업 규약 약정서|
    |**전송,교환 및 패키징**<br/>**(Transport,Routing and Packaging)**|ebXML 메시지를 상호운영, 보안, 전달을 위한 표준|

# Web Service와 XML(eXtensible Markup Language) 보안
## 1. 웹 서비스(Web Service) 개요
+ **웹 서비스(Web Service)**
    * 인터넷 표준 기술(WSDL, SOAP, UDDI, XML)을 활용하여 기업 내부 및 기업 외부의 정보시스템을 상호연계하기 위한 기술
+ Web Service 처리 방법

    ![Web Service](/assets/images/posts/Webservices.png)

    [이미지 출처](https://en.wikipedia.org/wiki/Web_service)

+ **Web Service 표준기술**

    |표준기술|세부내용|
    |:---|:---|
    |**WSDL(Web Service Description Language)**|서비스 제공자와 서비스 사용자 간의 웹 서비스 파라미터의 이름, 서비스가 위치한 URL 및 웹 서비스 호출에 관한 정보를 기술하는 표준|
    |**UDDI(Universal Description, Descovery and Integration)**|서비스 제공자가 웹 서비스를 등록하고 서비스 사용자가 웹 서비스를 검색하기 위한 레지스트리|
    |**SOAP(Simple Object Access Protocol)**|XML을 기반으로 하는 메시지 표준으로 서비스 사용자가 서비스 제공자에 의해서 노출된 웹 서비스를 호출하고 결과를 받기 위한 표준 프로토콜|

## 2. XML(eXtensible Markup Language) 보안 기술
+ **Web Service 및 XML 보안 기술**

    |보안 기술|세부 내용|
    |:---|:---|
    |**XML 전자서명**<br/>**(Digital Signature)**|XML 문서에 대해서 Element 단위 혹은 문서단위 **전자서명**|
    |**XML 암호화**<br/>**(Encryption)**|XML 문서에 대한 **대칭키 및 공개키 기반 암호화 수행**|
    |**XACML**<br/>**(eXtensible Access Control Markup Language)**|⦁ 정보자원에 대한 접근정책을 정의한 표준 XML 문서<br/>⦁ **접근 통제** 정책 정의함|
    |**XKMS**<br/>**(XML Key Management Specification)**|**공개키 관리**를 위한 공개키 획득, 검증, 키 등록, 폐기와 같은 메커니즘 제공|
    |**SAML**<br/>**(Security Assertion Markup Language)**|Security Token 형태로 **인증에 필요한 권한정보**|


