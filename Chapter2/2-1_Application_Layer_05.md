## DNS Records
- DNS 서버는 여러가지 호스트 이름과 IP 주소 매핑 정보(= Resource Records. RRs)를 저장하고 있음.

- RR은 4개 필드로 구성됨
   : Name, Value, Type, TTL
   > TTL: "Time To Live" = 이 정보가 유효한 시간.
   > TTL이 지나면 캐시에서 이 정보가 지워진다.

- **Type=A**  
   Name: hostname  
   Value: IP address

   ex. (relay1.bar.foo.com, 145.37.93.126, A)

   DNS의 가장 기본적인 기능은 host name과 ip 주소를 매핑시켜 주는 것. Type A가 Standard.

- **Type=NS**  
   Name: domain (ex. "foo.com")  
   Value: domain name server (hostname of authoritative name server for this domain)

   - 이 도메인에서 호스트의 IP 주소를 어떻게 얻어야 하는지 알고 있음
   - DNS 쿼리를 라우팅하는데 사용됨
   - TLD 서버가 여기에 해당

   ex. (foo.com, dns.foo.com, NS)

- **Type=CNAME**  
   Name: alias hostname  
   Value: canonical hostname

   - www.ibm.com을 주소창에 쳤을 때 접속해서 보면 servereast.backup2.ibm.com이다? 그럼 이 경우임
   (실제 hostname이 우리가 아는 alias와 다른 경우)

   ex. (foo.com, relay1.bar.foo.com, CNAME)

- **Type=MX**  
   Name: alias hostname  
   Value: 메일 서버의 canonical name

   - 메일 서버의 hostname 대신 심플한 alias를 쓸 수 있게 해줌

   ex. (foo.com, mail.bar.foo.com, MX)

> - 다른 서버의 canonical name을 얻고 싶다? : CNAME record를 쿼리해야 함
> - 메일 서버의 canonical name을 얻고 싶다? : MX record를 쿼리해야 함

## DNS Messages
- 종류는 딱 두 개. Query & Reply
- 포맷은 둘 다 똑같음

<img width="1029" alt="DNS Message Format" src="https://user-images.githubusercontent.com/26888051/77214056-7c964500-6b50-11ea-8f67-d7b4f287f089.png">

**Header Section**: 앞쪽 12Bytes의 고정 필드

   - Identification:
        - 쿼리 확인을 위한 16-bit 숫자
        - reply 메시지로 복사되어서 나중에 클라이언트가 query에 대한 reply가 뭔지 매칭할 때 쓰임
- Flags:
  - 여러 개 flags가 있음. ex. QR flag => 0이면 query, 1이면 reply라는 걸 나타냄.
  - 그 외에의 flag는 또 뭐가 있나 궁금하면 여길 보시라 => [DNS Header | 정보통신기술용어해설](http://www.ktword.co.kr/abbr_view.php?m_temp1=2194)
- 'number-of' 필드 4개: 헤더 뒤에 이어지는 4개의 가변 필드 각각 정보의 수
  

**Questions Section** 
   - query가 실림
   - 매핑 구하기를 원하는 name과 type을 적음  
      ("foo.com", A) => IP address 알려줘  
   ("foo.com", MX) => 메일 서버 알려줘

**Answer Section** 

   - RR이 실림 (Type, Value, TTL)
   - RR 여러 개 O

> 즉 Question Section이 query를 위한 필드라면, Answer Section은 reply를 위한 필드인 것. reply라면 Question Section이 비고, RR이 한 개 이상 들어감

**Authority Section**
  - 다른 authoritative server의 records.  
    authoritative 서버에서 reply가 오는 거라면 Answer Section 대신 Authority Section에 RR이 담김.

**Additional Section**
   - query에서 물어본 게 아니더라도 추가적으로 제공해줌직한 정보(RR)
      (MX 쿼리로 메일 서버가 뭐냐고 물었으면 그 답인 canonical hostname은 RR에 담지만, 추가적으로 서버 IP 주소는 여기에 담아 보내 주는 식... 말그대로 "helpful records")

> 터미널이나 명령 프롬프트 열어서 `nslookup` 으로 DNS 서버(root, TLD, authoritative)에 query 해보기  
>
> "Nslookup is a program to query Internet domain name servers."
>
> ```
> nslookup [-option] [name | -] [server]
> ```

## Inserting Records into DNS Database
"Network Utopia 스타트업을 창업했다! 뭘 해야 할까!"

1. Authoritative Server를 구매한다.
   회사에 구비하게 될 모든 컴퓨터에 대해서 hostname과 IP Addres 매핑해야 하니까.
   
+ Web Server, Mail Server도 사야지...
   
1. Authoritative Server에
   - Web Server hostname : IP Address 매핑할 type A
   - Mail Server를 위한 type A, type MX 
    저장한다.

1. 외부에 도메인을 등록한다.
   
   TLD 관리 기관(= DNS registrar. ex. [Network Solutions](https://www.networksolutions.com/). 한국은 '[한국인터넷정보센터](https://xn--3e0bx5euxnjje69i70af08bea817g.xn--3e0b707e/)')에 
   - "networkutopia.com"라는 유니크한 도메인 이름
   - Authoritative Server의 이름(dns1.networkutopia.com)
   - IP Address(212.212.212.1)
   
   를 등록한다.
   
1. 그럼 DNS registrar에서 DNS database(TLD com server)에 이 도메인 이름을 넣는다.  
   (networkutopia.com, dns1.networkutopia.com, NS)  
   (dns1.networkutopia.com, 212.212.212.1, A)

1. 이제 사람들이 우리 웹 사이트를 방문하거나 우리 회사에 메일을 보낼 수 있다!

## P2P File Distribution
### P2P 구조
   - 항상 온라인인 인프라 서버가 있는 게 아님! (minimal or no reliance on always-on infrastructure servers)
   - End Systems(= peers)끼리 직접 커뮤니케이션
   - Self-Scalability: 확장성 (사용자 수의 증대에 유연하게 대응할 수 있음)  
     : 각 엔드시스템이 service request & service capability를 동시에 갖고 들어오기 때문

   ex. 파일 분산 시스템(bitTorrent), 스트리밍(kankan), VoIP(Skype) 등

### Scalability(확장성)

   ```
   Us: server upload capacity (= 단위 시간 당 얼마나 많이 업로드할 수 있느냐)
   Ui: peer i upload capacity
   Di: peer i download capcity
   F: file size
   ```

   (1) File Distribution Time: Client - Server
   - 서버가 파일 하나 업로드하는데 걸리는 시간 : F/Us
   - 엔드 시스템이 N개라면, 서버가 업로드해야 하는 건 N * F/Us
   - 클라이언트가 다운받으려면 Di  
     그 중 가장 최악을 Dmin이라고 하면

    클라이언트 다운로드 최대 시간: F/Dmin

   - 결국 Dc-s >= max { NF/Us, F/Dmin}

    : N이 커지면 NF/Us는 linear하게 늘어남
    사용자가 많아질수록 File Distribution time이 ↑ 

   (2) File Distribution Time: P2P
   - 똑같이 F/Us, F/Dmim, N명의 엔드 시스템 상정
   - Dp2p >= max { F/us, F/Dmim, NF/(Us + ∑Ui)}  
      `NF/(Us + ∑Ui)` => 여기서 Us는 잠깐 제쳐두면, 결국 F/U. 즉, N이 증가해도 상수로 남음

<img width="887" alt="Distribution time for P2P and client-server architectures" src="https://user-images.githubusercontent.com/26888051/77214088-abacb680-6b50-11ea-9d5f-debe3156cd42.png">

### BitTorrent

1. 파일을 전부 250KB의 chunks(청크)로 쪼갠다.  
   이 생태계(?) 안엔 peers를 트래킹하는 tracker, file chunks 교환 그룹 torrent가 있다.
1. Alice가 진입하면, tracker로부터 필요한 파일을 가진 peers의 목록을 얻는다.
1. peers로부터 chunks를 받는다. 또 다른 chunks를 받는 동안 보유하고 있는 chunks를 공급한다.

<img width="741" alt="File Distribution with BitTorrent" src="https://user-images.githubusercontent.com/26888051/77214229-88363b80-6b51-11ea-8188-afb692806f76.png">


**churn**: peers들이 자유롭게 torrent를 드나듦.
이 특징으로 인해...
- 내 청크의 공급자가 사라지면 빨리 다음 공급자를 찾아야 함
- peer가 이기적이라 먹튀할 수도 있음...

여기서 원칙이 도출되는데,
- **request의 원칙**: "rarest first" 
- **send의 원칙**: "tit for tat"
   - 잘 공급받은만큼 드림
   - Top 4 Peers에게 많이 드림
   - 단, 30초마다 랜덤한 Peer를 골라 청크를 줘봄
   - 교환이 잘 일어나면 Top 4 Peers 갱신

# Socket Programming
- **소켓**이란 (프로그래머가 작성하는) 애플리케이션 프로그램과 운영체제가 컨트롤하는 transport 계층 사이의 인터페이스
- 클라이언트 프로세스와 서버 프로세스가 커뮤니케이션 할 때 소켓을 '연결부'로 이용함. 집 : 문 = 프로세스 : 소켓.
- 소켓 타입
   - UDP(User Datagram Protocol)
      - connection X
      - 독립적인 데이터 패킷
      - 데이터를 보내기 전 handshaking 없음
      - 각 데이터마다 IP Address와 포트 번호를 붙여서 보냄
      - 전송에 대한 보장 없음   
   - TCP(Trasmission Control Protocol)
      - connection O
      - reliable byte-stream channel (일종의 pipe처럼)

## Socket Programming with UDP

<img width="705" alt="The client-server application using UDP" src="https://user-images.githubusercontent.com/26888051/77214768-f0861c80-6b53-11ea-9b31-a2b1c4570cd5.png">

- 서버가 먼저 실행되고 있어야 함.
- 클라이언트에서 데이터 패킷을 보내기 전에, 일단 목적지 주소(IP Address, 포트 번호)를 패킷에 붙여야 함
- 인터넷이 이 주소를 보고 패킷을 목적지 호스트로 보냄
- 서버 소켓에서 패킷을 받으면, 서버 프로세스에서 패킷의 컨텐츠를 확인하고 적절한 액션을 취함

> 서버의 경우엔 명시적인 포트 번호가 있다. 그러나 클라이언트의 포트 번호는 알려져있지 않다.  
> 그렇다면 서버가 클라이언트의 IP Address와 포트 번호를 어떻게 아나?  
> => UDP 애플리케이션 코드 단에선 알 수 없지만 OS가 알아서 붙여주기 때문에, 서버는 이를 받은 세그먼트에서 추출할 수 있다.

## Socket Programming with TCP

<img width="511" alt="The TCP Server process has two sockets" src="https://user-images.githubusercontent.com/26888051/77214819-1f9c8e00-6b54-11ea-93b8-606d547e6023.png">

<img width="756" alt="The client-server application using TCP" src="https://user-images.githubusercontent.com/26888051/77214964-f92b2280-6b54-11ea-98a3-2aaf96375a15.png">

- 서버에서 Door Socket(=Welcoming Socket)을 생성하여 클라이언트의 contact를 받아들일 준비를 함
- 클라이언트에서도 서버와의 handshake를 위해 Socket을 생성해 서버 IP Address와 포트 번호를 명시함
- 커넥션이 생성되면 서버에서 이 클라이언트를 위한 전용 소켓(Connection Socket)을 별도로 생성함. 일단 커넥션을 셋업하면 서로를 알고 있기 때문에 목적지를 명시하지 않아도 됨
- 클라/서버 모두 데이터를 주고 받을 수 있음
- 커뮤니케이션이 끝나면 connection socket만 닫음
