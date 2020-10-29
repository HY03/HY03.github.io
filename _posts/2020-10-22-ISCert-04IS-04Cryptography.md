---
title: 04.정보보안 일반 - 04.암호학
date: '2020-10-28 10:00:00'
tags:
- 정보보안기사
- 정보보안 일반
- 암호학
related: true
categories:
- IS_Certification
toc: true
---

# 암호화
## 1. 시저암호와 암호화의 개요
+ 시저암호(Caesar Cipher) : 최초의 암호화, 알파벳을 일정한 문자 수만큼 평행 이동을해서 암호화
+ 평문

|1|2|3|4|5|6|7|8|9|10|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|A|B|C|D|E|F|G|H|I|J|

+ 암호문

|1|2|3|4|5|6|7|8|9|10|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|H|I|J|A|B|C|D|E|F|G|

+ 전사공격
    * 시저암호의 경우 영문자 26자에 대해서 평문과 암호문을 모두 나열해 보면 바로 해독됨
    * 모든 범위의 평문과 암호문을 나열하여 암호화를 해독하는 것을 전사공격이라 함.

## 2. 암호화(Encryption)
+ 암호화
    * 평문을 암호화키를 사용해서 암호문으로 만들고, 암호문을 복호화키를 사용해서 평문으로 만드는 일련의 과정
+ 암호화와 복호화 과정
  
    ![Symmetric Encryption](/assets/images/posts/symmetric.gif)

    [이미지 출처](https://ico.org.uk/for-organisations/guide-to-data-protection/guide-to-the-general-data-protection-regulation-gdpr/encryption/what-types-of-encryption-are-there/)

    * 평문(Plaintext)을 암호화키(Encryption)를 사용해서 암호문(Ciphertext)으로 만들고 복호화키(Decryption Key)를 사용해서 평문으로 복원하는 과정
+ 암호화 세부 내용

    |구분|특징|
    |:---|:---|
    |암호학(Cryptology)|{::nomarkdown}<ul><li>암호 기법(Cryptography) : 암호화와 복호화의 원리, 절차 및 방법론에 관한 학문</li><li>암호 해독(Cryptanalysis) : 암호문으로부터 복호화 키를 찾아내거나 암호문을 평문으로 복원하려는 노력 또는 그에 관한 학문</li></ul>{:/}|
    |평문(Plaintext)|일반인이 이해할 수 있는 형태의 정보|
    |암호문(Ciphertext)|평문을 이해할 수 없는 형태로 변형한 문장|
    |암호화(Encryption)|비밀성 보장을 위해 암호 알고리즘에 의해 평문을 암호문으로 바꾸는 과정|
    |복호화(Decryption)|암호화된 문장을 평문으로 바꾸는 과정|
    |알고리즘|특수한 순서로 평문에 적용되는 복잡한 수학공식|

## 3. 치환 암호(Substitution Cipher)
+ 암호화 역사

    |구분|특징|
    |:---|:---|
    |고전 암호|{::nomarkdown}<ul><li>단순한 문자 대입 방법으로 통계적 특성을 분석하여 암호문 해독이 가능했음</li><li>대표적 암호 : 시저 암호, Vigenere(비제네르, 비즈네르) 암호</li></ul>{:/}|
    |근대 암호|{::nomarkdown}<ul><li>기계를 이용하여 암호 알고리즘을 실현</li><li>대표적 암호 : ENIGMA(평문을 자판으로 입력하면 각 회전자에 의해 암호문 변환)</li></ul>{:/}|
    |현대 암호|{::nomarkdown}<ul><li>1940년 말 Claude elwood shannon(클로드 섀넌)의 정보 이론에 의해 현대 암호학 시작</li><li>다양한 이론에 의해 복잡도가 높은 암호 알고리즘의 실현</li></ul>{:/}|

+ 시저암호는 전사공격에 취약하므로 단일 치환 암호(Simple Substitution Cipher) 가 등장
    * 어떻게 변환하는지를 알 수 있는 치환표가 있어야 함.
    * 복호화도 치환표를 사용해서 거꾸로 복호화
    * 빈도수 공격에 취약
+ 다중 치환 암호(Polyalphabetic Substitution Cipher)
    * 단일 치환 암호의 빈도수 공격의 문제점을 해결하기 위해 암호문에 나타나는 빈도수를 거의 균등하게 만드는 암호화 기법
    * 비제네르(Vigenere) 및 힐(Hill) 암호화

## 4. 다중 치환 암호 힐(Hill) 암호화
+ 개요
    * 평문의 문자에 해당 정수 값을 부여하고 m개의 문자를 치환하는 암호화 방법
+ 힐 암호화
    * k는 암호화 키, p는 평문, C는 암호문, mod는 나머지 계산 (알파벳이 26개이므로 26으로 나머지를 계산)
    * C1 = (k11 p1 + k12 p2 + k13 p3) mod 26
    * C2 = (k21 p1 + k22 p2 + k23 p3) mod 26
    * C3 = (k31 p1 + k32 p2 + k33 p3) mod 26

## 5. 암호화 기법
+ 암호화 기법

    |구분|유형|특징|
    |:---|:---|:---|
    |고대|치환(Substitution)|{::nomarkdown}<ul><li>문자열을 다른 문자열로 이동하면서 교체</li><li>평문을 추론하기 어렵게 만듦</li></ul>{:/}|
    ||전치(Transposition) 순열(Permutation)|{::nomarkdown}<ul><li>무작위로 보이는 원칙에 따라서 문자의 순서를 바꾸는 것</li><li>확산의 성질로 암호문을 퍼뜨려 숨김</li></ul>{:/}|
    |현대|대칭키 암호화|송/수신자의 키가 동일한 암호화 방식|
    ||공개키 암호화|암호화 키와 복호화 키가 다른 암호화 방식|
    ||타원곡선암호(ECC)|{::nomarkdown}<ul><li>Elliptic Curve Cryptography</li><li>공개키 암호 시스템의 큰 키값을 이용해야 하는 단점을 보완</li></ul>{:/}|
    ||양자암호|{::nomarkdown}<ul><li>Quantum Cryptography, 현재 활발한 연구 중</li><li>이론적으로만 존재하는 것으로 여기던 완벽한 암호 시스템</li></ul>{:/}|

        - 근대암호에서 애니그마는 독일의 세르비우스에 의해서 개발됨
            + 송신자와 수신자는 애니그마 기계를 한 대씩 가지고 있어야 함
            + 송신자와 수신자 모두 코드북이 있어야 함
            + 코드북은 날짜 키를 가지고 있음

+ Claude Shannon의 Information Theory(정보이론)
    * 혼돈 : 암호문과 평문과의 상관관계를 숨김, 대치를 통해 구현함
    * 확산 : 평문의 통계적 성질을 암호문 전반에 퍼뜨러 숨김, 전치로 구현, 평문과 암호화 키의 각 Bit들은 암호문의 모든 Bit에 영향을 주어야 함.
+ 암호화가 해독되는 이유
    * 암호화 알고리즘이 공개된 경우
    * 해당 문자의 치우침에 따라 통계가 가능할 경우
    * 해당암호에 대한 예문을 많이 보유하고 있는 경우

# 스트림(Stream) 암호화
## 1. 스트림 암호화
+ 개념
    * 비트 혹은 바이트 단위로 암호화를 수행
    * 암호화와 복호화에 동일한 키 스트림 생성기를 사용
    * 고속으로 암호화할 수 있고 경량임 -> 무선 환경, 고속으로 동작하는 시스템에 적용
    * 하드웨어로 구현이 용이
+ 무선 LAN의 WEP에서 사용하는 RC4 암호화

    ![WEP RC4](/assets/images/posts/Wep-crypt-alt.svg.png)

    [이미지 출처](https://ko.wikipedia.org/wiki/%EC%9C%A0%EC%84%A0_%EB%8F%99%EB%93%B1_%ED%94%84%EB%9D%BC%EC%9D%B4%EB%B2%84%EC%8B%9C)

    * 정적키에서 난수(셔플링 기법-256개의 바이트 수를 섞은 후 256 바이트 중 하나를 선택)로 임의의 키를 선택해서 키 스트림을 결정
    * 송신자와 수신자 사이에 키 스트림을 사전에 교환
    * 메시지를 비트(Bit)로 변경하고 키 스트림과 XOR 하여 암호화
    * 암호화키 유출에 주의

## 2. 동기식 스트림 암호
+ Gilbert Vernam이 개발한 One Time Pad
+ 랜덤 번호를 생성해서 송신자와 수신자가 랜덤 키를 교환

## 3. 비동기식 스트림 암호
+ 키 스트림을 생성할 때 이전의 평문 및 암호문에 종속적으로 생성

# 블록 암호화
## 1. 블록 암호화 알고리즘 구조
+ 사전에 공유한 암호키를 사용해서 고정된 길이의 입력 블록을 고정된 길이의 출력 블록으로 변환하는 알고리즘
+ 암호화를 수행하기 전에 평문을 고정된 블록으로 나누어 각각 암호화를 수행
+ Feistel 구조
    * 암호화와 복호화 과정이 동일
    * 역변환이 가능한 방법
    * 하드웨어 및 소프트웨어로 구현이 쉬움
    * DES, SEED, CAST-128, BLOWFISH
+ SPN(Substitution Permutation Network) 구조
    * 혼돈과 확산 이론에 기반
    * 암호화 과정과 복호화 과정이 다름
    * AES, SAFER, SHARK, CRYPTON
+ 기타 구조
    * IDEA

## 2. **블록 암호화 기법(Block Cipher)**
### 1. ECB(Electronic Code Book) Mode

![ECB_Enc](/assets/images/posts/Ecb_encryption.png)

[이미지 출처](https://ko.wikipedia.org/wiki/%EB%B8%94%EB%A1%9D_%EC%95%94%ED%98%B8_%EC%9A%B4%EC%9A%A9_%EB%B0%A9%EC%8B%9D#%EC%A0%84%EC%9E%90_%EC%BD%94%EB%93%9C%EB%B6%81_(ECB))

* 가장 단순한 모드
* 평문을 일정한 블록단위로 나누어 순차적으로 암호화하는 구조
* DES : 64Bit, AES : 128Bit
* 각각의 블록은 독립적 -> 특정 블록의 에러가 다른 블록에 영향을 주지 않음
* 평문을 각각의 단위로 나눌 때, 배수에 미치지 못하여 남는 Bit는 패딩(Padding, 빈 데이터)을 추가하여 크기를 맞춰야 함

![ECB_Result](/assets/images/posts/Tux_ecb.jpg)

[이미지 출처](https://ko.wikipedia.org/wiki/%EB%B8%94%EB%A1%9D_%EC%95%94%ED%98%B8_%EC%9A%B4%EC%9A%A9_%EB%B0%A9%EC%8B%9D#%EC%A0%84%EC%9E%90_%EC%BD%94%EB%93%9C%EB%B6%81_(ECB))

* 한 개의 블록만 해독이 되면 나머지 블록 또한 해독되는 단점

### 2. CBC(Cipher Block Chaining) Mode

![CBC_Enc](/assets/images/posts/Cbc_encryption.png)

[이미지 출처](https://ko.wikipedia.org/wiki/%EB%B8%94%EB%A1%9D_%EC%95%94%ED%98%B8_%EC%9A%B4%EC%9A%A9_%EB%B0%A9%EC%8B%9D#%EC%A0%84%EC%9E%90_%EC%BD%94%EB%93%9C%EB%B6%81_(ECB))

* ECB 모드와 동일한 방법으로 평문을 일정한 블록단위로 나눔
* 최초 키의 생성 버퍼로 IV(Initialization Vector)가 사용되어 첫 번째 블록과 XOR 연산을 통해 암호화가 됨
* IV는 나누어진 일정한 블록 중 하나가 되거나 단위 블록과 길이가 같은 임의의 값이 될 수 있음
* 두 번째 블록부터는 첫 번째 블록의 암호화된 블록과 XOR 연산을 하여 암호화가 진행됨
* 암호문 한 개의 블록에서 에러 발생 시 현재 복호화 되는 평문 블록과 다음 복호화되는 평문 블록에 영향을 줌
* 블록 암호화 모드 중 보안이 가장 강력한 암호화 모드로 평가되며 가장 많이 사용
* ECB와 동일하게 배수를 맞추기 위한 패딩을 추가하여 크기를 맞춰야 함.

![CBC_Dec](/assets/images/posts/Cbc_decryption.png)

[이미지 출처](https://ko.wikipedia.org/wiki/%EB%B8%94%EB%A1%9D_%EC%95%94%ED%98%B8_%EC%9A%B4%EC%9A%A9_%EB%B0%A9%EC%8B%9D#%EC%A0%84%EC%9E%90_%EC%BD%94%EB%93%9C%EB%B6%81_(ECB))

* 암호화 및 복호화 시 병렬처리가 불가능하여 순차적으로 암호화해야 한다는 단점을 가지고 있음.

### 3. CFB(Cipher FeedBack) Mode

![CFB_Enc](/assets/images/posts/Cfb_encryption.png)

[이미지 출처](https://ko.wikipedia.org/wiki/%EB%B8%94%EB%A1%9D_%EC%95%94%ED%98%B8_%EC%9A%B4%EC%9A%A9_%EB%B0%A9%EC%8B%9D#%EC%A0%84%EC%9E%90_%EC%BD%94%EB%93%9C%EB%B6%81_(ECB))

* ECB, CBC와 달리 평문과 암호문의 길이가 같음.
* 패딩을 추가하지 않고 블록단위 암호화를 스트림 암호화 방식으로 구성하여 Bit 단위로 암호화를 수행 (패딩이 필요 없음, 암호문에 대해서 암호화를 반복하면 평문이 됨) 하기 때문
* CBC와 마찬가지로 IV가 사용되고, 암호화는 순차적으로 처리하며 복호화는 병렬처리가 가능

![CFB_Dec](/assets/images/posts/Cfb_decryption.png)

[이미지 출처](https://ko.wikipedia.org/wiki/%EB%B8%94%EB%A1%9D_%EC%95%94%ED%98%B8_%EC%9A%B4%EC%9A%A9_%EB%B0%A9%EC%8B%9D#%EC%A0%84%EC%9E%90_%EC%BD%94%EB%93%9C%EB%B6%81_(ECB))

* 암호문 한 개의 블록에서 에러 발생 시 현재 복호화 되는 평문 블록과 다음 복호화되는 평문 블록에 영향을 줌

### 4. OFB(Outer FeedBack) Mode

![OFB_Enc](/assets/images/posts/Ofb_encryption.png)

[이미지 출처](https://ko.wikipedia.org/wiki/%EB%B8%94%EB%A1%9D_%EC%95%94%ED%98%B8_%EC%9A%B4%EC%9A%A9_%EB%B0%A9%EC%8B%9D#%EC%A0%84%EC%9E%90_%EC%BD%94%EB%93%9C%EB%B6%81_(ECB))

* CFB와 동일하게 평문과 암호문의 길이가 같음
* 패딩을 추가하지 않고 블록단위 암호화를 스트림 암호화 방식으로 구성
* 암호화 함수는 키의 생성시에만 사용됨

![OFB_Dec](/assets/images/posts/Ofb_decryption.png)

[이미지 출처](https://ko.wikipedia.org/wiki/%EB%B8%94%EB%A1%9D_%EC%95%94%ED%98%B8_%EC%9A%B4%EC%9A%A9_%EB%B0%A9%EC%8B%9D#%EC%A0%84%EC%9E%90_%EC%BD%94%EB%93%9C%EB%B6%81_(ECB))

* 암호화와 복호화의 방법이 동일, 암호문을 다시 암호화하면 평문이 나옴
* 최초 키의 생성 버퍼로 IV가 사용됨
* 암호문 한 개의 블록에서 에러 발생 시 현재 복호화되는 평문 블록에만 영향을 줌
* 영상 데이터, 음성 데이터와 같은 디지털화된 아날로그(Digitized analog) 신호에 주로 사용됨.

### 5. CTR(CounTeR) Mode

![CTR_Enc](/assets/images/posts/Ctr_encryption.png)

[이미지 출처](https://ko.wikipedia.org/wiki/%EB%B8%94%EB%A1%9D_%EC%95%94%ED%98%B8_%EC%9A%B4%EC%9A%A9_%EB%B0%A9%EC%8B%9D#%EC%A0%84%EC%9E%90_%EC%BD%94%EB%93%9C%EB%B6%81_(ECB))

* 평문블록과 키스트림을 XOR 연산하여 암호문을 만듦
* 키 스트림은 암호화 시마다 1씩 증가하는 카운터를 암호화한 Bit열
* 암호화와 복호화 방법이 동일하므로 구현이 간단
* 블록의 암호화 순서가 임의의 순서가 될 수 있음 -> 병렬처리 가능
* OFB와 마찬가지로 암호문 블록의 에러 발생 시, 한 개의 평문 블록에만 영향을 줌.

# 암호화 알고리즘의 종류
## 1. DES(Data Encryption Standard)
+ DES(Data Encryption Standard)
    * IBM에서 개발한 대칭키 암호화 알고리즘
    * 1997년 미국 표준국(NIST)에서 표준으로 채택
+ DES 특징
    * 64Bit 블록단위 암호화를 수행하며 56Bit 키를 사용
    * 키 길이가 짧다는 문제점을 가짐
    * 64Bit의 평문과 키를 입력받아 64Bit 암호문을 생성
    * 56Bit에서 8Bit가 늘어난 이유는 7Bit 마다 Check Bit를 넣었기 때문
    * 치환 암호(Substitution Cipher)와 전치 암호(Transposition Cipher)를 혼합한 혼합 암호(Product Cipher)를 사용
    * DES는 내부에서 XOR, 자리바꿈, 순환이동, 치환 등을 사용하고 S-BOX를 제외하고 나머지 연산은 모두 선형임.
    * 즉, S-BOX를 제외하고 나머지는 역으로 취할 수 있으므로 DES의 안전성에서 S-BOX가 가장 중요한 부분이다.

    ![Feistel](/assets/images/posts/Feistel_cipher_diagram_en.svg.png)

    [이미지 출처](
https://en.wikipedia.org/wiki/Feistel_cipher#/media/File:Feistel_cipher_diagram_en.svg)

    * Feistel 구조이다.
+ DES 암호화 및 복호화의 혼합 암호화(Product Cipher)

    ![DES Structure](/assets/images/posts/des_structure.jpg)

    [이미지 출처](https://www.tutorialspoint.com/cryptography/data_encryption_standard.htm)

    * DES는 64Bit의 블록을 입력 받아 56Bit의 키 (실제로는 패리티 Bit를 넣어 64Bit)로 암호화해서 64Bit의 블록을 출력한다.

    ![Permutation](/assets/images/posts/initial_and_final_permutation.jpg)

    [이미지 출처](https://www.tutorialspoint.com/cryptography/data_encryption_standard.htm)

    * 입력 받은 64Bit를 초기 순열을 이용해서 재배열한다. 초기 순열에는 전치할 순서가 들어있다. 예를 들어 1번 자리는 45번으로, 34번은 4번으로 등의 순서가 저장된다. 그리고 치환되어 나온값은 키를 이용해 XOR 연산과 Feistel 연산을 거쳐서 변환한다.
    * 역 순열을 이용해 다시 재배열한다. 이것이 1라운드이며 16번 반복한다. 즉, 총 16라운드를 수행한다.
    * 현재의 암호화는 대칭키 암호화를 위해서 키의 길이는 128Bit 이상으로 사용한다.

## 2. IDEA(International Data Encryption Algorithm)
+ 스위스에서 개발한 대칭키 알고리즘으로 빠른 암호화 방법을 지원한다.
+ 128Bit의 키를 사용해서 64Bit의 평문을 8라운드에 걸쳐서 64Bit 암호문으로 생성한다.
+ DES 방식의 암호화 기법보다 2배 정도 빠르게 암호화를 수행한다.
+ 16Bit 단위로 연산이 이루어지며 16Bit 프로세서(Processor)에서 구현이 용이하다.
+ 안전성을 인정받아서 전자우편 보안을 위한 PGP 방식에 사용된다.
+ 블록암호화 알고리즘을 사용하고 하드웨어와 소프트웨어로 구현이 용이하다.

## 3. RC5(Ron's Code 5) 알고리즘
+ 1994년 미국 RSA 연구소의 리베스트(Rivest)가 개발한 알고리즘이다.
+ 알고리즘이 간단하여 빠르게 암호화하고 복호화할 수가 있으며 DES보다 약 10배 정도빠른 속도를 가진다.
+ 32Bit, 64Bit, 128Bit의 키를 가진다.

## 4. AES(Advanced Encryption Standard)
+ AES(Advanced Encryption Standard)
    * Rijindael 알고리즘

    ![SPN](/assets/images/posts/SubstitutionPermutationNetwork2.png)

    [이미지 출처](https://en.wikipedia.org/wiki/Substitution%E2%80%93permutation_network)
    
    * SPN 구조
    * 미국 연방표준 알고리즘
    * DES를 대신하는 차세대 표준 암호화 알고리즘
    * 미국 상무성 산하 NIST(National Institute of Standards and Technology) 표준 알고리즘
    * 블록길이는 128Bit, 192Bit, 256Bit 의 3종류로 구성됨
+ AES 특징
    * 암호화 및 복호화가 빠르고 공격에 대해서 안전하다.
    * 간단한 하드웨어 및 소프트웨어 구성이 편리하다.
    * 이론적으로 키의 크기는 제한이 없다.

## 5. SEED
+ SEED 개념
    * KISA와 ETRI에서 개발
    * TTA와 ISO/IEC에서 국제표준으로 제정된 128Bit 키 블록 단위로 메시지를 처리하는 대칭키 블록 암/복호화 알고리즘
+ SEED 특징

    |구분|특징|
    |:---|:---|
    |키 길이|128Bit 고정 키 사용|
    |블록 암호화|128Bit 길이 블록단위 암호화, 16Round|
    |암호화 방식|DES와 같은 Feistel(전치, 치환, XOR 사용)|
    |운영 모드|일반적 블록 암호화 운영 모드(ECB, CBC, CFB, OFB)|

+ SEED 세부 내용

    |구분|특징|
    |:---|:---|
    |평문 블록화|128Bit 평문을 64Bit씩 Lo, Ro 블록으로 나눔|
    |F 함수|64Bit Fiestel 형태로 구성(16Round)<br/>입력:64Bit 블록과 64Bit 라운드 키, 출력:64Bit 블록 출력|
    |암호문|암호화된 64Bit 블록 조합하여 128Bit 암호문 출력|

## 6. 대칭키 암호화 기법 차이점
### 1. 대칭키 암호화 기법

|구분|블록 크기|키 크기|Round|주요 내용|
|:---|:---|:---|:---|:---|
|DES|64Bit|56Bit|16|키 길이가 짧아 해독이 용이|
|3DES|64Bit|168Bit|48|DES의 Round 수를 늘려 보안성을 강화|
|AES|128Bit|128/192/256Bit|10/12/14|미국 표준 암호화 알고리즘|
|IDEA|64Bit|128Bit|8|암호화 강도가 DES 보다 강하고, 2배 빠름|
|SEED|128Bit|128Bit|16|국내에서 개발, ISO/IEC, IETF 표준|

### 2. 대표적인 대칭키 암호화 기법

|구분|SEED|3DES|AES|
|:---|:---|:---|:---|
|특징|안전성, 빠른 암호화 속도|DES 호환성, 느린 암호화 속도|안정성, 효율성, 구현 용이성|
|키 길이|128Bit|168Bit|128Bit, 192Bit, 256Bit|
|Block|128Bit(16Round)|64Bit(16*3 Round)|3Layer 기반 Round 구조|
|개발 기관|KISA, ETRI(국제표준)|IBM(레거시 시스템 표준)|Rijndael(미국 NIST 표준)|

### 3. 국내에서 개발한 암호화 알고리즘 종류
* HIGHT
    - HIGh security and light weigHT
    - RFID, USN 등과 같이 저전력, 경량화를 요구하는 컴퓨팅 환경에서 기밀성을 제공
    - 64Bit 블록 암호화 알고리즘
    - KISA, ETRI, 고려대학교 공동 개발
* ARIA
    - 경량 환경 및 하드웨어 구현을 위해서 최적화된 Involutional SPN 구조를 가지는 범용 블록 암호화 알고리즘
    - 128Bit 블록
    - 128/192/256 Bit 키 길이
* LEA
    - Lightweight Encryption Algorithm
    - 빅데이터, 클라우드 등 고속 환경 및 모바일 기기 등 경량 환경에서 기밀성 제공
    - 128Bit 블록 암호화 알고리즘
* LSH 해시함수
    - 메시지 인증, 사용자 인증, 전자서명 등 다양한 암호 응용분야에서 활용 가능한 암호학적 해시함수

# 암호분석 방법
## 1. **암호분석 방법의 종류**

|공격|주요 설명|
|:---|:---|
|암호문 단독 공격 (COA, Ciphertext only Attack)|{::nomarkdown}<ul><li>암호 해독자에게 가장 불리한 방법</li><li>공격자는 단지 암호문만을 가지고 공격</li><li>암호문으로부터 평문이나 암호키를 찾아내는 방법으로 통계적 성질과 문장의 특성등을 추정하여 해독</li></ul>{:/}|
|알려진 평문 공격 (KPA, Known Plaintext Attack)|{::nomarkdown}<ul><li>암호문에 대응하는 일부 평문이 가용한 상황에서의 공격, 선형 공격</li><li>공격자는 약간의 평문에 대응하는 암호문을 가지고 있는 상태에서 나머지 암호문에 대한 공격을 하는 방법으로 이미 입수한 암호문의 관계를 이용하여 새로운 암호문을 해독하는 방법</li></ul>{:/}|
|선택 평문 공격 (CPA, Chosen Plaintext Attack)|{::nomarkdown}<ul><li>평문을 선택하면 대응되는 암호문을 얻을 수 있는 상황에서의 공격</li><li>공격자가 사용된 암호기에 접근할 수 있을 때 사용하는 공격 방법</li><li>적당한 평문을 선택하여 그 평문에 대응하는 암호문을 얻을 수 있음</li></ul>{:/}|
|선택 암호문 공격 (CCA, Chosen Ciphertext Attack)|{::nomarkdown}<ul><li>암호문을 선택하면 대응되는평문을 얻을 수 있는 상태에서의 공격</li><li>적당한 암호문을 선택하고 그에 대응하는 평문을 얻을 수 있다.</li></ul>{:/}|

# 디피헬먼(Diffie-Hellman) 키 교환 알고리즘
## 1. Diffie-Hellman 키 교환 알고리즘 개념 및 특징
+ 최초의 공개키 암호화 알고리즘
+ 1976년 미국 스탠퍼드 대학 연구원 W.Diffie와 M.Hellman이 공동으로 개발한 암호화 방식
+ 공개키는 1개의 정수와 1개의 소수로 통신 직전에 통신하고자 하는 상대방과 공유
+ 다른 비밀키 전용의 숫자는 양쪽에서 각각 보유하도록 하여 이들과 공개키의 수치를 사용하여 공통 암호키용 수치를 산출하는 방식
+ IPSEC에서 IKE(Inter Key Exchange)의 디폴트 키 교환 알고리즘으로 채택됨
+ 중간자 공격(MITM, Man-in-the-middle) 공격에 취약한 문제점을 가짐

## 2. Diffie-Hellman 키 교환 과정
+ 송신자 A는 소수 P, 그리고 1부터 p-1까지의 정수 g를 선택하여 사전에 수신자 B와 공유
+ 송신자 A는 정수 a를 선택(정수 a는 외부 미공개, 수신자 B도 알 수 없음)
+ 송신자 A는 g^a mod p, 즉 g^a를 p로 나눈 나머지를 계산
+ 수신자 B도 마찬가지로 정수 b를 선택, B=g^b mod p 를 계산
+ 송신자 A와 수신자 B가 서로에게 A와 B를 전송
+ 송신자 A가 B^a mod p, 수신자 B가 A^b mod p를 계산
+ 마지막 단계에서 B^a = (g^b)^a = (g^a)^b, A^b = (g^a)^b = g^ab 이며, 따라서 K=g^ab mod p 라는 공통의 비밀키가 공유됨

# 혼합 암호화
## 1. 대칭키 암호화 기법과 공개키(비대칭키) 암호화 기법의 장/단점
+ 대칭키 암호화 기법
    * 장점 : 공개키 암호화 기법에 비해서 속도가 빠름
    * 단점 : 송수신자 중 누군가가 암호화키를 유출하면 모든 암호문이 복호화됨
+ 공개키 암호화 기법
    * 장점 : 암호화와 복호화 키가 다르므로 유출의 문제 해결 
    * 단점 : 키 길이가 길고 속도가 느림
+ 혼합 암호화
    * 대칭키 암호화와 공개키 암호화의 장점을 결합
        - 속도가 빠르다.
        - 암호화 키와 복호화 키가 다르다.
    * SSL, IPSEC, 전자서명 등 거의 모든 부분에서 사용

## 2. 문제 해결
+ 혼합 암호화의 단점
    * 어떤 암호화 기법도 동일한 암호화키를 계속 사용하면 평문과 암호문간의 관계를 파악할 수 있어 문제가 됨
    * 의도적인 암호화 키 유출에서 자유롭지 않음
+ 세션 키
    * 연결할 때마다 새롭게 생성된 키를 암호화 키로 사용
    * 대칭키로 사용

    ![Session Key](/assets/images/posts/session_key.png)

    [이미지 출처](https://docs.microsoft.com/en-us/windows/win32/seccrypto/manual-session-key-exchanges)

+ 상호 인증
    * 세션 키 공유도 중간자 공격에서 자유롭지 않음
    * 인증기관이 클라이언트 인증서와 서버 인증서를 발급하고 인증서는 정당한 사용자만 확인할 수 있도록 함
    * 인증기관에 등록하고 등록 이후 인증서를 확인할 수 있는 키를 받음
    * 상대방의 정보(웹브라우저, IP 등)와 시간 정보를 입력 값으로 하여 난수를 만들고, 난수를 세션키로 사용 (난수발생기 활용)
  
# **RSA(Rivest, Shamir, Adelman)**
## 1. RSA 개념 및 특징
+ 1976년 공개키 암호 방식의 개념 등장 이후에 1978년 Rivest, Shamir, Adelman 이 개발한 암호화 방식
+ 대표적인 공개키 암호화 방식으로 De-Facto Standard(산업표준)이다.
+ 소인수분해의 어려움을 기반으로 안정성을 제공한다.
+ 암호화 및 디지털 서명 용도로 사용이 가능하다.

## 2. RSA 암호화 및 복호화 과정

![RSA](/assets/images/posts/rsa.png)

[이미지 출처](https://hackernoon.com/how-does-rsa-work-f44918df914b)

A와 B가 보안이 보장되어 있지 않은 환경에서 서로 비밀 메시지를 주고 받고 싶다고 가정하자. B가 A에게 메시지를 전달하기 위해서는 A의 공개키가 필요하다. A는 아래와 같은 방법을 통해 그만의 공개키와 개인키를 제작한다.


p 와 q 라고 하는 두 개의 서로 다른 ({\displaystyle p\neq q}p\neq q) 소수를 고른다.


1. 두 수를 곱하여 {\displaystyle N=pq\,}N=pq\, 을 찾는다.
2. {\displaystyle \varphi (N)=(p-1)(q-1)\,}\varphi (N)=(p-1)(q-1)\, 를 구한다.
3. {\displaystyle \varphi (N)}\varphi (N) 보단 작고, {\displaystyle \varphi (N)}\varphi (N)와 서로소인 정수 e를 찾는다.
4. 확장된 유클리드 호제법을 이용하여 {\displaystyle d\times e}d\times e를 {\displaystyle \varphi (N)}\varphi (N)로 나누었을 때 나머지가 1인 정수 d를 구한다. ({\displaystyle de\equiv 1{\pmod {\varphi (N)}}}de\equiv 1{\pmod  {\varphi (N)}})


A의 공개키는 위에서 구한 두 개의 숫자로 이루어진 <N, e>이고, 개인키는 <N, d>이다. A는 <N, e>만을 B에게 공개하고, B는 이 공개키를 사용하여 자신의 메시지를 암호화하게 된다. 여기서 p와 q의 보안은 매우 중요하다. 이를 가지고 d와 e의 계산이 가능하기 때문이다. 그리하여 공개키와 개인키가 생성이 된 후에는 이 두 숫자를 지워버리는 것이 안전하다.

## 3. RSA 안전성 확보

|구분|안정성 확보|
|:---|:---|
|소수 선택|{::nomarkdown}<ul><li>p, q는 거의 같은 크기의 소수여야 안전함</li><li>p-1, q-1은 큰 소수를 인수로 가져야 안전함</li><li>p-1, q-1의 최대 공약수는 작아야 안전함</li></ul>{:/}|
|키 사이즈|1908년대까지 512Bit, 1996년에는 1024Bit, 2005년에는 2048Bit 권장|

# **해시(Hash)함수**
## 1. 해시(Hash) 알고리즘 특징
+ 키가 없고 복호화가 불가능한 특징을 가지는 암호화 방식, 일방향 암호 기술
+ MD(Message Digest)는 부결성만 제공하는 메커니즘
+ 다양한 길이의 입력을 고정된 짧은 길이의 출력으로 변환하는 함수(고정 길이 출력: 128Bit, 160Bit, 256Bit 등)
+ 표현 방식 : y=h(x)에서 x 는 가변 길이의 메시지, y는 해시함수를 통해서 생성, h는 해시값(Hash code)을 생성 

## 2. 해시함수의 조건

|조건|설명|
|:---|:---|
|압축|임의 길이의 평문을 고정된 길이의 출력 값으로 변환함|
|일방향 (One Way Function, 선이미지 회피성)|매시지에서 해시값(Hash code)을 구하는 것은 쉽지만 반대로 해시값에서 원래 메시지를 구하는 것은 매우 어려움(역방향 계산 불가능)|
|효율성|메시지로부터 h(메시지)를 구하는 데 많은 자원과 노력이 소요되지 않아야 함|
|충돌회피 (Collision free, 강한 충돌회피성)|{::nomarkdown}<ul><li>다른 문장을 사용하였는데도 동일한 암호문이 나오는 현상</li><li>h(M1)=h(M2)인 서로 다른 M1과 M2를 구하기는 계산상 불가능해야 함</li></ul>{:/}|
|2차 선이미지 회피성|{::nomarkdown}<ul><li>어떤 블록 x에 대해서 H(y)=H(x)인 y != x인 것을 찾는 것이 계산적으로 불가능해야 함</li><li>약한 충돌 회피성</li></ul>{:/}|

## 3. Salt
+ 동일한 값을 입력하였으나 다른 해시 값이 나오는 경우 (예: 리눅스 패스워드 해시값)
+ 리눅스만 알고 있는 임의의 값(Salt)을 추가해서 해시함

## 4. 해시함수 종류

|종류|특징|
|:---|:---|
|MD2|{::nomarkdown}<ul><li>Rivest 가 개발한 것으로 8Bit 컴퓨터를 위해 고안됨</li><li>매우 안전하지만 계산할 때 많은 시간이 걸림</li><li>128Bit의 출력 해시값을 생성</li></ul>{:/}|
|MD4|{::nomarkdown}<ul><li>Rivest 가 개발, MD2 보다 메시지 압축 속도가 빠름</li><li>속도가 빠른 반면 안정성이 떨어짐</li><li>128Bit 출력 해시값 생성</li></ul>{:/}|
|MD5|{::nomarkdown}<ul><li>Rivest 가 개발, 안정성이 떨어지는 MD4 알고리즘을 수정하여 만듦</li><li>128Bit의 출력 해시 값을 생성</li></ul>{:/}|
|SHA|Secure Hash Algorithm, MD 계열의 알고리즘과는 달리 160Bit의 출력 해시값을 생성|
|SHA-1|미국 표준의 메시지 압축 알고리즘으로 160Bit의 출력 해시값을 생성|

    * 국내에서 해시함수를 통하여 주민등록번호 및 패스워드 암호화 시 가장 권고하는 해시함수는 SHA-256임

### 1. MD5 (Message Digest Algorithm 5)
* 데이터 무결성을 보장하기 위한 단방향 해시함수
* 임의의 길이를 입력받아서 128Bit의 해시 값을 출력
* 고속연산이 가능, 공개키 알고리즘 및 대칭키 암호화 알고리즘보다 계산시간이 적게 걸림
* 메시지를 512Bit의 블록으로 나누고 128Bit를 출력함

### 2. SHA (Secure Hash Algorithm)
* 미국 국가안보국(NSA) 에서 1993년 처음으로 설계해서 미국 표준으로 제정
* 인터넷뱅킹, 전자서명, 메시지 인증코드, 키 교환 알고리즘, 키 생성 알고리즘 등에 사용
* MD4 해시 알고리즘 기반으로 만들어진 해시함수, 가장 널리 사용됨
* 안정성이 검증되었으며, SHA-256, SHA-384, SHA-512 등이 있음

### 3. 국내 해시함수 알고리즘 LSH
* 국내 TTA 표준으로 제정되었으며 지적재산권에 대한 사용료 없이 사용가능
* LSH는 메시지 인증, 사용자 인증, 전자서명 등의 다양한 암호 응용 분야에서 사용할 수 있는 해시함수
* 2014년 개발 되었고 224Bit, 256Bit, 384Bit, 512Bit 를 지원
* 국제표준인 SHA2와 SHA3에 비해서 2배 이상의 성능을 가지고 있음

## 5. 생일자 공격(Birthday Attacks)
+ 23명 중에서 같은 생일을 가지는 사람이 두 사람이나 그 이상이 있을 확률은 1/2보다 크다는 결과이다.
+ 생일자 역설을 근거로 한 해시함수의 최소 비트는 160비트 이상이 되어야 한다.

## 6. 랜덤 오라클 모델
+ 가장 이상적인 해시함수 모델로 사용자가 원하는 대로 오라클의 크기를 변경해서 사용할 수 있다.