# object
## constructors

```cpp
// empty constructor
ClassName() { /*default inits*/ }
// default constructor (overloading)
ClassName( int a; ) : a(a) { /*copy params*/ }
// copy constructor ( by reference or by pointer )
ClassName( const ClassName &c ) {}
ClassName( const ClassName *c ) {}
```
## destructor
```cpp
~ClassName () { /*manage pointers*/ }
```
## memory management
### deep copy
```cpp
class A {
public:
	int *var;
	A() { var = new int; }
	A(A& a) {
		var = new int;
		*var = *(a.var);
	}
	~A() { delete var; }
	void print_mem() { cout << var << endl; }
	void print_val() { cout << *var << endl; }
};
int main() {
	A a;
	*(a.var) = 5;
	A b = a;
	*(b.var) = 6;
	a.print_val();
	b.print_val();
	a.print_mem();
	b.print_mem();
	return 0;
}
```
### shallow copy
```cpp
	A(A* a) {
		var = a->var;
	}
	~A() { delete var; /*double free*/  }
	//TODO: solve with smart pointers*/
```
# inheritance
```cpp
struct A { int a; };
struct B : A { }; // public ( default )
struct B : private A { } // accesso attributi privati
struct B : protected A { } // poco usata
```

The **friend** keyword is used to give other classes and functions access to private and protected members of the class, even through they are defined outside the class's scope.
```cpp
struct A {
	int var;
public:
	friend void print_var(A a);
	friend class Printer;
};
void print_var(A a) { cout << a.var << endl; }
class Printer {
public:
	void static print(A& a) { cout << a.var << endl; }
};
int main() {
	A a = {10};
	print_var(a);
	Printer::print(a);
	return 0;
}
```

Since **virtual** base resides only in most derived object, there will be only one instance of A in D.
```cpp
struct A { void foo() { cout << "here\n"; } };
struct B : public virtual A {};
struct C : public virtual A {};
struct D : public B, public C {
	void bar() { foo(); }
};
```

**type aliases**
```cpp
struct type_def {
	typedef int alias_name;
};
struct template_type_def {
	template<typename T>
	using alias_name = std::vector<T>;
};
int main() {
	type_def::alias_name i = 5;
	template_type_def::alias_name<int> v;
	return 0;
}
```

# modifiers
## access

### attributes, methods
**public**: everyone has access
**private**: only the class itself and friends have access
**protected**: only the class itself, derived classes and friends have access

class: the default access specifier is private
struct: the default access specifier is public
It's even possible to have a class derive from a struct (or vice versa)
## non-access
### classes
**virtual**: abstract
**static**: 
**final**: the class cannot be inherited by other classes
### attributes, methods
**static**:  belongs to the class rather than an object

inheritance: extends, implements, super keyword
object orientation
- polymorphism ( liskov principle )
# polymorphism
## overloading
also known as static polymorphism or early binding
Method overloading is a way you can have two ( or more ) methods with same name in a single class

**operator overloading**
https://en.wikipedia.org/wiki/Operators_in_C_and_C++
TODO: ridefinizione interna, con metodo o con funzione ( keyword friend )
```cpp
class Complex {
public:
    int real, imag;
    Complex operator+(Complex const& obj) {
        Complex res;
        res.real = real + obj.real;
        res.imag = imag + obj.imag;
        return res;
    }
}
```
## overriding
also known as dynamic polymorphism or late binding (virtual functions)
method overriding is a substitution of the method body provided by the parent class

```cpp
class Parent {
	public: int fun() { return 1; }
};
class Child: Parent {
	public: int fun() { return 1 + Parent::fun(); }
};
```
