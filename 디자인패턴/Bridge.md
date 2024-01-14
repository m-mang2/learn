> **구현과 추상화 개념을 분리하여 각각을 독립적으로 변형** 할 수 있게 함
>
> PIMPL라고도 불림 (Point to IMPLementation) IMP3* impl;
> 
![image](https://github.com/m-mang2/learn/assets/135841268/8da61215-d503-451e-b16c-21269f5a182b)
특정 기능을 구현한 계층을 사용하는 사용자 1,2,3이 있는데 여기서 구현이 수시로 업데이트 된다면 사용자들도 같이 업데이트가 자주 되어야 하기 때문에 불편


![image](https://github.com/m-mang2/learn/assets/135841268/13c5de52-d397-4bf9-b5b6-21c6210e671b)
그래서 간접층을 만들어서 간접층만 업데이트 하여서 사용자로 하여금 업데이트 없이 기능을 이용할 수 있도록 변경함


## UML
![image](https://github.com/m-mang2/learn/assets/135841268/36e5365e-2624-44cb-85b4-bbfd9bc09176)

IPod 클래스를 바로 사용하면 다른 제품으로 불가능한 강한 결합이됨
```
class IPod
{
public:
	void play() { std::cout << "Play MP3 with IPod" << std::endl; }
	void stop() { std::cout << "Stop" << std::endl; }
};

class People
{
public:
	void use(IPod* p)  
	{				  					  
		p->play();
		p->stop();		

		p->play_one_minute();
	}
};

People p;
IPod pod;
p.use(&pod);
```
약한 결합으로 변경하기 위해 IMP3으로 인터페이스를 두고 사용
다른 제품으로 교체 가능
```
struct IMP3
{
	virtual void play() = 0;
	virtual void stop() = 0;

	virtual ~IMP3() {}
};

class IPod : public IMP3
{
public:
	void play() { std::cout << "Play MP3 with IPod" << std::endl; }
	void stop() { std::cout << "Stop" << std::endl; }
};

class People
{
public:
	void use(IMP3* p) 
	{				  					  
		p->play();
		p->stop();		

		p->play_one_minute();
	}
};
```

다른 제품으로 교체는 가능하나
mp3의 기능 업데이트가 일어나면 모든 클래스를 바꿔야함
그래서 중간층을 둠
![image](https://github.com/m-mang2/learn/assets/135841268/9bf78cbc-d3de-4478-b62f-4bb887d5a9f1)
```

class MP3
{
	IMP3* impl; // IMP3를 내부에 가지고있음
public:
	MP3(IMP3* p = nullptr) : impl(p)
	{
		if (impl == nullptr)
			impl = new IPod;
	}

	void play() { impl->play(); } // 데코랑 유사
	void stop() { impl->stop(); }
	
	void play_one_minute() // People계층에서 1분듣기가 필요하다면 IMP3까지 가서 변경할 필요없이 MP3단에서 수정 필요(물론 기능 조합으로 변경이 불가능하다면 IMP3로 가야함)
	{
		impl->play();
		// 1분 후에.. 
		impl->stop();
	}
};

class People
{
public:
	void use(MP3* p) 
	{				  					  
		p->play();
		p->stop();		

		p->play_one_minute();
	}
};
```


