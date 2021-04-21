# HLS 란 (번역 및 정리)

# Reference
[HLS 참고#1](https://d2.naver.com/helloworld/7122)
[MPEG-2 PS](https://m.blog.naver.com/PostView.nhn?blogId=tjdwns79&logNo=220535309743&proxyReferer=https:%2F%2Fwww.google.com%2F)

# 내용
HLS(HTTP Live Streaming) 의 약자로 HTTP프로토콜을 사용한 스트리밍 프로토콜   
비디오 파일을 다운로드 할 수 있는 segment(ts라는 파일)들로 분할하고, 리스트로 나열한 manifest 파일(index 파일)로 명시하여 단말에서는 HTTP파일을 다운로드 후 플레이 하는 방식   
HTTP를 사용하기때문에 이미 구축된 HTTP 프록시와 WEB 서버 장비들을 사용할 수 있고, 대부분의 장비의 방화벽을 통과 할 수 있다. 적응형 비트레이트 스트리밍(Adaptive Bitrate Streaming) 로 네트워크상태가 불량하더라도 저화질의 파일을 제공하여 스트림을 끊기지않고 제공   
파일을 생성후 스트리밍하기때문에 실시간 프로토콜(RTP, RTSP, WebRTC, RTMP)에 비해 latency가 높다

# HLS 특징
 - HTTP
 - manifest (m3u8, m3u)
 - ts
 - Adaptive Bitrate



## HTTP
HLS는 HTTP를 사용하여 스트리밍하는데, 그 방식은
1. HTTP GET [MS 또는 CDN에 해당되는 URL]/[콘텐츠]/manifest이름.m3u8 을 요청
2. manifest에 명시된 시간간격마다 HTTP파일 리스트들을 다운로드받아 플레이

# manifest (m3u, m3u8) ( TODO : 포멧 정리 추가)

# MPEG-2 TS ( TODO : 추가 내용  )
MPEG2- TS는 ts라고 불리며, 오디오,비디오,자막과 같은 미디어 데이터들을 전송하기 위한 표준 컨테이너 포맷
188바이트 단위의 MPEG-2 TS 패킷의 집합이며, Header, Adaptation, Data 영역으로 나뉜다.
Header : 4바이트이며 동기화르 위한 데이터 및 패킷의 속성과 상태를 나타냄   
Adaptation : 필요에 따라 존재하지 않거나, 필요한 만큼의 데이터 길이를 가지며 Header에서 알려주지 못한 추가 정보를 전달   
Data : 비디오, 오디오, 데이터 등이 실리며, 하나의 패킷 Data영역안에는 하나의 요소에 해당되는 데이터만을 가진다.

# Adaptive Bitrate
적응형 비트레이트 스트리밍이란 네트워크 환경에 맞추어 미디어 스트림의 품질을 결정하는 스트리밍 방식이다.   
일반적은 hls와 동일하나 미디어 서버는 여러 화질의 ts파일을 생성하고 manifest 파일에 BITRATE이 추가된다.

```
C->S 
GET /play/hls/sintel/index.m3u8 HTTP/1.1
S->C
HTTP/1.1 206 Particial Content (application/x-mpegurl)

#EXTM3U
#EXT-X-STREAM-INF:BANDWIDTH=705746
1.m3u8
#EXT-X-STREAM-INF:BANDWIDTH=120490
2.m3u8


C->S
GET /play/hls/sintel/2.m3u8 HTTP/1.1
S->C
HTTP1/1.1 206 Partial Content (application/x-mpegurl)
C->S
GET /play/hls/sintel/2.13.ts HTTP/1.1
S->C
...

네트워크 환경 변경시
C->S 
GET /play/hls/sintel/1.m3u8 HTTP/1.1
S->C
HTTP1/1.1 206 Partial Content (application/x-mpegurl)
C->S
GET /play/hls/sintel/1.20.ts HTTP/1.1
```




