> 인터페이스를 **클라이언트가 기대하는 형태의 인터페이스로 변환**

Adapter의 UML모습
![image](https://github.com/m-mang2/learn/assets/135841268/6100c47b-c54a-4d28-b8da-fa735dfb4db3)

예시)
CoolText는 기존에 만들어진 클래스로 문자열을 보관했다가 출력하는 기능
도형편집기에서 Rect, Circle를 그리는데 추가로 문자도 그릴 수 있는 도형이 필요함
도형 편집기에서 도형을 그리려면 Shape를 상속받아서 그리는 인터페이스를 제공해야함
```
class CoolText 
{
	std::string text;
	std::string font_name;
	std::size_t font_size;
	// ...... 	
public:
	CoolText(const std::string& text) 
			: text(text) 
	{		
	}

	void show() { std::cout << text << std::endl;}
};

class Shape
{
public:
	virtual ~Shape() {}

	virtual void draw() = 0;
};

class Rect : public Shape
{
public:
	void draw() override { std::cout << "draw Rect" << std::endl;}
};
class Circle : public Shape
{
public:
	void draw() override { std::cout << "draw Circle" << std::endl;}
};
```
Shape, CoolText를 상속받으며 draw에서는 CoolText::show()를 대신 호출함
Shape를 상속받았기 때문에 도형 편집기에 들어갈 수 있으며 CoolText의 show함수만 치환하여 그림
```
class ClsAdapter : public CoolText, public Shape 
{
public:
	ClsAdapter(const std::string& text) 
			: CoolText(text) {}

	void draw() override { CoolText::show();}
};	
```

## adapter의 종류
1. 클래스 어뎁터(상속) : 클래스를 상속받아서 사용(위 예시)
2. 객체 어뎁터(포함) : 기존에 생성된 객체의 데이터를 가지고 사용

객체 어뎁터
```
class ObjAdapter : public Shape 
{
	CoolText* ct;
public:
	ObjAdapter(CoolText* ct) : ct(ct) {}

	void draw() override { ct->show();}
};	

// 이렇게 사용가능
CoolText ct;
std::vector<Shape*> v;
v.push_back( new ObjAdapter(&ct)); // ObjAdapter로 한번 감싸서 넣음
```

![image](https://github.com/m-mang2/learn/assets/135841268/711a59a1-7128-4c70-94f8-6201e734ba19)

## STL

스택을 만들때 
### 방법1. list를 상속을 사용한다면 stack에서는 사용하지않는 push_front같은것도 사용 가능 하게됨(좋지못함)
```
template<typename T> 
class stack : public std::list<T>
{
public:
	void push(const T& a) 
	{ 
		std::list<T>::push_back(a); 
	}
	void pop()			  
	{ 
		std::list<T>::pop_back(); 
	}
	T&   top()            
	{ 
		return std::list<T>::back(); 
	}
};
```

## 방법2. private 상속을 받는다면 기반클래스의 맴버함수를 파생클래스에서 사용불가능 그렇지만 c++에서만 사용가능
```
template<typename T> 
class stack : private std::list<T>
{
public:
	void push(const T& a) 
	{ 
		std::list<T>::push_back(a); 
	}
	void pop()			  
	{ 
		std::list<T>::pop_back(); 
	}
	T&   top()            
	{ 
		return std::list<T>::back(); 
	}
};
```

## 방법3. 포함으로 사용(가장좋지만..) 만약 가상함수를 오버라이딩 하고 싶을 때 할 수 없음
```
template<typename T> 
class stack 
{
	std::list<T> c;
public:
	void push(const T& a) 
	{ 
		c.push_back(a); 
	}
	void pop()			  
	{ 
		c.pop_back(); 
	}
	T&   top()            
	{ 
		return c.back(); 
	}
};
```

## 방법4. 단위 전략 디자인(policty base design), 기본은 deque로 하되 원한다면 list나 vector등 다른것으로 교체 가능
```
template<typename T, typename C = std::deque<T> > 
class stack 
{
	C c; // class adapter, stl에는 이렇게 되어 있음 생성할 때 지정하니 굳이 교체할 필요가 없긴함
//	C* c; // object adapter
public:
	constexpr void push(const T& a) { c.push_back(a); }
	constexpr void pop()		    { c.pop_back(); 	}
	constexpr T&   top()            { return c.back(); }
};
```


