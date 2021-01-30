# C++ 17 Features

Following were newly introduced features in c++17.

## CTAD (Class template argument deduction)
* Reference: [CTAD](https://en.cppreference.com/w/cpp/language/class_template_argument_deduction)


## constexpr

* constexpr (constant expression) specifies that it is possible to evaluate value of the function or variable at compile time.
* using constexpr, we will be able to achieve runtime effficiency by doing computation at compile time.
* constexpr can be applied to: variables, functions and objects

### constexpr variables

```cpp
// both of below variables are evaluated at compile time
constexpr int a = 55;
constexpr int b = a + 50;
```

* as shown in the above example, constexpr variable's value can be computed at compile time.

### constexpr functions

```cpp
#include <iostream>

constexpr bool is_odd(int num){
  if(num % 2 == 0){
    return false;
  }
  return true;
}

int main() {

  // this is evaluated at compile time
  const int n = 11;
  const bool check_odd = is_odd(n);
  std::cout << std::boolalpha << check_odd << std::endl;
}
```

* constexpr functions are evaluated and executed at compile time.
* it can only call other constexpr functions
* its return value cannot be `void`
* it can refer only `const` or `constexpr` variables.
* its return values should always be stored in `const` or `constexpr` variables.

### constexpr v/s inline functions

Key difference:

* inline functions are expanded by compiler at compile time but still evaluated at runtime.
* constexpr functions are evaluated at compile time.

### constexpr objects

```cpp
#include <iostream>

class Rectangle {
  int height;
  int width;

  public:
  // constexpr constructor
  constexpr Rectangle(int h, int w): height(h), width(w) {}

  // constexpr method
  constexpr int area() const {return height * width;}
};

int main() {
    // object evaluated at compile-time
    constexpr Rectangle r(5, 10);
    const int area = r.area();
    std::cout << "area of Rectangle is: " << area << std::endl;
}
```
* constexpr objects are initialized at compile time
* all constructors must be constexpr
* all non-static members or subobjects must be initialized and they must have constexpr constructors.
* object methods also can be declared as constexpr as long as they comply with rules of constexpr functions.
* in the above example, Rectangle object `r` is constexpr object
* Rectangle class have its constructor as well as `area()` method also constexpr.

### C++11 - constexpr introduction

```cpp
#include <iostream>

constexpr int add(int x, int y) {
    return x + y;
}

int main()
{
    const int x = add(10, 20);
    std::cout << x << std::endl;
    return 0;
}
```

* constexpr was introduced in C++11 with limited features but over the later revisions of C++.
* limit of one return expression per function is allowed. This restriction is removed in C++14.
* constexpr function can call other constexpr functions and access only const global variables.
* constexpr function return values should also be stored in const expression, so if we remove `const` from `const int x` the expression `add(10, 20)` will be evaluated at runtime.


### C++14 - generalized constexpr

### C++17 - constexpr for metaprogramming

* If Constexpr
* constexpr Lambdas
* stl constexpr by default

### constexpr with constructors

```cpp
using namespace std;

class Rectangle
{
    int _h, _w;
public:

    constexpr Rectangle (int h, int w) : _h(h), _w(w) {}

    constexpr int area () const { return _h * _w; }
};

//  driver program to test function
int main()
{
    // Below object initialized at compile time
    constexpr Rectangle obj(10, 20);
    cout << obj.area() << std::endl;

    // Below object initialized at compile time
    // If we make h and w as non const - we will get compile time error
    const int h = 5;
    const int w = 10;
    constexpr Rectangle obj1(h, w);
    cout << obj1.area() << std::endl;

    // Below object is initialized at runtime
    int h1 = 5;
    int w1 = 10;
    Rectangle obj2(h, w);
    cout << obj2.area() << std::endl;

    return 0;
}
```

* Reference: [constexpr constructors - IBM](https://www.ibm.com/support/knowledgecenter/SSGH3R_13.1.0/com.ibm.xlcpp131.aix.doc/language_ref/constexpr_constructors.html)
* previously only expressions of built-in types could be valid constant expressions. With constexpr constructors, constructors of user defined types can also be included in valid constant expressions. The user defined class should satisfy following properties:
* the class should not have any virtual base class
* each parameter type is literal
* each non static data member and subobject is initialized
* all the constructors are constexpr

* in the above example, obj and obj1 are initialzed at compile time whereas obj2 will be initialized at runtime.
* if we make the h and w variables non-const, we will get compile error.

### constexpr versus const

* const is used for creating const objects whose values does not get modified during runtime
* constexpr is actually used for optimization of the program where we try to evaluate expressions at compile time.

## Structured binding

## Selection Statements with Initializers

## Fold Expressions

## Class template deduction

## Nested namespaces definition

## Pre-Processor predicate for header processing

## string_view


