# Chapter 3. Transport Layer

"Transport Services and Protocols"는  애플리케이션 프로세스 간의 논리적 커뮤니케이션을 제공.
메시지는 세그먼트로 잘라서 보냄

- network layer => host to host
- transport layer => process to process

|            TCP            |            UDP            |
|------------------|------------------|
| reliable | unreliable |
|in-order | unordered |
| congestion control | IP에 더 얹어주는 게 없음 |
| flow control | 프로세스 딜리버리만!! |


## Multiplexing and Demultiplexing
<img width="852" alt="Transport-layer multiplexing and demultiplexing" src="https://user-images.githubusercontent.com/26888051/77215124-db11f200-6b55-11ea-9e31-4f15b5ff8fc7.png">

1. 각 소켓이 메시지 전송을 요청
2. 각 메시지마다 목적지 확인해서 세그먼트의 header에다 도장을 찍어줌
   (목적지는 header에다가, 메시지는 payload에다가)
3. **Multiplexing**해서 이를 하나로 모아 network로 내려보내줌
4. 받는 쪽에선 세그먼트의 헤더를 확인해서 각 앱에 보내줘야 함 = **Demultiplexing**

▼ TCP/UDP Segment format
<img width="298" alt="Source and destination port-number fields in a transport-layersegment" src="https://user-images.githubusercontent.com/26888051/77215221-51aeef80-6b56-11ea-97b8-6c754c2a4f55.png">
