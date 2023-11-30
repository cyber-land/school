# primitive types
https://en.cppreference.com/w/cpp/language/types

# enum
```cpp
enum Direction {
	UP, DOWN, LEFT, RIGHT
};
Direction d = UP;
```

# I/O

## console
TODO

## file
```cpp
#include <fstream>
using namespace std;
int main() {
	ifstream ifs;
	ifs.open("./input.txt"); // reading only
	if (!ifs.is_open()) return 1;
	string name; int age;
	while (ifs >> name >> age) { }
	ifs.close();
    
	ofstream os("output.txt"); // writing only
	if(!os.is_open()) return 1;
	os << name << " " << age << endl;
	if (os.bad()) return 1; //Failed to write
	os.close();
    return 0;
}

```
## stream
TODO

# preprocessor directives
Are mostly used in defining macros, evaluating conditional statements, source file inclusion, pragma directive, line control, error detection etc.

**conditional compilation**

```cpp
#define INCL_DEFINITIONS "why not"
#ifdef INCL_DEFINITIONS
	# include <vector>
#endif
int main() { std::vector v({1,2,3}); }
```
The controlled text will get included in the preprocessor output if the `macro_name` is defined

```cpp
#include <iostream>
using namespace std;

#ifndef NAME
#define NAME
int fun() {return 3;}
#endif

int main() { cout << fun() << endl; }
```
The `#ifndef` directive is simply opposite to that of the `#ifdef` directive

```cpp
#include<iostream>
using namespace std;

#define var 7
#if var > 200
	#undef var
	#define var 200
#elif gfg < 50
	#undef var
	#define var 50
#else
	#undef var
	#define var 100
#endif

int main() { cout << var << endl; /* return 50 */ }
```

**error directives**
```cpp
#ifndef macro_name
#error macro_name not found !
#endif
int main() {}
```
