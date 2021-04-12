# RTMP

1. TCP

2. 1935 port


### Flow

1. handshake   
tcp 연결 후 handshake 과정을 수행해야한다.
클라이언트가 먼저 handshake C0+C1 를 전송하며, 데이터는 C0(버전) , C1(시간, 랜덤값) 이다.
서버가 handshake S0+S1+S2 를 응답하며, 데이터는 S0(버전), S1(시간 ,랜덤), S2(C1) 이다.
클라이언트가 마지막으로 handshake C2를 응답하며, S2를 채워 전송한다.

- C -> S : handshake (C0, C1)
```
C0 : 버전 ( 크기 1 Bytes)
C1 : 시간, 랜덤 ( 크기 1536 Bytes) 
```
- S -> C : handshake (S0, S1, S2)
```
S0 : 버전 (1Bytes)
S1 : 시간, 랜덤 (크기 1536 Bytes)
S2 : C1, 시간 (크기 1536 Bytes)
```
- C -> S : handshake (C2)
```
C2 : S2를 채워 전송 ( 크기 1536 Bytes)
```


2. connect   
handshake 이후 connect 메시지를 전송한다.   
C -> S : connect('live')
S -> C : Window Acknowledgement Size
S -> C : Set Peer Bandwidth
C -> S : Window Acknowledgement Size
S -> C : User Control Message (StreamBegin)
S -> C : _result(connect response)


- connect('live')
```
'connect'
TransactionId = 0x01(TRANSACTION_CONNECT)
Start Object

"app" : "live"   
"flashVer" : "LNX 9,0,124,2"   
"tcUrl" : "rtmp://sanghotest.iptime.org:1935/live"   
"fpad" : false   
"capabilities" : 15   
"audioCodecs" : 4071   
"videoCodecs" : 252  

End Of Object Marker
```