객체에 동적으로 서비스를 추가 할 수 있음

상속을 사용해서 서비스를 추가하는 것 보다 유연한 방법으로 새로운 서비스를 추가 할 수 있음

여러개의 기능을 **중복(중첩)** 해서 추가 할 수 있음


## UML
![image](https://github.com/m-mang2/learn/assets/135841268/bb5ee907-dcd9-42a1-b83e-1b061505592d)

예시)
그림을 Load해서 화면에 출력하는 클래스가 있는데
로드된 그림에 **액자나 말풍선등의 효과를 추가하고 싶을 때**
![image](https://github.com/m-mang2/learn/assets/135841268/e66ccc8b-ea25-412a-8292-989ba24f84e8)

```
class Image
{
	std::string image_path;
public:
	Image(const std::string& path) : image_path(path) 
	{	
		//이미지 로드
	}
	void draw() const 
	{ 
		std::cout << "draw " << image_path << std::endl; 
	}
};
```
## 방법1. 상속해서 액자를 그리는 기능 추가
```
class Frame : public Image
class Balloon : public Image 
```
* 상속을 사용한 서비스 추가의 특징
* 객체가 아닌 클래스에 기능을 추가 - Image img라는 객체를 만들면 여기에 그림이 이미 있는데 Frame fra이라는 객체를 만들어서 또 그림을 로드함
* 여러 개의 서비스를 중복해서 추가하기 어렵다 - Frame + Balloon의 그림을 그린다면 또 다른 클래스를 만들어야함

## 방법2. 상속이 아닌 포함
```
class Frame 
{
	Image* img;
public:
	Frame(Image* img) : img(img) {}

	void draw() const 
	{ 
		img->draw();
	}
};
```
* img는 포인터이므로 이미 생성된 객체를 가리킴
* Image는 재용해서 괜찮으나 Balloon + Frame를 하진 못함
![image](https://github.com/m-mang2/learn/assets/135841268/542dc69e-6f8d-4367-a640-feea0ba8161c)

## 방법.3 공통 인터페이스 추가
![image](https://github.com/m-mang2/learn/assets/135841268/99d71c2c-f69a-42e5-aa94-dc3743209f65)
```
class Frame : public IDraw
{
	IDraw* img;
public:
	Frame(IDraw* img) : img(img) {}

	void draw() const 
	{ 
		img->draw();
	}
};

// 최종 사용
Image img("www.image.com/car.jpg");
img.draw();

Frame frame(&img);
frame.draw();

Balloon balloon(&frame); // ?
balloon.draw();
```

## 최종
*모든 공통적인 특징을 담아서 데코레이터 클래스를 만듬
![image](https://github.com/m-mang2/learn/assets/135841268/5ebd75d0-7c64-4a61-892b-ec03e3f0a7ee)
```
struct IDraw
{
	virtual void draw() const  = 0;

	virtual ~IDraw() {}
};

class Image : public IDraw
{
	std::string image_path;
public:
	Image(const std::string& path) : image_path(path) {	}

	void draw() const { std::cout << "draw " << image_path << std::endl; }
};

class Decorator : public IDraw
{
	IDraw* img;
public:
	Decorator(IDraw* img) : img(img) {}

	void draw() const { img->draw();}
};

class Frame : public Decorator
{
public:
	Frame(IDraw* img) : Decorator(img) {}

	void draw() const 
	{ 
		Decorator::draw();
	}
};

class Balloon  : public Decorator
{
public:
	Balloon(IDraw* img) : Decorator(img) {}

	void draw() const 
	{ 
		Decorator::draw();
	}
};

int main()
{
	Image img("www.image.com/car.jpg");

	Frame frame(&img);

	Balloon balloon(&frame); // ?
	balloon.draw();
}
```
