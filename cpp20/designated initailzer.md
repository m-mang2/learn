## Aggregate initialization
배열, 클래스를 초기화 할 때 **중괄호를 사용해서 맴버들을 초기화**하는것
조건
1. static 맴버가 없어야 함
2. private, proteced 맴버가 없어야 함
3. 상속 받지 말아야 함
4. 가상 함수가 없어야 함
5. 생성자가 없어야 함

## designated initailzer
Aggregate initialization를 사용 할 때, **맴버의 이름을 사용해서 초기화** 하는 것
```
struct Point
{
    int x;
    int y;
}

Point p1 = {.x =1, .y = 2}; 
```
주의점
* 선언된 순서대로 초기화 해야함 {.y = 2, .x = 1} error
* 이름 지정 안하면 안됨 {.x = 1, 2}
* rect r3 = {leftTop.x = 10} leftTop, rightbottom 이렇게 있는데 앞에꺼만 초기화 해도 안됨
* 배열 중간에 넣으면 안됨 struct data {int arr[3]}, data d = {[1] = 2};
