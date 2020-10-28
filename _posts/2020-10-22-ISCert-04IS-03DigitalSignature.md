---
title: 04.정보보안 일반 - 03.전자서명
date: '2020-10-28 09:00:00'
tags:
- 정보보안기사
- 정보보안 일반
- 전자서명
related: true
categories:
- IS_Certification
toc: true
---

# 전자서명 인증 구조 및 주요 특징
## 1. 전자서명(Digital Signature)의 이해
+ 전자서명(Digital Signature)의 정의
    * 작성자의 신원과 전자문서 변경 여부를 확인할 수 있도록 전자문서를 비대칭 암호화 방식을 이용하여 전자서명 생성키로 생성한 정보
    * 개인의 고유성을 주장하고 인증받기 위해서 전자적 문서에 서명하는 방법으로 무결성, 추적성 확보를 목적으로 함
+ 암호화 키
    * 5개의 암호화키가 사용됨
    * 송신자의 사설키(개인키)와 공개키
    * 수신자의 사설키(개인키)와 공개키
    * 대칭키(비밀키)
+ 전자서명의 특징

    |특징|상세 내용|
    |:---|:---|
    |서명자 인증(Authentication)|전자서명을 생성한 서명인을 검증 가능(서명자의 공개키)|
    |부인방지(Non-Repudiation)|서명인은 자신이 서명한 사실을 부인 불가|
    |위조 불가(Unforgeable)|서명인의 개인키가 없으면 서명을 위조하는 것은 불가함|
    |변경 불가(Unalterable)|이미 한 서명을 변경하는 것은 불가|
    |재사용 불가(Not-Reusable)|한 문서의 서명을 다른 문서의 서명으로 재사용 불가|

+ 전자서명 기능
    * 사용자 인증
        - 서명문의 서명자임을 제3자가 확인
        - 비대칭(공개키) 암호화 기법 이용
    * 메시지 인증
        - 메시지 내용의 무결성 보증
        - 해시함수 이용

## 2. **전자서명 과정**

![Digital Envelope](/assets/images/posts/Digital_envelope.png)

[이미지 출처](http://www.maithean.com/docs/set_bk1.pdf)

+ 송신자
    * hash(message) -> message digest
    * enc(message digest, 송신자 개인키) -> 전자서명
    * enc(전자서명, 대칭키) -> 전자서명 암호화
    
    * enc(message, 대칭키) -> 암호화된 메시지
    
    * enc(대칭키, 수신자 공개키) -> 전자봉투
+ 수신자
    * dec(전자봉투, 수신자 비밀키) -> 대칭키
    
    * dec(전자서명 암호화, 대칭키) -> 전자서명
    * dec(전자서명, 송신자 공개키) -> message digest

    * dec(암호화된 메시지, 대칭키) -> message
    * hash(message) -> message digest

    * message digest 비교
## 3. 전자서명 기법의 종류

### 1. RSA 전자서명
* RSA 암호화 알고리즘을 사용해서 메시지에 전자서명을 한다.
* RSA 방식은 암호화와 복호화를 하는 경우 송신자의 개인키와 공개키로 사용한다.
* 소인수 분해의 어려움을 기반으로 하는 전자서명이다.

### 2. ElGamal 전자서명
* 1985년 발표된 것으로 이산대수 문제를 이용한 전자서명이다.
* 전자서명의 안전성은 이산대수 문제에 기반하고 있다.
* ElGamal 은 암호화 알고리즘으로는 사용할 수가 없고 오직 전자서명만 가능하다.
* 서명자 A는 자신의 개인키를 사용하여 메시지에 전자서명을 한다.

### 3. Schnorr 전자서명
* IC 카드에 적합한 전자서명 방식이다.
* ElGamal를 기반으로 하고 서명의 크기를줄이기 위해서 제안된 방식이다.

### 4. DSS
* Digital Signature Standard
* 미국 저자서명 표준으로 ElGamel 전자서명을 개량한 것이다.
* RSA와 다르게 암호화키 교환과 암호화는 지원하지 않고 오직 전자서명만 지원한다.
* ElGamal 전자서명 방식으로 서명 검증을 개량한 것으로 서명 검증의 부하를 줄이기 위해서 계산량을 줄인 방식이다.

### 5. KCDSA
* Korea Certificate-based Digital Signature Algorithm
* 1996년 개발된 국내 전자서명 표준으로 국내 전자서명법으 준용하여 인감과 같은 법적 효력을 가지고 있다.
* 1998년 한국정보통신기술 협회에서 국가 표준으로 제정되었다.
* 이산대수를 기반으로 하는 전자서명 알고리즘이다.

### 6. ECC 전자서명
* ECDSA(Elliptic Curve Digital Signature Algorithm)로 타원곡선을 기반으로 하는 전자서명이다.
* 타원곡선 알고리즘은 짧은 키를 사용하기 때문에 전자서명을 짧은 시간 내에 생성이 가능하게 되었다.

# PKI 구조
## 1. PKI(Public Key Infrastructure) 개요
### 1. 개념
* 공인인증의 구조를 말하며, 공인인증서(X.509, ITU-T 표준)를 통해서 인증 받는 구조
* 인증기관(CA)에 사용자 정보를 입력하고 공인인증서를 발급 받는다.
* 인증기관은 사용자 신원을 확인하고 인증서를 발급해 주는 구조
* 사용자가 너무 많기 때문에 사용자 신원확인을 대행해 주는 등록기관(RA) 필요
* 등록기관이 신원확인을 하고 인증기관이 공인인증서를 발급해 줌
* 인증기관 인증서 취소 목록(CRL) 을 관리해서 인증서의 유효성을 확인
* 공개키 알고리즘을 사용해서 암호화 및 전자서명을 하기 위한 보안 시스템으로 디지털 인증서를 사용해서 사용자를 인증할 때 사용

### 2. PKI(Public Key Infrastructure) 목적

|목적|주요 내용|요소기술|
|:---|:---|:---|
|인증(Authentication)|사용자에 대한 확인, 검증(공개키 인증)|Certificate|
|기밀성(Confidentiality)|송수신 정보에 대한 암호화|암호화, 복호화|
|무결성(Integrity)|송수신 정보의 위/변조 방지|해시함수(MD)|
|부인봉쇄(Non-Repudiation)|송수신 사실에 대한 부인방지|전자서명|
|접근 제어(Access Control)|허가된 수신자만 정보에 접근 가능|DAC, MAC, RBAC|
|키 관리(Key Management)|공개키 발급, 등록, 관리, 폐기||

## 2. PKI 구성
### 1. PKI 구조와 구성요소

![PKI](/assets/images/posts/building-a-pki.jpg)

[이미지 출처](https://cybersecuritynews.co.uk/how-does-public-key-infrastructure-pki-work/)

### 2. PKI 세부 구성 내용

|구성요소|주요 기능|
|:---|:---|
|인증기관(CA) Certification Authority|{::nomarkdown}<ul><li>인증 정책 수립, 인증서 및 인증서 폐기 목록 관리(생성, 공개, 취소, 재발급)</li><li>공개키 인증서를 자신의 개인키로 서명</li><li>공개키와 개인키 쌍의 소유자 신분 증명</li><li>다른 CA와 상호 인증</li><li>CRL(Certificate Revocation List, 인증서 폐기목록) 등록 및 인증 절차 작성</li></ul>{:/}|
|등록기관(RA) Registration Authority|{::nomarkdown}<ul><li>사용자 신원 확인, 인증서 요구 승인, CA에 인증서 발급 요청</li><li>디지털 인증서 신청자의 식별과 인증을 책임</li><li>PKI를 이용하는 Application과 CA 간 인터페이스 제공</li><li>대표적 RA : 은행, 증권사</li></ul>{:/}|
|CRL (Certificate Revocation List)|{::nomarkdown}<ul><li>인증서 폐기 목록</li><li>인증서의 지속적인 유효함을 점검하는 도구</li><li>폐지 사유 : 디지털 서명의 개인키 노출, 인증서가 필요 없을 경우, 개인키 분실, 인증서 효력 정지 등</li><li>OCSP : 인증서 상태에 관한 정보를 조회 또는 CRL 검색 프로토콜</li></ul>{:/}|
|Directory|{::nomarkdown}<ul><li>인증서, 암호키에 대한 저장, 관리, 검색 등의 기능, PKI 관련 정보 공개</li><li>디렉터리 표준 형식으로는 X.500(DAP, Directory Access Protocol)과 이것을 간략화시킨 LDAP(Lightweight Directory Access Protocol)가 있음</li></ul>{:/}|
|CPS (Certification Practice Statement)|{::nomarkdown}<ul><li>인증서 실무 준칙 문서</li><li>PKI를 구현하기 위한 절차를 상세히 설명해 놓은 문서로 CA의 운영을 통제하는 상세한 일련의 규정</li><li>인증 정책, 인증 절차, 비밀키 관리 절차 등을 포함</li><li>모든 사용자에게 반드시 공개해야 함(홈페이지에 게시)</li></ul>{:/}|
|X.509|{::nomarkdown}<ul><li>X.500 디렉터리 서비스에서 서로 간의 인증을 위해 개발된 것</li><li>CA에서 발행하는 인증서를 기반으로 함, 공개키 인증서 표준 포맷</li><li>공개키 인증서의 포맷 표준 : 발행자, 소유자, 소유자의 공개키, 유효기간, 고유번호, 알고리즘</li><li>사용자의 신원과 키 정보를 서로 결합한다는 것을 의미</li></ul>{:/}|

## 3. PKI 세부 내용
### 1. 인증서 발급센터와 인증기관
* 인증서 발급센터

    ![CA Hiearachy](/assets/images/posts/ca_hiearachy.png)

    [이미지 출처](http://itwiki.kr/w/%EA%B3%B5%EA%B0%9C%ED%82%A4_%EA%B8%B0%EB%B0%98_%EA%B5%AC%EC%A1%B0)
    
    - Root CA, 정책 승인 기관(Policy Approving Authority), 정책 인증기관(Policy Certification Authority), 인증기관(Certification Authority) 으로 나뉘어짐
    - 사용자들이 사용하는 X.509 인증서(공인 인증서)를 발급
    - 등록기관(RA)이 인증기관 하위에서 사용자 신원 확인 역할수행
    - 은행에서 발급받은 인증서를 증권회사에 인증받기 위해서 인증기관 간의 상호인증을 수행하는 실시간 프로토콜(OCSP)을 사용

### 2. OCSP(Online Certificate Status Protocol)

![OCSP](/assets/images/posts/OCSP.png)

[이미지 출처](https://doc.nexusgroup.com/display/PUB/Nexus+OCSP+Responder+overview)

### 3. 디렉터리 서비스(Directory Services)

![Directory Service](/assets/images/posts/directory-service.jpg)

[이미지 출처](http://what-when-how.com/networking/directory-services-networking/)

* API(Application Program Interface)를 활용하여 데이터를 입력, 수정, 삭제, 조회할 수 있는 서비스
* ITU-T 표준 X.500
* IETF 표준 LDAP
* DAP(Directory Access Protocol) 프로토콜을 활용하여 작업을 수행

### 4. 공개키 인증서(X.509 인증서)
* 1988년 ITU(International Telecommunications Union)에 의해 표준으로 개발
* 1993년 두 번째 버전 출시 : 2개의 인식자가 첨가
* 1997년 세 번째 버전에서 확장 영역이 추가
* IETF(Internet Engineering Task Force)가 인터넷상에서 X.509 인증서 사용을 결정함, 인증서의 확장영역에 인터넷 사용에 필요한 요건을 정함

### 5. X.509 인증서의 포함 내용
* 인증서 버전, 인증서 고유번호, 발급자의 서명, 발급자 정보
* 인증서 유효기간, 주체 정보, 공개키, 주체키
* X.509 인증서의 ASN.1 구조

    |구성|설명|
    |:---|:---|
    |OID(Object Identifiers)|다양한 정보를 나타내기 위해 사용(예: CA가 사용하는 RSA 또는 DSA와 같은 알고리즘, 인증 정책 등을 X.509 인증서에 기록하기 위해 사용)|
    |AI(Algorithm Identifiers)|암호 알고리즘과 키에 대한 정보(예: X.509 인증서가 사용하는 전자서명 알고리즘, 공개키와 관련된 알고리즘을 알 수 있음)|
    |DS(Directory String)|{::nomarkdown}<ul><li>텍스트(text) 정보</li><li>DS는 다양한 언어와 문자를 사용할 수 있도록 PrintableString, TeletexString, BMPString, UTF8String, UniversalString 등 여러 형태로 정의</li></ul>{:/}|
    |DN(Distinguished Names)|국제적 디렉터리(directory)에서 X.509 인증서를 식별해야 하기 때문에 계층적으로 이름을 부여|
    |GN(General Names)|X.509 인증서의 이름을 암호화하기 위한 것으로, GN은 7개의 표준 이름 형태를 사용|

# PMI
## 1. PMI(Privilege Management Infrastructure)
+ 권한관리 기반구조(PMI)는 Attribute Certificate를 발급, 저장, 유통을 제어하여 권한관리 자원과 소유자 간의 관계를 신뢰기관이 보증하고 유지하는 권한관리 기반구조이다.
+ 기존 공개키 인증서는 신원 확인용으로만 사용되어 권한인증 서비스가 불가능하고, 패스워드 방식의 접근통제에 대한 불편함을 해결하기 위하여 필요하다.

## 2. 속성인증서(AC : Attribute Certificate)
+ 속성인증서(AC : Attribute Certificate)는 속성관계를 확인하는 PMI용 인증서
+ 속성 관리 기관에서 속성정보를 바타으로 발급
+ 속성인증서와 속성인증서의 공개키 인증서를 연결하여 권한을 검증
+ 속성인증서는 기존의 신원 확인을 위한 공개키 인증서의 확장 필드를 이용하거나, 신원 확인용과 별도의 속성인증서를 발급하여 관리하는 두 가지 구현 방안이 있음.

## 3. PMI의 구성요소

![PMI](/assets/images/posts/pmi.png)

[이미지 출처](http://article.nadiapub.com/IJCA/vol7_no5/26.pdf)

## 4. PMI 세부 내용

|구성요소|세부 내용|
|:---|:---|
|SOA (Source of Authority)|PKI의 루트 CA와 유사한 역할, 권한 검증자가 무조건 신뢰함|
|AA (Attribute Authority)|SOA로부터 권한의 전부 또는 일부를 위임받아 인증서 발급 업무 수행|
|권한 소유자(Privilege Holder)|인증서를 통해 AA로부터 권한에 대한 소유권을 보증 받은 자(PKI의 End-Entity에 해당)|
|권한 검증자(Privilege Verifier)|속성 인증서로 사용자의 권한을 검증함|

# 전자서명 응용 프로그램
- 전자투표의 개념(Electronic Voting)
    + 투표소에 전자투표기를 설치하여 투표를 하거나 컴퓨터를 사용해서 투표하는 방식
    + e-투표라고도 하며 인터넷을 사용해서 투표를 할 수 있다.
- 전자투표 시스템 구성요소
    + 전자 투표기 : 사용자를 인증한 뒤 화면에 나오는 영상을 보고 투표 한다.
    + 개표 시스템 : 전자개표 프로그램을 사용해서 개표를 수행하는 컴퓨터 시스템
    + 전자검표 시스템 : 투표 기록기를 읽어서 판독을 수행한다.
- 전자투표 시스템의 요구사항
    + 투표소 전자투표(Poll Site E-Voting) : 유권자가 투표소를 방문해서 직접 투표
        * PSEV 방식 : 투표소에 설치된터치스크린을사용해서 투표
    + 인터넷 투표(Remote Internet E-Voting) : 투표소에 방문하지 않고 인터넷을 사용해서 투표
        * RESV 방식 : 컴퓨터를 사용하거나 SMS 문자, 디지털 TV 등을 사용하여 투표
            - 편의성 증대, 유권자의 참여가 증가
            - 비밀투표가 침해될 가능성, 보안에 대한 위험성이 높아짐
