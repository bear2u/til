# Enum 이해

```cpp
#include <iostream>

using namespace std;

enum Status
{
    normal = 0,
    abnormal,
    disconnect = 100,
    close
};

int main()
{
    Status number = close;
    
    if (number == Status::normal)
        cout<<"Status : normal" << endl;
    else if (number == abnormal)
        cout<<"Status : abnormal" << endl;
    else if (number == 101)
        cout<<"Status : disconnect" << endl;
    else
        cout<<"Status : close" << endl;
        
    return 0;
}

>>>> Status : disconnect
```

enum의 요소들은 정수형 값을 갖는다. 

그래서 normal =0 을 먼저 선언을 했기 때문에 abnormal 은 1이 된다. 

disconnect = 100 을 선언해서 close 도 101이 된다. 

그리고 enum 정의시 이름만으로도 사용가능하다.

```
Status number = close;
```

# Enum class 사용

```cpp
/******************************************************************************

                              Online C++ Compiler.
               Code, Compile, Run and Debug C++ program online.
Write your code in this editor and press "Run" button to compile and execute it.

*******************************************************************************/

#include <iostream>

using namespace std;

enum Status
{
    normal = 0,
    abnormal,
    disconnect = 100,
    close
};

enum class MachineStatus : char
{
    normal = 'n',
    abnormal,
    disconnect = 100,
    close
};

int main()
{
    MachineStatus machine = MachineStatus::abnormal;
    
    if(machine == MachineStatus::normal)
        cout << "Status : normal" <<endl;
    else if(machine == MachineStatus::abnormal)
        cout << "Status : abnormal" <<endl;
    else if(machine == MachineStatus::disconnect)
        cout << "Status : disconnect2" <<endl;    
    else
        cout << "Status : close" <<endl;    
    return 0;
}

```

enum에서 중복적으로 이름 사용을 못하는 단점을 극복하기 위해 나온 enum class이다. 



