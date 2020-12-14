---
title: Object-oriented Programming - Singleton Design Pattern
tags: Design Pattern
categories: notes
---
> We want exactly one instance( object ) for one class, no copy and no assignment, Singleton Design Pattern can do this.

There are 5 steps How To Turn An Object into Singleton DP in C++:
+ Create static instance of the object in private scope in the class definition
+ Move constructor to private scope
+ Create static accessor to the static instance in public scope
+ Prohibit copy and assign
+ Initialize before runtime

### Original implementation of `Player` class:
```cpp
#include <iostream>
using namespace std;

class Player {

public:
    Player(int value = 0) {
        val_ = value;
    }

    // accessor
    int getValue() {
        return val_;
    }

    // mutator
    void setPlayer(int value) {
        val_ = value;
    }

private:
    int val_;

};

int main(int argv, char** argc) {
    Player* player = new Player(28);
    // entity class can only be accessed by pointer( -> ) instead of instance( . )
    cout << player->getValue() << endl;
    return 0;
}
```

### Protect private member from mutability in `Player` class:
```cpp
#include <iostream>
using namespace std;

// hiding private member avoiding mutablility(modification by clients)
struct PlayerVal {
    int val;
};

class Player {

public:
    Player(int value = 0) {
        plv_ = new PlayerVal;
        plv_->val = value;
    }

    // accessor
    int getValue() {
        return plv_->val;
    }

    // mutator
    void setPlayer(int value) {
        plv_->val = value;
    }

private:
    PlayerVal* plv_;

};

int main(int argv, char** argc) {
    Player* player = new Player(28);
    cout << player->getValue() << endl;
    return 0;
}
```

### Singleton DP version of `Player` class:
```cpp
#include <iostream>
using namespace std;

class Player {
public:
    // step 3: add an static instance function to get the instance
    static Player* instance() {
        return &pl_;
    }

    // accessor
    int getValue() {
        return val_;
    }

    // mutator
    void setPlayer(int value) {
        val_ = value;
    }

private:
    // step1: static instance should be in scope of private
    static Player pl_;
    // step2: constructor must be in scope of private
    Player (int value = 0) {
        val_ = value;
    }

    int val_;
};

// step5: initialize the static private instance in global
// Decomposite it to "Player" and "Player::pl_(42)"
// First "Player" means the type of pl_, second "Player" is the class name, "pl_(42)" is to initilize "pl_" by invoking constructor
//Player::pl() would invoke the Player(int val = 0) constructor under the private scope to initialize pl_ to 42.
Player Player::pl_(42);

int main(int argv, char** argc) {
    cout << Player::instance()->getValue() << endl;
    return 0;
}
```

> Reference material: 
> Book: Thinking in C++, Volume 1, 2nd Edition,  Bruce Eckel. 
> Lectures: University of Waterloo, CS 247 (Software Abstraction and Specificati), 2020 spring term, professor Scott Chen.

