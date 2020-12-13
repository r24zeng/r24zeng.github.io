---
title: Object-oriented Programming - Error Handling
tags: c++, OOP
categories: notes
---
## Error Raise
Assertion: Must `#include "assert.h"`. If assertion fails, then program abort. We should avoid to use `assert`, because it leaves no chance to programer to recover.

Remove preprocessor( assertion ) from code during compiling: `g++ -DNDBUG`.

## Exception Handle
> When an error occurs deep in a call stack. If the error can't be handled, then the entire program will be shut down. In order to avoid that, error handle is important to let program recover when catches the error.

![Error recovery conception: one function fails, it exits current function and returns back to last function to handle this error and last status.]( /img/my40.jpg)

**Exception**: An exception is an object thrown to represent the occurrence of an error. In *C++*, **catch-try** syntax is applied.

**BDP**: self-defined package exceptions and store exception object on the heap. All exceptions are thrown by exception classes.

![C++ exception standard library]( /img/my39.jpg)

### Rational ADT Example:
```cpp
class Rational{		
    public: 
 	class DivideByZeroException {
 		public: 					
            DivideByZeroException( int n ) : numer_(n) {}
            //accessor
 			int numer() const { return numer_; }    
 		private: 					
            int numer_;     //exception data
 	};
…};

int main() {
    Rational r;
    try {
        cout << "Enter rational number (a/b): ";
        cin >> r;
    }
    catch (Rational::DividedByZeroException &e){
        cout << e.num() << "/0 is not a legal denominator value." << endl;
    }
}
```

**BDP**: Keep exception-safe statements out of the rry-block for optimal performance, such as moving "cout <<" out of `try`, and keep "cin >>" inside of `try`.

## Throw Exception
**Rethrowing**: when nearest up layer function can't handle this exception, then throw another exception in order to pass this exception to `function()` up the call stack by one layer.

![Rethrowing model]( /img/my41.jpg)

```cpp
catch(someException &e) { cout << "Exceptioin caught" << e << endl; throw;}
```

If `destructor` throws an exception that is not handled locally, the program terminates. Because deconstructor always manages the memory.

After `throw`, the program will not continue executing other lines.

**noexcept** Keyword:
```cpp
~ Rational() noexcept;
int numer() const noexcept;
```

**C++ 11 exception example**:
```cpp
class Rational final{

	public:
    // throw exception
    class DivideByZeroException{
    public:
        DivideByZeroException( int n) : numer_(n) {};
        int getNumer() const {return numer_;}
    private:
        int numer_;
    };

    explicit Rational(int num = 0, int denom = 1);
    ...
}

Rational::Rational(int num, int denom) throw (const char*){

	cout << "constructor called." << endl;

	rat_ = new PImpl;

	rat_ -> numerator_ = num;
	rat_ -> denominator_ = denom;

	if(rat_ -> denominator == 0) {
        // nothing below throw will be executed
	    throw Rational::DivideByZeroException(num);
	}
	_reduce();	
}

int main(int argc, char** argv){

	Rational a; //constructor called
	cout << "First Fraction (a/b): ";

	try{
        cin >> a; // risky code
	}
	catch(Rational::DivideByZeroException &e){
	    cout << e.getNumer() << ": Denominator can't be zero!" << endl;
	    cout << "Terminate the program now ..." << endl;
	    return -1;
	}
}
```
Explaination: When input zero denominator, an exception is throwed from `Rational` constructor, then catch this exception in `main function` to do what you want. You may just print some reminder message on screen then abort this program, or use a while loop to recover this exception until correct input format.

**Further example**:
```cpp
int main(int argc, char** argv){

	Rational a; //constructor called
	cout << "First Fraction (a/b): ";

	try{
        cin >> a; // risky code
	}
	catch(Rational::DivideByZeroException &e){
	    cout << e.getNumer() << ": Denominator can't be zero!" << endl;
	    // cout << "Terminate the program now ..." << endl;
	    // return -1;
	}

    	Rational b; //constructor called
	cout << "Second Fraction (a/b): ";

	try{
        cin >> b; // risky code
	}
	catch(Rational::DivideByZeroException &e){
	    cout << e.getNumer() << ": Denominator can't be zero!" << endl;
	    // cout << "Terminate the program now ..." << endl;
	    // return -1;
	}

    Rational c;
    c = a + b;
    cout << "a + b = " << c << endl;
}
```

**Running result**: 
```
constructor called. 
First Fraction (a/b): 1/0
0: Denominator can't be zero!
constructor called.
Seconde Fraction (a/b): 2/0
0: Denominator can't be zero!
constructor called.
Exception happen: DivideByZeroException raised, program abort...
```
Explaination: If we don't stop program immediately after exception, then the exception raises but continues executing until `c = a + b`, the program aborts. Because `c = a + b` invokes `Rational constructor`, exception is raised in the constructor, it happens on the top layer, so our program stops immediately. As the constructor we defined above, Under this situcaiton, `a` and `b` have been modified before `throw` and passed to `c`, this is bad. So for *BDP*, we shouldn't assgin `num` and `denomin` to `a` and `b` before we know they are legal. It can changed to below code:
```cpp
Rational::Rational(int num, int denom) throw (const char*){

	cout << "constructor called." << endl;

	rat_ = new PImpl;

	if(denom == 0) {
        // nothing below throw will be executed
	    throw Rational::DivideByZeroException(num);
	}

	rat_ -> numerator_ = num;
	rat_ -> denominator_ = denom;
	
	_reduce();	
}
```

**Running result**: 
```
constructor called. 
First Fraction (a/b): 1/0
0: Denominator can't be zero!
constructor called.
Seconde Fraction (a/b): 2/0
0: Denominator can't be zero!
constructor called.
a = 0/1
b = 0/1
a + b = 0/1
```

## Standard Exception example in C++
> Resource Acquisition is Initialization (RAII). Equates resource management with the lifetime of an object

![Let file can be opened along with program life time and don't forget the release the resource (`decontructor`)]( /img/my42.jpg)


> Reference material: 
> Book: Thinking in C++, Volume 1, 2nd Edition,  Bruce Eckel. 
> Lectures: University of Waterloo, CS 247 (Software Abstraction and Specificati), 2020 spring term, professor Scott Chen.