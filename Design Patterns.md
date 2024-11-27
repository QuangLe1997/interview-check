# Design Patterns in Python

## 1. Creational Patterns

### 1.1 Singleton Pattern
```python
class Singleton:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
    
    def __init__(self):
        # Initialize only once
        if not hasattr(self, 'initialized'):
            self.initialized = True
            self.data = []

# Usage
config1 = Singleton()
config2 = Singleton()
assert config1 is config2  # True
```

### 1.2 Factory Pattern
```python
from abc import ABC, abstractmethod

# Abstract Product
class Animal(ABC):
    @abstractmethod
    def speak(self):
        pass

# Concrete Products
class Dog(Animal):
    def speak(self):
        return "Woof!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

# Factory
class AnimalFactory:
    @staticmethod
    def create_animal(animal_type: str) -> Animal:
        if animal_type.lower() == "dog":
            return Dog()
        elif animal_type.lower() == "cat":
            return Cat()
        raise ValueError("Invalid animal type")

# Usage
factory = AnimalFactory()
dog = factory.create_animal("dog")
cat = factory.create_animal("cat")
```

### 1.3 Builder Pattern
```python
class Computer:
    def __init__(self):
        self.cpu = None
        self.ram = None
        self.storage = None

class ComputerBuilder:
    def __init__(self):
        self.computer = Computer()
    
    def configure_cpu(self, cpu):
        self.computer.cpu = cpu
        return self
    
    def configure_ram(self, ram):
        self.computer.ram = ram
        return self
    
    def configure_storage(self, storage):
        self.computer.storage = storage
        return self
    
    def build(self):
        return self.computer

# Usage
computer = ComputerBuilder()\
    .configure_cpu("Intel i7")\
    .configure_ram("16GB")\
    .configure_storage("512GB SSD")\
    .build()
```

## 2. Structural Patterns

### 2.1 Adapter Pattern
```python
# Existing class
class EuropeanSocket:
    def voltage(self):
        return 230
    
    def live(self):
        return 1
    
    def neutral(self):
        return -1

# Target interface
class USASocket:
    def voltage(self):
        return 120
    
    def live(self):
        return 1
    
    def neutral(self):
        return -1

# Adapter
class Adapter(USASocket):
    def __init__(self, socket):
        self.socket = socket
    
    def voltage(self):
        return 110
    
    def live(self):
        return self.socket.live()
    
    def neutral(self):
        return self.socket.neutral()

# Usage
european = EuropeanSocket()
adapter = Adapter(european)
```

### 2.2 Decorator Pattern
```python
from functools import wraps

def make_blink(function):
    """Defines the decorator"""
    
    @wraps(function)
    def decorator():
        ret = function()
        return f"<blink>{ret}</blink>"
    
    return decorator

@make_blink
def hello_world():
    return "Hello, World!"

# Usage
print(hello_world())  # <blink>Hello, World!</blink>
```

## 3. Behavioral Patterns

### 3.1 Observer Pattern
```python
class Subject:
    def __init__(self):
        self._observers = []
        self._state = None
    
    def attach(self, observer):
        self._observers.append(observer)
    
    def detach(self, observer):
        self._observers.remove(observer)
    
    def notify(self):
        for observer in self._observers:
            observer.update(self._state)
    
    @property
    def state(self):
        return self._state
    
    @state.setter
    def state(self, value):
        self._state = value
        self.notify()

class Observer:
    def update(self, state):
        pass

class ConcreteObserver(Observer):
    def update(self, state):
        print(f"State changed to: {state}")

# Usage
subject = Subject()
observer = ConcreteObserver()
subject.attach(observer)
subject.state = 123
```

### 3.2 Strategy Pattern
```python
from abc import ABC, abstractmethod

# Strategy interface
class PaymentStrategy(ABC):
    @abstractmethod
    def pay(self, amount):
        pass

# Concrete strategies
class PayPalPayment(PaymentStrategy):
    def __init__(self, email):
        self.email = email
    
    def pay(self, amount):
        print(f"Paying {amount} using PayPal account {self.email}")

class CreditCardPayment(PaymentStrategy):
    def __init__(self, card_number):
        self.card_number = card_number
    
    def pay(self, amount):
        print(f"Paying {amount} using Credit Card {self.card_number}")

# Context
class ShoppingCart:
    def __init__(self):
        self.payment_strategy = None
    
    def set_payment_strategy(self, strategy):
        self.payment_strategy = strategy
    
    def checkout(self, amount):
        if self.payment_strategy is None:
            raise Exception("Please select a payment method")
        self.payment_strategy.pay(amount)

# Usage
cart = ShoppingCart()
cart.set_payment_strategy(PayPalPayment("example@email.com"))
cart.checkout(100)
```

### 3.3 Command Pattern
```python
from abc import ABC, abstractmethod

# Command interface
class Command(ABC):
    @abstractmethod
    def execute(self):
        pass

# Concrete Commands
class LightOnCommand(Command):
    def __init__(self, light):
        self.light = light
    
    def execute(self):
        self.light.turn_on()

class LightOffCommand(Command):
    def __init__(self, light):
        self.light = light
    
    def execute(self):
        self.light.turn_off()

# Receiver
class Light:
    def turn_on(self):
        print("Light is on")
    
    def turn_off(self):
        print("Light is off")

# Invoker
class RemoteControl:
    def __init__(self):
        self.command = None
    
    def set_command(self, command):
        self.command = command
    
    def press_button(self):
        self.command.execute()

# Usage
light = Light()
light_on = LightOnCommand(light)
light_off = LightOffCommand(light)

remote = RemoteControl()
remote.set_command(light_on)
remote.press_button()  # Light is on
```

## 4. Advanced Pattern Applications

### 4.1 Composite Pattern with Iterator
```python
from abc import ABC, abstractmethod
from typing import List

class Component(ABC):
    @abstractmethod
    def operation(self):
        pass

class Leaf(Component):
    def __init__(self, name):
        self.name = name
    
    def operation(self):
        return f"Leaf {self.name}"

class Composite(Component):
    def __init__(self, name):
        self.name = name
        self.children: List[Component] = []
    
    def add(self, component: Component):
        self.children.append(component)
    
    def remove(self, component: Component):
        self.children.remove(component)
    
    def operation(self):
        results = [f"Branch {self.name}"]
        for child in self.children:
            results.append(child.operation())
        return "\n".join(results)

# Usage
root = Composite("root")
branch1 = Composite("branch1")
branch2 = Composite("branch2")
leaf1 = Leaf("leaf1")
leaf2 = Leaf("leaf2")

root.add(branch1)
root.add(branch2)
branch1.add(leaf1)
branch2.add(leaf2)

print(root.operation())
```

### 4.2 Chain of Responsibility with Logging
```python
class Handler:
    def __init__(self, successor=None):
        self._successor = successor
    
    def handle(self, request):
        handled = self._handle(request)
        
        if not handled and self._successor:
            self._successor.handle(request)
    
    def _handle(self, request):
        raise NotImplementedError('Must provide implementation in subclass!')

class LogHandler(Handler):
    def _handle(self, request):
        if 'log' in request:
            print(f"Logging: {request['log']}")
            return True

class EmailHandler(Handler):
    def _handle(self, request):
        if 'email' in request:
            print(f"Sending email: {request['email']}")
            return True

class FileHandler(Handler):
    def _handle(self, request):
        if 'file' in request:
            print(f"Writing to file: {request['file']}")
            return True

# Usage
chain = LogHandler(EmailHandler(FileHandler()))
chain.handle({'log': 'Debug message'})
chain.handle({'email': 'Hello!'})
```
