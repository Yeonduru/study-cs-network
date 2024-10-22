# 1. Four sources of packet delay 

![](../images/1-3-1.png)
$$
d_{\rm nodal} = d_{\rm proc} + d_{\rm queue} + d_{\rm trans} + d_{\rm prop}
$$

$$

$$
 **d_{proc}**: nodal processing < msec

**queueing dalay** : buffer에서 기다리는 정도

**transmission delay** : packet을 link에 밀어넣데 걸리는 시간 L/R. packet이 클수록 커지고 bandwidth가 커질수록 작아진다.

**propagation delay** : link에 실려서 propagation해야한다.  d/s (d: length of physical link / s : propagation speed )



#### **Queueing delay in detail** 

-->  이 중에서 queueing delay만 가변적이다. buffer에서 packet이 대기하게 되면서 발생하므로. 



R : Link bandwidth (bps)

L : packet length (bits)

a : average packet arrival rate

L*a : 단위시간당 유입되는 traffic의 양 = traffic intensity  (데이터를 주로 트래픽이라고 이야기 함)



La = R : 유입되는 양과 뽑아내는 속도가 같다. 

La/R ~ 0 : average queueing delay is small

La/R >= 1 : average queueing delay is large

La/R >= 1 : more ''work' arriving than can be serviced, average delay infinite! 



경우에 따라서 La >> R (일시적으로) 이므로 이 순간 delay 가 크게 발생하므로 traffic intensity 1 미만인 0.7에서도  크게 증가할 수 있다. 



![](../images/1-3-2.png)

# 2. Packet Loss 

- queue 는 한정적인 사이즈를 가지고 있으므로 발생하는 loss



# 3. Throughput

throughput : 단위시간당 전송된 트래픽의 양은 더 capacity가 작은 링크에 의해서 결정된다. 

![](../images/1-3-3.png)

Bottleneck link : link on end-end path that constrains end-end throughput 

보통은 internet core보다는 internet edge에서 발생한다.



# 4. Why layering ? 

- complex system을 관리하는데 있어서 유리하다.
- explicit structure allows identification, relationship of complex system's pieces
- modularization eases maintenance, updating of system 
- layer의 기능을 독립적으로 구현하다보면 레이어끼리 중복되는 기능이 있을 수 있다 (단점)
- 다른 layer의 정보가 필요한 경우 정보를 가져오는데 있어서 overhead가 발생한다. 





# 5. Internet protocol stack 

application에서 user message를 만들면 transport 계층에 process to process data transfer를 요청. transport 계층은 network계층에 host to host delivery 를 요청한다. 

![](../images/1-3-4.png)



![](../images/1-3-5.png)



Protocol Data Unit : message, segment, datagram , frame (어떤 계층이 만들었는지에 따라 이름이 다르다.)

Header는 나의 peer가 읽도록 하는 것이 목적이다. (ex) source transport <-> destination transport )

Transport 계층은 source와 destination간에 데이터를 잘 주고받았는지 확인한다. 



# 6. Network security 

1) 공격의 종류는 무엇인지 파악하는 것 

2) 어떻게 공격에 대비할 것인지 설계하는 것 

3) 공격에 immune한 네트워크 시스템을 설계하는 것





# 7. Malware

Malware에는 2가지 종류가 있다. (1) virus (2) worm

**(1) Virus** : receiving and executing해야 작동

**(2) worm** : 실행시키지 않아도 작동

ex) **Spyware malware** can record keystrokes, web sites visited, upload info to collection site

ex) **Botnet** is a set of compromised computers used for spam or DDoS attacks (쓸모없는 packet등을 계속 보내면서 서버를 다운시키는 것)



#### Denial of Service (DoS) : 트레픽을 끊임없이 보내면서  target server를 무력화시키는 것 



![](../images/1-3-6.png)



Packet "sniffing" : wifi처럼 서로 연결된 것인 end system에 NIC가 꽂혀있어서 나에게 오는 frame만 받아들이도록 설계되어있는데, NIC를 promiscuous mode로 변환시켜버리면 다른 host의 정보들까지 받아볼 수 있게된다.

IP sniffing : 마치 내가 다른 host인척을 하면서 ..  send packet with false source address 



# 8. 인터넷의 역사 

강의자료를 살펴보도록 합시다!
