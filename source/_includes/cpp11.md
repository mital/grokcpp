# C++ 11 Features

Following were newly introduced features in c++11.

## r-value reference 

> To authorize, use this code:

```cpp
#include <iostream>

int main()
{
	int x{7};

	// l-value references
	int &lvalueRef{ x };
	const int &constLvalueRef{ x };
	const int &constLvalueRefOfLiteral{ 5 };

	// r-value references
	int &&rvalueRef{ 5 }; // F
	const int &&constRvalueRef{ 5 }; // H

	return 0;
}
```

* prior to C++11 l-values, r-values and only one type of reference existed which is called *reference* and which is of type l-values reference.
* * l-values are the values which are on left side of an assignment expression. Originally there was only one type of l-values but after introduction of `const` keyword, l-values split into two categories - modifiable and non-modifiable l-values.
* r-values are everything which is not l-values. e.g. literals (3, "three" etc.) , temporary values (a + 5, x + y etc.), anonymous objects (new MyClass("myClass") etc. ). 
* l-values and r-values are properties of expressions. Every expression in C++ have two properties, *type* and *value category*. Type is used for type checking and value category is used for syntax checking.
* C++11 adds r-value reference is created using '&&' whereas the l-value reference (formally 'reference') is created using '&'.

The above code is 

## Move Semantics

```cpp
// Move Constructor and Move assignment operator
```

* if your class holds pointer to objects which are member variables and which takes considerable effort to construct,
* your object copy is expensive.
* many cases we create objects which are temporary in nature (e.g. return from a function or using standard library (before c++11).
* in the above scenarios the move constructor and move assignment operator would considerably improve the performance.
* we use std::move for converting any l-value to r-value and hence triggering move constructor or move assignment operator explicitly.

* [R Value Reference and Move Semantics](https://www.cprogramming.com/c++11/rvalue-references-and-move-semantics-in-c++11.html)

## Automatic type deduction and decltype

```cpp
#include <iostream>
#include <vector>


int main()
{
    // auto variables

    auto c = 'v'; // character
    auto d = 55; // integer
    auto f = 44.66; // float

    std::vector<int> values = {1, 2, 3, 4, 5};

    // type of the object is verbose. e.g.
    // std::vector<int>::const_iterator iter = values.begin();
    auto iter = values.begin();

    // decltype
    typedef decltype(values.begin()) VITER;
    VITER iter2;
    return 0;
}

```

* `decltype` inspects / returns the declared type of the entity or the type and value category of an expression.

## std::decay, std::forward, std::move

* std::decay - converts l-value to r-value, array to pointer and function to function pointer
* std::forward - forwards l-value as l-value and r-value as r-value
* std::move - converts l-value to r-value and keeps r-value as r-value

## Smart Pointer

* `auto_ptr` is deprecated
* `shared_ptr`, `weak_ptr` and `unique_ptr` is introduced

## noexcept

* `noexcept` performs compile time check that if the expression is declared to not throw any exceptions


## Difference between auto_ptr and unique_ptr

* auto_ptr is a smart pointer which manages the object created / obtained via new operator and deletes the same in the destructor of auto_ptr (exclusive ownership model)
* copy constructor of auto_ptr will transfer the ownership of object instead of copying. because of this they can't be used within stl
* std::unique_ptr is added in C++11 as replacement of auto_ptr
* no copy assignments, support for arrays and containers.
* std::unique_ptr can be moved using new std::move semantics. `unique_ptr<MyClass> ptr2 = std::move(ptr1)`

## shared_ptr and weak_ptr

* shared_ptr uses reference counting mechanism to manage multiple references to same object. 

## Guaranteed Copy and Move Elision 


## Ranged For loops

```cpp
std::vector<int> values;
for(const auto & v : values) {
    // perform operation using the value
}
```


## STL Addition of new templates

* unordered_map
* unordered_set

etc.

## new C++ Algorithms

* `std::all_of`
* `std::any_of`
* `std::none_of`

## nullptr

In C++11, nullptr is introduce to replace the NULL macro. (Literal 0)


## Variadic Templates

### Background and Details

```cpp
// Sample Template function
template<typename... Arguments>
void SampleFunction(Arguments... parameters);

// Demonstrating sizeof... operator
template<typename... Arguments>
class VariadicTemplate{
private:
    static const unsigned short int size = sizeof...(Arguments);
};
```

* [cplusplus variadic templates](http://www.cplusplus.com/articles/EhvU7k9E/)
* it uses ellipsis operator (...) in different contexts
* C++ uses ellipsis operator (...) also in try-catch block for catching any exception thrown.

### Variadic Function template

```cpp
#include <iostream> 

void print() {
    std::cout << "I am empty function and I am called at last." << std::endl;
}

template <typename T, typename... Types>
void print(T t, Types... types) {
    std::cout << t << std::endl;
    if (sizeof...(types) >= 1) {
        print(types...);
    }
}

int main()
{
    print(1, 2, 3.14, "Pass me any number of arguments", "I will print\n"); 
    return 0;
}
```

* As shown in abov example, print is a variadic function template.
* it can take variable number of arguments.
* if you remove the sizeof check the print() method without any arguments will be called at last.

### Variadic templates in inheritance & initialization list

```cpp

// variadic template in initialization list
template<typename... BaseClasses>
class VariadicTemplate : public BaseClasses...{
public:
    VariadicTemplate(BaseClasses&&... base_classes) : BaseClasses(base_classes)...{

    }
};
```

### Double ellipsis

```cpp
#include <cstdio>
#include <string>

template< typename T >
T const &printf_helper( T const &x )
    { return x; }

char const *printf_helper( std::string const &x )
    { return x.c_str(); }

template< typename ... Req, typename ... Given >
int wrap_printf( int (*fn)( Req... ... ), Given ... args ) {
    return fn( printf_helper( args ) ... );
}

int main() {
    wrap_printf( &std::printf, "Hello %s\n", std::string( "world!" ) );
    wrap_printf( &std::fprintf, stderr, std::string( "Error %d %d" ), 5, 7 );
}
```

* Above code is the example of double ellipsis.
* `wrap_printf` takes first argument as the function pointer and passes all further arguments as argument to variadic template expansion


### Variadic using statements (Since C++17)

```cpp
#include <iostream>
#include <utility>

template <typename ... B>
class Merged : B...
{
    public:
    template<typename ... T>
    Merged(T && ... t) : B(std::forward<T>(t))...
    {
    }

    using B::operator()...;
};

template <typename ... T>
Merged(T...) -> Merged<std::decay_t<T>...>;

int main() {
    const auto l1 = [] { return 4; };
    const auto l2 = [] (int i) { return i * 10; };
    const auto l3 = [] (const double d) { return d * 3.2; };

    Merged merged(l1, l2, l3);

    return merged(10.0);
}
```

* Above code demonstrates the usage of variadic using statements with C++ 17 's class template type deduction and deduction guides.




## Template Specialization

### Function Template Specialization

```cpp
#include <iostream>

template <typename T>
T mul(const T& a, const T& b) {
    return a * b;
}

template<>
std::string mul<std::string>(const std::string& a, const std::string& b) {
    return a + b;
}

int main()
{
    std::cout << mul(5, 6) << std::endl;
    std::cout << mul(std::string("5"), std::string("655")) << std::endl;

    return 0;
}
```

* Above is the example of template Function specialization. 
* We are trying to create a specific overload for `std::string` which concat the string instead of multiplying them.

### class template specialization using classes

```cpp
template<typename T>
class Template {
public:
    void SampleFunction(T param){ }
};

template<>
class Template<int> {
public:
    void SampleFunction(int param){ }
};

int main()
{
    Template<double> d;
    d.SampleFunction(4.3);
    Template<int> i;
    i.SampleFunction(5);

    return 0;
}
```

### Variadic function template specialization

```cpp
#include <iostream>

template <typename ... Params>
void printValues(Params&& ... p) {
    ((std::cout << p << std::endl), ...);
}

template<>
void printValues(int&& i, bool&& j) {
    std::cout << "Special int bool override " << i << " " << j;
}

int main(int, char**) {
    printValues(1, false);
    printValues(1, "This is Test String");
    return 0;
}
```

* In the above example printValues is a variadic template which has a specialized version for int and bool arguments.


### variadic class template specialization

```cpp
#include <iostream>

template<typename... Arguments>
class VariadicTemplate {
public:
    void SampleFunction(Arguments... params){
        ((std::cout << params << std::endl), ...);
    }
};

template<>
class VariadicTemplate<double, int, long>{
public:
    void SampleFunction(double param1, int param2, long param3){
        std::cout << "Specialized function : " << param1 << " " << param2 << " " << param3 << std::endl;
    }
};


int main()
{
    VariadicTemplate<int, double, std::string, char> v1;
    v1.SampleFunction(5, 3.3, "test", 'c');
    
    VariadicTemplate<double, int, long> v2;
    v2.SampleFunction(3.3, 5, 2.2L);
    return 0;
}
```

* Above is example of VariadicTemplate class specialization.
* We are using fold expression to expand the parameter pack.


