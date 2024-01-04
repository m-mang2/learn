> 부분과 전체의 계층을 표현하기 위해 **복합객체를 트리 구조**로 만듬
>
>  **개별 객체와 복합 객체를 모두 동일하게 다를수 있도록** 한다

장점
* 새로운 메뉴를 추가하거나 메뉴의 구조를 변경하는것이 쉬움

![image](https://github.com/m-mang2/learn/assets/135841268/0376fc43-31d3-4078-bd69-4a0e31c6f1c3)


```
class BaseMenu
{
	std::string title;
public:
	BaseMenu(const std::string& title) : title(title) {}

	virtual ~BaseMenu() {}
	std::string get_title() const { return title; }
	void set_title(const std::string& s) { title = s; }

	virtual void command() = 0;
};


class MenuItem : public BaseMenu
{	
	int id;
public:
	MenuItem(const std::string& title, int id)
		: BaseMenu(title), id(id) {}

	void command()
	{
		std::cout << get_title() << " 메뉴 선택\n";
		_getch();
	}
};

class PopupMenu : public BaseMenu
{
	std::vector<BaseMenu*> v;
public:
	PopupMenu(const std::string& title) : BaseMenu(title) {}

	void add_menu(BaseMenu* m) { v.push_back(m); }

	void command()
	{
		while(1)
		{
			system("cls");

			std::size_t sz = v.size();
			std::size_t i = 0;

			for (BaseMenu* p : v)
			{
				std::cout << ++i << ". " << p->get_title() << "\n";
			}
			std::cout << i + 1 << ". 종료\n";
			std::cout << "메뉴를 선택해 주세요 >> ";

			int cmd;
			std::cin >> cmd;

			if ( cmd == sz + 1 )
				break;

			if ( cmd < 1 || cmd > sz + 1)
				continue;
			v[cmd - 1]->command(); //  BaseMenu에서 꼭 virtual로 사용해야하는 부분
		}
    }

	BaseMenu* submenu(int idx)
	{
		return v[idx];
	}
};

int main()
{
	PopupMenu* root = new PopupMenu("ROOT");
	PopupMenu* pm1 = new PopupMenu("해상도 변경");
	PopupMenu* pm2 = new PopupMenu("색상 변경");

	root->add_menu(pm1);
//	root->add_menu(pm2);
	pm1->add_menu(pm2);

	pm1->add_menu(new MenuItem("HD", 11));
	pm1->add_menu(new MenuItem("FHD", 12));
	pm1->add_menu(new MenuItem("UHD", 13));

	pm2->add_menu(new MenuItem("RED",   21));
	pm2->add_menu(new MenuItem("GREEN", 22));
	pm2->add_menu(new MenuItem("BLUE",  23));
	pm2->add_menu(new MenuItem("BLACK", 24));

	// 메뉴를 시작하려면 ??
	root->command();
}

```




하위 객체에서add_menu()를 사용할 때는 MenuItem에는 add_menu()가 없기때문에 함수를 베이스 클래스에 올리거나 혹은 캐스팅하여 사용하야함
두가지 모두 가능하며 타입안정성이나 일관성 중 어느걸 중시하는냐에 따라 달라짐
```
// 타입 안정성
static_cast<PopupMenu*>(root->submenu(0))->add_menu( new MenuItem("HD", 21));

// 일관성 유지
class BaseMenu
{
public:
	BaseMenu(const std::string& title) : title(title) {}

	virtual BaseMenu* submenu(int idx) { throw no_implementation();}
	virtual void add_menu(BaseMenu* m) { throw no_implementation(); }
};
```
![image](https://github.com/m-mang2/learn/assets/135841268/1b7a19cc-ed13-4842-a3db-13c251e64436)
