# 지난시간 리뷰

**Internet**  = Core + Edge

**Host** = 인터넷 사용자 , 이전에는 데스크탑뿐이었지만 요즘에는 대부분의 가전들에 해당된다. Edge 부분

**Router** = 다양한 네트워크를 동시에 연결하는 것이 목적이다. 

**Switch** = 다양한 device를 동시에 연결하는 것이 목적이다. 

**Link** = router & swich 또는 host & route 들을 연결해주는 것. 물리적인 실제 회선이다. 

**Access network** 

--> 집이라면 : home network (router(혹은 switch 등)에 access pointer를 통해 연결

--> 회사/학교등의 기관이라면 : Ethernet switch 에 데스크탑이 물리게 되고 access point로 연결,  학교전체를 묶어주는 router에 연결된다. 


# 1. Host: sends packets of data 

* Host sending function:

  application 메시지를 받아드린다.

  packet이라는 단위로 잘라서 access network에 전송한다. 이때 packet의 크기가 L bit이고 transmission rate (= capacity , link bandwidth) 가 R이라면, 

  packet transmission delay = \frac{L (bits)}{R (bits/sec)}

  [image alt text](images01.png)

# 2. Physical media
* guided media : copper (Ethernet) , fiber & coax : HFC 

  fiber가 coaxial cable 보가 band width가 크다 . (빠르다). 따라서 여러 가구가 연결되어 있는 HFC의 경우에 Head End (HE)는 fiber로 연결한다. 

* Unguided media : radio (EM 를 이용해서 전파 , wifi, cellular)

* Twisted pair (TP // copper임) : Category 5 (100Mbps) , Category 6 (1Gbps)





Radio : no physical wire , 장애물에 의해서 전달되기 때문에 reflection, obstruction, interferencer가 있다. 



|                |               | 장점                                                         | 단점                                                         | 사용처                                                       |
| -------------- | ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Guided Media   | Coaxial cable | - Two concentric copper conductors                           |                                                              | - Multiple channels on cable  - HFC                          |
| Guided Media   | Fiber         | - glass fiber carrying light pulses, each pulse a bit  - 빠르다!! - low error rate |                                                              |                                                              |
| Unguided Media | Radio         | Physical wire가 없이 Electromagnetic Wave로 전달된다. Bidirectoinal 하다 | EM으로 전달되는 만큼 reflection, obstruction by objects, interference등의 부작용이 있다. | terrestrial microwave, LAN (e.g., WiFi) , wide-area , satellite (270msec 정도의 end-end delay가 생긴다) |

 , wide-are Coaxial cable VS fiber optic cable (비교하기추가 )

# 3. Circuit Switching Network 

* call set up & resource reservation  (자원을 분할하여 예약해야한다.)
* 분할방식은 FDM(Frequency Division)과 TDM(Time Division)으로 나뉜다.
* FDM : 사용자별로 별도의 frequency를 할당받는다.
* TDM : 사용자별로 시간별로 돌아가면서 사용한다.

![](D:\_User\Desktop\images02.PNG)



* 전화네트워크의 경우는 적합하지만 인터넷 네트워크에는 적합하지않다. 자원예약이 되면 다른 user는 전혀 share할 수가 없기 때문이다. 컴퓨터간의 통신은 굉장히 활발히 잘 이루어져야하기 때문이다.

# 4. Alternative core: Packet Switching

* No call set up, No resource reservation
* 필요가 발생하면 그때그때 사용할 수 있게 해준다. 
* 한 데이터가 링크를 차지하면, 링크대역 전체를 사용해서 data transmission을 한다. 
* 자원의 분할 없이, each packet transmitted at full link capacity 
* 한 링크가 오랜시간 dominate되어 있으면 다른 사용자의 데이터가 전송되지를 못한다. 따라서 hosts가 메시지를 packet단위로 자르고 목적지 주소가 명시되어있다. packet의 크기가 정해져있다면 링크를 점유하는 시간이 정해져있게된다. Router는 packet의 주소를 보고 어디로 보내줄지 판단하여 데이터를 보내준다. 받으면서 동시에 내보내는 것은 불가능하고, 데이터가 다 올때까지 store하고 다 도착하면 주소를 parsing하여 어느 링크로 parsing해서 보내야하는지 정한다. (Store and Forward)
* **Store and Forward** :

![](D:\_User\Desktop\images03.PNG)

store and forward 방식의 단점으로 인해서 end-end delay = 2L/R ( assuming zero propagation delay)가 생기게 된다. 

**queuing and loss** (congestion) :

![](D:\_User\Desktop\images04.PNG)

Router가 packet을 내보내는 속도보다 들어오는 속도가 더 빠르면 발생한다. packet들이 쌓이게되면서..

-> packets will queue, wait to be transmitted on link

-> packets can be dropped (lost) if memory (buffer) fills up 



**Packet switching VS Circuit switching**

-> packet switching이 훨씬 빠르다 !! resource sharing도 더 효율적으로 일어난다. 

|                  | 장점                                                         | 단점                                                         | 기타사항                                                     |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Packet switching | - Great for bursty data : resource sharing, simpler and no call setup | - Excessive congestion possible : packet delay and loss, protocols needed for reliable data transfer, congestion control | 스트리밍 등의 경우에는 딜레이가 없어야 하므로 이를 해결하기 위한 방법이 필요하다. -> chapter 7에 해결법이 나와있으나 이번 강의에서는 패스..! |



# 5. Internet structure : network of networks 

수 많은 access ISP들은 어떻게 서로 연결되어 있을까? 수 많은 네트워크들을 직접 연결하는 것은 매우 복잡하고 실현불가능하다. (확장성이 낮다.)

인터넷 사업자가 global ISP를 통해 연결된 라우터들을 가지고 있다. 우리가 케이블 회사등에 돈을 지불하듯이, 케이블 회사등도 global ISP에 돈을 내고 서비스를 사용하는것이다.  Global ISP도 여러개가 있다. 그럼 Global ISP들끼리는 또 어떻게 연결되어야할까? --> 서로간에 peering link를 가지고 있게된다. (Internet Exchange Point (IXP) 사업자들도 따로 존재한다.)

![](D:\_User\Desktop\images05.PNG)

또 regional ISP도 있고... (작은 지역담당 -> 큰 지역담당 등으로 hierarchical 하게 존재한다 , Multi-tier hierarchy !)

**Settlement free** 

Traffic이 많은 두 regional ISP간에는 peer link를 연결할 수도 있다 ( settlement free -> 비용과 delay를 모두 줄일 수 있다. )

**Multi home**

higher level ISP를 경우에 따라 여러개 둘 수도 있다. ISP하나가 fail이 일어나도 나머지 ISP와 연결되므로 잘 동작할 수 있다. 

**Content Provider Networks (google, amazon)**

회사가 데이터 센터를 여러개 가지고 있고 user들에게 데이터를 제공해야한다. 따라서 자체적으로 own network를 가지고있고 회사의 데이터 센터를 다 연결하고 user들까지 연결하기도 한다. 전 세계로의 연결이 보장된것은 아니다. ISP에 대한 비용을 줄이고 service 관리를 더 유연하게 하기 위해서. 일부 regional ISP 등에 연결되기도 한다. 

Ex) Google

- 30 ~ 50 data centers
- Private network to interconnect data centers
- Tries to bypass  upper tier ISP by directly connecting lower tier ISPs or thourh IXPs
- Still, some access ISPs are reachable through connections on tier-1 ISPs

![](D:\_User\Desktop\images06.PNG)

