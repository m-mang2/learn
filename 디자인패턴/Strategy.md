> 다양한 알고리즘이 존재하면 이들 각각을 하나의 **클래스로 캡슐화하여 알고리즘의 대체가 가능**하도록 함

```
struct IValidator
{
	virtual bool validate(const std::string& data, char c) = 0;
	virtual bool iscomplete(const std::string& data) { return true;}
	virtual ~IValidator() {}
};

class Edit
{
	std::string data;

	IValidator* val = nullptr; // 여기에 정책이 담김
public:
	void set_validator(IValidator* p) { val = p;}

	std::string get_text()
	{
		data.clear();

		while (1)
		{
			char c = _getch();

			if (c == 13 && ( val == nullptr || val->iscomplete(data)    )  ) break;
			
			if (val == nullptr || val->validate(data, c))
			{
				data.push_back(c);
			}
		}
		return data;
	}
};

```

인터페이스를 만들고 edit에서 약한 결합으로 다양한 Vaidation 정책 클래스 사용
![image](https://github.com/m-mang2/learn/assets/135841268/960e70a2-a843-45c5-a554-ae8634badea3)

### uml
![image](https://github.com/m-mang2/learn/assets/135841268/9505607c-64a8-441a-a17d-bdb004f40a19)


```
class DigitValidator : public IValidator // 숫자만 넣는 정책
{
	int count;
public:
	DigitValidator(int count = 9999) : count(count) {}

	bool validate(const std::string& data, char c) override 
	{
		return data.size() < count && isdigit(c);
	}
	bool iscomplete(const std::string& data) override 
	{
		return count != 9999 && data.size() == count;
	}
};


DigitValidator digit;
set_validator(&digit); // 새로운 정책을 넣음
```


## template method와의 차이점
* template method패턴은 가상함수로 만들어진 정책의 재사용이 불가능하지만 strategy 패턴은 정책과 기능이 분리되어 있어서 재활용이 가능함
  * 숫자만 쓸수있는 가상함수를 만들고 거기에 추가 조건을 붙이려면 새로 만들어야함
* template method은 실행 시간에 정책을 바꿀 수 없음(실행시간 교체가 없으니 빠름, 굳이 교체할 이유가 없다면 template method를 고려)
* template method은 가상함수로 구현 되어있어 있는 멤버 함수이므로 멤버 데이터에 접근 가능

