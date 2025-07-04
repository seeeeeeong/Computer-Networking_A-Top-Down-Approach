# 2.3 인터넷 전자메일

이 절에서는 인터넷 전자메일 구조의 중심에 있는 애플리케이션 계층 프로토콜을 알아본다.

## 전자메일 시스템 구성 요소

전자 메일 시스템의 상위 레벨 개념:

- **User Agent (UA)**  
  - a.k.a. "mail reader"  
  - 사용자가 메시지를 읽고, 응답하고, 전달하고, 저장하고, 구성할 수 있도록 도와줌  
  - 예: Microsoft Outlook, Apple Mail 등

- **Mail Server**  
  - 전자 메일 인프라스트럭처의 중심  
  - 각 수신자는 자신의 메일 서버에 메일박스(mailbox)를 가짐  
  - 메일박스는 수신자의 메시지를 저장 및 관리  
  - 메일 서버는 메시지를 큐(queue)에 저장하고, 수신자 메일 서버로 전달 실패 시 재시도 (약 30분 간격)  
  - 재시도에 실패하면 송신자에게 통보

- **SMTP (Simple Mail Transfer Protocol)**  
  - 전자메일 송수신을 위한 애플리케이션 계층 프로토콜  
  - 송신자의 메일 서버에서 수신자의 메일 서버로 메일을 전달할 때 사용  
  - TCP를 기반으로 신뢰성 있는 전송 제공  
  - 클라이언트-서버 모델 사용  
  - 메일을 송신할 때는 클라이언트, 수신할 때는 서버 역할 수행  

---

## 2.3.1 SMTP

- **기본 정보**
  - TCP 기반, 포트 번호 25
  - 클라이언트 → 서버로 직접 전송 (direct transfer)
  - 메시지 본문은 **7-bit ASCII** 형식만 가능  
    → 첨부파일, 대용량 전송에 제한

- **전송 절차 (Three Phases)**
  1. 핸드셰이킹 (Greeting)
  2. 메시지 전송 (Data exchange)
  3. 종료 (Closure)

### SMTP 동작 과정 예시

1. 앨리스가 사용자 에이전트에서 메시지 작성
2. 앨리스의 메일 서버로 메시지 전송 → 큐에 저장
3. SMTP 클라이언트가 밥의 메일 서버에 TCP 연결 시도
4. 핸드셰이킹 후 메시지 전송
5. 밥의 메일 서버가 메시지를 메일박스에 저장
6. 밥이 사용자 에이전트를 통해 메시지 확인

> SMTP는 중간 서버를 사용하지 않고, 실패 시 송신자의 메일 서버에 메시지를 유지함.

### SMTP 세션 예시 (telnet)

S: 220 hamburger.edu <br>
C: HELO crepes.fr <br>
S: 250 Hello crepes.fr, pleased to meet you <br>
C: MAIL FROM: alice@crepes.fr <br>
S: 250 alice@crepes.fr ... Sender ok <br>
C: RCPT TO: bob@hamburger.edu <br>
S: 250 bob@hamburger.edu ... Recipient ok <br>
C: DATA <br>
S: 354 Enter mail, end with ”.” on a line by itself <br>
C: Do you like ketchup? <br>
C: How about pickles? <br>
C: . <br>
S: 250 Message accepted for delivery <br>
C: QUIT <br>
S: 221 hamburger.edu closing connection <br>


- 클라이언트 명령: `HELO`, `MAIL FROM`, `RCPT TO`, `DATA`, `QUIT`
- 메시지 종료는 단일 점(`.`)으로 표시
- 응답: 코드 + 설명
- **지속 연결(persistent connection)** 지원

---

## 2.3.2 메일 메시지 포맷

- 헤더(header)가 본문(body) 앞에 위치
- 헤더는 RFC 5322에 정의됨
- 헤더와 본문은 **CRLF**로 구분
- 필수 헤더: `From`, `To`
- 선택 헤더: `Subject`, `Date`, `CC`, 등

### 예시:

From: alice@crepes.fr <br>
To: bob@hamburger.edu <br>
Subject: Searching for the meaning of life. <br>
 <br> <br>
Message body ... <br>


---

## 2.3.3 메일 접속 프로토콜 (Mail Access Protocols)

- **SMTP**: 메시지 **전달**용 (push 방식)
- **메일 접근 프로토콜**: 메시지 **조회**용 (pull 방식)

### 동작 방식

- 대부분의 사용자 환경은 로컬 호스트가 항상 켜져 있지 않기 때문에,
- 항상 켜져 있는 **공유 메일 서버**를 이용해 메일박스에 접근

### 전자메일 전송 흐름

1. 송신자 UA → 송신자 메일 서버 (SMTP 또는 HTTP)
2. 송신자 메일 서버 → 수신자 메일 서버 (SMTP)
3. 수신자 메일 서버 → 수신자 UA (IMAP 또는 HTTP)

> 수신자의 메일 서버가 직접 연결되지 않으면 두 단계 전송 필요

### 대표적인 메일 접근 프로토콜

- **IMAP (Internet Mail Access Protocol)**  
  - RFC 3501 정의  
  - 서버에 메일 저장, 클라이언트는 필요 시 동기화

- **HTTP**  
  - 웹메일, 모바일 앱 환경에서 자주 사용  
  - 메일 서버는 SMTP + HTTP 인터페이스 모두 지원 필요


