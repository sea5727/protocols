# HLS

C -> S : HTTL Get ( manifest file)
S -> C : 200OK ( index of a set of fragments)
S -> C : Window Acknowledgement Size
S -> C : Set Peer Bandwidth, Set Chunk Size, AMF Command Result('Connect.Success')
C -> S : Window Acknowledgement Size
S -> C : User Control Message (StreamBegin)
S -> C : _result(connect response)

manifest file