# DASH

``` xml
C->S
HTTP GET /dash/stream/stream.mpd HTTP/1.1

S->C
HTTP/1.1 206 Partial Content
Server: nginx/1.18.0
Date: Wed, 21 Apr 2021 06:40:29 GMT
Content-Type: application/dash+xml
Content-Length: 2304
Last-Modified: Wed, 21 Apr 2021 06:40:28 GMT
Connection: keep-alive
ETag: "607fc8dc-900"
Cache-Control: no-cache
Access-Control-Allow-Origin: *
Access-Control-Expose-Headers: Content-Length
Content-Range: bytes 0-2303/2304

<?xml version="1.0"?>
<MPD
    type="dynamic"
    xmlns="urn:mpeg:dash:schema:mpd:2011"
    availabilityStartTime="2021-04-21T06:37:34Z"
    publishTime="2021-04-21T06:40:28Z"
    minimumUpdatePeriod="PT5S"
    minBufferTime="PT5S"
    timeShiftBufferDepth="PT21S"
    profiles="urn:hbbtv:dash:profile:isoff-live:2012,urn:mpeg:dash:profile:isoff-live:2011"
    xmlns:xsi="http://www.w3.org/2011/XMLSchema-instance"
    xsi:schemaLocation="urn:mpeg:DASH:schema:MPD:2011 DASH-MPD.xsd">
  <Period start="PT0S" id="dash">
    <AdaptationSet
        id="1"
        segmentAlignment="true"
        maxWidth="1280"
        maxHeight="720"
        maxFrameRate="24">
      <Representation
          id="stream_H264"
          mimeType="video/mp4"
          codecs="avc1.42c01f"
          width="1280"
          height="720"
          frameRate="24"
          startWithSAP="1"
          bandwidth="0">
        <SegmentTemplate
            timescale="1000"
            media="stream-$Time$.m4v"
            initialization="stream-init.m4v">
          <SegmentTimeline>
             <S t="138898" d="5000"/>
             <S t="143898" d="5250"/>
             <S t="149148" d="6042"/>
             <S t="155190" d="5791"/>
             <S t="160981" d="5625"/>
             <S t="166606" d="5292"/>
          </SegmentTimeline>
        </SegmentTemplate>
      </Representation>
    </AdaptationSet>
    <AdaptationSet
        id="2"
        segmentAlignment="true">
      <AudioChannelConfiguration
          schemeIdUri="urn:mpeg:dash:23003:3:audio_channel_configuration:2011"
          value="1"/>
      <Representation
          id="stream_AAC"
          mimeType="audio/mp4"
          codecs="mp4a.40.2"
          audioSamplingRate="44100"
          startWithSAP="1"
          bandwidth="125000">
        <SegmentTemplate
            timescale="1000"
            media="stream-$Time$.m4a"
            initialization="stream-init.m4a">
          <SegmentTimeline>
             <S t="138898" d="5000"/>
             <S t="143898" d="5250"/>
             <S t="149148" d="6042"/>
             <S t="155190" d="5791"/>
             <S t="160981" d="5625"/>
             <S t="166606" d="5292"/>
          </SegmentTimeline>
        </SegmentTemplate>
      </Representation>
    </AdaptationSet>
  </Period>
</MPD>
```


```
C->S 
GET /dash/stream/stream-init.m4v HTTP/1.1
S->C
HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Wed, 21 Apr 2021 06:40:29 GMT
Content-Type: application/octet-stream
Content-Length: 660
Last-Modified: Wed, 21 Apr 2021 06:37:41 GMT
Connection: keep-alive
ETag: "607fc835-294"
Cache-Control: no-cache
Access-Control-Allow-Origin: *
Access-Control-Expose-Headers: Content-Length
Accept-Ranges: bytes


```

```
C->S 
GET /dash/stream/stream-149148.m4v HTTP/1.1

S->C 
HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Wed, 21 Apr 2021 06:40:29 GMT
Content-Type: application/octet-stream
Content-Length: 548964
Last-Modified: Wed, 21 Apr 2021 06:40:11 GMT
Connection: keep-alive
ETag: "607fc8cb-86064"
Cache-Control: no-cache
Access-Control-Allow-Origin: *
Access-Control-Expose-Headers: Content-Length
Accept-Ranges: bytes

[Binray Data 548964]
```

```
C->S 
GET /dash/stream/stream-init.m4a HTTP/1.1

S->C 
HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Wed, 21 Apr 2021 06:40:29 GMT
Content-Type: application/octet-stream
Content-Length: 599
Last-Modified: Wed, 21 Apr 2021 06:37:41 GMT
Connection: keep-alive
ETag: "607fc835-257"
Cache-Control: no-cache
Access-Control-Allow-Origin: *
Access-Control-Expose-Headers: Content-Length
Accept-Ranges: bytes


```

