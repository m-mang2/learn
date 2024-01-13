> 다른 객체에 접근하기 위해 **중간 대리 역할을 하는 객체**둔다
> 다양한 문제를 해결하기 위한 간접층

![image](https://github.com/m-mang2/learn/assets/135841268/a106d3d2-218d-45d6-849b-7c3c4b047ed6)

그림을 로드해서 그려주는 객체가 있음
```
class Image
{
	std::string name;
public:
	Image(const std::string& name) : name(name)
	{
		std::cout << "open " << name << '\n';
	}
	void draw() { std::cout << "draw " << name << '\n'; }

	int width()  const { return 100;}
	int height() const { return 100;}
};
```
그런데 실제 사용을 보니 대다수가 width, height만 사용하고 있음 그래서 사용하지 않는 이미지를 로드를 하여 메모리가 증가함

실제 그림을 로드하진 않고 크기정보만 얻어오면 됨

```
struct IImage
{
	virtual void draw() = 0;
	virtual int width() const = 0;
	virtual int height() const = 0;
	virtual ~IImage() {}
};

class Image : public IImage
{
	std::string name;
public:
	Image(const std::string& name) : name(name)
	{
		std::cout << "open " << name << '\n';
	}
	void draw() { std::cout << "draw " << name << '\n'; }

	int width()  const { return 100;}
	int height() const { return 100;}
};


class ImageProxy : public IImage
{
	std::string name;
	Image* img = nullptr;
public:
	ImageProxy(const std::string& name) : name(name) {}

	int width()  const { return 100;} // 파일헤더에서 정보 획득
	int height() const { return 100;} // 파일헤더에서 정보 획득

	void draw() 
	{
		if ( img == nullptr ) // 실제 그림을 그리기 전까진 메모리에 생성하지 않고 지연시킴
			img = new Image(name);

		img->draw(); // 그리는 기능은 Image에 위임
	}
};
```
이런 proxy객체로 여러기능을 할수있음
* 지연된 생성
* 보안(인증)추가
* 기능의 추가(로그 기록 같은것)
* 원격지 서버에 대한 대행자
