
#### Visitor
1. N different items needing a single operation without changing the existing implementation of these classes in a drastic manner.
2. Use the Visitor when you need to perform an operation on all elements of a complex object structure (for example, an object tree).
3. Use the pattern when a behavior makes sense only in some classes of a class hierarchy, but not in others.
4. C++ has the Partial types which makes visitor very easy/

#### Template Method Pattern
1. A pretty useful pattern which creates the blueprint of an algorithm in the superclass but lets subclasses override specific behavior.
2. Pretty simple and straighforward
3.  Use the pattern when you have several classes that contain almost identical algorithms with some minor differences. As a result, you might need to modify all classes when the algorithm changes.
4.  Use the Template Method pattern when you want to let clients extend only particular steps of an algorithm, but not the whole algorithm or its structure.


#### Strategy
The OG pattern.
1.  Use the Strategy pattern when you want to use different variants of an algorithm within an object and be able to switch from one algorithm to another during runtime.
2. Use the Strategy when you have a lot of similar classes that only differ in the way they execute some behavior.
3.  Use the pattern to isolate the business logic of a class from the implementation details of algorithms that may not be as important in the context of that logic.


#### State Pattern
The main idea is that, at any given moment, there’s a _finite_ number of _states_ which a program can be in. Within any unique state, the program behaves differently, and the program can be switched from one state to another instantaneously.
The State pattern suggests that you create new classes for all possible states of an object and extract all state-specific behaviors into these classes.
Instead of implementing all behaviors on its own, the original object, called _context_, stores a reference to one of the state objects that represents its current state, and delegates all the state-related work to that object.
1.  Use the State pattern when you have an object that behaves differently depending on its current state, the number of states is enormous, and the state-specific code changes frequently.
2.  Use the pattern when you have a class polluted with massive conditionals that alter how the class behaves according to the current values of the class’s fields.

#### Observer
1.  Use the Observer pattern when changes to the state of one object may require changing other objects, and the actual set of objects is unknown beforehand or changes dynamically.
2.  Use the pattern when some objects in your app must observe others, but only for a limited time or in specific cases.


#### Memento
Example: Supporting Undo and Redo and text editors

Delegates creating the state snapshots to the actual owner of that state, since it has access to its own state
It depends on nested classes
1.   Use the Memento pattern when you want to produce snapshots of the object’s state to be able to restore a previous state of the object.
2.  Use the Memento pattern when you want to produce snapshots of the object’s state to be able to restore a previous state of the object.

### Mediator
Example: A login box with a bunch of settings interacting with each other and the login button
Defines an object that defines how a set of other objects interact with one another. Restricts direct communication between objects and allows them to communicate through it. 
This design patterns basically works on the paradigm of sending an event instead of procedurally writing the communications. 
1.  Use the Mediator pattern when it’s hard to change some of the classes because they are tightly coupled to a bunch of other classes.
2. Use the pattern when you can’t reuse a component in a different program because it’s too dependent on other components.
3.  Use the Mediator when you find yourself creating tons of component subclasses just to reuse some basic behavior in various contexts.

#### Iterator
Example: Writing a dfs iterator
Extracts the traversal behavior of a collection into a separate object called iterator. It traverses the elements without knowing the internal implementation of these iterator.
1.  Use the Iterator pattern when your collection has a complex data structure under the hood, but you want to hide its complexity from clients (either for convenience or security reasons).
2.  Use the pattern to reduce duplication of the traversal code across your app.
3. Use the Iterator when you want your code to be able to traverse different data structures or when types of these structures are unknown beforehand.


#### Command 
