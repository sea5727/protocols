# RTSP

# Reference
[RFC2326](https://tools.ietf.org/html/rfc2326)
# RTSP 내용
스트리밍 미디어 서버를 컨트롤 하기 위해 고안된 프로토콜 ( RFC 2326 )
PLAY, PAUSE 와 같은 커맨드로 스트리밍 컨트롤
실제 미디어는 RTP로 전송 ( TCP ) 즉, RTP 미디어 스트림을 따로 연결하기때문에 NAT같은 환경에서는 통신되지 않을 수 있다.

# 특징/특성
- TCP/UDP ( 주로 TCP )
- 실시간성
- RTP


# 메시지
RTSP 로 미디어 스트림을 연결하기 위한 메시지   
HTTP 규약과 문법이나 동작이 비슷하나 RTSP는 미디어재생을 위한 컨트롤 시퀀스를 정의한다는점에서 다르다.    
HTTP는 Stateless 이나 RTSP는 Stateful 이다.  
## OPTIONS
먼저 OPTIONS 메시지로 서버가 URL과 서버가 받아줄 타입들을 주고받는다.
```
C -> S
OPTIONS
rtsp://sanghotest.iptime.org:12345/myrtsp/test
CSeq : 1

S -> C
RTSP/1.0 200OK
CSeq : 1
Public : OPTIONS, DESCRIBE, ANNOUNCE, GET_PARAMETERS, PAUSE, PLAY, RECORD, SETUP, SET_PARAMETERS, TEARDOWN
```

## DESCRIBE
DESCRIBE 요청에는 RTSP URL 및 SDP 를 응답받는다.    
응답 SDP에는 RTSP에서만 볼수있는 일부 필드가 있는데   
### Control URL : "a=control:"   
제어할 URL을 나타낸다. s(세션)과 m(미디어) 어트리뷰트에 모두 사용될 수 있다.   
m라인에서 개별 미디어에 사용되는 경우 해당 미디어의 제어를 위한 URL을 나타낸다.  
s(세션) 레벨이라면 집계 통제(aggregate control?)을 나타낸다.  
Example : a=control:rtsp://example.com/foo   
control 필드는 상대경로와 절대경로 모두 사용된다. URL 검색로직 구현 순서로는 
1. RTSP Content-Base field
2. RTSP Content-Location field
3. RTSP request URL

만약 * 라면, 전체 base URL을 상속받으며 비어있는 URL이라고 보면 된다 (?)

```
C->S: 
DESCRIBE 
rtsp://sanghotest.iptime.org/test RTSP/1.0
CSeq: 3
User-Agent: LibVLC/3.0.8 (LIVE555 Streaming Media v2016.11.28)
Accept: application/sdp

S->C: 
RTSP/1.0 200 OK
CSeq: 3
Content-Type: application/sdp
Content-Base: rtsp://sanghotest.iptime.org/test
Server: GStreamer RTSP server 
Date: Mon, 22 Mar 2021 13:31:14 GMT
Content-Length:661


v=0
o=- 119 75779673297454952 1 IN IP4 192.168.0.34
s=Session stream with GStreamer
i=rtsp-server
t=0 0
a=tool:GStreamer
a=type:broadcast
a=control:*
a=range:npt=0-3146.002
m=video 0 RTP/AVP 96
c=IN IP4 0.0.0.0 
b=AS:118
a=rtpmap:96 H264/90000 
a=framerate:30 
prop-parameter-sets=Z01AH+iAbB7zeAtQEBAUAAADAAQAAAM8DxgxEg=,aOvvIA==
a=control:stream=0
a:ts-refclk:locak
a=mediaclk:sender
m=audio 0 RTP/AVP 97
c=IN IP4 0.0.0.0
b=AS:127
a=rtpmap:97 MP4A-LATM/44100
a=fmtp:97 cpresent=0;config=400024200000000000000000000000000000
a=control:stream=1
a=ts-reflck:local
a=mediaclk:sender
```

# SETUP
SETUP 이 요청되어야 PLAY를 요청할 수 있다   
스트림이 어떻게 전송(단말에서 사용할 포트, 서버 포트 등 SD ANSWER 에 해당)되어야 하는지 협의한다.   

예를들어 rtsp://sanghotest.iptime.org/test/stream=0 의 의미는 DESCRIBE 응답 sdp에서 명시된 control을 의미한다. 

SETUP을 컨트롤별로 개별적으로 수행한다. (오디오, 비디오)

TCP로 RTP를 통신해야한다면(NAT환경 필요) : 이미 연결된 포트로 미디어스트림을 통신하기때문에 NAT환경에서도 스트리밍이 가능하다.
Transport: RTP/AVP/TCP;unicast;interleaved=0-1;ssrc=AF1FCB89;mode="PLAY"


```
C->S:  
SETUP 
rtsp://sanghotest.iptime.org/test/stream=0 RTSP/1.0
CSeq:4
User-Agent: LibVLC/3.0.8 (LIVE555 Streaming Media v2016.11.28)
Transport: RTP/AVP;unicast;client_port=60516-60517

S->C:  
RTSP/1.0 200 OK
CSeq:4
Transport: RTP/AVP;unicast;client_port=60516-60517;server_port=47588-47589;ssrc=8A201DD0;mode="PLAY"
Server: GStreamer RTSP server
Session: jLGXzv3eUwRccSl
Date: Mon, 22 Mar 2021 13:31:15 GMT

C->S:
SETUP 
rtsp://sanghgotest.iptime.org/test/stream=1 RTSP/1.0
CSeq:5
User-AGent: LibVLC/3.0.8 (LIVE555 Streaming Media v2016.11.28)
Transport: RTP/AVP;unicast;client_port=60518-60519
Sesion: jLGJXzv3eUw3ccSl

S->C:
RTSP/1.0 200 OK
CSeq:4
Transport: RTP/AVP;unicast;client_port=60518-60519;server_port=43570-43571;ssrc=346C724;mode="PLAY"
Server: GStreamer RTSP server
Session: jLGXzv3eUwRccSl
Date: Mon, 22 Mar 2021 13:31:15 GMT
```

# PLAY
미디어 스트림을 재생한다. 범위를 지정하지 않으면 처음부터 끝까지 재생하며, 일시 중지된 상태라면 중지된 지점부터 이어서 재생한다. (즉, RTSP 는 상태를 가지는 Stateful 프로토콜이다)
```
C->S:  
PLAY 
rtsp://sanghotest.iptime.org:12345/test/ RTSP/1.0
CSeq:6
User-Agent:LibVLC/3.0.8 (LIVE555 Streaming Media v2016.11.28)
Session: jLGXzv3eUwRccSl
Range: npt=0.000-

또는 pause 후 재생시 Rage: npt=324.667

S->C:
RTSP/1.0 200 OK
CSeq:6
RTP-Info: url=rtsp://sanghotest.iptime.org:12345/test/stream=0;seq=22423;rtptime=2551474179, url=rtsp://sanghotest.iptime.org:12345/test/stream=1;seq=23462;rtptime=1385494271
Range: npt=0-3146.002
Server: GStreamer RTSP server
Session: jLGXzv3eUwRccSl
Date: Mon, 22 Mar 2021 14:28:57 GMT
```

# TEARDOWN
URL의 미디어 스트림을 종료한다.
```
C->S:
rtsp://sanghotest.iptime.org:12345/test/
CSeq:7
User-Agent:LibVLC/3.0.8 (LIVE555 Streaming Media v2016.11.28)
Session: jLGXzv3eUwRccSl

S->C:
RTSP/1.0 200 OK
CSeq:7
Server: GStreamer RTSP server
Connection: close
Date: Mon, 22 Mar 2021 14:28:57 GMT

```