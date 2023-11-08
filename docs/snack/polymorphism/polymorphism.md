# Cracking Polymorphism

## What Is It?
Polymorphism is a fundamental concept in object-oriented programming (OOP) that encompasses various mechanisms and **allows objects/instances of different classes to be treated as the objects of a common super class.** The term comes from the Greek words `poly`, meaning **many**, and `morphe`, meaning **form**.

In short, polymorphism permits you to write code snippet that works on "many forms" of data by `inheritance` and the `passing of parameters`, achieving different results, making it more flexible, less verbose and extensible.

???+ quote "Quote"
    "The capacity to pass parameters in a way that enables polymorphism is a feature supported at the language level."

### Types of Polymorphism

??? example "1. Compile-Time Polymorphism (Static)" 
    - Also known as method overloading.
    - Occurs at compile time.
    - Same method name, different parameters.

???+ example "2. Run-Time Polymorphism (Dynamic)" 
    - Also known as method overriding.
    - Occurs at runtime.
    - Uses inheritance and virtual functions in languages like C++ or method overriding in languages like Java and Python.

## What Does It Do?

The idea behind polymorphism is to promote code reusability and make the code more maintainable and extendable. When you write code that can work with objects of different types/classes, you can add new types without modifying existing code. This allows for a more modular and organized structure.

??? abstract "Python Example: Run-Time Polymorphism"
    ### Python Example

    ```python
    # Define the superclass
    class Animal:
        def make_sound(self):
            return "Some generic sound"

    # Define the subclasses
    class Dog(Animal):
        def make_sound(self):
            return "Woof"

    class Cat(Animal):
        def make_sound(self):
            return "Meow"

    # Initialize polymorphism
    def animal_sound(animal):
        print(animal.make_sound())

    # Test
    a = Animal()
    d = Dog()
    c = Cat()

    animal_sound(a)  # Output: Some generic sound
    animal_sound(d)  # Output: Woof
    animal_sound(c)  # Output: Meow
    ```

In this Python example, the function `animal_sound` doesn't need to know the type of `animal`; it calls the `make_sound` method on whatever object is passed to it. This is possible because of polymorphism.

??? abstract "JavaScript Example: Run-Time Polymorphism"
    ### JavaScript Example

    ```javascript
    // Define the superclass
    class Animal {
        makeSound() {
            return "Some generic sound";
        }
    }

    // Define the subclasses
    class Dog extends Animal {
        makeSound() {
            return "Woof";
        }
    }

    class Cat extends Animal {
        makeSound() {
            return "Meow";
        }
    }

    // Initialize polymorphism
    function animalSound(animal) {
        console.log(animal.makeSound());
    }

    // Test
    const a = new Animal();
    const d = new Dog();
    const c = new Cat();

    animalSound(a);  // Output: Some generic sound
    animalSound(d);  // Output: Woof
    animalSound(c);  // Output: Meow
    ```

Here, the JavaScript example also demonstrates run-time polymorphism in a manner similar to the Python example. The function `animalSound` operates on any object that has a `makeSound` method, showcasing the power and flexibility of polymorphism.

## How Does Is It Work?

It's all about writing code that's more generic, thereby making it flexible, extensible, less verbose, and maintainable. By treating different objects as instances of their common superclass, you can write functions or methods that can work with a wide range of data types. This means you can add new subclasses or modify existing ones without changing the code that calls these objects, adhering to the Open/Closed Principle—one of the SOLID principles of object-oriented design, which states that "software entities should be open for extension but closed for modification."

??? example "Inheritance & Polymorphism"
    ### Inheritance
    The relationship between inheritance and polymorphism: Inheritance sets up the hierarchical relationship between a superclass and its subclasses, while polymorphism leverages this relationship to allow objects of different classes to be treated as objects of a common superclass.

    Here's a breakdown to emphasize the connection:

    1. **Inheritance**: Through inheritance, subclasses inherit the attributes and behaviors (methods) of the superclass. This allows us to establish a "is-a" relationship between the subclass and the superclass (e.g., a Dog "is-a" Animal).

    2. **Polymorphism**: Building upon this inheritance relationship, polymorphism allows us to use a generic type (usually the superclass type) to work with objects of any subclass derived from that superclass. This is achieved through overriding methods in the subclass, allowing for specific behaviors.

    3. **Method Invocation**: When you call a method on a superclass reference that points to a subclass object, the subclass version of the method is invoked. This is known as dynamic method dispatch, and it happens at runtime.

    4. **Independent Function with Generic Parameter**: As pointed out, a separate, independent function can take a parameter, allowing it to work with objects of any subclass type. This function can then call the common method (e.g., `makeSound()`), and the appropriate subclass implementation will be executed.

??? example "Methods in Polymorphism"
    ### Methods
    1. **Method Overloading (Static/Compile-Time Polymorphism)**: Multiple methods within the same class have the same name but different parameters. The correct method to be called is determined at compile-time based on the method signature.
    
    2. **Method Overriding (Dynamic/Run-Time Polymorphism)**: A subclass provides a new implementation for a method that is already defined in its superclass or one of its superclasses. The method in the subclass overrides the method in the superclass, and the version of the method to be called is determined at run-time based on the object's actual class.

??? example "The Benefits"
    ### Key Benefits
    - **Code Reusability**: You can write a function once and use it on different types of objects, reducing code duplication.
    
    - **Loose Coupling**: Polymorphism allows you to write code that doesn't need to know the exact type of the object it's interacting with, making the system more modular.

    - **Ease of Maintenance and Extensibility**: You can easily extend the code with new types without changing existing logic.

- Here's another small Python code snippet to emphasize the reusability and flexibility:

??? abstract "Python Example: Math"
    ### Python Example

    ```python
    class Shape:
        def area(self):
            pass

    class Circle(Shape):
        def __init__(self, radius):
            self.radius = radius
        
        def area(self):
            return 3.14159 * (self.radius ** 2)

    class Rectangle(Shape):
        def __init__(self, length, width):
            self.length = length
            self.width = width
        
        def area(self):
            return self.length * self.width

    def get_area(shape):
        return shape.area()

    # Test
    circle = Circle(5)
    rectangle = Rectangle(4, 6)

    print(get_area(circle))  # Output: 78.53975
    print(get_area(rectangle))  # Output: 24
    ```

In this example, `get_area()` is a generic function that can compute the area for any shape that follows the contract set by the `Shape` class, demonstrating polymorphism.

## Duck Typing & Polymorphism

???+ quote "Quote"
    Python's "duck typing" is a form of polymorphism where the type or class of an object is less important than the methods it defines.

Duck typing and polymorphism are closely related concepts, and duck typing can be seen as a form of polymorphism. Both concepts deal with how objects of different types can be used interchangeably in a flexible and dynamic manner, but they manifest differently depending on the type system of the programming language.

### Duck Typing

- Duck typing is a concept often associated with dynamically typed languages like Python and Ruby.
- It focuses on the behavior and capabilities of objects rather than their specific types (Class names).
- The term "duck typing" is often summarized with the phrase: "If it looks like a duck, swims like a duck, and quacks like a duck, then it probably is a duck." In other words, an object is judged by what it can do (its methods and properties) rather than by its formal type.
- Duck typing allows you to work with objects based on their behavior, and it doesn't require explicit type declarations or inheritance hierarchies.

### Polymorphism

- Polymorphism is a broader concept found in object-oriented programming (OOP) languages, including both statically and dynamically typed languages like Java, C++, Python, and JavaScript.
- Polymorphism encompasses various mechanisms that allow objects of different types to be treated as objects of a common superclass or to respond to a common interface.
- It often involves inheritance, method overriding, and explicit type hierarchies to enable the use of subclasses in place of their superclass.
- Polymorphism can be compile-time (static) or run-time (dynamic) and is a core feature of OOP.

So, while duck typing is a specific manifestation of polymorphism in dynamically typed languages, polymorphism as a broader concept applies to a wide range of programming paradigms and type systems, including statically typed languages where the type is explicitly declared and checked at compile time. **Both concepts share the common goal of promoting code flexibility and reusability by allowing objects to be treated generically based on their capabilities rather than their specific types.**

??? abstract "Python Example: Duck Typing in Python"
    ### Python Example

    ```python
    # Duck Typing
    class Dog:
        def speak(self):
            return "Woof"

    class Cat:
        def speak(self):
            return "Meow"

    class Duck:
        def speak(self):
            return "Quack"

    def animal_speak(animal):
        print(animal.speak())

    # Test
    animal_speak(Dog())  # Output: Woof
    animal_speak(Cat())  # Output: Meow
    animal_speak(Duck())  # Output: Quack

    ```
The example above doesn't explicitly demonstrate inheritance, but it does illustrate a concept closely related to inheritance, which is polymorphism through duck typing. In this example, there's no explicit inheritance hierarchy defined, but there's an implicit form of "inherited" behavior through the `speak()` method.

While traditional inheritance-based polymorphism has its advantages in enforcing a structured and organized class hierarchy, duck typing offers a more flexible and less formal way of achieving polymorphism in dynamically typed languages. It allows for more spontaneous and behavior-driven object interactions without the need for a predefined class hierarchy.