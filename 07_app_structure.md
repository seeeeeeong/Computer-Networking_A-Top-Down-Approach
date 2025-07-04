# 2.1 네트워크 애플리케이션의 원리

네트워크 애플리케이션 개발의 중심은 서로 다른 위치의 종단 시스템에서 동작하며, 네트워크를 통해 통신하는 프로그램을 작성하는 것이다.

예를 들어, 웹 애플리케이션에는 서로 통신하는 두 가지 프로그램이 있다:

- 서버: 웹 서버 프로그램
- 클라이언트: 사용자 호스트에서 실행되는 브라우저 프로그램

> 우리는 라우터나 링크 계층 스위치 같은 네트워크 코어 장비에서 실행되는 소프트웨어까지 작성할 필요는 없다.  
> (애플리케이션 계층에서 기능하지 않기 때문에 작성할 수 없다.)

---

## 2.1.1 네트워크 애플리케이션 구조

- 애플리케이션 구조는 애플리케이션 개발자가 설계한다.
- 네트워크 구조는 고정되어 있으며, 해당 애플리케이션의 특정 서비스 집합을 제공한다.

### 애플리케이션 구조의 종류

#### 클라이언트-서버 (Client-Server) 구조

- 항상 동작 중인 서버가 존재
- 클라이언트는 서버에 서비스 요청
- 클라이언트는 서로 직접적으로 통신하지 않음
- 서버는 고정된 IP 주소를 가짐
- 많은 요청을 처리하기 위해 데이터 센터를 사용 (보통 10만 개 이상의 서버 보유)

#### P2P (Peer-to-Peer) 구조

- 항상 켜져있는 인프라스트럭처 서버에 최소로 의존
- 피어(peer) 간 직접 통신
- 자가 확장성 보유 (피어가 서버 역할도 수행 가능)
- 비용 효율적, 데이터 센터 불필요

---

## 2.1.2 프로세스 간 통신

- 실제 통신은 프로그램이 아닌, 실행 중인 프로세스 간에 일어난다.
- 두 종단 시스템의 프로세스는 메시지를 주고받으며 통신한다.

### 클라이언트와 서버 프로세스

- 클라이언트: 통신을 초기화하는 프로세스
- 서버: 통신을 위해 대기하는 프로세스

요청을 보내는 프로세스 → 클라이언트  
요청을 받는 프로세스 → 서버

P2P 구조에서는 하나의 피어가 동시에 클라이언트이자 서버가 될 수 있다.

### 소켓 (Socket)

- 프로세스와 네트워크 간의 인터페이스
- 애플리케이션 계층과 트랜스포트 계층을 연결하는 창구
- 소켓 = 문, 프로세스 = 집

#### 개발자가 제어할 수 있는 항목

- 트랜스포트 프로토콜 선택 (TCP/UDP)
- 최대 버퍼 크기, 세그먼트 크기 등 일부 매개변수

### 프로세스 주소 지정

- IP 주소: 호스트의 주소
- 포트 번호: 해당 호스트 내의 프로세스 식별자

---

## 2.1.3 애플리케이션이 이용 가능한 트랜스포트 서비스

- 송신 애플리케이션은 소켓을 통해 메시지 전송
- 트랜스포트 프로토콜은 메시지를 수신 측 소켓까지 전달

### 트랜스포트 서비스의 종류

1. 신뢰적 데이터 전송 (Data Integrity)  
   데이터가 손실 없이, 오류 없이 도착.  
   신뢰성 없는 경우: 실시간 비디오/오디오처럼 손실 허용 애플리케이션

2. 처리율 (Throughput)  
   가용 처리율은 시간에 따라 변화.  
   - 대역폭 민감: VoIP, 스트리밍 등  
   - 탄력적(Elastic): 이메일, 파일 전송 등

3. 시간 보장 (Timing)  
   예: 100ms 이내 전달 보장  
   실시간 애플리케이션에서 중요

4. 보안 (Security)  
   데이터 암호화/복호화 지원  
   보통 TCP + TLS 조합 사용

---

## 2.1.4 인터넷 트랜스포트 프로토콜이 제공하는 서비스

### TCP (Transmission Control Protocol)

- 연결 지향형 서비스  
  전송 전 핸드셰이킹 필요  
  전이중(Full-duplex) 통신 가능

- 신뢰적 데이터 전송  
  손실 없이, 순서대로 전달

- 혼잡 제어  
  네트워크 혼잡 시 전송 속도 조절

### UDP (User Datagram Protocol)

- 비연결형  
  핸드셰이킹 없음

- 비신뢰적 전송  
  데이터 전달 여부 보장하지 않음

- 혼잡 제어 없음  
  속도 제한 없이 전송 (혼잡 시 성능 저하 가능)

### 미제공 서비스

- 처리율 보장 없음
- 시간 지연 보장 없음

---

## 2.1.5 애플리케이션 계층 프로토콜

서로 다른 시스템의 애플리케이션 프로세스 간에 메시지를 어떻게 주고받을지 정의

### 정의 요소

- 메시지의 타입
- 문법(Syntax): 메시지 포맷
- 의미(Semantics): 각 필드의 의미
- 규칙(Rules): 메시지 송수신 타이밍과 반응 방식

### 표준 vs 독점 프로토콜

- 많은 프로토콜은 RFC 문서로 제공됨 (예: HTTP)
- 일부는 비공개(독점) 프로토콜로 공개되지 않음

애플리케이션 계층 프로토콜은 전체 애플리케이션의 한 구성 요소  
예: 웹 = HTML + 브라우저 + 서버 + HTTP

---

## 2.1.6 이 책에서 다루는 네트워크 애플리케이션

중요하고 인기 있는 다섯 가지 애플리케이션 분야를 다룬다.

1. 웹 (Web)
2. 전자메일 (Email)
3. 디렉터리 서비스 (Directory Service)
4. 비디오 스트리밍 (Video Streaming)
5. P2P 애플리케이션 (Peer-to-Peer Applications)
