---
title: 정보보안 - snort
date: '2022-01-26 09:00:00'
tags:
- 정보보안기사
- snort
related: true
categories:
- IS_Certification
toc: true
---

# snort
- 개요
    + IDS (네트워크 이상징후 감시) 중 하나
        * 모든 트래픽을 감시하거나 샘플링을 통해 감시
        * 미리 설정값을 저장
        * DB 와 비교, 알려진 공격 시그니처와 비교
        * 공격자의 침입 경로 Block
        * 사용자의 패턴을 분석해서 이상징후 탐지
    + 마틴 로시가 1998 년에 sniffer and more 라는 표어 (네트워크 트래픽을 더 많이 감시) 로 개발
    + 프로토콜 검색, 컨텐츠 분석, 웜, 취약점 공격시도 탐지, 포트스캔, CGI
- 기능
    + snort 의 운영모드
        * Sniffer Mode
        * Packet Logger Mode
        * Network IDS/IPS Mode
    + 패킷 스니퍼
        * 패킷 스니핑을 통해 계속 화면에 출력 -> 작은 네트워크 규모에서 사용
    + 패킷 로거
        * 로그 남기기
    + 네트워크 IDS/IPS
        * 네트워크 트래픽 분석을 통한 공격 탐지/차단
- 설치
    + RHEL (CentOS) 기준
        * DAQ (Data Acquistion Package) 설치
        * Snort 패키지 설치
    + Talos(제작사) 에서 Ruleset(최근 공격 패턴을 탐지할 수 있는) 을 만들어 배포
- Snort 구조
    + sniffer : 패킷을 보기 좋게 Decode
    + preprocessor : 비정상 헤더구조 패킷, 트래픽 구분
    + Detection Engine : Rule 기반의 탐지 엔진, 사전 정의된 Rule 에 매칭 되는지 확인, Payload 확인
    + Alert / logging : Ruleset 에 일치하는 패킷에 대한 처리방법, SMB, SNMP, trap 등을 활용해 저장
    + 설정값 저장 위치 : `/etc/snort/snort.conf`
        * 모니터링하고자 하는 Network 설정
        * Rules 위치 수정
- Snort 명령어
    + Sniffer Mode
        + TCP/IP 패킷 헤더를 출력 : `snort -v`
        + TCP/ICMP/UDP 헤더와 IP를 출력 : `snort -vd` 
        + 패킷 데이터를 추가해서 표현하기 위한 명령어 : `snort -vde`
        + `-v` : snort 를 패킷 sniffing 모드로 변경
        + `-d` : 모든 네트워크 계층 포함
        + `-e` : 데이터링크 계층의 헤더를 포함
        + `-c` : 패킷 검출
- Snort Rule / Signature
    + Header
        * 내용
            - 처리방식
            - 프로토콜
            - IP주소
            - 포트번호
        * Rule 헤더 설정
            - Rule Actions
                + 패킷이 탐지되었을 때 처리방식
                + 8가지 유형이 존재
                    * alert : 알람을 발생시키고 로그를 남김
                    * log : 패킷을 로그에 남김
                    * pass : 패킷을 무시함
                    * activate : alert 을 발생시키고, dynamic rule을 활성화
                    * dynamic : activate rule 에 의해 활성화 되고, log rule 과 동일하게 동작
                    * drop : 패킷을 차단하고 로그에 남김
                    * reject : 패킷을 차단하고 로그를 남김, 메시지를 전달
                        - TCP : TCP RST
                        - UDP : ICMP port Unreachable
                    * sdrop : 패킷을 차단하지만 로그를 남기지 않음 (silent drop)
                + Rule 이 매칭 되었을 때 처리방식
            - Protocols 
                + 탐지할 프로토콜을 설정. 
                + TCP, IP, UDP, ICMP
            - IP Addresses
                + 출발지, 목적지 IP 주소. 
                + 단일 IP, IP대역(CIDR표기), IP 리스트 (구분자:,) 등으로 표기, 부정연산자(!) 표현가능
            - Port Numbers
                + 출발지, 목적지 Port
                + 단일 Port, Port Range (:이용), Port 리스트 표현
                    * 1:1023 : 1~1023
                    * :39910 : 1 ~ 39910
            - Direction Operator
                + 방향 지시자
                    * 출발지 -> 목적지 (단방향)
                    * 출발지 <> 목적지 (양방향)
                    * 출발지 <- 목적지 : 이러한 표현은 사용하지 않음
        * 형태 : `action protocol ipaddr port direction ipaddr port`
            - `alert tcp any any -> 192.168.59.0/24 80`
                + 192.168.59 대역의 80 포트에 들어오는 패킷을 탐지
            - `log tcp $EXTERNAL_NET any <> $HOME_NET 80`
                + 외부 네트워크 IP 에서 내부 네트워크 80 포트간 송수신되는 패킷을 탐지
                + `$EXTERNAL_NET`, `$HOME_NET` : `snort.conf` 에 정의된 환경변수
    + Body : 옵션 설정
        * 내용
            - 패킷탐지 규칙을 명시
            - snort 설정 generator 를 사용하여 간편하게 입력하는 방법도 존재
        * 종류
            - 일반
            - Payload / 범위 관련
            - Payload / 정규표현식
            - Payload / HTTP
            - 이벤트 제한 관련
        * 일반 옵션 설정
            - msg : alert, log 출력 시 이벤트 명으로 사용
            - reference : rule 과 관련된 외부 취약점 정보 참조시 사용
            - sid : 룰 식별자
                + 99이하 : 시스템 예약 sid
                + 100 ~ 1000000 : snort 에서 배포하는 ruleset 에서 사용하는 sid
                + 1000001 이상 : 사용자가 정의한 rule sid
            - classtype : 공격유형, 기본우선순위(위험도) 정의
            - priority : 위험도에 대한 숫자, classtype 에서 지정한 우선순위(위험도) 를 덮어씀
            - rev : revision - rule 수정횟수
        * **Payload / 범위 관련** : 범위를 지정해주는 옵션을 사용하면 성능 향상, 오탐을 줄임
            - 옵션
                + **content** : Payload 에서 검사할 문자열 설정 (전체 Payload 비교)
                    * 문자열 설정 시 text, text/binary, binary 형태 중 하나로 설정, 부정연산자 사용 가능
                        - text 형식 : `content:"문자열`
                        - binary 형식 : `content:"|FFFF|문자열"`
                            + FF : 헥사 값 예시
                        - text/binary 형식 : `content:"|FFFF|"`
                        - !(부정연산자) : `content:!"문자열"` - 문자열이 없는 것
                        - Escape(`\`) 처리 필요 문자 : `"`, `;`, `\`
                + uricontent : HTTP Client 의 요청 URI (전체 Payload X) 에서 검사할 문자열 설정
                + **offset** : Payload 에서 Content 문자열을 검사할 시작 위치를 지정
                    * Payload 의 첫 번째 바이트 위치는 0 부터 시작
                + **depth** : offset 부터 몇 바이트까지 검사할 것인지 지정
                + **distance** : `content:"문자열"` 이 매치된 경우 
                    * 이후부터 몇 바이트 떨어진 위치에서 다음 content를 검사할 것인지 지정
                + **within** : `content:"문자열"` 이 매치된 경우
                    * distance 부터 몇 바이트 범위 내에서 다음 content를 검사할 것인지 지정
                + nocase : Payload 검사 시 대소문자를 구분하지 않음 (바이너리 형식도 동일)
            - 예시 (29:10)
                + FTP root 로그인 시도 탐지 (제공 Ruleset 사용)
                    ```
                        alert tcp any any -> 10.10.10.0/24 21
                        (msg:"FTP root user access";content:"USER root";nocase;sid:100100;)
                    ```
                + 테스트 (content)
                    ```
                        alert tcp $EXTERNAL_NET any -> $HOME_NET any
                        (msg:"[TEST]content_text";content:"/etc/passwd";nocase;sid:1000020;)
                    ```
                + 테스트 (offset) - 3번째 바이트부터 (2) 3바이트 이내(2~4)에 5457 문자열이 있는지 확인
                    ```
                        alert tcp $EXTERNAL_NET any -> $HOME_NET any
                        (msg:"[TEST]offset";content:"5457";offset:2;depth:3;nocase;sid:1000030;)
                    ```
                + 테스트 (distance, within) - 3번째 바이트부터 (2) 5바이트 이내 (2~6) 에서 369 문자열을 찾고, 4바이트 (7,8,9,A) 떨어진 위치 (B) 에서부터 3바이트 이내 (B,C,D) 에서 ABC 문자열을 찾아 만족하면 알림 출력
                    ```
                        alert tcp $EXTERNAL_NET any -> $HOME_NET any
                        (msg:"[TEST]distance, within";content:"369";offset:2;depth:5;content:"ABC";distance:4;within:3;nocase;sid:1000034;)
                    ```
        * Payload / 정규표현식
            - 정규표현식 : 일정 규칙을 갖는 문자열을 표현하는 방식
            - 옵션 : PCRE (Perl Compatible Regular Expression) - Snort 에서는 Perl 기반 정규표현을 사용
                + `pcre="/정규표현삭/"`
            - 메타문자 
                + `\` : escape 문자로 사용 (특수문자 기능 제거)
                + `^` : 캐럿. 다음에 나오는 문자는 문자열의 시작을 의미
                + `$` : $ 앞의 문자는 문자열의 끝을 의미
                + `.` : 임의의 문자 한 개를 의미함
                + `[]` : 집합 안에 지정한 문자들 중 하나와 일치
                    * `[a-zA-Z]` : 소문자 a-z 사이 + 대문자 A-Z 사이 중 한 문자와 일치
                    * `[0-9]` : 0~9 사이의 숫자 중 하나
                + `[^]` : 집합 안에 지정한 문자들을 제외한 임의의 문자 중 하나와 일치
                    * `[^0-9]` : 숫자를 제외한 임의의 문자 중 하나와 일치
                + `|` : OR, 항목을 묶을 때는 소괄호를 이용
                    * `(x|y|z)` : x or y or z
                    * `(tree|leaf|can)` : tree or leaf or can
                + `*` : * 앞에 나오는 문자가 0회 이상 나타남을 의미
                    * `Hs*deo` : Hssdeo(O), Hdeo(O), Hsdeo(O), Hssssdeo(O) .... Hudeo(X)
                + `+` : + 앞에 나오는 문자가 1회 이상 나타남을 의미
                + `?` : ? 앞에 나오는 문자가 0회 또는 1회 이상 나타남을 의미
                    * `x?` : x가 존재하거나 존재하지 않음
                + `x{n}` : `{n}` 앞에 오는 문자가 정확히 n 회 나타남
                    * `Ha{3}deo` : Haaadeo
                + `x{n,}` : x를 n번 이상 반복함을 의미
                    * `Ha{3,}deo` : Haaadeo(O), Haaaadeo(O), ..., Haadeo(X)
                + `x{n,m}` : x를 n번 이상 m번 이하 반복함을 의미
            - 예시
                + Get 또는 Post 로 시작하는 '=' 나 'a=a' 가 들어간 패킷 찾기
                    ```
                        alert tcp $EXTERNAL_NET any -> $HOME_NET any
                        (msg:"[TEST]pcre";content:"+or+";
                        content:"%27%3D%27";nocase;
                        distance:0;within:20;pcre="/^(GET|POST)/"; sid:100050;)
                    ```
                    * `+` 는 space 를 의미
                    * `%27%3D%27` : a=a
        * Payload / HTTP 관련 : HTTP 패킷에 대해 전체가 아닌 특정필드에서 패턴을 검사하는 기능 지원
            - HTTP 구조 : `GET /index.php HTTP/1.1`
            - 옵션 : 옵션에 raw 가 붙은 경우 (http_raw_*) 디코딩 없이 검사. (uri, header, cookie)
                + `http_method` : HTTP 요청 시 method 부분을 검사 (GET)
                + `http_uri` : HTTP URI 부분을 검사 (index.php)
                + `http_header` : HTTP의 Header 부분을 검사 (response/request)
                + `http_cookie` : HTTP의 Cookie 부분을 검사
                + `http_client_body` : HTTP client 의 Body 부분을 검사 (POST 요청에 대한 검사)
                + `http_stat_code` : HTTP 의 Status_code 부분을 검사 (HTTP/1.1 200 OK 의 200 부분) 
                + `http_stat_msg` : HTTP 의 Status_msg 부분을 검사 (HTTP/1.1 200 OK 의 OK 부분) 
            - 예시
                + http_method 테스트 : 클라이언트의 http 요청 라인 중 method 부분을 검사, content 가 GET 인 패킷을 탐지
                    ```
                        alert tcp $EXTERNAL_NET any -> $HOME_NET any
                        (msg:"[TEST]http_GET";content:"GET";http_method;sid:1000055;)
                    ```
                + http_uri 테스트 : 클라이언트의 http 요청 중 uri 정보를 검사, uri 정보 중 `<script>` 를 포함하는 패킷을 탐지 (XSS)
                    ```
                        alert tcp $EXTERNAL_NET any -> $HOME_NET any
                        (msg:"[TEST]http_uri";content:"<script>";http_uri;nocase;sid:1000063;)
                    ```
        * **Event Threshold(이벤트 제한)** 관련
            - 용도
                + 특정 시간동안 발생되는 이벤트 수를 제한하기 위한 옵션
                + 과도하게 반복되는 이벤트를 막기 위한 옵션
                + `threshold:type <limit|threshold|both>, track <by_src|by_dst>, count <c>, secods <s>`
            - 옵션
                + type
                    * `--limit` : 매 s초 마다 패킷 count 개 발생 시 Action 을 1번 수행 (임계시간)
                    * `--threshold` : 매 s초 내에 패킷 count 개 발생 시 마다 Action 을 수행 (패킷양)
                    * `--both` : 매 s초 내에 패킷 count 개 발생 시 한번 Action 을 수행 (IP)
                + track
                    * `by_src` : 출발지 IP 기준
                    * `by_dst` : 목적지 IP 기준
            - 예시
                + limit 테스트 (임계시간)
                    ```
                        threshold:type limit, track by_dst, count 100, seconds 2
                    ```
                    * 2초 내에 패킷 100개 : 로그 1개
                    * 2초 내에 패킷 200개 : 로그 1개
                    * 4초 내에 패킷 400개 : 로그 2개
                + threshold 테스트 (패킷양)
                    ```
                        threshold:type threshold, track by_dst, count 100, seconds 2
                    ```
                    * 2초 내에 패킷 100개 : 로그 1개
                    * 2초 내에 패킷 200개 : 로그 2개
                    * 4초 내에 패킷 400개 : 로그 4개
                + both 테스트 (IP)
                    ```
                        threshold:type both, track by_dst, count 100, seconds 2
                    ```
                    * 2초 내에 패킷 100개 : 로그 1개
                    * 2초 내에 패킷 200개 : 로그 1개
                    * 4초 내에 패킷 400개 : 로그 1개
- Snort 공격 패킷 탐지
    + FTP root 로그인 시도 탐지
        * FTP 서비스는 평문으로 송수신 되기 때문에 계정이 노출될 위험이 있음
        * Wireshark 등을 통한 FTP 메시지 분석
            - `USER root\r\n` 메시지 가 평문으로 전달됨 : 포트 21, User root 메시지 탐지
            ```
                alert tcp any any -> 10.10.10.0/24 21
                (msg:"FTP root user access"; content:"USER root"; nocase; sid:1000070;)
            ```
                + alert 액션 : 메시지와 탐지 패킷 로그(pcap(default) 형식)를 남긴다.
                    * `snort.log.15151....` : wireshark, tcpdump 등으로 확인 가능
                    * `tcpdump -r snort.log.15151....`
    + Telnet root 로그인 성공 탐지
        * Wireshark 등을 통한 Telnet 메시지 분석
            - Telnet 클라이언트 로그인 성공 시 서버의 응답 데이터로 `login` 키워드 와 쉘프롬프트 정보가 평문 전달됨
                + 예) 쉘프롬프트 : `[root@test~]#`
                + root 계정은 중요한 계정이므로, telnet, ssh 등으로 직접 로그인이 가능하면 공격 대상이 될 수 있다.
                + root 계정에 대한 원격접속은 허용하지 않도록 설정한다.
                + 텔넷 서버에서부터 응답 데이터를 탐지, content 를 통해 login 문자열이 포함되고, pcre 를 통해 정규표현식 포함
                ```
                    alert tcp 10.10.10.0/24 23 -> any any
                    (msg:"Telnet root login success"; content:"login"; pcre:"/root@.*#/"; nocase; sid:1000100;)
                ```
    + 패스워드 크래킹 공격 탐지
        * 패스워드 크래킹 종류
            - Brute Force
            - Dictionary Attack
        * Telnet 로그인 실 시 응답 Data : `Login incorrect`
        * threshold 옵션, by_dst, 5초 동안 1번째 이벤트에만 액션 수행
            ```
                alert tcp 10.10.10.0/24 23 -> any any
                (msg:"Telnet login brute force attack"; content:"login incorrect"; nocase;
                threshold:type limit, track by_dst, count 1, seconds 5; sid:1000101;)
            ```

- 비정상 패킷 탐지
    + 비정상 패킷 : RFC(Request for Comments) 문서에 정의된 프로토콜 표준 패킷 이외의 패킷
        * RFC : 인터넷 관련기술을 구현하는데 필요한 상세절차, 기본 틀 등을 제공하는 공문서와 같은 간행물
            - IETF(Internet Engineering Task Force) - 인터넷 국제 표준화 기구 에서 발행한 문서
        * IPS/IDS 에서 제대로 처리하지 못하면 시스템 오류/장애가 발생할 수 있음
    + 비정상 IP 패킷
        * 인터넷 구간의 사설 IP : 사설 IP 로 예약된 주소는 WAN 에서 사용할 수 없음
            - 인터넷 구간에서 사설 IP 가 유입된 경우 조작된 IP임
            - 사설 IP 대역
                + A Class : 10.0.0.0 ~ 10.255.255.255 (10.0.0.0/8)
                + B Class : 172.16.0.0 ~ 172.31.255.255 (172.16.0.0/12)
                + C Class : 192.168.0.0 ~ 192.168.255.255 (192.168.0.0/16)
            - 예시
                + UDP 트래픽 중 사설 IP로 설정되어 있는 비정상 패킷을 탐지
                    ```
                        alert udp 10.0.0.0/8 any -> $HOME_NET any
                        (msg:"Private IP(10~) Detect"; sid:1002100;)
    
                        alert udp 172.16.0.0/12 any -> $HOME_NET any
                        (msg:"Private IP(172~) Detect"; sid:1002101;)
    
                        alert udp 192.168.0.0/16 any -> $HOME_NET any
                        (msg:"Private IP(192~) Detect"; sid:1002102;)
                    ```
        * 출발지와 목적지가 동일한 IP : Land Attack
            - 예시
                + 출발지와 목적지가 동일한 IP 인 패킷 탐지
                    ```
                        alert ip any any -> $HOME_NET any
                        (msg:"Land Attack(src/dst same IP) Detect"; sameip; sid:1002300;)
                    ```
                + 메시지 결과  :
                    ```
                        [1002300] Land Attack(src/dst same IP) Detect
                        시간값 10.10.11.21 -> 10.10.11.21
                    ```
    + 비정상 TCP 패킷
        * 정상적인 TCP 패킷
            - 3-Way Handshake : SYN -> SYN_ACK -> ACK (정상적 TCP 연결시 사용)
            - 4-Way Handshake : FIN_ACK -> ACK (정상적 연결 종료시 사용)
            - RST_ACK (연결을 즉시 종료할 때)
            - 연결 후에는 송수신되는 패킷에 디폴트로 ACK Flag 를 포함 (필요시 PSH, URG 사용)
        * 비정상적인 TCP 패킷
            - SYN_FIN : 동시에 설정될 수 없는 플래그. 구형의 IDS/IPS, FW 에서는 탐지 못함
                + SYN : 연결 설정
                + FIN : 연결 종료
                + 스캔 도구들이 SYN_FIN 패킷을 많이 사용
                + 예시
                    ```
                        alert tcp any any -> 10.10.10.0/24 any
                        (msg:"SYN_FIN Scan Detect"; flags:SF; sid:1000340;)
                    ```
                    * flags 옵션 연산자 사용 예
                        - `flags:SF` : 연산자가 없는 경우 (SYN,FIN만 설정된 경우)
                        - `flags:SF+` : + 연산자가 있는 경우 (SYN,FIN은 반드시 포함) 
                        - `flags:SF*` : * 연산자가 있는 경우 (지정한 flag를 하나 이상 포함)
                        - `flags:!SF` : ! 연산자가 있는 경우 (지정한 flag를 포함하지 않음)
                + 메시지 결과
                    ```
                        [1000340]SYN_FIN Scan Detect
                        시간값 10.10.10.10 80 -> 10.10.10.20 80
                        *****SF
                    ```
                    * 패킷의 출발지 포트는 1024 이상이어야 함. (위 예시는 비정상)

                + SYN_FIN 변형 TCP 패킷 : SIN_FIN 플래그만을 탐지하는 IDS/IPS 를 우회하기 위함
                    * SYN,FIN,PSH 또는 SYN,FIN,RST 또는 SYN,FIN,PSH,RST 을 조합하여 사용
                    ```
                        alert tcp any any -> 10.10.10.0/24 any
                        (msg:"SYN_FIN + SCAN Detect"; flags:SF+; sid:1000350;)
                    ```
            - FIN : FIN_ACK 는 정상적인 연결 종료 패킷, FIN 만 설정되는 경우 비정상 TCP 패킷
                + 스텔스 스캐닝 기법 중 하나 : FIN 플래그만 설정된 비정상 TCP 패킷
                    ```
                        alert tcp any any -> 10.10.10.0/24 any
                        (msg:"FIN scan detect"; flags:F; sid:1000360;)
                    ```
            - NULL : 플래그가 설정되지 않은 비정상 TCP 패킷
                + 모든 패킷은 Flag 가 설정되어 있음
                + NULL 패킷은 스텔스 스캐닝 기법 중 하나
                    ```
                        alert tcp any any -> 10.10.10.0/24 any
                        (msg:"Null scan detect"; flags:!UAPRSF; sid:1000370;)
                    ```