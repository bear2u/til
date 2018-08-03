# Day1

```cpp
// Example program
#include <iostream>
#include <string>

using namespace std;

int main()
{
  cout << "Hello C++ World" << endl;

  return 0;
}
```

* cout : 출력
* end1 : 개행한다는 뜻
* using namespace std

```cpp
// Example program
#include <iostream>
#include <string>

using namespace std;

int main()
{
  int number = 0;

  cin >> number;

  cout << "your code is " << number << " ok!!" << endl;

  return 0;
}
```

* cin : 값을 입력받음

```cpp
// Example program
#include <iostream>
#include <string>

using namespace std;

int main()
{
  int x = 10;
  int y = -1;
  
  if (x > y)
    cout << "x is bigger than y" << endl;
  else
    cout << "x is lower than y" << endl;
  
  return 0;
}
```

# 네임스페이스 이용

```cpp
// Example program
#include <iostream>
#include <string>

using namespace std;

namespace silla
{
    int year = 938;
    
    void CentralArea()
    {
        cout << "Busan" << endl;
    }
}

namespace baekjae
{
    int year = 660;
    
    void CentralArea()
    {
        cout << "Seoul" << endl;
    }
}


using namespace silla;
using namespace baekjae;

int main()
{
  silla::CentralArea();
  baekjae::CentralArea();
  
  return 0;
}

```

# 캐스팅 연산자 이해하기

```cpp
// Example program
#include <iostream>
#include <string>

using namespace std;

int main()
{
    int x =2;
    double y = 4.4;
    
    int i = static_cast<int>(y / x);
    int j = (int)y / x;
    double k = y / x;
    
    cout << "4.4 / 2 = ( static_cast<int>) " << i << endl;
    cout << "4,4 / 2 = (int) " << j << endl;
    cout << "4.4 / 2 = " << k << endl;
  
  return 0;
}

```

* **static\_cast &lt;&gt; : 가장 기본적인 캐스트 연산 방법**
* dynamic\_cast&lt;&gt; : 객체지향 언어의 다형성을 이용하여 모호한 타입 캐스팅 오류를 막아줌
* const\_cast : 자료형이 갖고 있는 상수 속성을 제거
* reinterpret\_cast&lt;&gt; : 어떠한 포인터 타입끼리도 변환할 수 있게끔 도움



