[[bucket]]

gli script di codice che non indicano il main e gli include sottintendono questa struttura
```cpp
#include<iostream>
using namespace std;
int main() {
	// code
	return 0;
}
```

# caratteristiche
## uml
- associazione vs aggregazione/composizione
	composition: when an object dies, the other one also dies
	association: each object has an independent life
- navigabilità
- constraints
- generalization ( hierarchy )
- dependency, use, create
- interface
## operator overloading
[[OOP#polymorphism#overloading]]

## memory management
TODO: differenza tra i C pointers ed i C++ references

le variabili sono composte da:
- nome (identificativo)
- tipo (definisce quanti bit occupa in memoria)
- locazione di memoria (left value) (memory address) 
- valore (right value) (memory value)
### pointers
```c
#include<stdio.h>
int main(int argc, char *argv[]) {
char str[] = "abcdef"; // chars array (mutable)
char *ptl = str; // pointer to the same address
printf("size (%li byte) value (%s) address (%p) that point to (%c) \n",
    sizeof(str), str, &str, *str );
printf("size (%li byte) value (%s) address (%p) that point to (%c) of address (%p) \n",
    sizeof(ptl), ptl, &ptl, *ptl, ptl );
	return 0;
}
```
compile to something like
```
size (7 byte) value (abcdef) address (0x7ffea2939a49)
that point to (a) 
size (8 byte) value (abcdef) address (0x7ffea2939a28)
that point to (a) of address (0x7ffea2939a49)
```

### reference and dereference

**Referencing** means taking the address of an existing variable (using &) to set a pointer variable. In order to be valid, a pointer has to be set to the address of a variable of the same type as the pointer, without the asterisk.

**Dereferencing** a pointer means using the * operator (asterisk character) to retrieve the value from the memory address that is pointed by the pointer.

A **pointer** is a variable which stores the address of another variable.
Pointers are said to "point to" the variable whose address they store.
 
```cpp
int a = 5;
int *b = &a; // reference/inderection: store the memory address of 'a'
cout << "a has address: " << &a << " and value: " << a << endl;
cout << "b has address: " << &b << " and value: " << b << endl;
// dereferencing 'b' return the value of 'a'
cout << "and point to " << *b << endl;
```
compile to something like
```
a has address: 0x7ffe3819a384 and value: 5  
b has address: 0x7ffe3819a378 and value: 0x7ffe3819a384
and point to 5
```

### Stack and heap

**Stack**
Allocating data into the `stack` is very fast compared to `heap` allocation, as all the memory has already been assigned for this purpose.
Data stored on the stack is only valid so long as the scope that allocated the variable is still active.

**Heap**
Areas of memory allocated from the `heap` may live longer than the original scope in which it was allocated.
When using `new` and `delete` instead `malloc` and `free`, the constructor and destructor will get executed (Similar to stack based objects).

```cpp
#include <iostream>
using namespace std;
struct type { int v1, v2; };
int main() {
	// storing on the stack
	{
		int a = 0;
		type b = { 6, 7 };
		cout << &b << " " << b.v1 << endl;
	} // end lifetime of 'a' and 'b'

	// storing on the heap
	int* c = new int; // allocation
	char* d;
	{
		char* e = new char[] {'a', 'b', 'c'};
		d = e;
	} // end lifetime of 'e' but the memory is never freed
	  // so 'd' is still valid
	
	delete c; // deallocation
	cout << d << endl; // print 'abc'
	delete [] d; // now the memory is finally released
	type *f = new type { 8, 9 };
	// f (a pointer) store the reference to a memory address in the heap
	cout << &f << " " << f << " " << f->v1 << endl;
}
```
compile to something like
```
0x7fffd46514d0 6
abc
0x7fffd46514b8 0x55bfcc7612e0 8
```
si noti che `b` ed `e` sono vicini in memoria mentre l'indirizzo nello heap è molto distante

## templates
The basic idea of a template is that the template parameter gets substituted by a type at compile time. The result is that the same code can be reused for multiple types. 
```cpp
template<typename T1, typename T2>
struct MyPair {
	T1 first;
	T2 second;
};
```

STL is a subset of [[STD]]
- Algorithms ( mutating and non )
- Containers
- Functions: includes classes that overload the function call operator
- Iterators
## exceptions
[noexcept](https://www.geeksforgeeks.org/noexcept-operator-in-cpp-11/)

```cpp
#include <iostream>
#include <exception>
using namespace std;
int main() {
	try {
		// throw by value rather than by pointer
		throw runtime_error("Error!");
	} catch (const runtime_error& e) {
		cout << e.what() << endl;
		// throw e; // rethrow (propagate)
	}  catch (const exception& e) {
		// handle all exceptions
		cout << e.what();
	}
	return 0;
}
```
TODO: custom exceptions

### optional

```cpp
#include <iostream>
#include <optional>
#include <complex>
using namespace std;

optional<int> fun(optional<int> potential_value = nullopt) {
	return 6; // automatically wrapped into optional
}

int main() {
	// creation
	optional<int> o_empty /*= nullopt*/;
	optional<int> o_int(10);
	auto o_double = make_optional(3.0);
	auto o_complex = make_optional<double>(3.0, 4.0);
	optional<complex<double>> o_cmx{{3.0, 4.0}};
	auto o_int_copy = o_int;
	
	// accessing
	if (auto o = fun(); o /*.has_value()*/ )
		cout << "The value is: " << *o << endl;
	else cout << "is null" << endl;
	optional<int> o = fun();
	try {
		cout << o.value() << endl;
	} catch (const bad_optional_access& e) {
		cout << e.what() << endl;
	}
	cout << o.value_or(42) << endl; // fallback value
	
	// assign a new value
	o.emplace(4);
	o = 5;
	o.reset();
	o = std::nullopt;
	if (o) {cout << *o << endl;}
	// se non viene controllata la validità ritorna comunque 5
	
	// comparisons
	optional<int> oEmpty;
	optional<int> oTwo(2);
	optional<int> oTen(10);
	
	(oTen > oTwo); // true
	(oTen < oTwo); // false
	(oEmpty < oTwo); // true
	(oEmpty == nullopt); // true
	(oTen == 10); // true
}
```

never use optional with pointer because they are naturally nullable and that behavior cannot be checked
never use optional with bools too

`sizeof(double) = 8`
`sizeof(int) = 4`
`optional<double> od; // 16 bytes`
`optional<int> oi; // 8 bytes`
occupa il doppio dello spazio perché memorizza un flag che indica la validità, e deve rispettare le regole di allineamento

## (multiple) inheritance [[OOP]]
## namespaces
A C++ namespace is a collection of C++ entities (functions, classes, variables), whose names are prefixed by the name of the namespace.
When writing code within a namespace, named entities belonging to that namespace need not be prefixed with the namespace name, but entities outside of it must use the fully qualified name.
The fully qualified name has the format `<namespace>::<entity>`

Used to prevent name collisions when using multiple libraries, a namespace is a declarative prefix for functions, classes, types, etc.

```cpp
namespace ns {
	int var = 5;
	class cls { public: string str = "3"; };
	int fun() { return 0; }
}
int main() {
	cout << ns::var << endl;
	ns::cls c;
	cout << c.str << endl;
	cout << ns::fun() << endl;
	return 0;
}
```
# tecniche moderne
## range-based loop
```cpp
#include <iostream>
#include <map>
#include <vector>
using namespace std;
int main() {
	map<string, int> omap = {
		{"One", 1},
		{"Two", 2},
		{"Three", 3}
	};
	for (auto const& [key, val] : omap)
		cout << key << ':' << val << endl;
	vector<int> v = {0, 1, 2};
	for (auto i : v) cout << i << endl;
	for (int i : {3, 4, 5}) cout << i << endl;
}
```
## rvalues and move semantics
cpp NfP chapter 74
https://en.cppreference.com/w/cpp/language/value_category

![[value_category.png]]

**lvalue**: expressions with identity but not movable from
**xvalue**: expressions with identity that are movable from
**prvalue**: expressions without identity that are movable from
C++ does not have expressions which have no identity and cannot be moved from
C++ defines two other value categories, each based solely on one of these properties: **glvalue** : expressions with identity
**rvalue** :expressions that can be moved from

```c++
#include <iostream>
using namespace std;
class Foo {
public:
	std::string name;
	Foo(std::string some_name) : name(std::move(some_name)) { }
	std::string& original_name() { return name; }
	std::string copy_of_name() const { return name; }
};
int main() {
	Foo foo("hello");
	string s = foo.copy_of_name();
	cout << s << endl;
	// is a prvalue, because returns an object, not a reference.
	// every prvalue is also an rvalue. (Rvalues are more general.)
	string &s1 = foo.original_name();
	cout << s1 << endl;
	// is an lvalue, because original_name returns an lvalue reference
	// every lvalue is also a glvalue. (Glvalues are more general.)
	Foo foo2 = move(foo);
	cout << foo2.name << endl;
	// is an xvalue, because move returns an rvalue reference (string&&)
	// Every xvalue is also both a glvalue and an rvalue.

	// names for objects and references are always lvalues:
	string a;
	string& b = a;
	return 0;
}
```

## runtime type identification and deduction
???

## auto, structured binding
The basic syntax for structured bindings is as follows:
```cpp
auto [a, b, c, ...] = expression;
auto [a, b, c, ...] { expression };
auto [a, b, c, ...] ( expression );
```

The compiler introduces all identifiers from the `a, b, c, ...` list as names in the surrounding scope and binds them to sub-objects or elements of the object denoted by expression.
```cpp
// gcc -lstdc++ -std=c++20 -lm pointers.cpp
// the expression is copied into a tuple-like object
#include <iostream>
#include <format>
#include <tuple>
using namespace std;
int main() {
	pair p(5, "hello");
	auto [x, y] = p;
	auto foo = [&x, &y]() {
		cout << format("{}, {}", x, y) << endl;
	};
	foo();
	tuple <char, int, float> t('a', 10, 15.5);
	auto [a, b, c] = t;
	return 0;
}
```
## deduced return and class types
???

## smart pointers

manage the lifetime of a dynamically stored object so you not need to worry about de-allocation

### unique_ptr
the object is owned by only one instance at any time
When a unique pointer that owns an object goes out of scope, the owned object is deleted.
```cpp
#include<memory>
using namespace std;
unique_ptr<int> foo(unique_ptr<int> ptr) {
	return ptr;
}
int main() {
	unique_ptr<int> ptr(new int(30));
	ptr = make_unique<int>(20);
	// you can access it just like a raw pointer
	// because it overloads those operators
	*ptr = 10;
	auto ptr2 = move(ptr);
	// ptr is now a nullptr
	ptr = foo( move(ptr2) );
	ptr.reset(); // delete
	ptr.reset(new int(40)); // another assignment

	auto arr_ptr = make_unique<int[]>(10);
	arr_ptr[2] = 10;
}
```

### shared_ptr
is able to share ownership of an object with other shared pointers
when the reference counting reach zero the object is destroyed
```cpp
	shared_ptr<int> first = make_shared<int>(5);
	shared_ptr<int> second(first);
```

### weak_ptr
Instances of `std::weak_ptr` can point to objects owned by instances of `std::shared_ptr` while only becoming temporary owners themselves. This means that weak pointers do not alter the object's reference count and therefore do not prevent an object's deletion if all of the object's shared pointers are reassigned or destroyed.

In the following example instances of `std::weak_ptr` are used so that the destruction of a tree object is not inhibited:

```c++
#include<memory>
#include<vector>
using namespace std;
struct TreeNode {
	weak_ptr<TreeNode> parent;
	vector< shared_ptr<TreeNode> > children;
};
int main() {
	shared_ptr<TreeNode> root(new TreeNode);
	for (size_t i = 0; i < 100; ++i) {
		shared_ptr<TreeNode> child(new TreeNode);
		root->children.push_back(child);
		child->parent = root;
	}
	root.reset();
}
```

assicura che il valore puntato sia ancora valido e quindi crea un nuovo `shared`
```cpp
if (shared_ptr shp = wp.lock()) {
	// We know the resource is valid, we can use it
}
```

## lambda expressions
```
[ capture clause ] (parameters) -> return_type {  
	body
}
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
int main() {
	auto add = [] (int a, int b) {
		return a+b; //implicitly deduce the return type
	};
	int var = 100;
	// only readable
	auto capture_by_value = [num_main] () {};
	// read and modify
	auto capture_by_reference = [&num_main] () {};
	// capture all the variables of the enclosing fun
	auto implicit_capture_by_value = [=] () {};
	auto implicit_capture_by_reference = [&] () {};
	// lambda function as argument
	vector<int> nums = {1, 2, 3, 4, 5, 8, 10, 12};
	int even_count = count_if(
		nums.begin(), 
		nums.end(), 
		[](int num) { return num % 2 == 0; }
	);
	return 0;
}
```

```cpp
// generic lambdas
auto twice = [](auto x){ return x+x; };
twice(2); // 4
twice("hello"); // "hellohello"
```

from lesson
```cpp
#include <iostream>
#include <vector>
#include <functional>
using namespace std;

int main() {
	vector<int> v(16);
	vector<function<float()> > vf(16);
	int j=0;
	for (unsigned int i=0;i<v.size();++i) {
		//v[i]=i;
		auto x=[&,j](int i){return i*j;};
		v[i]=x(i);
		function<float()> f=[i,&j](){return i/(1.0*j);};
		vf[i]=f;
		j+=i;
	}
	for (auto x:v) std::cout<<x<<" "; cout << endl;
	for (auto x:vf) std::cout<<x()<<" "; cout << endl;
	return 0;
}
```

## introduzione al meta-programming
meta-programming refers to the use of macros or templates to generate code at compile-time
template specializzati: https://www.cplusplus.com/reference/vector/vector-bool/

```cpp
#include <iostream>
using namespace std;

template <typename T1, typename T2>
T1 min (T1 a, T2 b) {
	return a < b ? a : b;
}

// recursion
template <unsigned int n>
struct factorial {
	enum { value=n*factorial<n-1>::value };
};
template<>
struct factorial<0> {
	enum { value=1 };
};

// if
template <bool condition, class Then, class Else>
struct IF { typedef Then RET; };
template <class Then, class Else>
struct IF <false,Then,Else> { typedef Else RET; };

int main() {
	cout << min(3,1.7) << endl; // return 1
	cout << factorial<7>::value << endl; // return 5040
	IF < sizeof(int) < sizeof(long) , long , int >::RET i;
	cout << sizeof(i) << endl; // return 8
}
```

key points:
- Espressioni calcolate al tempo di compilazione
	spostare computazioni dall'esecuzione alla compilazione
- Laziness, vengono istanziati solo i tipi che si usano
- Deduzione dei tipi dei template
	si possono generare tipi che dipendono dall'hardware
- Specializzazione dei template

# multi-thread programming
libraries (to include in examples)
`#include <thread>`
`#include <atomic>` rendere atomiche le operazioni su una certa variabile
`#include <mutex>` definire delle sezioni critiche come mutuamente esclusive

functions with params
TODO: get return values
```c++
int f(int a) {return a;}
unsigned int c = thread::hardware_concurrency();
// number core available
void main() { thread t (f, 0); };
```

avoid data races
```c++
static atomic<int> counter(0); // avoid data race
void inc_thread() { for (int i=0;i<10000;i++) counter++; }
void dec_thread() { for (int i=0;i<10000;i++) counter--; }

mutex my_mutex;
static int counter2=0;
void inc2_thread() {
	for(int i=0;i<10000;i++) {
		my_mutex.lock();
		counter2++;
		my_mutex.unlock();
	}
}
void dec2_thread() {
	for (int i=0;i<10000;i++) {
		my_mutex.lock();
		counter2--;
		my_mutex.unlock();
	}
}

int main() {
	thread first (inc_thread);
	thread second (dec_thread);
	first.join(); // wait thread
	second.join(); // wait the second one
	cout << "f1 e f2 completati, counter = " << counter << endl;
	return 0;
}
```

```c++
mutex g_display_mutex2;
void thread_function() {
	lock_guard<mutex> guard(g_display_mutex2);
	thread::id this_id = this_thread::get_id(); // get thread ID
	cout << "sono il thread " << this_id << endl;
}
int main() {
	thread t1(thread_function);
	t1.join();
}
```

Deadlock: situazioni in cui uno o più processi/thread si bloccano
- `lock_guard<mutex>` : Terminazione l'inattesa di una funzione
- `recursive_mutex` : Funzioni annidate che chiamano lo stesso mutex
- `lock(mutex, mutex)` : ordine di locking di diversi mutex
Starvation: la politica di accesso impedisce ad un processo di accedere alla risorsa

# low-level programming

```cpp
#include <iostream>
#include <bitset>
#include <sstream>
using namespace std;

int main() {
	bitset<16> b;
	int size = b.size();
	cout << b << endl;          // 0000000000000000
	string s="1011101";
	b=bitset<16>(s);
	cout << b << endl;          // 0000000001011101
	cout << (b<<3) << endl;     // 0000001011101000
	cout << (b&(b<<3)) << endl; // 0000000001001000
	cout << (b|(b<<3)) << endl; // 0000001011111101
	istringstream iss(s);
	iss>>b;
	cout << ~b << endl;        // 1111111110100010
	// or b.flip()
	cout << b.to_ulong() << endl; // 93 ( 64+16+8+4+1 )
	// to_string , to_ulong , to_uulong
	b[10] = 0;
	return 0;
}
```

**bitset::reset()** resets the bits at the given index in the parameter. If no parameter is passed then all the bits are reset to zero.

# Introduzione all'ottimizzazione del codice
???

---
# esame
## teoria (16)
- interpretazione di codice
- domande a risposta multipla ed aperte
- piccoli pezzi di codice da scrivere
## pratica (16)
- esercizi di scrittura di codice separati tra loro
