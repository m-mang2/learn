> **일관된 하나의 인터페이스**를 제공
> 
> 서브 시스템을 **사용하기 쉽게하기 위환 포괄적 개념의 인터페이스**


(구조가 딱히 중요한것은 아니고 사용자들이 쉽게 사용하도록 인터페이스를 제공하는것)
 
![image](https://github.com/m-mang2/learn/assets/135841268/3c619806-1e75-4963-9172-19b611b9990e)

# 네트워크를 연결하는 로직
## 1차 API 기반의 코드
c기반 코드로 복잡함
```
int main()
{
	WSADATA w;
	WSAStartup(0x202, &w);

	int sock = socket(PF_INET, SOCK_STREAM, 0); 

	struct sockaddr_in addr = { 0 };
	addr.sin_family = AF_INET;
	addr.sin_port = htons(4000);
	addr.sin_addr.s_addr = inet_addr("127.0.0.1");

	bind(sock, (SOCKADDR*)&addr, sizeof(addr));

	listen(sock, 5);

	struct sockaddr_in addr2 = { 0 };
	int sz = sizeof(addr2);

	accept(sock, (SOCKADDR*)&addr2, &sz);

	WSACleanup();
}
```

## 2차 API 기반코드
객체 지향 코드로 기능별로 코드가 분리되어있음
그러나 이걸 사용하기 위해서 bind, listen, accept가 순서대로 작동해야한다거나 어쨋든 기능에 대해 학습이 된 상태이어야함
```
class NetworkInit
{
public:
	NetworkInit();
};

// IP 주소를 관리하는 클래스
class IPAddress
{
	SOCKADDR_IN addr;
public:
	IPAddress(const char* ip, short port);
	SOCKADDR* getRawAddress();
};

// Socket 작업을 책임지는 클래스
class Socket
{
public:
	Socket(int type) { sock = socket(PF_INET, type, 0); }
	void Bind(IPAddress* ip);
	void Listen();
	void Accept();
};

// 사용
NetworkInit init;
Socket sock(SOCK_STREAM); 

IPAddress addr("127.0.0.1", 4000);
sock.Bind(&addr);
sock.Listen();
sock.Accept();
```

## facade
학습 비용이 크지 않음
결국 사용하기 쉽게하기 위환 포괄적 개념의 인터페이스를 말하는것으로 구조는 따로 없음
```
class TCPServer
{
	NetworkInit init;
	Socket sock{SOCK_STREAM};
public:
	void Start(const char* ip, short port)
	{
		IPAddress addr(ip, port);
		sock.Bind(&addr);
		sock.Listen();
		sock.Accept();
	}
};

// 사용
TCPServer server;
server.Start("127.0.0.1", 4000);
```
