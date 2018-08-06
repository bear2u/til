# Day2 \( 31 ~\)

자료형의 크기를 이해해보자. 

```cpp
#include <iostream>

using namespace std;

class Temp {
    int no;
    bool is_on;
};

int main()
{
    cout << "char sizeOf : " << sizeof('p') << endl;
    cout << "int sizeOf : " << sizeof(10) << endl;
    cout << "double sizeOf : " << sizeof(10.0) << endl;
    cout << "class sizeOf : " << sizeof(Temp) << endl;
    
    return 0;
}

=================
char sizeOf : 1                                                                                                                                                                   
int sizeOf : 4                                                                                                                                                                    
double sizeOf : 8                                                                                                                                                                 
class sizeOf : 8 
```

> Temp 클래스 사이즈가 8은 왜 이렇게 나오는 걸까?

int 가 4이고 bool 이 1인데 5가 나와야 하지 않을까?? 라는 의문을 갖게 된다. 

**이건 컴퓨터 프로세서의 데이터 버스와 관련이 있는데, 데이터 버스는 한번에 4바이트 데이터를 담아 이동한다. 그래서 4바이트 단위로 몇번 움직이냐에 따라 sizeof의 결과가 달라집니다. 클래스 Temp는 5바이트이기 때문에 데이터 버스는 두번 이동합니다. 그래서 4바이트를 담는 버스가 두번 이동하여 결과는 8일 나옵니다. **





