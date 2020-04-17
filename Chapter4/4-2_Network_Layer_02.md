# Chapter 4: Network Layer



- Segment, Datagram, Packet, Fragment 차이 및 정리

  https://stackoverflow.com/questions/11636405/definition-of-network-units-fragment-segment-packet-frame-datagram





## 4.3 What's inside a router

<img src="../images/4-2-1.png" alt="image-20200411091418776" />



라우터는 크게 두가지의 주요한 기능을 수행한다. 

1. 라우터 : 라우터들끼리 목적지까지 이르기 위한 경로를 계산하고 프로토콜을 통해 이를 주고받는 것
2. 포워딩 : 각 목적지에 가기 위해 정해진 경로에 따라 패킷을 전달해 주는 것



크게 control plane과 data plane으로 나뉜다. 



앞으로 이야기할 내용은 데이터 전송(포워딩)을 담당하는 data plane을 다룰 것. 이는 input port / switching fabric / output port로 나뉜다. 



![image-20200418070638250](C:\projects\study-cs-network\images\4-2-2)

인풋 포트는 

line termination(비트를 모아서 frame형성) / link layer protocol(프레임이 잘 구성돼 있는지 확인) / look up -forwarding(목적지를 계산해 포워딩) 으로 구성 돼 있다.

- 인풋 포트의 모든 처리는 데이터가 유입되는 속도와 동일한 속도로 데이터를 내보내는 것을 목표로 한다. 
- switching fabric으로 내보내기 전에 datagram들이 모여 있는 버퍼에는 내보내는 속도보다 더 빨리 datagram이 도달해 queing이 발생할 수 있기 때문에 버퍼에서의 데이터 유실이나 데이터 지연 등의 이슈가 발생할 수 있다. 



- 만약 fabric이 인풋 포트보다 느리다면, input port에서 queing이 일어나는데 이때에 HOL(Head of line) Blocking이 일어날 수 있다. 이는 앞의 datagram이 대기하는 동안 그 뒤에 있는 datagram이 다른 포트로 전송될 수 있는 상황임에도 전송되지 못하는 현상을 말한다. 

![image-20200418071346636](C:\projects\study-cs-network\images\4-2-4)





![image-20200418071100092](C:\projects\study-cs-network\images\4-2-3)

 switching fabric은 input port의 buffer에서 목적지에 해당하는 output port의 buffer로 데이터를 이동시키는 역할을 한다. 이때 switching rate는 여러개의 인풋 / 아웃풋 포트간에 전송된 데이터 양으로 측정될 수 있다.

 Switching fabric은일종의 버스 역할을 하는데 총 3가지 종류의 fabric들이 존재한다.



1. Memory
   - Cpu의 제어를 받는 메모리를 직접 활용하는 전통적인 방식이다.
   - 인풋 포트에 있는 패킷이 먼저 시스템 메모리로 복사된 뒤 이를 다시 복사해 아웃풋 포트로 가져가는 것이다.
   - 버스를 두번 건너가야 해(input 때 메모리로 데이터 올리기 / output 때 메모리의 데이터 가져가기) 속도 지연이 발생한다.

2. Switching via bus

   - 중간 다리 역할을 하는 bus를 활용해 한번에 데이터를 전송한다. 
   - bus contention : 버스 대역폭에 따라 전송속도가 제한되게 된다. 
   - ex) Cisco 5600 32Gbsps

3. Switching via interconnection network(corssbar)

   - 버스의 bandwidth로 인해 속도가 제한되는 문제를 해결하려고 고안됐다.

   - 이러한 interconnection net 형태는 멀티프로세서 환경을 처리하기 위해 최초로 고안되었다.

   - 데이터 전송을 보다 유리하게 하기 위해 일종의 cell 형태로 데이터 크기를 제한해 보다 효율적으로 전송될 수 있도록 하는 방식 또한 고안되었다.

     

![image-20200418072504120](C:\projects\study-cs-network\images\4-2-6)

아웃풋 포트는 인풋포트에서 datagram를 전달할때와 정반대 순서로 이루어진다. 즉 buffer / link layer protocol / line termination(physical layer)

 데이터를 전송하는 속도와 switching fabric으로부터 전달받는 데이터의 도달 속도간의 차이가 발생할 수 있으므로 인풋 포트와 마찬가지로 버퍼링이 발생한다. 

 이때 큐잉이 발생하므로 어떤 방식으로 데이터 전송을 처리할지(누가 먼저 받을건지, 어느 데이터를 먼저 보낼건지 +망 중립성 이슈)에 따른 스케쥴링 문제가 발생하게 된다.

 큐잉 과정에서는 datagram이 유실(lost)되거나, 정체(congestion)될 수 있으므로 이를 방지해야 한다. 

 

RFC는 권장 버퍼 크기에 대해 정의해 두었는데 이는

![image-20200418073439418](C:\projects\study-cs-network\images\4-2-7)

로 정리된다. 일반적인 RTT * 연결된 링크 갯수(switching faric을 통해 도달가능한 링크들?) / 데이터 플로우 갯수 의 루트값.(루트값으로 나눈 연산은 최신 연구 결과 반영.)





## 4.4 IP: Internet Protocol

![image-20200418073750586](C:\projects\study-cs-network\images\4-2-8)

네트워크 레이어에는 크게 세가지 종류의 프로토콜들이 정의돼 있다.

- routing protocol(경로 계산, 경로 선택 등에 관함) 
  - 라우팅 프로토콜에 따라 계산된 경로에 따라 포워딩 테이블이 만들어지고 이에 따라 데이터를 정송하게 된다.
  - 앞서 잠깐 나왔던 control plane과 관련된 부분
- IP protocol
  - datagrame의 포맷이나 패킷 핸들링, 데이터 전송에 관한 정보들을 다루고 있다.
  - dataframe에 대한 프로토콜이라고 할 수 있다.
- ICMP protocol
  - 호스트 간의 데이터 교환이외의 서로간의 리포팅이나 다양한 컨트롤 메시지 교환에 대해 정의하고 있습니다.



![image-20200418074208375](C:\projects\study-cs-network\images\4-2-9)

IP 데이터그램의 해더는 20 바이트의 고정 필드와 가변길이 옵션을 갖는다. 

- head length - 가변필드로 인해 크기를 정의하는 역할

- type of service - 사용된 적 없다고 함(목표에 따라 어디서 쓰일지를 정하기 위하)

- time to live - 데이터 그램을 거칠 수 있는 최대 hop(라우터 to 라우터)을 의미한다. 이 값은 한 라우터를 거칠 때 마다 1씩 감소한다. 0이 되면 라우팅 루프구나 하고 방황한다고 판단해서 drop시키게 된다.  ~라우팅 오류 방지

- upper layer : tcp / udp인지 판단. tcp와 udp가 멀티플랙식 돼서 들어오기 때문에 받는 측에서 데이터그램의 데이터를 어떤 프로토콜 프로세스로 올려보내야 할지를 판단하게 하기 위함.

- length : 헤더 포함 전체 길이 표시

- 16-bit identifiler, flgx, fragment offset : 프레그맨테이션과 재조합을 위함

- option : 가변 길이로 시간, 경로지 등등을 담고 있음

  

![image-20200418075005747](C:\projects\study-cs-network\images\4-2-10)

- 네트워크 링크들은 물리적 제약에 의해(전송량, 속도) MTU 가 있으므로 불가피하게 큰 datagram을 쪼개서 내보내야 할 수 있다. 분리되면 헤더 분리에 따른 오버해드가 발생하므로 바람직한 상황은 아니다. 하지만 물리적 제약으로 인해 어쩔 수 없는 상황이 있으므로  모두 분리돼있다가 최종 목적지에서 다시 재조합되게 된다. 이 때 위에서 설명한 IP header의 빗들이 사용된다. 

![image-20200418075157917](C:\projects\study-cs-network\images\4-2-11)

- 각 분리된 datagram마다 헤더가 생겨서 길이 늘어남
- flg는 마지막 제외하고 1으로 표시. 마지막을 확인할 수 있게 함. 
- id가 동일한 것들을 모은다.
- offset은 8로 나눈값





## 4.4 IP: Internet Protocol

![image-20200418075755112](C:\projects\study-cs-network\images\4-2-12)

IP는 호스트 혹은 라우터의 네트워크 인터페이스의 아이디(고유값)을 의미한다. 라우터는 통상적으로 여러개의 interface를 갖고, 각 호스트들은 1개나 2개의 인터페이스(와이파이 + 전용랜)를 갖게 된다.  

IP는 8비트 4자리로 표현하고 이를 보통 10진법으로 각 자리수를 변환해 사용한다. (알아보기 쉽게)



![image-20200418080133159](C:\projects\study-cs-network\images\4-2-13)

- 라우터의 개입 없이 직접 연결돼 있는 디바이스 인터페이스를 의미한다. 
- 동일한 서브넷 내에서 223.1.1.3 이런 주소를 보면 서브냇 내에서는 high order 3개 자리가 같은 서브냇임을 공유함을 나타내게 되고 이 가장 마지막 한자리수가 호스트들을 구분하는 부분이 된다. 



![image-20200418080455832](C:\projects\study-cs-network\images\4-2-14)

원래는 서브넷에 크기 따라 클래스가 나뉘어져 있었다. 각 클래스 별로 자리수를 할당받을 때 C class(마지막 한자리만 서브넷으로 사용하는 것)은 너무 적다고 생각해 B클래스를 모두가 요구하게 됐고 해당 클래스가 너무 빨리 소진되는 현상이 발생하게 되었다. /뒤에 오는 숫자는 것은 앞에서부터 공유하는 초기 비트의 수를 의미한다.



---



- IP주소는 아이피 관리자로부터 할당 받아서 사용하게 된다.

  - 자기가 설정 가능(제어판에서) 사실상 변하지 않는 IP 이지만 비효율적이다.
  - DHCP - 어드민으로부터 다이나믹하게 IP를 할당받게 된다. 호스트가 네트워크에 접속중일 때만 아이피 주소를 할당해, 주어진 ip집합을 돌아가면서 활용하게 해 활용도가 높아지게 된다.

  

- DHCP
  - 호스트가 존재함을 알리고 인정해주는 메시지(discover, offer)
  - IP 주소 요청을 보내고 IP주소 할당을 알리는 등의 내용이 정리돼 있다. (request, ack)




![image-20200418081716513](C:\projects\study-cs-network\images\4-2-15)

- 할당시간을 lifetime으로 보내 사용할 수 있는 기간을 명시하게 된다.



DHCP의 ack에는 단순히 IP 주소보다 더 많은 내용들을 담고 있다.

1. 라우터의 주소. 네트워크 계층의 라우터는 매우 단순하게 이루어져 있어서 어디로 가야 되는지만 담고 있다. 따라서 이동할 첫번째 라우터의 주소를 명시한다. 호스트에게 데이터 통신을 하고 싶으면 이 라우터에게 패킷을 내보내면 된다. 라고 서브넷의 호스트에게 알려주는 것이다. 
2. 다른 호스트들과 통신을 하기 위해 필요한 ip주소를 알기 위해 로컬 DNS 서버의 IP주소도 알려준다.
3. 서브넷 호스트들끼리 공유하고 있는 네트워크 마스크가 무엇인지 알려준다.



Q. 서브넷들의 주소는 어떻게 할당될까?





### Network layer

transport 계층의 segment를 host to host delivery 해주는 것이 목적

네트워크 계층 헤더를 붙여서 segment를 encapsulate 해서 datagrams를 생성