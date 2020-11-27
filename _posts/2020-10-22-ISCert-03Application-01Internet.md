---
title: 03.애플리케이션 보안 - 01.인터넷 응용 보안
date: '2020-10-28 03:00:00'
tags:
- 정보보안기사
- 애플리케이션 보안
- 인터넷 응용 보안
related: true
categories:
- IS_Certification
toc: true
---

# **FTP**
## 1. FTP 개요
### 1. FTP (File Transfer Protocol)
* 인터넷에 연결된 시스템 간 파일을 전송하는 통신 프로토콜
* TCP 프로토콜 사용

### 2. FTP 특징
* 명령 채널과 데이터 전송 채널이 독립적으로 동작
    - 클라이언트가 명령 채널을 통해 서버에게 파일 전송을 요구
    - 서버는 데이터 전송 채널을 통해 데이터를 전송
* 서버의 명령 채널은 21번 포트를 사용
* 데이터 포트는 전송 모드에 따라 포트 변경
    - Active Mode : 포트 20번
    - Passive Mode : FTP 서버가 자신의 데이터 포트를 결정
* FTP 서비스는 보안에 취약(Sniffing) -> sFTP 사용

![FTP 로그인 과정](/assets/images/posts/ftp_3way.png)

[이미지 출처](https://www.programmersought.com/article/28932461830/)

* **FTP 로그인 과정**
    - 3Way Handshaking
    - Recv : 220
    - Send : 사용자 ID
    - Recv : 331
    - Send : Password
    - Recv : 230 (로그인 성공)

### 3. FTP 종류

|종류|내용|
|:---|:---|
|FTP|ID 및 Password 인증을 수행하고 **TCP 프로토콜**을 사용하여 사용자의 데이터를 송수신|
|tFTP|**인증 과정 없이 UDP 기반**으로 데이터를 빠르게 송수신 함. **69번 포트** 사용|
|sFTP|**전송 구간에 암호화 기법을 사용**하여 기밀성을 제공|

### 4. FTP 접근 통제 파일

|파일명|내용|
|:---|:---|
|`/etc/ftpusers`|파일에 적용된 **사용자에 대한 FTP 접근 제한**|
|`/etc/hosts.deny`|**특정 IP의 접근 제한**|
|`/etc/hosts.allow`|**특정 IP의 접근 허용**|

### 5. FTP Active Mode 와 Passive Mode
![FTP Active](/assets/images/posts/ftp_active.png)

[이미지 출처_FTP Active](https://infosys.beckhoff.com/english.php?content=../content/1033/cx8190_hw/27021604587584011.html&id=)

![FTP Passive](/assets/images/posts/ftp_passive.png)

[이미지 출처_FTP Passive_](https://infosys.beckhoff.com/english.php?content=../content/1033/cx8190_hw/27021604587584011.html&id=)

|전송방식|내용|
|:---|:---|
|**Active Mode**|⦁ Client : Server 21번 포트로 접속, 데이터채널 포트 제공 / Server : 응답<br/>⦁ **Server : Client 제공 포트로 데이터채널 연결 요청** / Client : 응답|
|**Passive Mode**|⦁ **Client** : Server 21번 포트로 접속, **PASV 모드 요청** / **Server** : 응답, **데이터채널 포트 (1024~65535 범위의 Random 포트) 제공**<br/>⦁ **Client : Server 제공 포트로 데이터채널 연결 요청** / Server : 응답|

### 6. FTP 서비스 로그 기록
* **FTP 서비스 기동 시에 `-l` 옵션을 부여해서 실행하면 xferlog 파일을 기록**
* `Sat Oct 29 21:35:39 2016 1 1.226.137.47 70 /home/devtest/aa.html a _ i r devtest ftp 0 * c`
    - 70 : 파일전송 크기
    - i : 파일 업로드
    - c : 성공 (i=실패)
* `Sat Oct 29 22:58:38 2016 1 110.15.241.24 70 /home/devtest/aa.html b _ o r devtest ftp 0 * c`
    - o : 파일 다운로드
* `Thu Apr 8 15:40:32 2016 1 201.1.1.10 254 /usr/kisa.z b_o r test ftp 0 * c`
    - Thu Apr 8 15:40:32 2016 1 : 접근 날짜 및 시간
    - 201.1.1.10 : 접속 IP
    - 254 : 전송 파일 Size
    - /usr/kisa.z : 전송 파일
    - b(Binary), a(ASCII) : 파일 종류
    - _(아무일도 수행하지 않음) : 행위
    - o(파일을 받았음) : 파일 동작
    - r(인증된 사용자) : 사용자 접근 방식
    - test : 로그인 ID
    - 0(없음) : 인증 방법
    - c(전송성공) : 전송 상태

## 2. FTP 보안
### 1. FTP 보안 취약점

|취약점|내용|
|:---|:---|
|**Bounce Attack**|⦁ 익명 FTP 서버를 경유하여 호스트를 스캔<br/>⦁ **네트워크 포트 스캐닝을 위해서 사용**<br/>⦁ **FTP 바운스 공격을 통해서 전자메일을 보내는 공격을 Fake Mail**이라고 함|
|**tFTP Attack**|⦁ 인증절차를 요구하지 않기 때문에 설정이 잘못되어 있으면 누구나 해당 호스트에 접근하여 파일을 다운로드 할 수 있음<br/>⦁ FTP보다 간단함|
|**Anonymous FTP Attack**|⦁ 보안 절차를 거치지 않은 익명의 사용자에게 FTP 서버 접근 허용<br/>⦁ 익명 사용자가 서버에 쓰기 권한이 있을 떄 악성코드 생성이 가능|
|**FTP 서버 취약점**|wuftp 포맷 스트링 취약점 및 각종 버퍼 오버플로우 공격|
|**스니핑**|ID 및 Password 입력 후 접속 시도 시 암호화가 이루어지지 않음. 네트워크 스니핑에 취약|
|**Brute Force Attack**|무작위 대입법을 사용|

### 2. FTP 보안대책
* **Anonymous**
    - 사용자의 Root 디렉터리, bin, etc, pub 디렉터리 소유자의 권한을 관리
    - `$root/etc/passwd` 파일에서 anonymous ftp에 불필요한 항목을 제거
* **tFTP**
    - tFTP가 필요한 경우 secure mode로 운영
* **inetd FTP 서비스(inetd.conf 설정파일)**
    - `/etc/passwd` 파일에 FTP 사용자를 `/sbin/nologin` 으로 설정

# E-Mail 보안
## 1. E-Mail 전송 방법
### 1. SMTP(Simple Mail Transfer Protocol)
* 인터넷에서 전자우편을 보낼 때 사용되는 표준 통신 규약
* **E-Mail을 보내기 위해서 TCP 25번 포트를 사용**
* 상대 서버를 지시하기 위해 DNS의 MX 레코드가 사용됨
* OSI 7 계층의 응용 계층(Application Layer)에서 동작

### 2. POP3(Post Office Protocol Version 3)
* 응용 계층 프로토콜
* 원격 서버로부터 TCP/IP 연결을 통해 E-Mail을 읽어오는 데 사용
* **E-Mail을 읽은 후 원격 서버의 E-Mail을 삭제**
* **TCP 110번으로 메일 서버에 접속하여 저장된 메일을 내려받는 MDA 프로그램**
* POP3 특징
    - 항상 연결 상태가 아닌 네트워크 접근에 유리
    - 사용자는 메일의 Local 복사본을 검색하고 오프라인으로 읽을 수 있음
    - 서버에 읽지 않은 메일만 보관, 메일을 읽은 후 삭제
    - 메일을 서버에 저장하는 방법이 없음

### 3. IMAP 및 IMAP4
* **POP과 달리 메일을 내려 받더라도 메일 박스에 원본은 계속 저장됨**
* **TCP 143**
* IMAP 및 IMAP4의 특징
    - 온라인 및 오프라인에서도 유효
    - 오프라인 작업 시 MUA 측에서 트랜잭션을 저장하고 서버에 연결되면 Commit한 것으로 반영한다.
    - 메일은 메일 서버에 항상 저장되고 Local 복사본을 검색한 시점에 서버에서 제거할 수도 있음
    - 메일상태를 중앙에서 관리할 수 있고 여러 단말을 통해서 동일한 E-Mail을 읽을 수 있음
    - 메일 소프트웨어와 관계없이 메일을 관리할 수 있음
    - 메일의 헤더만 검색해서 텍스트 부분만 가져오기 등의 작업이 가능
    - IMAP 4는 프로토콜의 복잡성과 보안문제를 개선

### 4. SMTP 메일 처리 방식
![Schema of E-mail Delivery](/assets/images/posts/Schema_of_e-mail_delivery.svg.png)

[이미지 출처](https://en.wikipedia.org/wiki/Email_agent_(infrastructure))

* **MTA(Mail Transfer Agent)** : 메일을 전송하는 서버
* **MDA(Mail Delivery Agent)** : 수신측에 고용된 우체부 역할, MTA에게 받은 메일을 사용자에게 전달
* **MUA(Mail User Agent)** : 사용자들이 사용하는 클라이언트 애플리케이션

## 2. E-Mail 보안 기법
### 1. PGP(Pretty Good Privacy)
+ 개요
    * **MIME(Multipurpose Internet Mail Extension, RFC 1521) 객체에 암호화와 전자서명 기능을 추가한 암호화 프로토콜**
    * 네트워크를 통해 주고받는 메시지에 대해 송수신자에게 보안서비스를 제공
    * 평문 메시지 암호화
    * 서명, 압축, 분할, 전자 우편 호환성
    * 수신 부인 방지와 메시지 부인 방지는 제공하지 않는다.
    * **분산형 키관리**
+ PGP 특징

    |PGP 서비스|상세 내용|
    |:---|:---|
    |전자서명|**DSS/SHA 또는 RSA/SHA로 전자서명이 가능**|
    |메시지 암호화|**CAST-128, IDEA, 3DES로 메시지 암호화**|
    |1회용 세션키 생성|**Diffie-Hellman 혹은 RSA로 키 분배**|
    |이메일 호환|RADIX-64로 바이너리를 ACS Code로 변환|
    |세그먼테이션|메시지 최대 사이즈를 제한|

### 2. PEM(Privacy Enhanced Mail)
* 프라이버시 향상 이메일
* **중앙집중화된 키 인증 방식**
* 구현이 어렵고 높은 보안성 제공 (군사, 은행)
* **DES CBC MD5**

### 3. S/MIME(Secure Multi-Purpose Internet Mail Extensions)
+ 개요
    * **표준 보안 메일 규약, 송신자와 수신자를 인증, 메시지 무결성 증명, 첨부파일 포함**
    * 메시지 내용의 Privacy를 보증하는 표준 보안 메일 프로토콜, 메일 전체를 암호화
    * 인터넷 MIME 메시지에 전자 서명과 함께 암호화를 더한 프로토콜로서 **RSA 암호 사용**
    * CA(인증기관)로부터 자신의 공개키를 보증하는 인증서를 받아야 함
    * 첨부 파일을 보안
    * **서명 기능**
+ S/MIME에 사용되는 암호화 키
    * DSS : 디지털 서명 알고리즘
    * 3중 DES : 메시지의 암호
    * SHA-1 : 디지털 서명을 지원하기 위한 해시함수

# Sendmail 보안
## 1. Sendmail
+ 대표적인 Mail Transport Agent(MTA)   

## 2. Sendmail 운영 모드
+ **`/usr/sbin/sendmail -<Option>`**
    * `-bd` : 데몬 모드로 실행 
    * `-bi` : 앨리어스 데이터베이스 파일을 초기화
    * `-bm` : Be a mail sender(MDA만의 기능으로 작동)
    * `-bp` : 큐에 있는 내용을 프린터
    * `-bs` : 표준 입력으로 SMTP를 실행
    * `-bt` : 테스트 모드, rewrite를 테스트하기 위해 이용
    * `-bv` : 확인 모드
+ 웜 및 바이러스 헤더를 입력한 후 결과로 필터링 동작 여부를 확인
    * `/etc/mail/sendmail.cf` : 룰셋
    * `# /usr/lib/sendmail -bt` : 테스트 모드 실행
    * `> check_nv boundary="====_ABC1234567890DEF_====` : NIMDA worm test

## 3. Sendmail 접근 파일(SPAM Mail 차단)
+ **`/etc/mail/access` 작성 규칙**
    * OK : **다른 rule이 거부하여도 들어오는 메일을 받아들임**
    * RELAY : **relay를 허용**, 다른 rule이 거부하는 경우도 지정된 특정 도메인이 있는 사용자에게 오는 메일을 받음
    * REJECT : **수신 및 발신을 완전히 거부**
    * DISCARD : 메일을 받기만 하고 **메시지를 완전히 폐기**
    * 501 : 지정된 e-mail 또는 도메인에 대해 보내는 사람의 주소가 전체 또는 부분적으로 일치할 경우 메일 거부
    * 550 : 특정 도메인에 관련된 메일을 거부
+ `/etc/mail/access` 설정
    * `spam@abc.com    REJECT` : 발송되는 메일의 수신 및 발신을 모두 거부 
    * `spammail.com    REJECT` : 발송되는 메일의 수신 및 발신을 모두 거부
    * `attacker.com    DISCARD` : 수신 및 발신 모두를 폐기 (발신자에게 폐기통보 X)
    * `useful.org      OK` : 메일을 수신
    * `201.1.1         RELAY` : 201.1.1.0/24에서 발송되는 모든 메일의 수신 및 발신을 허용 (중계역할)
    * `211.1.1         RELAY` : 2101.1.1.0/24에서 발송되는 모든 메일의 수신 및 발신을 허용 (중계역할)

# 스팸메일(Spam Mail) 차단 방법
## 1. Spam Mail 차단 방법
+ Spam Mail 차단 기법의 종류

![RBL](/assets/images/posts/RBL.gif)

[이미지 출처](https://spam.kisa.or.kr/rbl/sub2_R.do?idx=10&currentPage=1)


![SPF](/assets/images/posts/spf.png)

[이미지 출처](https://www.cyber.gov.au/acsc/view-all-content/publications/how-combat-fake-emails)


|**RBL(Real Time Blocking)**|**SPF(Sender Policy Framework)**|
|:---|:---|
|⦁ 스팸메일에 사용되는 IP 리스트를 등록하고 차단<br/>⦁ 국내 : KISA의 RBL 서버에 특정 IP를 등록하고 차단함|⦁ 발신자 : 자신의 메일 서버 정보와 정책을 나타내는 SPF 레코드를 해당 DNS에 등록함<br/>⦁ 수신자 : 메일 수신 시 발송자의 DNS에 등록된 SPF 레코드를 확인하여 해당 메일에 표시된 발송 IP와 비교하고 그 결과에 따라 수신 여부를 결정함|

## 2. SpamAssassin
+ 정의 : 점수를 이용하여 스팸 여부를 판단, Perl로 개발됨
+ 특징 : Rule 기반 하에 Header와 Body를 분석하여 실시간 차단 리스트 (Internet-based real time blacklist(RBL)) 를 참고하여 각각의 룰에 매칭될 경우 **점수를 매겨서 총 점수가 기준점을 초과하는지 여부에 따라 스팸 여부를 결정**
+ 첨부파일 검사 X

## 3. 메일 보안 기술(도구)의 종류
+ **RBL(Real time Black List)**
    * SPAM 메일 방지를 위해서 IP Black List 관리
    * 한국에서는 KISA 에 IP 등록
+ **SPF(Send Policy Framework)**
    * 허용된 도메인 혹은 IP 등에서 발송 여부를 확인
    * DNS를 설정하여 SPAM 메일을 방지함
    * **DNS 에 발신 IP 등록**
+ **Spamassassin**
    * Rule을 기반으로 하여 메일 헤더 및 내용을 분석
    * RBL 참조
    * Rule 매칭되고 **총점수가 임계치를 넘으면 Spam 메일로 결정**
+ **Inflex**
    * 내부 혹은 외부로 발송되는 메일을 검사하고 **첨부파일을 필터링**
    * 내용스캔, 메일 In 혹은 Out 정책, 첨부파일 필터링
+ **Sanitizer**
    * 확장자를 사용한 필터링
    * MS Office 매크로 검사
    * 악성파일 Score
    * 감염된 메시지 보관장소 설정
+ **Procmail**
    * 메일크기, 내용, 보낸 사람 등으로 필터링을 지원 

# **웹 서버 보안(Web Server Security)**
## 1. 웹(World Wide Web) 개요
### 1. WWW(World Wide Web)
* 메뉴 방식으로 서비스하던 기존의 인터넷 서비스를 Tim Berners-Lee 의 제안으로 하이퍼텍스트를 기반으로 웹문서를 활용하여 편의성을 향상시킨 서비스

### 2. WWW 특징

|특징|내용|
|:---|:---|
|일관된 사용자 인터페이스|웹은 인터넷상에서 제공되는 많은 서비스의 통합된 접속 도구 역할을 하여 기존 프로토콜과 서비스를 제공함|
|하이퍼텍스트(Hypertext)|다른 문서를 지정하는 포인터(Pointer)가 존재하여 정보와 연결된 다른 정보에 쉽게 접근할 수 있음|
|능동적 참여|방대한 데이터 창고 역할뿐만 아니라 참여자들 사이에 다양한 정보를 공유|
|분산된 저장소|서비스를 제공하는 서버에 모든 데이터가 집중되지 않고 웹의 각종 정보가 분산 저장됨|
|개방형 표준|W3C 표준 프로토콜인 HTTP의 개방형 프로토콜을 지원하고 웹 페이지에 대한 웹 표준을 준수하여 하드웨어 및 소프트웨어 플랫폼에 독립적인 서비스를 지원함|

### 3. Apache 웹 서버
* 웹서버의 역할
    - 웹브라우저와의 세션(연결)을 관리
    - 웹브라우저에서 전송되는 HTTP Request 메시지를 해석
    - 필요한 프로세스 실행 후 HTML 형태로 HTTP Response 를 전송
* httpd 라는 데몬(Daemon) 프로세스가 실행되어서 기동
    - httpd는 root 소유자의 권한으로 기동 (80번 포트 사용)
    - 부모 프로세스 하나만 root 사용자로 실행
    - 웹브라우저들이 접속을 하면 httpd 프로세스를 실행해서 요청처리
    - 웹브라우저의 연결을 처리하는 프로세스는 apache 사용자로 기동
        + fork 를 사용한 자식 프로세스 생성
* 설정파일 : `/etc/httpd/conf/httpd.conf`
    - DocumentRoot : 홈 사이트 디렉터리

## 2. 웹 서버(Web Server) 보안
### 1. Apache 웹 서버 보안 설정

|보안 설정|내용|
|:---|:---|
|**주요 디렉터리 및 파일 접근 권한**|Root에 의해 실행 가능한 모든 명령어는 다른 사용자가 수정하지 못하도록 설정⦁ `# chown 0. bin conf logs`<br/>⦁ `# chgrp 0. bin conf logs`<br/>⦁ `#chmod 755 . bin conf logs`<br/>⦁ `#chmod 511 /user/loca/httpd/bin/httpd`|
|**불필요한 파일 삭제**|⦁ 아파치 설치 시 기본적으로 설치되는 cgi-bin은 공격에 이용될 수 있으므로 삭제<br/>⦁ 매뉴얼 파일은 시스템에 대한 정보를 제공할 수 있어서 공격에 도움이 될 수 있으므로 삭제<br/>⦁ /var/www/manual 및 /var/www/cgi_bin 삭제|
|**Directory Listing**|index.html이 없거나 Listing을 보여주는 옵션이 indexes에 설정되어 있는 경우 웹 페이지의 디렉터리가 보임|
|**FollowSynLinks**|심볼릭 링크를 이용해서 파일 시스템에 접근하여 Root 권한을 획득할 수 있으므로 FollowSymLink를 제거|
|**Directory indexes**|⦁ 우선순위를 결정함<br/>⦁ index.cgi>index.html>index.htm의 순서<br/>⦁ # DirectoryIndex: sets the file that Apache will serve iof a directory is requested.<br/>⦁ <IfModule dir-module>DirectoryIndex index.html</IfModule>|
|**Server Tokens**|웹 서버에 접속할 경우 최소한의 정보만 보이도록 설정|
|**ServerSignature**|ServerSignature on:on 으로 설정된 경우 아파치 버전 및 서버 이름이 노출됨|
|**접근 제어**|클라이언트의 이름 및 IP 주소 등을 사용해 접근 제어 수행<br/>\<Directory /\><br/>    Options FollowSynLinks<br/>    AllowOverride None<br/>    Order deny,allow<br/>    Deny from all<br/>\</Directory\>|
|put 과 post의 제한||
|CGI 스크립트 제한|AddHandler cgi-script.cgi 주석처리|

### 2. Apache 웹 서버 Session 관리

|보안 설정|내용|
|:---|:---|
|Timeout|⦁ 웹브라우저가 웹 페이지에 접근한 뒤, 클라이언트의 요청에 서버가 대기하는 시간을 설정<br/>⦁ 기본값은 300초|
|MaxKeepAliveRequests|⦁ 하나의 지속적인 연결에서 서비스를 제공할 요청의 최대 값을 설정<br/>⦁ 0일 경우 무제한 기본값은 100|
|KeepAliveTimeout|설정된 시간 동안 서버는 한 번의 요청을 받고 접속을 끊지 않고 유지한 상태에서 다음의 요청을 받아들임|
|KeepAlive|⦁ 접속 연결에 대한 재요청을 허용할 것인지 설정<br/>⦁ 기본값은 off이며 on으로 설정되면 MaxKeepAliveRequests 값과 연계됨|

### 3. Apache 웹 서버 httpd.conf 파일 설정
+ **예시**
    ```
    <Directory "D:/document">
        Options Indexes FollowSynLinks
        AllowOverride All
        Order Deny, Allow
        Deny from all
        Allow from 127.0.0.1
    </Directory>
    ```

    * Order Deny, Allow
        - 먼저 Deny, 나중에 Allow
    * Deny from all
        - 위의 설정은 모든 트래픽을 막는다.
    * Allow from 127.0.0.1
        - 1번의 order deny, allow 및 2번의 Deny from all 조건에 따라 127.0.0.1 만 접속을 허용한다.
        - 127.0.0.1 은 localhost 이기 때문에 자신의 컴퓨터를 제외한 다른 곳에서는 접속이 불가능한 상태로 설정한다.
+ httpd.conf 특정 IP 주소만 차단
    ```
    <Directory "D:/document">
        Options Indexes FollowSynLinks
        AllowOverride All
        Order Allow, Deny
        Deny from all
        Allow from 210.1.1.1
    </Directory>
    ```
+ `ServerTokens [OS|Prod]`
    * OS : HTTP Response 응답 시 Header의 Server 필드에 웹 서버 버전과 운영체제 정보를 제공
    * Prod : HTTP Response 응답 시 Header의 Server 필드에 웹 서버 종류만 제공

### 4. 웹 서버 IIS 구성
* 윈도우에서 제공하는 웹 서버
* 웹 서버 및 FTP, SMTP, NNTP 등의 다양한 서비스 제공

    |IIS 구성요소|내용|
    |:---|:---|
    |서비스|⦁ 웹과 FTP 관리를 위한 IIS 관리 서비스<br/>⦁ World Wide Web 서버 서비스<br/>⦁ FTP 서비스<br/>⦁ 메일 발송을 위한 SMTP(Simple Mail Transport Protocol)<br/>⦁ 뉴스그룹을 위한 NNTP(Network News Transport Protocol)|
    |계정 및 그룹|⦁ IUSR_MACHINE(인터넷으로 접근하는 익명 계정)<br/>⦁ IWAM_MACHINE(out-of-process로 실행되는 웹 애플리케이션이 실행되는 계정)|
    |폴더|⦁ %windir%\system32\inetsrv(IIS 프로그램)<br/>⦁ %windir%\system32\inetsrv\iisadmin(IIS 관리 프로그램)<br/>⦁ %windir%\help\iishelp(IIS 도움말 파일)<br/>⦁ %systemdrive%\inetpub(웹,FTP,SMTP 루트 폴더)|
    |웹 사이트|⦁ 기본 웹 사이트(80번 포트) : %systemdrive%\inetpub\wwwroot<br/>⦁ 관리 웹 사이트(3693번 포트) : %systemdrive%\system32\inetsrv\iisadmin|

# 웹 로그(Web Log) 분석
## 1. 웹 로그(Web Log) 개요
### 1. 웹 로그(Web Log)
* 웹서버의 에러로그, 접속로그, 에이전트로그, 참조로그
* `lsof -p [PID]` : 사용하고 있는 자원 확인 (예: 프로세스가 Open 하고 있는 파일)
* httpd.conf 파일에 로그파일의 위치를 지정
* access.log 구조
    - SQL Injection 공격 : sqlmap, select, union 등의 SQL 구문 기록
    - XSS 공격 : `<script>`, `<iframe>`, `<a>` 등과 같은 스크립트문 기록
    - 업로드 파일 중 확장자가 `*.php`, `*.jsp`, `*.asp` 등이 있는지 확인
    - `203.251.189.47 - - [03/Aug/2000:21:56:55 +0900] "GET /doc/images/index.gif HTTP/`

    |구조|내용|
    |:---|:---|
    |Host|클라이언트의 호스트명 혹은 IP 주소|
    |dent|⦁ IdentityCheck 가 enable되고 있고, 클라이언트가 ident에 응답을 보내면 identity 정보를 기록<br/>⦁ 보통은 "-"로 대체|
    |Authuser|⦁ 인증이 있는 경우 사용자명이 기록됨<br/>⦁ 보통은 "-"로 대체|
    |Date|접속한 날짜와 시간|
    |Request|클라이언트가 요청한 자료|
    |Status|요청한 것에 대한 상태 코드|
    |Bytes|Header를 제외한 전송된 바이트|

### 2. ErrorLog
+ ErrorLog 지시어
    * `ErrorLog /var/log/httpd/error_log` : 에러로그 파일 기록
    * `ErrorLog syslog` : 에러 발생 시 로그를 syslog에 기록
+ **ErrorLog Level**

    |Error Level|내용|
    |:---|:---|
    |Emerg|에러를 의미함|
    |Alert|불안정한 시스템 상황|
    |Crit|중대한 에러 발생|
    |Error|비교적 중대하지 않은 에러|
    |Warn|경고 발생|
    |Notice|일반적인 메시지 발생|
    |Info|정보 제공|
    |Debug|디버깅 정보|

+ Error Level별 설정
    * 기본값 local7 퍼실리티를 통해 에러 기록
    * `Local7.warn /var/log/httpd.warn.log` : , `syslog.conf` 에서 제어
    * `Local7.debug.!=notice /var/log/httpd.error_log` : 디버그보다 높고 notice 이하에 대해 로그 기록 
+ 웹 서버 접속로그 access log
    * `TransferLog /var/log/access.log` : TransferLog 지시어를 사용, 로그위치 지정
    * IP, 요청일, 시간, HTTP 메소드, HTTP 상태 코드, 전송 바이트, 시간 기록
+ 로그파일 포맷 지정
+ Access LogFormat 변경
    * `LogFormat "%h %l %u %t \"%r\" %>s %b`
+ CustomLog 지시자
    * TransferLog 및 LogFormat 지시자를 하나로 합쳐 놓은 지시자
    * `CustomLog logfile format|nickname env=[!]environment-variable`
    * `CustomLog /logs/referrer_log "%{Referer}i -> %U`
    * **Common Log Format의 의미**

        |구분|세부 내용|
        |:---|:---|
        |`%h`|원격지 호스트. 즉, 접속한 클라이언트 IP 주소를 의미|
        |`%I`|원격지 사용자 ID|
        |`%u`|인증이 요청된 원격 사용자 이름|
        |`%t`|요청한 시간과 날짜|
        |`%r`|HTTP 메소드를 포함한 요청의 첫 라인|
        |`%s`|HTTP 상태코드|
        |`%b`|HTTP 헤더를 제외하고 전송된 바이트|
        |`%{Referer}i`|요청된 URL이 참조되거나 링크된 URL|
        |`%{User-Agent}i`|접속한 클라이언트의 운영체제 및 브라우저 버전|
        |`%T`|요청을 처리하는 데 걸린 시간(초)|

## 2. HTTP 상태 코드(Status Code)
+ **HTTP 상태코드**
    * 100 Continue
    * 101 Switching Protocols
    * 200 OK
    * 201 Created
    * 202 Accepted
    * 203 Non-Authoritative Information
    * 204 No Content
    * 205 Reset Content
    * 206 Partial Content
    * 300 Multiple Choices
    * 301 Moved Permanently
    * 302 Moved Temporarily
    * 303 See Other
    * 304 Not Modified
    * 305 Use Proxy
    * 400 Bad Request
    * 401 Unauthorized
    * 402 Payment Required
    * 403 Forbidden
    * 404 Not Found
    * 405 Method Not Allowed
    * 406 Not Acceptable
    * 407 Proxy Authentication Require
    * 408 Request Time-out
    * 409 Conflict
    * 410 Gone 
    * 411 Length Required
    * 412 Precondition Failed
    * 413 Request Entity Too Large
    * 414 Request-URI Too Large
    * 415 Unsupported Media Type
    * 500 Internal Server Error
    * 501 Not Implemented
    * 502 Bad Gateway
    * 503 Service Unavailable
    * 504 Gateway Time-out
    * 505 HTTP Version not supported

# 웹 방화벽(Web Firewall)
## 1. 웹 방화벽(Web Firewall) 개요
### 1. 웹 방화벽(Web Firewall)
* 홈페이지 자체 및 웹 프로토콜을 기반으로 하는 모든 서비스와 애플리케이션 데이터 기반 정보 시스템에 보안서비스를 제공
* 웹 애플리케이션 취약점을 이용한 공격에 대응
* 웹 서버 해킹을 경유하여 기업 내부의 중요 데이터 유출을 방지

### 2. 구간별 웹 취약점
        
|구간|Client|Internet|Web Server<-->Was|Was<-->DB|
|:---:|:---|:---|:---|:---|
|취약점|⦁ XSS<br/>⦁ ActiveX|⦁ Sniffing|⦁ Web Server S/W 취약성<br/>⦁ 입력 값 검증 부재<br/>⦁ 사이트 구조<br/>⦁ Application 로직 공격|⦁ 질의 조작<br/>⦁ 질의 조작을 통한 System Call|


### 3. 웹 방화벽 종류
        
|종류|내용|
|:---|:---|
|네트워크 기반 방화벽|⦁ 방화벽이나 침입방지 시스템과 유사하게 네트워크 구간에 인라인 Transparent 및 Proxy 방식으로 구성<br/>⦁ 웹 트래픽에 대한 분석 및 차단 기능|
|호스트 기반 방화벽|웹 서버에 설치된 보안 에이전트와 마스터 서버, 관리자용 콘솔로 구성되며 웹 서버의 에이전트가 해킹 시도 및 이상 징후를 탐지하고 보안 정책을 실행|
|Proxy Method|⦁ 웹 서버 앞 단에서 클라이언트 요청을 받아 필터링 처리 후 웹 서버와 재접속 하는 방식<br/>⦁ 소프트웨어 기반 및 하드웨어 기반 방화벽 지원|
|Filtering Module Method|⦁ 웹 방화벽이 웹 서버의 플러그인 모듈처럼 동작하는 방식<br/>⦁ 웹 서버가 처리 대기상태에서 보안 모듈에 필터링 처리된 후 정상 트래픽만 클라이언트에 응답하는 방식|

### 4. 웹 방화벽 주요 기능

|주요 기능|내용|
|:---|:---|
|웹 공격 방지|논리적 공격, Client Side 공격, 명령어 실행, 정보 유출 등의 공격에 보안 및 차단|
|Positive Security Model 지원|강제 접근 및 알려지지 않은 공격을 차단|
|SSL 지원|SSL 트래픽에 복호화를 지원|
|애플리케이션 구조 변화에 대응|서비스 지속성을 보장|

### 5. 워터링 홀(Watering Hole) 공격

![Watering Hole](/assets/images/posts/water-hole.jpg)

[이미지 출처](https://www.profwoodward.org/2013/04/beware-watering-hole.html)

* 사막의 오아시스(특정 사이트) 에 다가오는 초식동물(Victim) 을 노리는 육식동물 (Hacker)
* **워터링 홀 공격(특정 타겟)은 홈페이지에 접속하는 특정 사회적 그룹을 대상으로 악성코드를 유포하여 특정 사회적 그룹에 추가적인 APT 공격 등을 목적으로 한다.**
    - 예) 외교 관련 사이트에 악성코드를 심고 외교부 관계자가 접속하면 해당 PC에 악성코드를 설치, 그 PC에서 정보를 빼냄

# DNS 보안
## 1. DNS(Domain Name Service) Query  
### 1. DNS (Domain Name Service)
* 인터넷 네트워크상에서 컴퓨터의 이름을 IP 주소로 변환하거나 해석하는 데 사용되는 분산 네이밍 시스템
* URL 주소에 대한 IP 주소를 알려주는 서비스
* **`nslookup`** : DNS 확인 도구
    - `nslookup www.naver.com`
* DNS 질의 우선순위
    - Windows
        + DNS cache table 확인 (`ipconfig /displaydns`)
        + `C:\windows\system32\drivers\etc\hosts.ics` 파일 내용 확인
        + `C:\windows\system32\drivers\etc\hosts` 파일 내용 확인
        + DNS 서버에게 질의
        + `ipconfig /flushdns` : DNS 캐시 테이블을 모두 삭제
    - Linux
        + nscd(Name Service Cache Daemon) 확인 (별도 데몬 설치필요)
        + `/etc/hosts.conf` 파일 내용 확인
        + DNS 서버에게 질의

### 2. DNS 구조

![DNS Schema](/assets/images/posts/DNS_schema.svg.png)

[이미지 출처](https://en.wikipedia.org/wiki/Domain_name)

* Root Domain(.) : 모든 도메인의 근본이 되는 최상 Root level Domain
* Top Level Domain : com, org, kr 등의 국가, 지역을 나타냄
* Second Level : 사용자가 도메인 명을 신청해서 등록할 수 있는 영역

### 3. DNS 서비스 방식

![DNS Query](/assets/images/posts/dns_query.gif)

[이미지 출처](https://www.internetsociety.org/resources/deploy360/dns-privacy/intro/)

* DNS Query 종류
    - **Recursive Query(순환)** : Local DNS 서버에 Query를 보내 완성된 답을 요청
    - **Iterative Query(반복)** : Local DNS 서버가 다른 DNS 서버에게 Query를 보내어 답을 요청, 외부 도메인에서 개별적인 작업을 통해 정보를 얻어와 종합해서 알려줌
* DNS Query 세부 방식
    - 클라이언트가 UDP로 DNS Request를 DNS Server에 전송
    - DNS 서버는 DNS Request에 대해서 DNS Response로 응답, 요청한 IP 주소를 넣어서 보내줌
* **DNS 증폭공격**
    - 출발지 IP를 Victim의 IP (DNS Server) 로 변조함
    - Open DNS Resolver 서버를 이용
    - DNS Query의 Type을 "ANY"로 설정
        + 다양한 TYPE인 A, NS, CNAME, AAAA 등의 모든 레코드를 요청하기 때문에 요청한 쿼리 패킷보다 크게 증폭됨
    - DoS 공격으로, DNS Reflector Attack 이라고도 함
* **DNS 레코드**

    |종류|내용|
    |:---|:---|
    |**A(Address)**|호스트 이름을 IPv4 주소로 매핑|
    |**AAAA(IPv6 Address**)|호스트 이름을 IPv6 주소로 매핑|
    |**PTR**<br/>**(Reverse-lookup Pointer records)**|IP 주소를 기반으로 도메인 이름을 찾는 데 사용|
    |**NS(Name Server)**|주어진 호스트에 대한 공식적인 Name Server를 알려줌|
    |**MX(Mail Exchanger)**|DNS 도메인 이름에 대한 메일 교환 서버를 알려줌. 이 정보는 SMTP 가 전자 메일을 적절한 호스트로 라우팅하는데 사용.|
    |**CNAME(Canonical Name)**|호스트의 다른 이름(별칭)을 정의하는 데 사용, A레코드의 별명을 달아 주는 것|
    |SOA(Start of Authority)|도메인에 대한 권한을 갖는 서버를 표시함. 도메인에서 가장큰 권한을 부여받은 호스트를 선언 (기본 Name Server, 도메인 관리자의 전자메일, 도메인 일련번호 등 DNS 핵심정보)|
    |Any(ALL)|위의 모든 레코드를 표시|

## 2. DNS 보안
### 1. dnsspoof
* DNS Spoofing 을 할 수 있는 공격 도구
* 지정된 DNS 파일을 참조해서 DNS를 수행하게 함
* 53번 포트로 전송되는 UDP 데이터를 모니터링 하고 있다가 지정된 URL에 대해 요청이 오면 특정 IP로 응답함
    - UDP는 세션이 존재하지 않아서 먼저 도착한 패킷을 신뢰하며 다음에 도착한 정보는 버림.
* **한국인터넷진흥원 DNS 싱크홀**
    ![DNS Sinkhole](/assets/images/posts/kisa_dns_sinkhole.gif)

    [이미지 출처](https://www.krcert.or.kr/webprotect/dnsSinkhole.do)
    - 악성봇에 감염된 PC를 공격자가 조종하지 못하도록 **악성봇과 공격자의 명령을 차단**하는 서비스
    - 자체 DNS 서버를 운영하는 민간기관을 대상으로 제공하는 서비스

### 2. DNSSEC
+ DNSSEC
    * **DNS 캐시 포이즈닝과 DNS의 보안 취약점을 보완하기 위해서 등장**한 기술
    * DNS 응답 정보에 **전자서명 값을 첨부**하여 보내고 수신측이 해당 서명 값을 검증
    * **DNS 위변조 방지**, 정보 무결성 제공
+ DNSSEC 정보 제공
    * **공개키로 서명용 키 쌍을 생성하여 사전에 배포**
    * **개인키를 가지고 자신이 제공하려는 정보의 해시값을 서명 처리하여 전자서명 값을 생성**
    * **원본 데이터와 추가된 전자서명 값을 함께 인터넷상에 제공**
+ DNSSEC 정보 이용자
    * 정보제공자로부터 수신한 정보의 원본 데이터와 전자서명 값을 분리
    * 사전에 정보제공자로부터 수신한 공개키 값을 가지고 전자서명 값을 복호화
    * 원본 데이터를 해시 처리한 값과 복원된 전자서명 값을 비교하여 무결성 여부 확인

### 3. DNS Cache Poisoning Server Attack
* DNS 조회 방식
    - Client는 Local 네트워크인 DNS에 DNS Query를 보내면 Root DNS 로부터 시작해서 Query를 조회한 다음 사용자가 요청한 `www.abc.com` 의 정보를 받아서 쿼리에 대한 응답을 제공한다.
    - **Local DNS는 해당 정보를 Cache에 저장해 두고 비슷한 요청이 오면 다른 서버에 묻지 않고 바로 전송**한다.
* DNS Cache Poisoning Server 공격
    - **Local DNS Server로 DNS 쿼리 응답 패킷을 전송**한다.
    - DNS 유효 패킷은 출발지 포트번호, 목적지 포트번호, 트랜잭션 ID가 맞아야 한다.
    - 트랜잭션 ID 는 16Bit 필드이고 0부터 값을 유추해서 계속 넣으면 32768번의 공격으로 트랜잭션 ID를 맞출 수 있다
    - 이 때 Local DNS 서버에 잘못된 쿼리를 삽입한다.
