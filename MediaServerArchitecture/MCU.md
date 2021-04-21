# MCU
[참고#1](https://www.frozenmountain.com/hubfs/5%20-%20pdfs/frozenmountain-mcu-multipoint-control-unit-whitepaper.pdf)

# Introduce

SFU 방식은 매우 효율적이므로 많이 사용되는 방식이지만, 스트림 세션 참가자가 많은경우 Client 측에서 근본적인 문제점이 있다. 어느 시점에서 단말이 미디어 스트림을 처리할수 있는 수를 초과하게 된다. 이경우 MCU 를 사용하여 클라이언트의 부하를 서버로 이동할 수 있다. 

# Mix 장점

모든 참가자의 컨텐츠를 하나의 스트림으로 믹싱함으로써 단말은 오직 하나의 세션으로만 모든사람의 오디오, 비디오를 볼 수 있다. 하지만 각 인바운드 스트림을 믹싱후 인코딩하기전 디코딩해야 하므로 CPU/GPU Cost가 발생하게 된다.

서버에서 참가자들의 코덱을 지원한다면 호환되지 않는 단말들간의 연결 또한 지원할 수 있다. 마찬가지로 CPU/GPU 비용은 발생한다.

비용이 발생함에도 불구하고 MCU 의 Mix 기능은 많은 참가자가 있는 대규모 회의에 가장 적합한 옵션이다.


# Mix 정리
Mix의 구현방식은 간단하다. RTP 스트림의 최종단으로 수신된 RTP 패킷을 디코딩한다. 각 스트림은 각자의 Decoder를 가지며, Decoder는 Mixer에게 전달한다. Mixer는 모든 참가자의 오디오, 비디오 프레임을 인풋으로 하여 혼합된 오디오, 비디오 프레임을 생성한다. 혼합된 프레임은 Encoder로 보내지며, 모든 참가자가 동일한 코덱을 사용한다면 Encoder가 하나만 있을 수 있고, 그룹간에 공통 코덱이 없는경우 개별 Encoder 가 있을 수 있다. Encoder를 통해 압축된 스트림은 RTP Packet으로 변환되어 개별 참가자에게 전송된다. 


# Audio Mixing
오디오 믹싱은 각 인바운드 스트림에서 모든 오디오 패킷을 가져와서 Jitter 버퍼와 함께 동기화 이후, Mixer를 통해 혼합한다. 그런 다음 혼합된 오디오는 인코딩되어 참가자들에게 전송된다. 일반적으로 참가자의 오디오는 인코딩 전에 먼저 믹서에서 제거되어야만 하는데 그렇지 않으면 Client는 자신의 오디오를 듣게되기 때문이다.


# Video Mixing
각 인바운드 스트림 디코더는 디코딩된 이미지를 공유 캔버스에 기록한 다음, 초당 프레임으로 인코딩한 다음 전송한다. 비디오 처리는 오디오 처리보다 훨씬 많은 CPU를 소모하며, 오디오와 같은 에코 효과가 없기때문에 각 참가자에게만 구분되는 비디오를 전송하는것이 아닌 공통 비디오를 전송하여도 된다.즉 비디오를 보내는 참가자 또한 자신의 비디오를 볼 수 있다

# Transcoding
MCU는 코덱별 Transcoding 기능을 제공하기 때문에 단말에서 사용하는 코덱을 서버에서 지원한다면 단말간 코덱이 같지 않더라도 서비스를 제공할 수 있다. 예를들어 VP8을 지원하는 단말과 H.264.를 지원하는 단말의 통신을 제공한다. Transcode 과정은 CPU/GPU 를 소모하는 비용이 추가된다.

# VoIP Integration
따라서 MCU는 VoIP에서 필수적인 요소이다. VoIP 에서는 특수한 단말이 사용될수 있어 Transcoding 기능이 필수적이며, 많은 SIP 단말은 암호화를 사용하지 않으나 WebRTC는 암호화가 필수이다. 이런 여러 환경에 맞추어 단말간 통신을 지원하기 위해서는 MCU의 기능이 절대적이다.

# Mixing Limitations
믹싱의 한계는 2가지 정도가 있다.
### 1. 서버에서 디코딩, 믹싱, 인코딩 하는 과정에 대한 비용이 든다.
MESH, SFU에 비해 인바운드 스트림이 증가할 수록 단말의 부담은 줄지만 서버의 부담은 증가한다.   

### 2. 단말에서 특정한 화면 설정이 어렵다.
오디오 믹싱과정에서는 개별 참가자에 믹서(에코 문제 해결을 위한)가 존재하여 단일설정 (예: 음소거 등)이 가능하지만, 비디오의 경우 공통 화면으로 혼합이 되므로 단말에서 특별한 커스터마이징이 어렵다. 오디오처럼 단말별로 비디오도 혼합을 한다면 가능 할 수 있으나, 비디오는 오디오를 처리하는데 더 많은 CPU/GPU 자원이 소모되는것을 명심해야한다.

