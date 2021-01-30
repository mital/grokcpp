# C++ 14 Features

Following were newly introduced features in c++14.
Major part of C++14 conpraises of bugfixes in c++11.

## auto return type deduction

## Lambda 

### What is Lambda functions

```cpp
// demonstrate lambda params

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

// demonstrate lambda captures

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

// lambda Generics and Generalized lambda expressions

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


* lambda adds more code if not compiled with higher optimization flag (e.g. -O1 or -O2)
### stateful lambda

```cpp
#include <memory>
#include <iostream>

int main()
{
    auto myLambda = [i = 0] () mutable {return i++; };
    std::cout << myLambda() << std::endl;
    std::cout << myLambda() << std::endl;

    return 0;
}
```
* as shown in the code above myLambda is a stateful lambda expression, it counts the number of times the lambda is invoked

### Fibonaci sequence generation through lambda

```cpp
#include <memory>
#include <iostream>

int main()
{
    auto myFib = [i = 0, j = 1] ()  mutable {
            i = std::exchange(j, j+i);
            return i;
        };
    std::cout << myFib() << std::endl;
    std::cout << myFib() << std::endl;
    std::cout << myFib() << std::endl;
    std::cout << myFib() << std::endl;
    std::cout << myFib() << std::endl;
    std::cout << myFib() << std::endl;
    std::cout << myFib() << std::endl;

    return 0;
}
```

* The above code is a mutable lambda expression which is able to generate the fibonacci sequence all at compile time.

### inheriting from lambda (Combining two lambdas)

```cpp
#include <memory>
#include <iostream>

template <typename L1, typename L2>
struct S: L1, L2 {
    
    S(L1 l1, L2 l2) : L1(std::move(l1)), L2(std::move(l2)) {}
    using L1::operator();
    using L2::operator();
};

int main()
{
    auto l = []() { return 4; };
    auto l2 = [](int i) { return i * 10; };
    
    auto combined = S(l, l2);
    std::cout << combined() << std::endl;
    
    return 0;
}
```

* as shown in the above code, we can actualy combine two lambada by creating a template struct which inherits from both the lambadas
* C++14 would have required a wrapper method `make_combined` which is no longer required with C++17 onwards.

### Usage of combining multiple lambdas

```cpp

// Compile at https://godbolt.org/ with extra flags: -O3 -std=c++1z -Wall -Wextra

#include <memory>
#include <variant>
#include <array>
#include <iostream>
#include <algorithm>

template <typename ... A>
struct Merged : A...
{
    template <typename ... T>
    Merged(T && ... t) : A(std::forward<T>(t))...
    {
    }

    using A::operator()...;
};

template <typename ... T>
Merged(T...) -> Merged<std::decay_t<T>...>;


int main()
{
    std::array<std::variant<double, int, char, std::string>, 2> a{3.2, 2};
    
    int intTotal = 0;
    double doubleTotal = 0;
    
    Merged merged{[&intTotal](const int i) { intTotal += i; },
                    [&doubleTotal](const double d) { doubleTotal += d; } };
                    
    std::for_each(begin(a), end(a),
        [&merged](const auto &v) {std::visit(merged, v);});
    

    return intTotal;
}
```

* As shown in above code, we can use merged class to combine different type of variant objects and do operations on top of them.


### Generic Lambda

```cpp
#include <memory>
#include <variant>
#include <array>
#include <iostream>
#include <algorithm>

int main()
{
    std::array<std::variant<double, int>, 2> a{3.2, 2};
    
    int intTotal = 0;
    double doubleTotal = 0;
    std::common_type_t<decltype(intTotal), decltype(doubleTotal)> grandTotal = 0;
    
    auto generic_merged = [&intTotal, &doubleTotal, &grandTotal](const auto v) {

        grandTotal += v;

        if constexpr (std::is_same<double, decltype(v)>{}) {
            doubleTotal += v;
        } else {
            intTotal += v;
        }
    };
                    
    std::for_each(begin(a), end(a),
        [&generic_merged](const auto &v) {std::visit(generic_merged, v);});
    

    return intTotal;
}
```

* Generic Lambda support was added in C++14
* we can do significant optimization on the `Merged` code we have writen in previous example using generic lambda
* they are way more readable and easier to understand compared to inheriting multiple lambdas.
* we can share values across multiple types of variant whereas in previous case the only way we could do it is by passing same object reference to both the lambdas

### Lambdas as custom comparator on stl containers 


```cpp
#include <set>
#include <iostream>
#include <algorithm>

struct Person
{
    std::string name;
};

template <typename Type, typename ... Comparator>
auto make_set(Comparator && ... comparator)
{
    struct Compare : std::decay_t<Comparator>... {
        using std::decay_t<Comparator>::operator()...;
        using is_transparent = std::true_type;
    };
    return std::set<Type, Compare>{Compare{std::forward<Comparator>(comparator)...}};
}

int main()
{
    auto myset = make_set<Person>(
        [](const Person& lhs, const Person& rhs) { return lhs.name < rhs.name;},
        [](const auto& lhs, const Person& rhs) { return lhs < rhs.name;},
        [](const Person& lhs, const auto& rhs) { return lhs.name < rhs;}
        );
    
    myset.count("Bob");

}
```

* Above is the example usage where lambdas are used as transparent comparators for std::set or alaternatively std::map

### Lambda in Fold Expression

```cpp
#include <iostream>

auto do_stuff(T ... t) {
template <typename ... T>
    return ([t]{return t;}()  + ...);
}

int main()
{
    return do_stuff(1, 2, 3, 4, 5);
}
```

* using lambdas inside fold expression is perfectly valid
* we have done variadic expansion of capturing lambda.


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


