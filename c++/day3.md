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



