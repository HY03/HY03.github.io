---
title: 정보보안 - HTTP
date: '2021-06-23 18:00:00'
tags:
- 정보보안기사
- 어플리케이션
related: true
categories:
- IS_Certification
toc: true
---

# Web 보안
- 웹프로토콜인 HTTP, TCP/IP 가 데이터에 대한 보안서비스를 제공하지 않음

## 웹트래픽 보안
- IPSec
    + 네트워크 계층에서 사용
- SSL/TLS
    + 범용 해결방안
    + 전송계층에서 사용
- S/MIME, Kerveros
    + 응용계층에서 사용

## SSL/TLS
- 개요
    + 전송 계층의 보안 프로토콜
    + 기밀성 (DES, RC4 와 같은 대칭키 알고리즘 / 비밀키 : Handshake Protocol) , 무결성 (MAC Code), 인증 보안 서비스 (공개키 인증서) 를 제공
    + Netscape 사에서 제안한 웹브라우저를 위한 보안프로토콜
    + 1996년 SSL 3.0 발표
    + 2014년 SSL 3.0 이 Puddle 공격에 취약점이 발견되어 안전하지 않은 것으로 판명
    + 1999년 IETF(국제 인터넷 표준화 기구) 에서 TLS1.0 버전 발표
    + 2006년 TLS1.1 (RFC 4346) , AES 암호화 지원
    + TLS1.2 (RFC 5246),DES 암호화 지원
- 구조
    + Application Layer 와 Transport Layer 사이에 SSL/TLS Layer 가 존재
    + TCP 기반
    + 상태 정보를 유지하는 프로토콜
    + 암호화 / 복호화 진행
    + Application Layer : HTTP
    + SSL / TLS Layer
        * Handshake / Change Cipher Spec / Alert / Application Data
        * Record
            - Application Data : 응용계층에 있는 데이터 전달
            - Handshake : 양 종단 간 보안 파라미터 협상을 위한 프로토콜
            - Change Cipher Spec : 협상된 보안파라미터의 적용, 변경을 알림
            - Alert : SSL / TLS 통신 중 오류가 발생하면 통보
            - Record : 적용, 변경된 보안파라미터를 이용하여 암호화/복호화/압축/해제/무결성보호 기능 제공
    + Transport Layer : TCP
- 상태정보 유지 프로토콜
    + 세션 상태정보
        * Full Handshake (완전협상) 이후 세션 생성
    + SSL / TLS 연결 (Connection) 상태정보
        * Abbreviated Handshake (단축협상)
    + 완전협상에 의해 세션을 생성하고, 세션정보 기반 단축협상을 하여 Connection 생성
        * 완전협상 (Session)
            - SessionID (32bit) : 100 
            - 암호화정보 : AES256/CBC (Cipher suit 중 선택)
            - Master Secret : Hash (서버랜덤값(32byte), 클라이언트랜덤값(32byte), premaster secret)
        * 단축협상 (Connection) & Data 송수신
            -  SessionID : 100
            -  Key Block : Hash (서버랜덤값, 클라이언트랜덤값, Master Secret)
            -  Key Block : 아래의 값들을 생성하는데 이용
                +  Client Write Key : 암호화 비밀키 (서버 Read Key)
                +  Server Write Key : 암호화 비밀키 (클라이언트 Read Key)
                +  Client Mac Key : MAC 생성 시 사용하는 인증키
                +  Server Mac Key : MAC 생성 시 사용하는 인증키
                +  Client IV : 블록 암호화에 사용하는 초기벡터값
                +  Server IV : 블록 암호화에 사용하는 초기벡터값
    + 세션이 유지되는 동안 다수의 Connection 이 생성됨
- **완전협상** (Full Handshake)
    + 과정
        * Client --> Server : Client Hello
            - 클라이언트가 지원 가능한 SSL/TLS 버전
            - Cipher Suites (암호화 도구들)
            - 압축방식
            - Client Random (난수 28바이트 + Datetime 4바이트)
            - Session ID (빈 값) : Session ID Length:0
            - Cipher Suites (지원 가능한 Cipher Suite 목록)
                + Cipher Suites (10 suites)
                    * Cipher suite: TLS_DHE_RSA_with_AES_256_CBC_SHA
                        - DHE : (Ephermeral Diffie Hellman) 임시디피헬먼
                        - ECDHE : (Elliptic Curve + DHE) 타원곡선암호 + 임시디피헬먼
                        - DH : Diffie Hellman
                    * Cipher suite: ...
                    * ...
                + Cipher Suite 란?
                    * 키 교환 및 인증 알고리즘 필드
                    * Cipher Spec (암호명세) 필드 (블록암호화모드,HMAC용 해시알고리즘)
                        - SSL_TLS_키교환및인증_with_암호명세
                        - `TLS_RSA_with_AES_256_CBC_SHA256`
                            + 키교환및인증 : RSA
                            + 대칭 암호화 알고리즘 : AES
                            + 암호키길이 : 256bit
                            + 블록암호화모드 : CBC
                            + HMAC용 해시알고리즘 : SHA256
                        - `TLS_DHE_DSS_with_AES_256_GCM_SHA256`
                            + 키교환 / 인증 : DHE (E:임시 Diffie Hellman (익명X)) / DSS
                            + 대칭 암호화 알고리즘 : AES
                            + 암호키길이 : 256bit
                            + 블록암호화모드 : GCM
                            + HMAC용 해시알고리즘 : SHA256

        * Client <-- Server : Server Hello
            - SSL/TLS 버전
            - Cipher Suite (클라이언트 암호화 도구들 중 택1)
            - 압축방식
            - Server Random (난수 28바이트 + Datetime 4바이트)
            - Session ID (생성 값) : Session ID Length : 32
        * Client <-- Server : Server Certificate (Optional)
            - 서버 인증서 목록
                + Certificates
                    * Certificate Length : ...
                    * Certificate ID : ...
                    * Certificate Length : ...
                    * Certificate ID : ...
        * Client <-- Server : Server Key Exchange (Optional)
            - 키 교환 필요 시 키 교환에 필요한 정보를 서명값과 함께 전송
        * Client <-- Server : Certificate Request (Optional)
            - 클라이언트 인증서 요청 메시지
            - 서버에서 인증 가능한 인증기관목록 전송
        * Client <-- Server : Server Hello Done
            - Server Hello 종료 알림 메시지

        * Client --> Server : Client Certificate (Optional)
            - 클라이언트 인증서 목록 전달 (요청시)
        * Client --> Server : Client Key Exchange
            - 키 교환에 필요한 premaster secret 값 생성하여 전달
            - 암호화 방식에 따라 전달하는 값이 달라짐
        * Client --> Server : Certificate Verify (Optional)
            - 인증서에 대한 증명 메시지
            - 클라이언트가 보낸 인증서에 대한 개인키를 가지고 있음을 증명 (요청시)
        * Client --> Server : Change Cipher Spec
            - 협상한 암호명세 적용/변경함을 알려줌
        * Client --> Server : Finished
            - 협상 완료 메시지

        * Client <-- Server : Change Cipher Spec
            - 협상한 암호명세 적용/변경함을 알려줌
        * Client <-- Server : Finished
            - 협상 완료 메시지

        * Application Data 를 주고 받음 (단축협상이 이루어진 이후)

- 단축협상 (Abbreviated Handshake)
    + 과정
        * Client --> Server : Client Hello
            - Session ID
                + Session ID Length : 32
                + Session ID : ...
            - Client Random
            - Cipher Suites
            - 압축 방식

        * Client <-- Server : Server Hello
            - Server Random
            - Session ID
                + Session ID Length : 32
                + Session ID : ...
            - Cipher Suite
        * Client <-- Server : Change Cipher Spec (완전 협상과의 차이점)
            - Content-Type : Change Cipher Spec(20)
        * Client <-- Server : Finished

        * Client --> Server : Change Cipher Spec
        * Client --> Server : Finished

        * Data 송수신
    + SSL/TLS 계층 중 Record 계층에 대해서...
        * 아래 과정 진행 후 송수신
            - 메시지 압축
            - 압축해제
            - 무결성 검증
            - 암호화 / 복호화
        * 과정
            - Application Data 를 단편화
            - 단편화 한 데이터를 압축 + MAC 추가 => 암호화
            - 전송

- 공격 및 대응
    + SSL/TLS 공격
        * OPENSSL 의 HeartBleed 취약성
            - 2014년 Google 에서 발견
            - HeartBeat Protocol 이란?
                + HandShake / ChangeCipherSpec / Alert / ApplicationData
                    * Application Data | HeartBeat 로 나눠짐
                + 네트워크 상에서 컴퓨터와 네트워크 간 정상동작을 나타내기 위해 사용
                + 네트워크 상 동기화를 위한 주기적인 신호
                + 보조서버, 주서버 동기화 시 HeartBeat 가 없으면 서버 간 재연결 시도
            - Alert Protocol (2바이트)
                + Level (1바이트) / Alert (1바이트)
            - OpenSSL 사용 시, HeartBeat 프로토콜의 요구 데이터 길이에 대한 점검이 이루어지지 않아 메모리의 관계없는 데이터까지 상대에게 넘어가는 취약점
            - 대응책
                + SSL 버전 업 (1.0.1g 이상)
                + HeartBeat 확장 기능을 사용하지 않는 옵션 (재컴파일 필요)
                + 인증서 재발급 
        * SSL 3.0 취약성 (POODLE 공격)
            - 공격자가 TLS1.0 에서 SSL3.0 으로 다운그레이드 강요 요청
            - 이후 MITM (중간자공격) 진행하여 암호화되어있는 쿠키, 데이터 추출
            - SSL 3.0 은 블록암호화 모듈 CBC 사용
                + 패딩된 암호 블록이 MAC 에 의해 보호되지 않는 취약점이 존재
            - 대응책
                + SSL 3.0 을 사용하지 않음
        * FREAK 공격 (수출용 RSA 키의 소인수 분해 로 다운그레이드)
            - 프랑스 국립연구소 2015년 발견
            - 중간자 공격을 이용, SSL 을 통해 강제로 RSA 인증 (취약) 을 사용하도록 함
                + RSA Export Suite : 512 비트 -> 약한 암호화
            - 대응책
                + 최신 Open SSL 사용
        * 완전 순방향 비밀성 (PFS, Perfect Forward Secrecy)
            - SSL/TLS 통신 중 서버의 개인키 노출문제
                + 키교환 RSA 방식 중 MITM 공격을 통해 트래픽을 가로채고, 서버의 개인키를 이용하여 세션키, 비밀키, 송수신 데이터 복호화
                + 노출된 인증서를 폐기하여도, 이전 트래픽은 복호화 가능
            - 완전 순방향 비밀성 : 이전 트래픽 정보의 기밀성이 유지됨
                + 키 교환시마다 새로운 DH 개인키를 생성
                    * DHE (Ephermeral Diffie-Hellman) 이용하여 생성
                    * premaster secret (서버,클라이언트 공통) 생성
                    * 서버 개인키는 DH의 파라미터를 인증하는 용도로만 사용
            - PFS 적용의 어려움
                + DH 키 교환방식은 RSA 에 비해서 속도가 느림
                    * 서버에서 DHE, ECDHE cipher suite 를 비활성화하는 경우가 많음
                    * 대부분의 웹 브라우저가 DHE, ECDHE 를 지원하지 않음