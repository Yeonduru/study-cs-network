# 4.4 ICMP & IPv6

### ICMP

- 네트워크 계층 제어정보를 교환하는데 사용되는 프로토콜
- 에러 보고 (unreachable host, network, port, protocol)
- 네트워크 상태체크 (ping pong)

- IP datagram에 담겨 전송됨 (ICMP 정보를 IP에 encapsulation)

  ![Screen Shot 2020-04-25 at 9.06.47 AM](/Users/yeonjulee/Desktop/Screen Shot 2020-04-25 at 9.06.47 AM.png)



### traceroute

![Screen Shot 2020-04-25 at 9.12.21 AM](/Users/yeonjulee/Desktop/Screen Shot 2020-04-25 at 9.12.21 AM.png)



- 목적지까지 존재하는 라우터들에 대한 RTT(round trip time)을 측정해 보여주는 application
- TTL 1, TTL 2, TTL 3... 인 packet set(여기서는 한 set에 3개)을 계속 보냄
- 이 packet의 destination으로는 매우 엉뚱한 port number가 지정되어 있음
- 목적지 이전에 만나는 라우터에 대해서는 time out으로 source로 돌아옴
- 목적지에 도달하면 port unreachable 코드와 함께 source로 돌아옴. 이것을 보고 목적지에 도달했음을 알고 프로그램 종료.



### IPv6

- IPv4의 주소가 고갈되고 있음 (CIDR, DHCP, NAT 등으로 주소 최대한 재사용, 촘촘히 사용 시도)
- 보완을 위해 등장한 프로토콜
- 128 bit address space
- router에서의 packet processing speed-up
  - 최대한 HW적으로 처리
  - header 길이 고정 (40 byte)
  - option 필드를 없애고 optional header 만듦.

![Screen Shot 2020-04-25 at 9.22.05 AM](/Users/yeonjulee/Desktop/Screen Shot 2020-04-25 at 9.22.05 AM.png)

- next hdr를 이용해서 optional header 여부를 표시해줌
- datagram이 너무 크면 drop함 (no fragmentation), source에게 알려서 source가 fragmentation해서 보내도록 함. router에서 하는 일 더욱 minimize
- ICMPv6
  - 새 message 추가
  - multicast를 위한 지원



### Transition from IPv4 to IPv6

![Screen Shot 2020-04-25 at 9.26.35 AM](/Users/yeonjulee/Desktop/Screen Shot 2020-04-25 at 9.26.35 AM.png)

- 갑자기 세상의 모든 라우터를 IPv6로 변경하는 것은 현실적으로 불가능..
- 중간중간의 몇 개만 교체되게 될 것
- Tunneling
  - 인터넷 코어에서 새 protocol을 deploy할 때 일반적으로 사용하는 기술
  - IPv4 datagram속에 IPv6를 encapsulation한다.
  - source를 자기자신, destination을 다음 IPv6로 설정한 IPv4데이터그램을 생성하여 보냄.
  - 목적지 (E)에 도착하면 header를 떼어냄. E는 남은 IPv6 데이터그램을 가지고 다시 packet forward

