# [네트워크] OSI 7계층과 TCP/IP



OSI 7 계층

- ISO(국제표준기준)가 정의한 네트워크 통신 표준
- 통신이 일어나는 과정을 7계층으로 표현
- 하위계층(1~3계층)은 네트워크의 데이터 전송 처리
- 상위계층(4~7계층)은 응용의 데이터 송수신 처리



TCP/IP 4계층

- IETF(국제인터넷표준화기구)에서 정의한 모델
- OSI 모델을 현실적인 구현에 맞춰 4계층으로 변경
- 현재 TCP/IP 모델을 일반적으로 사용





OSI

Application

- Network Applications
- 프로토콜 : Http, Https ex... => form Application Layer
  - FTP => File Transfer, 
  - Http/s => Web surfing, 
  - SMTP => Emials, 
  - Telnet => Virtual Terminals
- User Activity를 지원하는 프로토콜에 의해  Application Layer에서는 위의 기능들을 제공

Presentation

- 컨버팅 : 문자/숫자형 데이터 => byte + 
- Data compression
  1. Lossy
  2. Lossless
- Encryption / Decryption
  - SSL (Secure Sockets Layer)

Session

- Authentication/Authorization
- session management
  - seprate session으로 image file, text file 전달 form of data packets
  - 받은 data packet이 Image file인지, text file 인지 구별해서 어디로 갈지 정함

Transport

- controls reliability of communication
- Segmentaton
  - Dataㄴㄴ
- Flow control, 
- Error control

Network

Data Link

Physical







protocol 

규격을 맞춤

다양한 벤더들을 이용하더라도 정상적으로 전달될 수 있도록 표준/법 제정되어 있음