---
title: Object-oriented Programming - Special member functions
tags: c++, OOP
categories: notes
---

| data type | 
| --- |
| primitive data types |
| pointer members |
| member objects |
| inherited members |

## Initialize constructor and deconstructor
> primitive data and pointer don't have default initilization, pointer is defualt deallocated but not deleted. Class object is initilized and deconstructor by default definition, and inherited object is initilized by base class default constructor and deconstructor.

## Copy constructor
+ compiler first searches defined copy constructor, if don't define conpy constructor, then uses its own default copy constructor.
+ All default copy is shallow copy.
+ pointer default copy is shallow copy, create a new pointer, then points the same reference address as the original pointer.

步骤：新建指针对象，深度复制指针对象。

以下这些例子都只调用copy constructor，只有`Money m`调用构造函数。

**Some copy examples:**
```cpp
int main() {
    Money m;    // class default constructor
    Money n{m};     // create a new object "n", then call default copy constructor
    Money p = m;     // same as above
    foo(p);     // pass by value, this value is copied as same as above
}
```

**Fixed form of class object shallow copy constructor:**
```cpp
Money(const Money &m){
    amount_ = m.amount
}
```

*caution: pass by **reference**, parameter must be **const**, same function name as constructor, but it's a copy constructor。*

**shollow copy saves space, for example, pointer shollow copy is two pointers point to one reference address in heap. shollow copy is also safer than deep copy, because it doesn't allocate extra room on heap which may be occupied to result in memory leak.**

## Assignment constructor
> very similar as copy constructor, the only difference is that assigmnent copy is to the exsiting object.

步骤：新建指针对象，深度复制指针对象，返回`*this`。

以下例子只调用assignment copy constructor，只有`Money m,p`调用构造函数。

**assignment exemple:**
```cpp
Money m,p;
p = m;
```

**fixed form for overloading assingment constructor:**
```cpp
Money& operator= (const Money &m){
    this->amount_ = m.amount_;
    return *this;
}
```
*caution: Must have return value, because `p=m` creates local container instance `this`, return `this` to lvalue. Must pass by reference.*

## Deep copy and Swap
### Deep copy
> Non-pointer memebers are copied over from the source(pass by reference), which means they are shollow copy because they don't need deep copy

**How pointer to do deep copy:**

Deep copy for pinter of a class object:<br>
![my6]( /img/my6.jpg)

`delete this->ptr` is to deallocate `this->ptr`, it then pointers nowhere. Because the size of the reference data `this->ptr` is not you finally want, such as the reference data is list or dynamic data varied in size.

**toxic pointer**: But this method has a potential problem. If memory of heap is not enough(as `delete pointer` is only to deallocate the pointer but the reference data is not deleted), then the function above will return `Null` or original pointer. If return original pointer, because the pointer's memory has been free, it points nowhere, which is called toxic pointer.

`new` and `delete` key words are to allocate and free memory for pointer on stack, not about the reference data in heap.

`static` is set to let object have only coding run time life and immutable.

In order to solve the problem, **swap** solution shows up.

### Deep copy with swap
swap solution for pointer of a class object:<br>
![my7]( /img/my7.jpg)

Deep copy solution is to pass by reference, but swap solution is to pass by value. Because swap solution doesn't allocate space for a pointer, so the pointer inside of the function will be deleted by compiler's default deconstructor. The pointer's explicit copy is on stack, which will be deleted after class function end. Referenced data is in heap.

## Move constructor
> default move constructor makes shallow copy for pointer

步骤：新建指针对象，指针直接赋值（swap是为了释放旧指针，让其内存可以被析构函数自动释放，毕竟右值留不留都无所谓），其他成员赋值。

移动构造函数只有当用纯**右值**复制的时候才会调用。`n=foo2()`只调用`move constructor`，因此必须要像复制构造函数一样，先新建一个对象，再深度复制，这时候可以在`move constructor`里调用自己写的`copy conostructor`(必须是深度复制)来新建对象，然后把旧对象的指针（无论是几维指针都行）赋值给`*this`，。

**move constructor usage example:**
```cpp
class Money;  // an ADT
Money foo();  // function return an ADT

int main() {
    Money m;   // call constructor
    Money n = foo2();  // call move constructor upon foo() return
}
```

**fixed form for move constructor:**
```cpp
class Money{
    public:
        Money();
        ~Money();

        Money(Money &&m){
            amount_ = m.amount_;
            m.amount_ = 37416782;
        }

    private:
        int amount_;
}
```
*caution: must pass by rvalue reference.*

## Move assignment constructor
> similar to move constructor, except the destination of move already exists. It's shallow copy!

步骤：删除`*this`里的指针，深度复制指针对象，要swap，返回`*this`。

方法一：新建一个临时指针对象`*temp`，这个函数会先调用`copy constructor`，所以直接`swap`即可，前提是复制构造函数必须是深度复制；方法二：释放原有指针，重新分配内存，再新建`*temp`用来做`*this`和`*other`里指针的交换，这个也是深度复制。

**usage example:**
```cpp
class Money;

int main() {
    Money m, p;  // call constructor
    p = Money();  // call constructor then move assignment operator
}
```
**fixed form for move assignment constructor:**
```cpp
class Money{
    public:
        Money();
        ~Money();

        Money& operator= (Money &&m){
            this->amount_ = m.amount_;
            m.amount_ = 37416782;
            return *this;
        }

    private:
        int amount_;
}
```

*For move and move assignment, there is no need to do deep copy for preserving source data, because we don't care source object and don't use it any more. We only care about the copy so that we don't delete the existing pointer. Because if we pass by lvalue, it must mess around the source object, instead of doing that, we pass rvalue. If we pass value instead of reference, it will cause extra call of copy constructor.*

## Coding
![my8]( /img/my8.jpg)

**updated Rational code with adding 6 special member functions(SMF)**
+ constructor
+ deconstructor
+ copy constructor
+ copy assignment constructor
+ move constructor
+ move assignment constructor

总结：copy constructor 和 copy assignment constructor 必须是深度复制（指针一级级的复制），而且在复制前一定要 `new pointer` 如果有指针对象的话。move constructor 在复制前一定要 `new pointer` ，然后复制可以用`swap`或者不用都行。move assignment constructor 无需新建指针，必须	`swap`。move constructor 和 move assignment constructor 无论遇到几维的指针都可以当作一级指针直接复制。copy assignment 和 move assignment 都可以跟想对应的 copy constructor 和 move constructor 一模一样，只是需要返回指针。

```cpp
#include <iostream>
using namespace std;


//my private implementation (representation) of a Rational number
struct PImpl{
	int numerator_;
	int denominator_;
};

//Adding final - make this class underivable (prevent it from being derived)
//Because my Rational ADT is a Value ADT 
//(should be made immutable as much as possible)
class Rational final{

	public:
	
		//Constructors
		/////////////////////////
		
		//default constructor
		
		//adding explicit has the following two effects:
		//1. Forces the compiler to use this specific constructor to type 
                //cast
                //2. Forces client to always explicit type cast.  implicit type 
		//conversion is completely prohibited.
		explicit Rational(int num = 0, int denom = 1) throw (const char*);
		
		//Destrcutor
		~Rational();
		
		//Copy Ctor
		Rational(const Rational &r); //lvalue reference: &
		
		//Copy Assignment
		Rational& operator= (const Rational &r);
		
		//Move Ctor
		Rational(Rational &&r); //rvalue reference: &&
		
		//Move Assignment
		Rational& operator= (Rational &&r); //rvalue reference: &&
					
					
		//Accessors and Mutators (Getters and Setters)
		/////////////////////////
		
		//Accessors
		//By setting these accessors const, all the involved data members 
		//in the function will remain read-only
		int getNumerator() const;
		int getDenominator() const;
				
		//Mutators - deprecated because Value ADTs are supposed to be 
		///immutable!!!
		//DO NOT SET const for function
		
		//void setNumerator(const int num);
		//void setDenominator(const int denom) throw (const char*);
		
		
		//IO stream nonmember function (friended)
		///////////////////////////////////////////
		//Enables operator>> to have access to the private data members
		friend istream& operator>> (istream&, Rational&);
			
	private:
	
		//Data members (fields / attributes)
		//////////////////////////////////////
		
		//Exposed Implementation - Probably not a good practice...
		//int numerator_;
		//int denominator_;
		
		//Deploy PImpl 
		PImpl* rat_;
				
		void _reduce();
	
};

//Arithmetic operator prototype (overloaded)
//defined as NON-MEMBER functions
Rational operator+ (Rational a, Rational b); // a + b = result (Rational)
Rational operator- (Rational a, Rational b); 
Rational operator* (Rational a, Rational b); 
Rational operator/ (Rational a, Rational b); 

//Boolean (Logic) Operator prototype (overloaded)
bool operator== (Rational a, Rational b); // is a == b? T/F

//Outstream operator prototype - I forgot to add it in Week 1 demo
ostream& operator<< (ostream& sout, const Rational& r);



Rational::Rational(int num, int denom) throw (const char*){

	cout << "constructor called." << endl;

	rat_ = new PImpl;

	if(denom == 0){ //ILLEGAL!!!
		throw "Denominator is not supposed to be zero!!";
		return; //this line is not needed (won't be executed anyways)
		        //only here for readability.
	}

	rat_ -> numerator_ = num;
	rat_ -> denominator_ = denom;
	
	_reduce();	

}


//Destrcutor
Rational::~Rational(){
	if(rat_ != NULL){
		delete rat_; //prevent memory leak
	}
}
		
//Copy Ctor
Rational::Rational(const Rational &r){ //lvalue reference: &

	cout << "Copy Ctor Called" << endl;

	rat_ = new PImpl;
	rat_->numerator_ = r.rat_->numerator_;
	rat_->denominator_ = r.rat_->denominator_;
}
		
//Copy Assignment
Rational& Rational::operator= (const Rational &r){

	cout << "Copy Assignment Called" << endl;

	//no copy-swap
	delete rat_;
	rat_ = new PImpl; //redundant for this ADT, but generally done for more complex 
 			  //ADTs with dynamic data members.
 			  
 	rat_->numerator_ = r.rat_->numerator_;
	rat_->denominator_ = r.rat_->denominator_;
	
	return *this; //return the local container instance
 	
}
		
//Move Ctor            vv COPY
Rational::Rational(Rational &&r){ //rvalue reference: &&

	cout << "Move Ctor Called" << endl;

	//copy-swap
	
	rat_ = new PImpl;
	
	//SWAP
	PImpl *temp = this->rat_; //auto-generated ptr member
	this->rat_ = r.rat_; //redirecting the local container ptr member reference to  
			     //the source reference data structure on the heap.
 	r.rat_ = temp; //have the auto-generated ptr member stored into the source
 
 	//r is removed (popped) from stack, r.rat_ is now deleted.
}
		
//Move Assignment               vv COPY
Rational& Rational::operator= (Rational &&r){ //rvalue reference: &&

	cout << "Move Assignment Called" << endl;

	//copy-swap

	//SWAP
	PImpl *temp = this->rat_; //auto-generated ptr member
	this->rat_ = r.rat_; //redirecting the local container ptr member reference to  
			     //the source reference data structure on the heap.
 	r.rat_ = temp; //have the auto-generated ptr member stored into the source
 
 	return *this;
 	//r is removed (popped) from stack, r.rat_ is now deleted.
}

//Private helper function for reducing the fraction
void Rational::_reduce(){
	
	int gcd = 1;
	
	//find the GCD of the num and denom	
	for(int i = 1; (i <= rat_->numerator_) && (i <= rat_->denominator_); i++){
		if((rat_->numerator_ % i == 0) && (rat_->denominator_ % i == 0)){
			gcd = i;
		}
	}
	
	rat_->numerator_ /= gcd;
	rat_->denominator_ /= gcd;
	
}


int Rational::getNumerator() const{
	return rat_->numerator_;
}

int Rational::getDenominator() const{
	return rat_->denominator_;
}

//Mutators are deprecated to ensure immutability of Rational ADT
/*
void Rational::setNumerator(const int num){
	numerator_ = num;
	_reduce();

}

void Rational::setDenominator(const int denom) throw (const char*){

	if(denom == 0){ //ILLEGAL!!!
		throw "Denominator is not supposed to be zero!!";
		return;
	}

	denominator_ = denom;
	_reduce();
	
}
*/


//add const to the input params makes them read-only to the implementation
//it makes sense to keep input param const and PbV only for these arithmetic ops.
Rational operator+ (const Rational a, const Rational b){
	Rational result = Rational(a.getNumerator() * b.getDenominator() + 
				   b.getNumerator() * a.getDenominator(), 
				   a.getDenominator() * b.getDenominator());
	return result;
}

Rational operator- (const Rational a, const Rational b){
	Rational result = Rational(a.getNumerator() * b.getDenominator() - 
				   b.getNumerator() * a.getDenominator(), 
				   a.getDenominator() * b.getDenominator());
	return result;
}

Rational operator* (const Rational a, const Rational b){
	Rational result = Rational(a.getNumerator() * b.getNumerator(), 
				   a.getDenominator() * b.getDenominator());
	return result;
}


Rational operator/ (const Rational a, const Rational b){
	Rational result = Rational(a.getNumerator() * b.getDenominator(), 
				   a.getDenominator() * b.getNumerator());
	return result;
} 

bool operator== (const Rational a, const Rational b){
	int numA, numB;
	numA = a.getNumerator() * b.getDenominator();
	numB = b.getNumerator() * a.getDenominator();
	
	if(numA == numB) return true;
	else		 return false;
}

//IO Streamer (cin / cout)
/////////////////////////////////////////////////
ostream& operator<< (ostream& sout, const Rational& r){
	//1. Grabbing the reference to the output stream buffer
	
	//2. Attach the output strings to the buffer based on my likings
	sout << r.getNumerator() << "/" << r.getDenominator();
	
	//3. Return the reference of the modified output stream buffer
	return sout;
}

//Only friended non-member function that can mutate the Rational ADT
//Maybe...we should only allow istream to mutate the Rational ADT ONCE

istream& operator>> (istream& sin, Rational& r){

	//This implementation is poorly done (on purpose)
	//We will come back in Module 7 and use some helpful tools to fix this function.


	//1. Grabbing the reference to the input stream buffer
	
	
	//2. Acquire the num and the denom from the input and save it to r
	char slash;
	sin >> r.rat_->numerator_ >> slash >> r.rat_->denominator_;
	
	//Check legal value for denominator. Maybe should throw exception.
	//Cannot access private helper function.  Therefore, think about 
	//other ways to reduce the fraction!  How?
	
	//by the end of the operator function, slash is deleted from the stack
	
	//3. Return the reference of the modified input stream buffer
	return sin;
}


//Invoking copy constructor for PbV via stack
void foo(Rational g){
	return;
}

//Invoking copy ctor, then move ctor / move assignment upon return
Rational foo2(Rational q){
	return q;
}

int main(int argc, char** argv){

	Rational a; //constructor called
	cout << "First Fraction (a/b): ";
	cin >> a;
				
	cout << "Test Start: " << endl ;
	cout << endl << "Copy Constructor: " << endl;
	cout << "===================" << endl;
	
	Rational b{a};
	Rational c = a;
	foo(c); 
	
	cout << endl << "Copy Assignment: " << endl;
	cout << "===================" << endl;
	
	b = c; 
	
	cout << endl << "Move Constructor: " << endl;
	cout << "===================" << endl;
	
	Rational d = foo2(c);  //complex action: it will invoke more than one SMF
	
	cout << endl << "Move Assignment: " << endl;
	cout << "===================" << endl;
	
	b = foo2(c);  //complex action: it will invoke more than one SMF
	
}
```

**test output result：**
![my9]( /img/my9.jpg)


> Reference material: 
> Book: Thinking in C++, Volume 1, 2nd Edition,  Bruce Eckel. 
> Lectures: University of Waterloo, CS 247 (Software Abstraction and Specificati), 2020 spring term, professor Scott Chen.