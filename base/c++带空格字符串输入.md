## 带空格字符串输入

```C++
#include<iostream>
#include<map>
#include<string.h>
#include<string>

using namespace std;

int main()
{
    int n;
    cin >> n;
    getchar();//输入n回车后的'\n'需要忽略，使用getchar() 或cin.ignore()
    vector<string> store;
    for (int i = 0; i < n; i++)
    {
      getline(cin,s); //使用getline 输入带空格的字符串
      store.push_back(s);
    }
    for (string t : store)
      cout << t << endl;
    return 0;
}
```
