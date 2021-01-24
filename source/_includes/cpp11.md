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
#include <iostream>

std::vector<int> processValues(const std::vector<int>& values)
{
    std::vector<int> newValues;
    newValues.reserve(values.size());
    for (auto itr = values.begin(); itr != values.end(); ++itr )
    {
        new_values.push_back( 2 * *itr );
    }
    return new_values;
}

int main()
{
    std::vector<int> values;
    for ( int i = 0; i < 100; i++ )
    {
        values.push_back(i);
    }
    values = processValues(values);
}
```
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

## Smart Pointer

* `auto_ptr` is deprecated
* `shared_ptr`, `weak_ptr` and `unique_ptr` is introduced

## noexcept

### Difference between auto_ptr and unique_ptr

### shared_ptr and weak_ptr



## Ranged For loops

```cpp
std::vector<int> values;
for(const auto & v : values) {
    // perform operation using the value
}
```


## Variadic Templates


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



