# Python Object-Oriented Programming Guide

## 1. Cơ Bản về OOP trong Python

### 1.1 Classes và Objects
```python
class Dog:
    # Class attribute
    species = "Canis familiaris"
    
    # Constructor (Initializer)
    def __init__(self, name, age):
        # Instance attributes
        self.name = name
        self.age = age
    
    # Instance method
    def bark(self):
        return f"{self.name} says Woof!"

# Creating objects
dog1 = Dog("Rex", 3)
dog2 = Dog("Buddy", 5)

print(dog1.bark())  # Rex says Woof!
print(dog2.species) # Canis familiaris
```

### 1.2 Inheritance (Kế thừa)
```python
class Animal:
    def __init__(self, name):
        self.name = name
    
    def speak(self):
        pass

class Cat(Animal):
    def speak(self):
        return f"{self.name} says Meow!"

class Dog(Animal):
    def speak(self):
        return f"{self.name} says Woof!"

# Using inheritance
cat = Cat("Whiskers")
dog = Dog("Rex")

print(cat.speak())  # Whiskers says Meow!
print(dog.speak())  # Rex says Woof!
```

### 1.3 Encapsulation (Đóng gói)
```python
class BankAccount:
    def __init__(self):
        self._balance = 0  # Protected attribute
        self.__id = 123    # Private attribute
    
    def deposit(self, amount):
        if amount > 0:
            self._balance += amount
            return True
        return False
    
    def get_balance(self):
        return self._balance

account = BankAccount()
account.deposit(100)
print(account.get_balance())  # 100
# print(account.__id)  # AttributeError
```

## 2. OOP Nâng Cao

### 2.1 Properties và Decorators
```python
class Person:
    def __init__(self, name):
        self._name = name
        self._age = 0
    
    @property
    def age(self):
        return self._age
    
    @age.setter
    def age(self, value):
        if value >= 0:
            self._age = value
        else:
            raise ValueError("Age cannot be negative")
    
    @property
    def name(self):
        return self._name
    
    @name.setter
    def name(self, value):
        if value.strip():
            self._name = value
        else:
            raise ValueError("Name cannot be empty")

person = Person("John")
person.age = 25  # Uses setter
print(person.age)  # Uses getter
```

### 2.2 Multiple Inheritance và MRO (Method Resolution Order)
```python
class A:
    def method(self):
        return "A method"

class B:
    def method(self):
        return "B method"

class C(A, B):
    pass

class D(B, A):
    pass

c = C()
d = D()
print(c.method())  # "A method"
print(d.method())  # "B method"

# Checking MRO
print(C.__mro__)  # Shows method resolution order
```

### 2.3 Abstract Classes và Interfaces
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass
    
    @abstractmethod
    def perimeter(self):
        pass

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def area(self):
        return self.width * self.height
    
    def perimeter(self):
        return 2 * (self.width + self.height)

# shape = Shape()  # TypeError: Can't instantiate abstract class
rect = Rectangle(5, 3)
print(rect.area())  # 15
```

### 2.4 Magic Methods (Dunder Methods)
```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)
    
    def __str__(self):
        return f"Vector({self.x}, {self.y})"
    
    def __len__(self):
        return 2
    
    def __getitem__(self, key):
        if key == 0:
            return self.x
        elif key == 1:
            return self.y
        raise IndexError("Vector index out of range")

v1 = Vector(2, 3)
v2 = Vector(3, 4)
v3 = v1 + v2
print(v3)  # Vector(5, 7)
print(len(v3))  # 2
print(v3[0])  # 5
```

## 3. Design Patterns trong Python

### 3.1 Singleton Pattern
```python
class Singleton:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
    
    def __init__(self):
        if not hasattr(self, 'initialized'):
            self.initialized = True
            self.data = []

s1 = Singleton()
s2 = Singleton()
print(s1 is s2)  # True
```

### 3.2 Factory Pattern
```python
class Animal:
    def speak(self):
        pass

class Dog(Animal):
    def speak(self):
        return "Woof!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

class AnimalFactory:
    def create_animal(self, animal_type):
        if animal_type.lower() == "dog":
            return Dog()
        elif animal_type.lower() == "cat":
            return Cat()
        raise ValueError("Invalid animal type")

factory = AnimalFactory()
dog = factory.create_animal("dog")
cat = factory.create_animal("cat")
print(dog.speak())  # Woof!
print(cat.speak())  # Meow!
```

## 4. Best Practices và Tips

### 4.1 Dependency Injection
```python
class Database:
    def save(self, data):
        print(f"Saving {data} to database")

class Logger:
    def log(self, message):
        print(f"Logging: {message}")

class UserService:
    def __init__(self, database, logger):
        self.database = database
        self.logger = logger
    
    def create_user(self, user_data):
        self.database.save(user_data)
        self.logger.log("User created")

# Using dependency injection
db = Database()
logger = Logger()
user_service = UserService(db, logger)
user_service.create_user({"name": "John"})
```

### 4.2 Type Hints (Python 3.5+)
```python
from typing import List, Dict, Optional

class User:
    def __init__(self, name: str, age: int) -> None:
        self.name = name
        self.age = age
    
    def get_info(self) -> Dict[str, any]:
        return {
            "name": self.name,
            "age": self.age
        }
    
    @staticmethod
    def create_users(data: List[Dict[str, any]]) -> List['User']:
        return [User(u['name'], u['age']) for u in data]
    
    def get_nickname(self) -> Optional[str]:
        return None
```

### 4.3 Context Managers
```python
class FileManager:
    def __init__(self, filename: str, mode: str) -> None:
        self.filename = filename
        self.mode = mode
        self.file = None
    
    def __enter__(self):
        self.file = open(self.filename, self.mode)
        return self.file
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.file:
            self.file.close()

# Using context manager
with FileManager('test.txt', 'w') as f:
    f.write('Hello, World!')
```
