```cpp
string s1;                  // 空字符串
string s2 = "Hello";        // 初始化
string s3("World");         // 构造函数方式
string s4(5, 'A');          // "AAAAA"

s.size();     // 字符个数
s.length();   // 同 size()
s.empty();    // 是否为空（true / false）

s += "abc";        // 追加
s.push_back('!');  // 追加单个字符
	s.pop_back();      // 删除最后一个字符

s.find("lo");      // 返回位置，找不到返回 string::npos
s.find('o');

if (s.find("abc") != string::npos) {
    // 找到了
}

s.insert(2, "xyz");   // 在下标 2 插入
s.erase(2, 3);        // 从下标 2 删除 3 个字符

s.replace(1, 3, "123"); // 从 1 开始，替换 3 个字符

#include <cstdlib>
int x = stoi(s);

#include <string>
string s = to_string(123);
```