# Call By Value

```cpp
// Example program
#include <iostream>
#include <string>

using namespace std;

void Func(int arg)
{
  cout << "before changed : " << arg << endl;
  arg+=10;
  cout << "after changed : " << arg << endl;
}

int main()
{
  int year = 10;

  Func(year);

  cout << "finsh : " << year << endl;

  return 0;
}
```

전달 한 인자만 변경이 이루어지므로 year값은 그대로 이다.

```
10
20
10
```

# Call By Reference

```cpp
// Example program
#include <iostream>
#include <string>

using namespace std;

void Func(int &arg)
{
  cout << "before changed : " << arg << endl;
  arg+=10;
  cout << "after changed : " << arg << endl;
}

int main()
{
  int year = 10;

  Func(year);

  cout << "finsh : " << year << endl;

  return 0;
}
......................
10
20
20
```

# Call By Address

```
// Example program
#include <iostream>
#include <string>

using namespace std;

void Func1(bool *is_on)
{
  cout << "Call by address : " << sizeof(is_on) << endl;
}

void Func2(bool &is_on)
{
  cout << "Call by reference : " << sizeof(is_on) << endl;
}

int main()
{
  bool test = true;
  
  Func1(&test);
  Func2(test);
  
  return 0;
}

.....................
8
1
```

Func1 인자로 주소를 명시적으로 알려는 경우 포인터자체만의 그 해당 사이즈인 8이 나오고.

Func2 인자로 넘길경우 자료형 크기로 넘어가서 1이 나온다. 







