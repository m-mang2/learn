## 단위 전략 패턴
* 전략패턴(strategy) + 성능향상


### vector의 메모리 관리 방법으로 보는 패턴
#### template method
* 코드 재활용이 어려워 list나 set을 만들때도 파생클래스를 만들어야함 
```
template<typename T>
class vector
{
	T* ptr;
pubilc:
	void resize(std::size_t newsize)
	{
		ptr = allocate(size);

		deallocate(ptr, size);
	}

	virtual T*   allocate(std::size_t size)   { return new T[size];}
	virtual void deallocate(T* ptr, std::size_t size) { delete[] ptr;}
};

template<typename T> 
class malloc_vector : public vector<T>
{
public:
	T*   allocate(std::size_t size)   { return static_cast<T*>(malloc(sizeof(T)*size));}
	void deallocate(T* ptr, std::size_t size) { free(ptr);}
};
```

#### strategy
* 파생 클래스를 만들지 않고 재활용을 할 수 있음
* 가상함수에 비해 **느림**
```
template<typename T> struct IAllocator
{
	virtual T* allocate(std::size_t newsize) = 0;
	virtual void deallocate(T* ptr, std::size_t size) = 0;
	~IAllocator() {}
};

template<typename T>
class vector
{
	T* ptr;

	IAllocator<T>* ax = nullptr;
pubilc:
	void set_allocator(IAllocator<T>* p) { ax = p;}

	void resize(std::size_t newsize)
	{
		ptr = ax->allocate(size);

		ax->deallocate(ptr, size);
	}
};

template<typename T>
class malloc_allocator : public IAllocator<T>
{
public:
	T*   allocate(std::size_t size)  override { return static_cast<T*>(malloc(sizeof(T)*size));}
	void deallocate(T* ptr, std::size_t size) override { free(ptr);}
};

```

#### policy
* 인터페이스 교체 대신 템플릿 인자를 사용해서 교체
```
template<typename T, typename Ax = std::allocator<T> >
class vector
{
	T* ptr;
	Ax ax;
pubilc:
	void resize(std::size_t newsize)
	{
		ptr = ax.allocate(size);

		ax.deallocate(ptr, size);
	}
};

template<typename T>
class malloc_allocator 
{
public:
	inline T*   allocate(std::size_t size)  { return static_cast<T*>(malloc(sizeof(T)*size));}
	inline void deallocate(T* ptr, std::size_t size) { free(ptr);}
};
```

![image](https://github.com/m-mang2/learn/assets/135841268/33b515e8-6a3a-40c2-8196-be77fac199a8)
