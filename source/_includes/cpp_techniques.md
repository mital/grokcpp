# C++ Techniques


## RAII (Resource Acquisition Is Initialization)

```cpp
#include <iostream>
#include <string>

class ResourceGuard {
    const std::string resource;
  public:
    ResourceGuard(const std::string& res) : resource(res) {
      std::cout << "Acquire the " << resource << "." <<  std::endl;
    }
    ~ResourceGuard() {
      std::cout << "Release the "<< resource << "." << std::endl;
    }
};

int main() {
  ResourceGuard resGuard1{"resGuard1"};

  std::cout << "\nBefore local scope" << std::endl;
  {
    ResourceGuard resGuard2{"resGuard2"};
  }
  std::cout << "After local scope" << std::endl;
  
  std::cout << "Before try-catch block" << std::endl;
  try{
      ResourceGuard resGuard3{"resGuard3"};
      throw std::bad_alloc();
  }   
  catch (std::bad_alloc& e){
      std::cout << e.what();
  }
  std::cout << "After try-catch block" << std::endl;
}

Example 2:
From cppreference.com

std::mutex m;

void bad()
{
    m.lock();                    // acquire the mutex
    f();                         // if f() throws an exception, the mutex is never released
    if(!everything_ok()) return; // early return, the mutex is never released
    m.unlock();                  // if bad() reaches this statement, the mutex is released
}

void good()
{
    std::lock_guard<std::mutex> lk(m); // RAII class: mutex acquisition is initialization
    f();                               // if f() throws an exception, the mutex is released
    if(!everything_ok()) return;       // early return, the mutex is released
}
```


* [cppreference RAII](https://en.cppreference.com/w/cpp/language/raii)
* Resource should be acquired in the constructor of an object and released in the destructor of the object.
* Destructor is automatically called if the object goes out of scope.

## CRTP (Curiously Recurring Template Pattern)

```cpp
#include <iostream>

template<typename specific_shape>
struct Shape {
    std::string name() { return static_cast<specific_shape*>(this)->name(); }
    int area() { return static_cast<specific_shape*>(this)->area(); }
};
struct Rectangle : Shape<Rectangle> {
    std::string name() { return "Rectangle"; }
    int area() { return 100; }

};
struct Circle : Shape<Circle> {
    std::string name() { return "Circle"; }
    int area() { return 90; }
};

template<typename specific_shape>
void print(Shape<specific_shape> &animal) {
    
    std::cout << animal.name() << " area is: " << animal.area() << std::endl;
}

int main() {
    Circle c;
    print(c);

    Rectangle r;
    print(r);
    return 0;
}
```

* CRTP is static alternative to virtual functions.
* The above code achieves the polymorphic behavior for each shape objects without bearing cost of virtual dispatch mechanism.


## SNIFE

## RVO (Return Value Optimization)

## NRVO (Named return value optimization) - not mandatory

## Templates

* templates defines family of classes or family of functions or family of variables.
* [cppreference template documentation](https://en.cppreference.com/w/cpp/language/templates)


### difference of keywords 'typename' and 'class' in template parameters

```cpp
// dependent types where we have to always use typename
template<typename param_t>
class Foo
{
    typedef typename param_t::baz sub_t;
};

// ex2
typename some_template<T>::some_type

// template templates (however the keywords are interchangeable since C++17)
template < template < typename, typename > class Container, typename Type >

// explicitly instantiating a templates
template class Foo<int>;

```

* for all basic usages, `typename` and `class` keywords are interchangeable.
* Except following cases:
* case of dependent types where we are referencing a nested type that depends on another template parameter.

## Template Meta Programming


