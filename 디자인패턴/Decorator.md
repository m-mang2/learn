객체에 동적으로 서비스를 추가 할 수 있음

상속을 사용해서 서비스를 추가하는 것 보다 유연한 방법으로 새로운 서비스를 추가 할 수 있음


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
