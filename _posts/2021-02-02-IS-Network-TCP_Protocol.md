---
title: 정보보안 - 네트워크 TCP 프로토콜
date: '2021-02-02 13:00:00'
tags:
- 정보보안기사
- 네트워크
related: true
categories:
- IS_Certification
toc: true
---

# TCP 프로토콜
- 개요
    + 연결지향적 프로토콜 (Connection Oriented Protocol)
        * 논리적 연결 통로를 먼저 생성 후 통신
        * 순서 제어 : 데이터 전송순서 보장
    + Stream 기반 전송방식
        * 정해진 크기로 전송하지 않고 임의의 크기로 나눔
        * 연속적으로 데이터 전송
    + 신뢰할 수 있는 프로토콜 (Reliable Protocol)
        * 흐름 제어 (Flow Control)
            - 송신측 버퍼가 500 Byte, 수신측 버퍼가 100 Byte 일 때 수신측 버퍼가 받을 수 있는 데이터만큼 전송
            - 슬라이딩 윈도우 (Sliding Window)
                + 기본
                    * 송신 측에서 수신 측에 Segment 를 전송
                    * 수신 측은 송신 측에 Ack (데이터 받았음) 을 전송
                    * 반복 -> RTT (Round Trip Time : 왕복시간) 가 증가함
                + 수신측 Window Size 에 기반한 전송
                    * 수신 측 Window Size 에 기반해 Ack 확인 없이 연속적으로 데이터를 전송
                    * Throughtput 증가
        * 오류 제어 (Error Control)
            - 오류, 누락(손실) 이 발생할 경우 재전송 (Retransmission) 수행
        * 혼잡 제어 (Congestion Control)
            - 네트워크가 혼잡하여 효율이 떨어질 경우
            - 네트워크 혼잡도에 따라 Data 의 양을 조절
            - 네트워크 혼잡도 판단 기준
                + Data 의 손실 유무로 판단

- TCP 헤더 (20~60Bytes)
    + Source Port (16Bit) : 출발지 포트
    + Destination Port (16Bit) : 목적지 포트
    + Sequence Number (32Bit) : 시작 바이트 순번
        * ISN (Initial Sequence Number) : 초기순서번호, 송/수신 측에서 연결할 때 미리 주고받음
        * ISN + 전송한 데이터의 바이트
        * 전송한 데이터가 없을 경우 1씩 증가
    + Acknowledgement Number (32Bit) : 상대방이 다음에 전달할 순서번호, ACK 와 함께 보냄
    + Data Offset(HLEN) : 20~60Byte 에 해당하는 값
    + Reserved : 예약, 사용안함
    + Control Flags (6Bit)
        * UAPRSF
            -  SYN + ACK : 010010
        * URG : 긴급데이터
        * ACK : 수신확인응답
        * PSH : 송수신 버퍼에 있는 Data 를 즉시 처리
        * RST : Reset the connection - 연결 강제종료
        * SYN : Synchronize Sequence Number - 시퀀스 넘버 동기화 / 연결설정 
        * FIN : 연결 정상종료
    + Window : Window Size - 수신받을 수 있는 버퍼 공간
        * 수신측에서 송신측이 알 수 있도록 보내는 리시버 윈도우 사이즈
        * Window Sliding 제어방식에 쓰임
    + Checksum : 헤더를 포함한 전체 Segment 에 대한 오류검사에 활용
    + Urgent Pointer : Segment 가 긴급 데이터를 포함하는 경우 긴급 데이터의 위치값
        * 사용하기 위해서 URG Flag 가 설정되어야 함.

- TCP 의 MSS 
    + IP 의 경우 Fragment 의 단편화 기준 MTU (Maximum Transmission Unit)
        * IP Header + IP Payload
    + TCP 의 경우 Segment 의 분할 (Segmentation) -> 재조합 (Reassembly)
        * MSS : Maximum Segment Size
        * TCP 헤더를 제외한 TCP Payload 부
        * 크기값은 MTU 환경에 영향을 받게 됨

- TCP 연결
    + 논리적 연결 설정 -> 데이터 전송 (송수신) (재전송 포함) -> 연결 종료
    + ACK 의 의미 : 다음에 받을 SYN No
    + 연결 설정 : 3-Way Handshake (논리적 연결설정 과정)
        * Client : Active Open 상태
        * Server : Passive Open 상태
        * Client : SYN 패킷 전송 
            - ISN 포함 = Seq No:100
            - Window = Receiver Window Size (RWND) : 65535
        * Client : SYN_SENT
        * Server : SYN_RCVD (SYN_RECEIVED, SYN_RECV)
        * Server : SYN + ACK 패킷 전송 (010010)
            - ISN 포함 = Seq No:200
            - ACK : 101
            - Window = Receiver Window Size (RWND) : 65535
        * Client : ESTABLISHED (연결설정완료)
        * Client : ACK 패킷 전송
            - Seq : 101
            - ACK : 201
            - RWND : 65535
        * SERVER : ESTABLISHED (연결설정완료)
    + TCP 데이터 송수신
        * Client : ESTABLISHED 상태
        * Server : ESTABLISHED 상태
        * Client : ACK + PSH (011000)
            - Seq : 102 (3-Way Handshake 에 이어서)
            - ACK : 201
            - Data : 102~300 (Byte 순번) 에 해당하는 내용
        * Client : ACK + PSH (011000)
            - Seq : 301 (3-Way Handshake 에 이어서)
            - ACK : 201
            - Data : 301~400 (Byte 순번) 에 해당하는 내용
        * Server : ACK
            - Seq : 201
            - ACK : 401
            - Data : 201~300
        * Client : ACK
            - Seq : 401
            - ACK : 301
            - RWND : 2000
    + TCP 재전송 (Retransmission) 과정
        * Client : A Data 전송 
            - RTO : Retransmission Time Out - A Data 재전송 Timer 동작
        * Client : B Data 전송 (Window Sliding)
            - RTO : Retransmission Time Out - B Data 재전송 Timer 동작
        * (B Data 전송 실패)
        * Server : A Data ACK 전송
        * (Client : B Data RTO 초과)
        * Client : B Data 재전송
            - RTO 가 지나 재전송하는 경우 네트워크가 매우 혼잡한 상태로 판단
            - 혼잡 제어 (Congestion Control) 와 연관
        * Server : B Data ACK 전송
    + 빠른 재전송 (Fast Retransmission) 과정
        * Client : A Data 전송
        * Client : B Data 전송 (Window Sliding)
        * (B Data 전송 실패)
        * Server : A Data ACK 전송
        * Client : C Data 전송 (Window Sliding)
        * Server : B Data 요청 ACK 전송 
            - C Data 는 Seq no 가 맞지 않음.
        * Client : D Data 전송 (Window Sliding)
        * Server : B Data 요청 ACK 전송
        * Client : E Data 전송 (Window Sliding)
        * Server : B Data 요청 ACK 전송
        * (B Data 요청 ACK 3번 중복)
        * Client : B Data 재전송
            - 재전송보다 덜 혼잡한 상태로 판단\
    + 연결종료
        * 3 Way Handshake
            - 대게는 클라이언트 요청에 의해서 연결 종료
            - Client : Active Close 상태
            - Server : Passive Close 상태
            - Client : FIN 패킷 전송 (000001)
                + Seq : xx
                + ACK : yy
            - Server : FIN + ACK 패킷 전송 (010001)
                + Seq : yy
                + ACK : xx+1
            - Client : CLOSE 상태
            - Client : ACK 패킷 전송
                + Seq : xx+1
                + ACK : yy+1
            - Server : CLOSE 상태
        * 4 Way Handshake (절반폐쇄/절반종료 방식)
            - Client : Active Close 상태
            - Server : Passive Close 상태
            - Client : FIN + ACK 패킷 전송 (010001)
                + Seq : xx
                + ACK : yy
            - Client : FIN_WAIT_1 상태
            - Server : ACK 패킷 전송 (010001)
            - Server : CLOSE_WAIT 상태
            - Client : FIN_WAIT_2 상태
            - Server : 서버 APP 이 TCP 연결 종료
            - Server : FIN + ACK 패킷 전송 (010001)
                + Seq : yy
                + ACK : xx+1
            - Server : LAST_ACT 상태
            - Client : TIME_WAIT 상태
            - Client : (일정시간 경과-2MSL 후) CLOSE 상태
            - Client : ACK 패킷 전송
                + Seq : xx+1
                + ACK : yy+1
            - Server : CLOSE 상태
    + 연결 요청 거부
        * Client : Active Open 상태
        * Client : SYN 패킷 전송
        * Client : SYN_SENT 상태
        * Server 가 Listen (포트 대기상태) 상태가 아님
        * Server : RST+ACK (010100) 패킷 전송
        * Client : CLOSED 상태
    + 연결중단 (Abort)
        * Client : ESTABLISHED 상태
        * Server : ESTABLISHED 상태
        * Client : RST+ACK (010100) 패킷 혹은 RST(000100) (상대방에게 받은 패킷에 Ack Number 필드가 설정됨) 전송
        * Server : CLOSED 상태

- Port
    + 전송계층 (TCP, UDP)
        * Process to Process 통신
        * 프로세스를 식별하기 위해 포트 사용
    + 분류방식
        * 사용목적에 따른 포트범위 
            - Well-Known Port : 예약된 포트 (0~1023)
                + FTP (21,22)
                + Telnet (23)
                + HTTP (80)
            - Registered Port : 제조사에서 IANA(Internet Assigned Number Authority) 에 용도를 등록 (1024~49151)
                + MySQL (3306)
                + Oracle (1521)
                + MSSQL (1433)
            - Dynamic Port : 동적으로 사용, Client (49152~65535)
        * 사용권한에 따른 포트범위
            - 관리자 포트 (Privileged Port) (0~1023)
            - Unprivileged Port (2014~65535)

# TCP Session Hijacking

- 연결설정을 위해 주고 받는 정보를 스니핑
- 중간에서 값을 스푸핑 하여 전송

