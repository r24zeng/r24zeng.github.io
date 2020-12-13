---
title: Object-oriented Programming - ADT
tags: c++, OOP
categories: notes
---
> ADT is Abstract Data Type. How can we use OOP to make ADT as primitive data structure in programming?

### Three steps:
+ define a ADT class
+ overload operators
+ optimize visualization to downstream clients

## Throw exception
> throw exception in function will stop execution; catch exception in main function will show error message; add `return 1` in exception catch will stop execution.

**NOTE: Dynamic Exception (throw (const char*)) is no longer supported in C++17.**

### C++ Example
```cpp
#include <iostream>
using namespace std;

class Rational{
public:
    // constructor
    Rational(int num = 0, int deno = 1) throw(const char*);

    // accessor and mutator
    int getNumerator();
    int getDenominator();
    void setNumerator(int num);
    void setDenominator(int deno) throw(const char*);

private:
    int _numerator;
    int _denominator;

};

Rational::Rational(int num, int deno) throw(const char*){
    if(deno == 0) {
        throw "denominator is not supposed to be zero!";
        return;
    }

    _numerator = num;
    _denominator = deno;
}

int Rational::getNumerator(){
    return _numerator;
}

int Rational::getDenominator(){
    return _denominator;
}

void Rational::setNumerator(int num) {
    _numerator = num;
}

void Rational::setDenominator(int deno) throw(const char*){
    if(deno == 0){
        throw "denominator is not supposed to be zero!";
    }
    _denominator = deno;
}

int main(int argc, char** argv){
    Rational a;
    try{
        a.setNumerator(7);
        a.setDenominator(0);
    }
    catch(const char* e) {
        cout << "Exception: " << e << endl;
        //return 1;
    }
    cout << "a = " << a.getNumerator() << '/' << a.getDenominator() << endl;
    return 0;
}
```

### output:
```ruby
Exception: denominator is not supposed to be zero!
a = 7/1
```

*Caution: `const` is neccessary in `try-catch` structure, otherwise the error message won't show up.*


## Keyword: const
> Accessor(Getter): All accessor functions should be `const` whenever possible; 
>  Muator(Setter): Pass input parameters by `const` reference possible

### C++ Example
```cpp
class Rational{
public:
    // constructor
    Rational(int num = 0, int deno = 1) throw(const char*);

    // accessor and mutator
    int getNumerator() const;
    int getDenominator() const;
    void setNumerator(const int num);
    void setDenominator(const int deno);

private:
    int _numerator;
    int _denominator;

};
```
## Operator overloading
> Can only create existed operations(+, -, *, /, ==, <<, >>, etc); They are non-member functions.

### C++ Example
```cpp
#include <iostream>
using namespace std;

class Rational{
public:
    // constructor
    Rational(int num = 0, int deno = 1) throw(const char*);

    // accessor and mutator
    int getNumerator() const;
    int getDenominator() const;
    void setNumerator(const int num);
    void setDenominator(const int deno) throw(const char*);

private:
    int _numerator;
    int _denominator;

};
// Arithmetic operators overloading
Rational operator+ (const Rational a, const Rational b);
Rational operator- (const Rational a, const Rational b);
Rational operator* (const Rational a, const Rational b);
Rational operator/ (const Rational a, const Rational b) throw(const char*);
// other operators overloading
bool operator== (const Rational a, const Rational b);


Rational::Rational(int num, int deno) throw(const char*){
    if(deno == 0) {
        throw "denominator is not supposed to be zero!";
        return;
    }

    _numerator = num;
    _denominator = deno;
}

int Rational::getNumerator() const{
    return _numerator;
}

int Rational::getDenominator() const{
    return _denominator;
}

void Rational::setNumerator(const int num) {
    _numerator = num;
}

void Rational::setDenominator(const int deno) throw(const char*){
    if(deno == 0){
        throw "denominator is not supposed to be zero!";
    }
    _denominator = deno;
}

Rational operator+ (const Rational a, const Rational b){
    Rational result = Rational(a.getNumerator()*b.getDenominator()+a.getDenominator()*b.getNumerator(),
                               a.getDenominator()*b.getDenominator());
    return result;
}

Rational operator- (const Rational a, const Rational b){
    Rational result = Rational(a.getNumerator()*b.getDenominator()-a.getDenominator()*b.getNumerator(),
                               a.getDenominator()*b.getDenominator());
    return result;
}

Rational operator* (const Rational a, const Rational b){
    Rational result = Rational(a.getNumerator()*b.getNumerator(), a.getDenominator()*b.getDenominator());
    return result;
}

Rational operator/ (const Rational a, const Rational b) throw(const char*){
    if(b.getNumerator() == 0){
        throw "The dividend cannot be zero!";
    }
    Rational result = Rational(a.getNumerator()*b.getDenominator(), a.getDenominator()*b.getNumerator());
    return result;
}

bool operator== (const Rational a, const Rational b){
    if(a.getNumerator()*b.getDenominator() == a.getDenominator()*b.getNumerator()) {
        return true;
    } else {
        return false;
    }
}

int main(int argc, char** argv){
    Rational a = Rational(1, 3);
    Rational b = Rational(2, 3);
    Rational result = a/b;

    cout << "a/b = " << result.getNumerator() << "/" << result.getDenominator() << endl;
    if(a == b) {
        cout << "same" << endl;
    } else {
        cout << "different" << endl;
    }

    return 0;
}
```
### Output:
```ruby
a/b = 3/6
different
```

## iostream overloading and Keyword: friend
> `friend` operator>> overloading in class public member enables operator>> to have access to the private data members

```cpp
class Rational{
public:
    // constructor
    Rational(int num = 0, int deno = 1) throw(const char*);

    // accessor and mutator
    int getNumerator() const;
    int getDenominator() const;
    void setNumerator(const int num);
    void setDenominator(const int deno) throw(const char*);
    //Enables operator>> to have access to the private data members
    friend istream& operator>> (istream& sin, Rational& r);

private:
    int _numerator;
    int _denominator;

};
// Arithmetic operators overloading
Rational operator+ (const Rational a, const Rational b);
Rational operator- (const Rational a, const Rational b);
Rational operator* (const Rational a, const Rational b);
Rational operator/ (const Rational a, const Rational b) throw(const char*);
// other operators overloading
bool operator== (const Rational a, const Rational b);
ostream& operator<< (ostream& sout, const Rational& r);
..........

ostream& operator<< (ostream& sout, const Rational& r){
    //1. Grabbing the reference to the output stream buffer

    //2. Attach the output strings to the buffer based on my likings
    sout << r.getNumerator() << "/" << r.getDenominator();

    //3. Return the reference of the modified output stream buffer
    return sout;
}

istream& operator>> (istream& sin, Rational& r){
    //1. Grabbing the reference to the input stream buffer

    //2. Acquire the num and the denom from the input and save it to r
    char slash;
    sin >> r._numerator>> slash >> r._denominator;

    //3. Clear slash, return the reference of the modified input stream buffer
    return sin;
}

int main(int argc, char** argv){
    Rational a;
    Rational b;
    cout << "first fraction (a/b): ";
    cin >> a;
    cout << "second fraction (a/b): ";
    cin >> b;

    Rational result;
    result = a/b;
    cout << "a/b = " << result << endl;
    if(a == b) {
        cout << "same" << endl;
    } else {
        cout << "different" << endl;
    }

    return 0;
}
```

### Output:
```ruby
first fraction (a/b): 1/3
second fraction (a/b): 2/3
a/b = 3/6
different
```

## keyword: explicit
> 1. Forces the compiler to use this specific constructor to type cast
> 2. Forces client to always explicit type cast.  implicit type conversion is completely prohibited.

### C++ Example(Wrong, can't compile)
```cpp
class Rational{
    public:
        explicit Rational(int num = 0, int denom = 1) throw (const char*);
    ......

}

int main(int argc, char** argv){
    Rational a = Rational(2, 1);
    if(a == 3) {
        cout << "same" << endl;
    } else {
        cout << "different" << endl;
    }
    return 0;
}
```

### C++ Example(Correct)
```cpp
class Rational{
    public:
        explicit Rational(int num = 0, int denom = 1) throw (const char*);
    ......

}

int main(int argc, char** argv){
    Rational a = Rational(3, 1);
    if(a == Rational(3)) {
        cout << "same" << endl;
    } else {
        cout << "different" << endl;
    }
    return 0;
}
```

### Output:
```ruby
a = 3/1
same
```

## Optimize(helper function)
> Add `_reduce()` helper functioin as a private member of the class; `_reduce()` means reduce fraction form

`_reduce()` is only be added in `Rational()`, `setNumenator()`, `getDominator()` and `operator>>`. Because other operator(+, -, *, /) will construct a `Rational result` automatically which will invoke `Rational()`. `operator<<` will not call `Rational()` but `Rational()` is always called before `<<`.

### C++ Example:
```cpp
#include <iostream>
using namespace std;

class Rational{
public:
    // constructor
    explicit Rational(int num = 0, int deno = 1) throw(const char*);

    // accessor and mutator
    int getNumerator() const;
    int getDenominator() const;
    void setNumerator(const int num);
    void setDenominator(const int deno) throw(const char*);
    //Enables operator>> to have access to the private data members
    friend istream& operator>> (istream& sin, Rational& r);

private:
    int _numerator;
    int _denominator;
    void _reduce();

};
......

Rational::Rational(int num, int deno) throw(const char*){
    if(deno == 0) {
        throw "denominator is not supposed to be zero!";
        return;
    }

    _numerator = num;
    _denominator = deno;

    _reduce();
}

void Rational::_reduce() {
    int gcd = 1;
    for(int i = 1; i <= _numerator && i <= _denominator ; i++){
        if((_numerator%i == 0) && (_denominator%i == 0))
            gcd = i;
    }

    _numerator = _numerator/gcd;
    _denominator = _denominator/gcd;
}
```

`_reduce()` in function `operator>>` is different, because it's a non-member function but it's a firend function.

**c++ exmple**
```cpp
istream& operator>> (istream& sin, Rational& r){
    //1. Grabbing the reference to the input stream buffer

    //2. Acquire the num and the denom from the input and save it to r
    char slash;
    sin >> r._numerator>> slash >> r._denominator;

    r._reduce();
    //3. Clear slash, return the reference of the modified input stream buffer
    return sin;
}
```

Because `r` has been saved in stack, in order to change `r`, we have to refer `r` to invoke `_reduce()`. 

Invoke `_reduce()` instead of `r._reduce()` will result in compile error, although `operator>>` is a friend function which have access to the private members. *why?*

## Keyword: virtual and final
> 1. virtual – permits child ADT to override the implementation of these functions; The client programmer can use the keyword override to override the base implementations
> 2. final – prohibits child ADT to override these functions

### C++ Example1: prohibits whole class overided
```cpp
class Rational final {
    ......
}
```

### C++ Example2: prohibits function overided
```cpp
class Rational final {
    public:
        final getNumerator();
}
```

# Last version of fraction ADT
```cpp
#include <iostream>
using namespace std;

class Rational {
public:
    // constructor
    explicit Rational(int num = 0, int deno = 1) throw(const char*);

    // accessor and mutator
    int getNumerator() const;
    int getDenominator() const;
    void setNumerator(const int num);
    void setDenominator(const int deno) throw(const char*);
    //Enables operator>> to have access to the private data members
    friend istream& operator>> (istream& sin, Rational& r);

private:
    int _numerator;
    int _denominator;
    void _reduce();

};
// Arithmetic operators overloading
Rational operator+ (const Rational a, const Rational b);
Rational operator- (const Rational a, const Rational b);
Rational operator* (const Rational a, const Rational b);
Rational operator/ (const Rational a, const Rational b) throw(const char*);
// other operators overloading
bool operator== (const Rational a, const Rational b);
ostream& operator<< (ostream& sout, const Rational& r);


Rational::Rational(int num, int deno) throw(const char*){
    if(deno == 0) {
        throw "denominator is not supposed to be zero!";
        return;
    }

    _numerator = num;
    _denominator = deno;

    _reduce();
}

void Rational::_reduce() {
    int gcd = 1;
    for(int i = 1; i <= _numerator && i <= _denominator ; i++){
        if((_numerator%i == 0) && (_denominator%i == 0))
            gcd = i;
    }

    _numerator = _numerator/gcd;
    _denominator = _denominator/gcd;
}


int Rational::getNumerator() const{
    return _numerator;
}

int Rational::getDenominator() const{
    return _denominator;
}

void Rational::setNumerator(const int num) {
    _numerator = num;

    _reduce();
}

void Rational::setDenominator(const int deno) throw(const char*){
    if(deno == 0){
        throw "denominator is not supposed to be zero!";
    }
    _denominator = deno;

    _reduce();
}

Rational operator+ (const Rational a, const Rational b){
    Rational result = Rational(a.getNumerator()*b.getDenominator()+a.getDenominator()*b.getNumerator(),
            a.getDenominator()*b.getDenominator());
    return result;
}

Rational operator- (const Rational a, const Rational b){
    Rational result = Rational(a.getNumerator()*b.getDenominator()-a.getDenominator()*b.getNumerator(),
            a.getDenominator()*b.getDenominator());
    return result;
}

Rational operator* (const Rational a, const Rational b){
    Rational result = Rational(a.getNumerator()*b.getNumerator(), a.getDenominator()*b.getDenominator());
    return result;
}

Rational operator/ (const Rational a, const Rational b) throw(const char*){
    if(b.getNumerator() == 0){
        throw "The dividend cannot be zero!";
    }
    Rational result = Rational(a.getNumerator()*b.getDenominator(), a.getDenominator()*b.getNumerator());
    return result;
}

bool operator== (const Rational a, const Rational b){
    if(a.getNumerator()*b.getDenominator() == a.getDenominator()*b.getNumerator()) {
        return true;
    } else {
        return false;
    }
}

ostream& operator<< (ostream& sout, const Rational& r){
    //1. Grabbing the reference to the output stream buffer

    //2. Attach the output strings to the buffer based on my likings
    sout << r.getNumerator() << "/" << r.getDenominator();

    //3. Return the reference of the modified output stream buffer
    return sout;
}

istream& operator>> (istream& sin, Rational& r){
    //1. Grabbing the reference to the input stream buffer

    //2. Acquire the num and the denom from the input and save it to r
    char slash;
    sin >> r._numerator>> slash >> r._denominator;

    r._reduce();
    //3. Clear slash, return the reference of the modified input stream buffer
    return sin;
}

int main(int argc, char** argv){
    Rational a;
    Rational b;
    cout << "first fraction (a/b): ";
    cin >> a;
    cout << "second fraction (a/b): ";
    cin >> b;
    cout << "new a = " << a << endl;

    Rational result;
    result = a/b;
    cout << "a/b = " << result << endl;
    cout << "a = " << a << endl;
    if(a == Rational(3)) {
        cout << "same" << endl;
    } else {
        cout << "different" << endl;
    }

    return 0;
}
```

> Reference material: 
> Book: Thinking in C++, Volume 1, 2nd Edition,  Bruce Eckel. 
> Lectures: University of Waterloo, CS 247 (Software Abstraction and Specificati), 2020 spring term, professor Scott Chen.



