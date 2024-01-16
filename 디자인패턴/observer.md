> 객체 사이의 **1:N의 종속성을 정의**하고 **한 객체의  상태가 변하면  종속된 다른 객체들에 통보가 가고 자동으로 수정**이 일어나게 함


![image](https://github.com/m-mang2/learn/assets/135841268/ac5be303-a489-4f82-b9ca-17ef3d6955da)

옵저버 패턴을 만들기 위해서는
* 공통의 인터페이스가 필요
* 다양한 종류의 차트를 보관이 필요
* 차트를 등록/취소하는 함수가 있어야함
* 변화가 생기면 등록된 모든 곳에 통보해야함
![image](https://github.com/m-mang2/learn/assets/135841268/38df73b9-9a52-400d-bde5-2eb181619676)

```
struct IChart
{
	virtual void update(int n) = 0;
	virtual ~IChart() {}
};

class Subject
{
	std::vector<IChart*> v;
public:
	virtual ~Subject() {}
  // 등록/등록 취소
	void attach(IChart* p) { v.push_back(p); }
	void detach(IChart* p) { }

  // 등록된 모든 챠트에 알려준다.
	void notify(int data)
	{
		for (auto p : v)
			p->update(data);
	}
	
};

class Table : public Subject
{
	int data;
public:
	void edit()
	{
    notify(data);// 변경이 일어나면 notify를 통해 등록된 함수들한테 알려줌
	}
};

// notify를 받을 객체들
class BarChart : public IChart
{
public:
	void update(int n) override{}
};

class PieChart : public IChart
{
public:
	void update(int n) override{}
};

Table t;
BarChart bc; t.attach(&bc);
PieChart pc; t.attach(&pc);
	
t.edit();	// table 편집
```
