---
title: 정보보안 - 이메일
date: '2021-04-30 18:00:00'
tags:
- 정보보안기사
- 어플리케이션
related: true
categories:
- IS_Certification
toc: true
---

# 이메일 보안
## 이메일 구조
- UA (User Agent)
    + 메시지 구성, 메시지 읽기 / 보내기 용도의 S/W
- MTA (Message Transfer Agent)
    + SMTP
    + MTA Client (User)
        * UA 와 동시 호출됨
        * Mail Server 의 MTA Server 에 메일 저장 (SMTP)
    + MTA Server
        * 1) MTA Client 로부터 전송받은 메일을 저장
            - spool
        * 2) Mail Server 내 MTA Client 에게 메일 송신
    + MTA Client (Mail Server)
        * 연결된 상대방 MTA Server 에 메일 송신 (SMTP)
            - PUSH : Client -> Server
- MAA (Message Access Agent)
    + POP3, IMAP3
    + MAA Client
        * UA 가 MAA Client 에 메일 가져올 것을 요청
        * Mail Server 내 MAA Server 에 메일 요청 (POP3/IMAP)
        * PULL : Server -> Client
    + MAA Server
        * 수신된 Mail 서버 내 Mail Box 내용 검색 (MTA 가 보관중인 메일)

## 이메일 송수신 프로토콜
- SMTP
    + MTA (Message Transfer Agent) Protocol
    + 암호화 기능이 없음
    + 과거 : 여러 메일서버를 거쳐서 (중계) 최종 목적지 도달
    + 현재 : DNS 서버를 통해 메일 서버를 한 번만 거침
    + ESMTP (확장 SMTP) : 기존 SMTP 의 단점을 보완
        * 텍스트 외 영상 등 첨부
        * 수신확인 기능
- POP3 (Post Office Protocol Ver.3)
    + MAA (Message Access Agent) Protocol
    + 메일서버 MAA (POP3 / 110 TCP)
    + 클라이언트 MAA (POP3) 에서 user/pw 정보로 메일서버에 PULL 요청
        * Mail BOX 에서 해당하는 메일 PULL
- IMAP4 (Internet Mail Access Protocol Ver.4)
    + MAA (Message Access Agent) Protocol
    + 메일서버 MAA (IMAP4 / 143 TCP)
    + POP3 보다 많은 기능을 포함
        * 데이터 신뢰성 보장
        * 순서대로 송수신
        * 이메일 수신 전에 헤더 검사
        * 이메일 수신 전에 내용 검사
        * 부분적으로 이메일을 수신
        * 메일서버에서 편지함(Mail Box) 을 생성, 삭제, 이름변경

## 이메일 보안기술
- PEM (IETF 에서 개발) - Privacy Enhanced Mail
    + 중앙집중화된 키 인증 방법
    + 인터넷 표준
    + 구현 어려움 (많이 사용되지 않음)
    + 높은 보안성 (군사용, 금융계)
    + 이론중심적
- PGP (Phill Zimmermann 개발) - Pretty Good Privacy
    + 분산화된 키 인증
    + 구현 용이
    + 현실적 (현재 많이 사용)
    + 전자서명 제공
    + 기밀성 : 대칭블록암호
    + ZIP 압축 기능
    + 전자우편 호환성, 단편화
- S/MIME (RSA Data Security 개발)
    + (Secure) / MIME 기반 (Multipurpose Internet Mail Extention)
        * 이메일은 ASCII 기반으로 전송
        * 첨부파일 등 바이너리 데이터를 ASCII (텍스트) 전환, 전송하는 프로토콜
    + 다양한 상용 툴킷
    + X.509 인증서 지원
    
## PGP 보안기술
- 기밀성 (메시지 암호화)
    + 메시지 암호화 : 3DES, IDEA, CAST-128 - 대칭키 알고리즘
    + 세션키 암호화 : RSA - 공개키(서명, 메시지암호화) 알고리즘
- 인증 (전자서명)
    + 암호화 : RSA, DSS - 공개키(서명, 메시지암호화) 알고리즘
    + 해시함수 메시지 다이제스트 : SHA-1, MD5, RIPE-MD/160 - 해시 알고리즘
- 압축 (ZIP)
    + ZIP 메시지 압축 - 압축 알고리즘
    + 서명 후 암호화 전에 압축
        * 암호화된 결과를 이용해서 평문 추측을 어렵게 하기 위함
- 전자우편 호환성
    + Radix-64 를 이용해 ASCII 부호로 변환
    + PGP 결과로 나오는 메시지블록은 8bit 스트림
    + 기존의 이메일에서 ASCII 문장으로 이루어진 블록만을 사용
    + Base-64 + 오류검출 (Radix-64) 변환을 이용해서 3개의 8Bit 를 4개의 ASCII 문자로 변환
- 단편화/재조합
    + fragmentation / reassembly
    + 전자메일의 최대 길이 : 보통 50,000 Byte
    + 50,000 Byte 이상 메시지는 단편화 전송
    + 단편화 과정 : Radix-64, Base-64
    + 수신 측에서는 모든 전자메일 헤더 제거 후 블록 재조립
- PGP 에서 사용하는 키
    + 세션키
        * 하나의 메시지에 대해서 암호화/복호화할 때 사용
        * 예측할 수 없는 키값
    + 공개키
        * 사용자는 공개/개인키 쌍을 여러 개 보관 가능
    + 개인키
        * 사용자는 공개/개인키 쌍을 여러 개 보관 가능
    + 패스워드 기반의 대칭키
        * 저장한 개인키를 암호화
    + 키 식별자
        * 누구의 공개키인지 확인 필요
    + 키 링
        * 각 사용자들은 개인키링, 공개키링을 갖는다
        * 개인키 링 : 자신의 개인키/공개키 쌍들을 저장
        * 공개키 링 : 자신이 알고 있는 다른 사용자들의 공개키 저장
- 과정
    + 1. 세션키 생성 (의사난수생성기)
    + 2. 수신자의 공개키로 세션키 암호화 (비대칭키 알고리즘) -> 암호화된 세션키
    + 3. 메시지 압축
    + 4. 메시지 암호화 (세션키 이용 : 대칭키 알고리즘) -> 암호화된 메시지
    + 5. 암호화된 세션키, 암호화된 메시지 결합 (2진데이터)
    + 6. Radix-64(Base-64 + Data 오류검출) 변환 (ASCII 데이터) -> 송신 데이터
- Open PGP 인증서 사용

## S/MIME 보안기술
- 전송할 수 있는 데이터 형식
    + SMTP 형식 (RFC 822) 기반
        * Date, From, Subject, To, CC, ...
    + 실행파일 (전송불가) -> MIME 타입으로 변환
- MIME 타입
    + RFC 822 헤더
        * Date, From, Subject, To, CC, ...
    + MIME 헤더
        * MIME Version : 전자메일 메시지가 MIME 표준
        * Content-Type : 메시지 유형이 Text, audio, image ...
            - text/plaintext, img/jpeg(sub-type) ...
        * Content-Transfer-Encoding : 인코딩 방식
            - base 64, radix 64 ...
    + Body
- 보안기술 (S/MIME Application 이용하여 MIME 타입을 S/MIME 변환)
    + 디지털 서명 : RSA / SHA-256
    + 메시지 암호화 : AES
    + 압축기능 : 제한 없음
    + 이메일 호환성 : Radix-64
- S/MIME 데이터를 SMTP 로 송/수신
- 메시지 구성
    + Enveloped Data (동봉된 데이터) - 메시지 비밀성을 위함
        * 임의의 타입의 데이터의 암호화된 내용
        * 하나 이상의 다수 수신자를 포함한 암호화 키
    + Signed Data (서명된 데이터)
        * 내용, 서명을 서명자의 개인키로 암호화
        * Radix-64 변환한 내용
            - S/MIME 기능을 가진 수신자만 내용을 볼 수 있음
    + Clear-Signed Data
        * 디지털 서명만을 Base-64 변환
            - S/MIME 기능이 없더라도 메시지 내용을 볼 수 있음
    + Signed and Enveloped Data
        * 암호화 메시지는 서명, 서명된 메시지는 암호화
- X.509 V3 인증서 사용

## 스팸메일의 기술적 대응 방안
- 메일서버 수신 차단
    + 콘텐츠 필터링
        * 헤더 정보 확인
        * 제목 확인
        * 메시지 내용 확인
    + 송신자 필터링 - 지속적 관리 (스팸 블랙리스트 관리) 필요
        * IP
        * 메일주소
        * URL 정보
    + 네트워크 레벨 필터링
        * 패킷필터링
        * DNS 변경
    + 발송량 기준 차단
        * 최대 수신용량 관리 / 차단
    + 시간대별 차단
        * 송신자 정보 기준 업무시간 외 메일 차단
- 메일 서버 보안
    + 릴레이 스팸 방지
        * 외부에서 전송된, 자신의 메일서버를 통해 다른 메일서버로 전송하려는 메일들 차단
        * 송신자별 릴레이 허용 - DB 설정
        * Domain 리스트 설정
    + Anti-SPAM 솔루션 도입
- 메일 클라이언트 보안
    + 콘텐츠 필터링
    + 송신자 필터링
- 스팸 필터 솔루션
    + 메일 서버 앞단 (F/W와 IPS 장비 뒤) 에서 필터링
    + 프락시 메일서버로 동작
    + 필터링 종류
        * 메일헤더
        * 제목
        * 본문
        * 첨부파일
- 스팸 블랙리스트 기법 (Tool)
    + RBL (Real-time Black List) : 실시간 차단
        * IP 관련 Black List DB
    + SPF (Sender Policy Framework) : 메일서버 등록제, 메일 검증 기술
        * 메일해더의 발송지 IP 가 실제 서버인지 확인 (Spoofing 확인)
        * 메일서버를 DNS 에 미리 등록
        * 등록된 서버로부터 발송이 되었는지 확인 / 차단
        * 포털업체에서 송신측 메일서버를 DNS 에 넣고 관리 -> 확인 / 차단
    + procmail
        * sendmail (unix SMTP 프로토콜 프로그램 - MTA) 과 연동하여 사용 (.cf 파일 - config)
        * 받은 메일 제목 / 내용의 특정 키워드로 필터링
        * 메일 사이즈, 첨부 파일명에 따라 메일 반송
        * 메일 반송/차단 로그분석
    + sanitizer
        * procmail 의 룰셋
        * 파일 확장자 필터링
        * 메일 내 매크로 검사 기능
    + spamAssassin
        * Apache 오픈소스 프로젝트
        * Perl 언어 사용
        * 헤더 / 본문의 정해진 규칙으로 검색
        * 여러가지 기술의 이용이 가능 (RBL - 스팸 데이터베이스 와 협동적 연계)
        * 베이지안 필터링 이용
        * 스코어링을 통해 특정 점수를 넘으면 스팸으로 판단
    + inflex
        * 로컬이나 외부로 나가는 이메일 검사
        * 첨부파일에 대한 필터링 (inbound / outbound)
- 유닉스 환경의 메일 프로그램
    + sendmail - SMTP (MTA)
    + Dovecot - POP3, IMAP (MAA)

