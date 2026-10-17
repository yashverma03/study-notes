# Index

- [Intro](#intro)
  - [Input/ output](#input-output)
  - [Operators](#operators)
  - [Data types](#data-types)
- [Functions](#functions)
  - [range()](#range)
  - [Generators](#generators)
  - [\*args](#args)
  - [\\kwargs](#kwargs)
  - [Lambda](#lambda)
  - [map()](#map)
  - [reduce()](#reduce)
  - [filter()](#filter)
- [Decorators](#decorators)
  - [@staticmethod](#staticmethod)
  - [@classmethod](#classmethod)
  - [@property](#property)
- [Data Structures](#data-structures)
  - [String](#string)
  - [List](#list)
  - [Tuple](#tuple)
  - [Dictionary](#dictionary)
  - [Set](#set)
  - [Array](#array)
  - [List Comprehension](#list-comprehension)
- [OOP](#oop)
  - [Encapsulation](#encapsulation)
  - [Abstraction](#abstraction)
  - [Inheritance](#inheritance)
  - [Polymorphism](#polymorphism)
- [Exception Handling](#exception-handling)
- [File Handling](#file-handling)


# Intro


## Input/ output

```python
name = input("Enter your name: ")
print("Hello", name)
```


## Operators

`==` is Equality operator. Checks if the values of two objects are equal

`is` is  Identity operator. Checks if two variables point to the same object in memory

Logical operators- and, or, not


## Data types

Numeric: int, float, complex

Sequence: string, list, tuple

Mapping: dict

Boolean: bool

Set: set, frozenset

None: none


# Functions


## range()

generates a sequence of numbers.

range(start, stop, step)


## Generators

- special type of function that let you generate values one-by-one instead of creating and storing the entire result in memory
- use yield keyword
- Produces one value at a time
- Memory efficient- Does not store all values in memory. Faster for large data

```python
def my_gen():
    yield 1
    yield 2
    yield 3

for x in my_gen():
    print(x)
```


## \*args

- arguments
- Allows a function to accept any number of positional arguments (0 or more).
- args is a tuple of all positional arguments passed.

```python
def greet(*args):
    for name in args:
        print(f"Hello, {name}!")

greet("Alice", "Bob", "Charlie")
```


## \*\*kwargs

- keyword arguments
- Allows a function to accept any number of keyword arguments (0 or more)
- kwargs is a dictionary of all keyword arguments passed

```python
def introduce(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

introduce(name="John", age=23, city="Delhi")
```

Pass by Reference- Mutable objects (list, dict)

Pass by Value- Immutable objects (integers, strings, tuples)


## Lambda

lambda arguments : expression

```python
add = lambda x, y: x + y
```


## map()

map(function, iterable)

Returns a map object, convert to list, tuple, dict

```python
def square(x):
    return x ** 2

numbers = [1, 2, 3, 4]
squared = map(square, numbers)
print(list(squared))  # Output: [1, 4, 9, 16]
```

```python
numbers1 = [1, 2, 3]
numbers2 = [4, 5, 6]
result = map(lambda x, y: x + y, numbers1, numbers2)
print(list(result))  # Output: [5, 7, 9]
```


## reduce()

applies a function cumulatively to an iterable, reducing it to a single value

reduce(function, iterable\[, initializer\])

```python
from functools import reduce

def add(x, y):
    return x + y

a = [1, 2, 3, 4, 5]
res = reduce(add, a)
print(res)
```


## filter()

extract elements from an iterable (like a list, tuple or set) that satisfy a given condition

filter(function, iterable)

```python
def starts_a(w):
    return w.startswith("a")

li = ["apple", "banana", "avocado", "cherry", "apricot"]
res = filter(starts_a, li)
print(list(res))
```


# Decorators

a function that takes another function as an argument and returns a new function with enhanced functionality

```python
def decorator_name(func):
    def wrapper(*args, **kwargs):
        print("Before execution")
        result = func(*args, **kwargs)
        print("After execution")
        return result
    return wrapper

@decorator_name
def add(a, b):
    return a + b

print(add(5, 3))
```


## @staticmethod

define a method that doesn't operate on an instance of class (i.e., it doesn't use self). Static methods are called on class itself, not on an instance of class.

```python
class MathOperations:
    @staticmethod
    def add(x, y):
        return x + y

# Using the static method
res = MathOperations.add(5, 3)
print(res)
```


## @classmethod

It is used to define a method that operates on class itself (i.e., it uses cls). Class methods can access and modify class state that applies across all instances of class.

Cannot directly access instance attributes

```python
class Employee:
    raise_amount = 1.05
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary
        
    @classmethod
    def set_raise_amount(cls, amount):
        cls.raise_amount = amount

# Using the class method
Employee.set_raise_amount(1.10)
print(Employee.raise_amount)
```


## @property

It is used to define a method as a property, which allows you to access it like an attribute

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        return self._radius

    @radius.setter
    def radius(self, value):
        if value >= 0:
            self._radius = value
        else:
            raise ValueError("Radius cannot be negative")

    @property
    def area(self):
        return 3.14159 * (self._radius ** 2)

# Using the property
c = Circle(5)
print(c.radius) 
print(c.area)    
c.radius = 10
print(c.area)
```


# Data Structures


## String

Immutable

| Operation | Syntax |
| --- | --- |
| Returns the length of the string | `len(s)` |
| Converts all characters to lowercase/ uppercase | `s.lower() / s.upper()` |
| Removes leading and trailing whitespaces | `s.strip()` |
| Replaces all occurrences of old with new | `s.replace(old, new)` |
| Splits string into a list by separator | `s.split(sep)` |
| Joins list of strings into a single string with separator | `s.join(list)` |
| Returns index of first occurrence of substring, -1 if not found | `s.find(sub)` |
| Returns number of occurrences of substring | `s.count(sub)` |
| Access a substring or range (slicing) | `s[start:end:step]` |
| Access a character by index | `s[index]` |


## List

| Operation | Syntax |
| --- | --- |
| Access an element by index | `l[index]` |
| Access a sublist or range (slicing) | `l[start:end:step]` |
| Returns the number of elements | `len(l)` |
| Adds an element to the end | `l.append(element)` |
| Inserts an element at a specific index | `l.insert(index, element)` |
| Removes the first occurrence of a value | `l.remove(value)` |
| Removes and returns element at index | `l.pop(index)` |
| Deletes element(s) by index or slice | `del l[index] / del l[start:end]` |
| Sorts the list | `l.sort() / l.sort(reverse=True)` |
| Returns a sorted copy without modifying original | `sorted(l)` |
| Reverses the elements in place | `l.reverse()` |
| Returns a reversed iterator | `reversed(l)` |
| Creates a shallow copy of the list | `l.copy()` |
| Extends list by appending elements from iterable | `l.extend(iterable)` |
| Removes all elements | `l.clear()` |

2D list/ matrix

| Operation | Syntax |
| --- | --- |
| Access element at row i, column j | `l[i][j]` |
| Number of rows | `len(l)` |
| Number of columns in row i | `len(l[i])` |


## Tuple

immutable ordered collection of elements

| Operation | Syntax |
| --- | --- |
| Create a tuple | `t = (1, 2, 3)` |
| Create a single-element tuple | `t = (1,)` |
| Access element by index | `t[index]` |
| Access a range (slicing) | `t[start:end:step]` |
| Length of tuple | `len(t)` |
| Check membership | `value in t` |
| Concatenate tuples | `t1 + t2` |
| Convert list to tuple | `tuple(l)` |
| Convert tuple to list | `list(t)` |
| Unpack tuple elements | `a, b, c = t` |


## Dictionary

- stores the value in key: value pairs
- Values in a dictionary can be of any data type and can be duplicated, whereas keys can't be repeated and must be immutable
- Internally uses hashing

| Operation | Syntax |
| --- | --- |
| Create a dictionary | `d = {"a": 1, "b": 2}` |
| Access value by key | `d["a"]` |
| Safely get a value from a dictionary without throwing an error | `d.get(“a”, None)` |
| Check if key exists | `"name" in data` |
| Add or update key-value pair | `d["c"] = 3` |
| Remove key-value pair | `del d["a"]` |
| Iterate over keys | `for key in d:`<br>`    print(key)` |
| Iterate over values | `for value in d.values():`<br>`    print(value)` |
| Iterate over key-value pairs | `for key, value in d.items():`<br>`    print(f"{key}: {value}")` |
| Remove and return value for a key | `d.pop("a")` |
| Remove and return last inserted item | `d.popitem()` |
| Clear all items | `d.clear()` |


## Set

- unordered collection
- do not contain duplicates
- Implemented using hash tables internally

| Operation | Syntax |
| --- | --- |
| Create a set | `s = {1, 2, 3}` |
| Add an element | `s.add(4)` |
| Remove an element (error if not found) | `s.remove(2)` |
| Remove an element (no error if not found) | `s.discard(2)` |
| Clear all elements | `s.clear()` |


## Array

| Operation | Syntax |
| --- | --- |
| Create an array | `a = [1, "Hello", [3.14, "world"]]` |
| Access element by index | `a[index]` |
| Update element by index | `a[index] = value` |
| Append an element | `a.append(value)` |
| Extend array with another iterable | `a.extend([4, 5, 6])` |
| Insert element at index | `a.insert(index, value)` |
| Remove first occurrence of value | `a.remove(value)` |
| Remove and return element at index | `a.pop(index)` |
| Slice elements | `a[start:end:step]` |
| Reverse array in place | `a.reverse()` |
| Get index of first occurrence | `a.index(value)` |


## List Comprehension

\[expression for item in iterable if condition\]

- expression → the value to store in the list
- item → variable representing each element in the iterable
- iterable → a sequence (like list, range, string, etc.)
- condition (optional) → filters which elements are included

```python
a = [1, 2, 3, 4, 5]
res = [val * 2 for val in a if val % 2 == 0]
print(res)
```


# OOP

```python
class User:
    def __init__(self):
        self.name = "John"
        self.age = 22

u = User()

# --- hasattr ---
print(hasattr(u, "name"))   # True
print(hasattr(u, "city"))   # False

# --- getattr ---
print(getattr(u, "name"))           # John
print(getattr(u, "city", "Unknown"))  # Unknown (default)

# --- setattr ---
setattr(u, "city", "Delhi")   # create new attribute
setattr(u, "age", 23)         # update existing attribute

print(u.city)  # Delhi
print(u.age)   # 23
```

```python
# Define a class
class Car:
    def __init__(self, brand, model):
        self.brand = brand       # attribute
        self.model = model       # attribute

    def drive(self):             # method
        print(f"{self.brand} {self.model} is driving")

# Create objects (instances) of the class
car1 = Car("Toyota", "Corolla")
car2 = Car("Honda", "Civic")

# Access attributes
print(car1.brand)   # Output: Toyota
print(car2.model)   # Output: Civic

# Call methods
car1.drive()        # Output: Toyota Corolla is driving
car2.drive()        # Output: Honda Civic is driving
```


## Encapsulation

```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance  # private attribute

    def deposit(self, amount):
        self.__balance += amount

    def get_balance(self):
        return self.__balance

acc = BankAccount(1000)
acc.deposit(500)
print(acc.get_balance())  # Output: 1500
# print(acc.__balance)    ❌ Error: cannot access private attribute
```


## Abstraction

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Rectangle(Shape):
    def __init__(self, w, h):
        self.width = w
        self.height = h

    def area(self):
        return self.width * self.height

r = Rectangle(5, 4)
print(r.area())  # Output: 20
```


## Inheritance

```python
class Vehicle:
    def __init__(self, brand):
        self.brand = brand
    
    def start(self):
        print(f"{self.brand} is starting")

class Car(Vehicle):  # Car inherits from Vehicle
    def __init__(self, brand):
        super().__init__(brand)  # Call parent constructor
    
    def drive(self):
        print(f"{self.brand} is driving")

c = Car("Toyota")
c.start()  # Output: Toyota is starting (inherited)
c.drive()  # Output: Toyota is driving
```


## Polymorphism

```python
class Bird:
    def speak(self):
        print("Some generic sound")

class Parrot(Bird):
    def speak(self):
        print("Squawk!")

class Crow(Bird):
    def speak(self):
        print("Caw!")

b1 = Parrot()
b2 = Crow()

b1.speak()  # Output: Squawk!
b2.speak()  # Output: Caw!
```


# Exception Handling

```python
try:
      # Code 
except SomeException:
      # Code 
else:
     # Code 
finally:
    # Code
```

```python
raise Exception("Error message")
```


# File Handling

```python
import os

# 1. Create and write to a file
file_name = "example.txt"
with open(file_name, "w") as f:  # 'w' mode creates the file
    f.write("Hello, this is the first line.\n")
    f.write("This is the second line.\n")

print("File created and written successfully!")

# 2. Read the entire file
with open(file_name, "r") as f:  # 'r' mode for reading
    content = f.read()
print("\nRead entire file:")
print(content)

# 3. Read file line by line
print("Read file line by line:")
with open(file_name, "r") as f:
    for line in f:
        print(line.strip())

# 4. Append to the file
with open(file_name, "a") as f:  # 'a' mode for append
    f.write("This line is appended.\n")
print("\nLine appended successfully!")
```
