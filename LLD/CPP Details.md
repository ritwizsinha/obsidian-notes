

### VTable(Enabling Runtime Polymorphism)
- Created for each derived and base class containing at least one virtual keyword.
- Initialized as a static array in each of the classes,
- Each object has a vtable pointer to the correct vtable.
- Constructor initializes  this vtable pointer to the correct vtable
Virtual functions cannot be static because static functions are tied to the class itself



### Virtual Destructors
```cpp
class Base {
    public:
    Base() 
    {
        std::cout <<"Base constructor" << std::endl;
    }
    
    ~Base() {
        std::cout << "Base Destructor" << std::endl;
    }
};

class Derived: public Base {
    public:
    Derived() {
        std::cout << "Derived constructor called" << std::endl;
    }
    
    
    ~Derived() {
        std::cout << "Derived destructor" << std::endl;
    }
};
int main()
{
    Base *b = new Derived();
    
    delete b;
}
```

Output doesn't call derived destructor because there is no vtable pointer to it. To fix this code we need to make the destructor virtual.
	Virtual destructors are kind of necessary. 
Virtual constructors are nothing