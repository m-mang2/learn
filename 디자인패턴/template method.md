> 알고리즘의 처리과정은 변경하지 않고 알고리즘 각 단계의 처리를 서브클래스에서 처리

```
class Shape
{
public:
	virtual ~Shape() {}

	void draw() 
	{
		path.begin();
		draw_imp(path); // 중복되는 부분은 그대로 두고 다른곳만 가상함수로 빼서 서브 클래스에서 구현함
		path.end();

	}

protected:
	virtual void draw_imp(PainterPath& path) = 0;
};

class Rect : public Shape
{
protected:
	void draw_imp(PainterPath& path) override
	{
		path.draw_rect();
	}
};


class Circle : public Shape
{
protected:
	void draw_imp(PainterPath& path) override
	{
		path.draw_circle();
	}
};


Shape* s1 = new Rect;
Shape* s2 = new Circle;
s1->draw();
s2->draw();

```
## 핵심
* 변하지 않는 코드 내부에서 변해야 하는 코드를 찾음
* 변해야 하는 코드는 가상함수로 분리
* 파생 클래스에서 가상함수를 재정의하여 변경이 필요한 부분만 다시 만듦
