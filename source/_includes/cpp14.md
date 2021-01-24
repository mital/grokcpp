# C++ 14 Features

Following were newly introduced features in c++14.
Major part of C++14 conpraises of bugfixes in c++11.

## auto return type deduction

## Lambda 

### What is Lambda functions

```cpp
> demonstrate lambda params

#include <iostream>

int main()
{
    auto addFunc = [](int a, int b) -> int {
        return a + b;
    };

    std::cout << "5 + 6 = " << addFunc(5, 6) << std::endl;

    return 0;
}
```

* Lambdas are unnamed function objects.
* They are introduced in C++11. [cppreference Lambda Expressions](https://en.cppreference.com/w/cpp/language/lambda)
* In the above code snippet, addFunc is a lambda function which takes two parameters a & b and returns their sum.

### captures and params

```cpp

> demonstrate lambda captures

#include <iostream>

int main()
{
    int fixedValue = 20;
    // fixedValue as captures by value
    /*auto addFunc = [fixedValue](int a, int b) -> int {
    	fixedValue--;
        return a + b + fixedValue;
    };*/

    // fixedValue as captures by reference
    auto addFunc = [&fixedValue](int a, int b) -> int {
    	fixedValue--;
        return a + b + fixedValue;
    };

    std::cout << "5 + 6 + fixedValue(" << fixedValue << ") = " << addFunc(5, 6) << std::endl;
    std::cout << "Updated fixedValue: " << fixedValue << std::endl;

    return 0;
}
```

* In the above snippet, a and b are the parameters of the lambda and we are specifying return type of the lambda with `-> int`.
* modifying the above code, we now want to add a fixedValue which always gets added when we call addFunc. 
* here the fixedValue is passed in *captures* section of the lambda and its value would be available within the lambda.
* we can pass the captures by value or by reference.
* In the above example the commeted definition of addFunc takes fixedValue variable by value hence any changes in the same will not be reflected in main() variable.
* If you execute the above code, you can verify that the fixedValue is updated in the main function.
* Capture all main() variables by value `[=](){}`
* Capture all main() variables by reference `[&](){}`
* Capture all main() variables by reference except fixedValue by value`[&, fixedValue](){}`
* Capture all main() variables by value except fixedValue by reference`[=, &fixedValue](){}`
* Capture custom variable derived from fixedValue `[newFixedValue{fixedValue*10}](){}`

### lambda Generics and Generalized lambda expressions (Added with C++14)

```cpp

> lambda Generics and Generalized lambda expressions

#include <iostream>

int getFixedValue(int i) {
    int fixedValue = 20;
    return i + fixedValue;
}

int main()
{
    
    // Generics lambda
    auto glambda = [] (auto a, auto b) { return a + b; };
    std::cout << "5 + 6 = " << glambda(5, 6) << std::endl;
    std::cout << "5.50 + 6.60 = " << glambda(5.50, 6.60) << std::endl;
    std::cout << "432543535 +  432543535 = " << glambda(432543535, 432543535) << std::endl;
    
    // Generalized lambda expression  
    int fv = 10;
    const auto addFunc = [fixedValue = getFixedValue(fv)](int a, int b) -> int {
        return a + b + fixedValue;
    };

    std::cout << "5 + 6 + getFixedValue(" << fv << ") = " << addFunc(5, 6) << std::endl;

    // Generalized lambda expression with another lambda
    const auto addFuncWithLambdaEmbedded = [fixedValue = [fv]() {return 20 + fv;}() ](int a, int b) -> int {
        return a + b + fixedValue;
    };

    std::cout << "5 + 6 + addFuncWithLambdaEmbedded[fv] = " << addFuncWithLambdaEmbedded(5, 6) << std::endl;

    return 0;
}
```

* With C++14 lambda expression also support Generics.
* e.g. `auto glambda = [] (auto a, auto b) { return a + b; };`
* The above `glambda` is a generics lambda which can be used for any data type.
* The lambda expression can take custom values derived from other methods like `getFixedValue`.
* The `getFixedValue` can also be replaced by another lambda as shown in the example.

## std::make_unique

```cpp
#include <iostream>
#include <memory>

struct Dimentions
{
    int x, y, z;

    Dimentions(int x, int y, int z) : x(x), y(y), z(z) { }
    Dimentions() : Dimentions(0, 0, 0) { }
    friend std::ostream& operator<<(std::ostream& os, Dimentions& v) {
        return os << '{' << "x:" << v.x << " y:" << v.y << " z:" << v.z  << '}';
    }
};

auto getDefaultDimentions() {
    return std::make_unique<Dimentions>();
}

auto getDimentions(int a, int b, int c) {
    return std::make_unique<Dimentions>(a, b, c);
}

int main()
{
    // default constructor.
    std::unique_ptr<Dimentions> v1 = getDefaultDimentions();
    // Use the constructor that matches these arguments
    std::unique_ptr<Dimentions> v2 = getDimentions(0, 1, 2);
    // Create a unique_ptr to an array of 5 elements
    std::unique_ptr<Dimentions[]> v3 = std::make_unique<Dimentions[]>(5);

    std::cout << "make_unique<Dimentions>():      " << *v1 << std::endl
              << "make_unique<Dimentions>(0,1,2): " << *v2 << std::endl
              << "make_unique<Dimentions[]>(5):   " << std::endl;
    for (int i = 0; i < 5; i++) {
        std::cout << "     " << v3[i] << '\t';
    }
    std::cout << std::endl;
}
```

* std::make_shared was also introduced in C++11. along with shared_ptr.
* However for instantiating unique_ptr, the std::make_unique was introduced in C++14.
* From C++14 onwards, we actually can replace all the `new` and `delete` operations with `std::unique_ptr`.
* As shown in the example above, we can have auto type deductions on return values of functions and can safely use `std::make_unique` without making extra copies of the same object.


