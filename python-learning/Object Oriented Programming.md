```python
class Person:
	def __init__(self, name, age, salary):
		self.__salary = salary 
		self._age = age
		self.name = name

	def get_salary():
		return self.__salary

	def info():
		print(f'This is {self.name} who is {self._age} years old and earns {self.__salary}')

Person p = Person("xqc", 22, 10000000)
print(p.info())
print(p._age) # CAN ACCESS 
# print(p.__salary) ERROR - This line would indeed cause an AttributeError 
p._age = 23 # CAN MODIFY
p.__salary = 10000001 # This creates a *new* attribute, it doesn't modify the original __salary 
print(p.__salary) # This prints the *newly created* attribute 
p.info() # This prints the original __salary as accessed by the method
```

```python
from datetime import date

class Employee:
    def __init__(self, name, birth_date):
        self.__name = name
        self.__birth_date = birth_date

    @property
    def name(self):
        return self.__name.upper()

    @name.setter
    def name(self, value):
        self.__name = value.upper()

    @property
    def birth_date(self):
        return self.__birth_date

    @birth_date.setter
    def birth_date(self, value):
        self.__birth_date = date.fromisoformat(value)

e = Employee("s", "12-1-2002")
print(e.name) # S
print(e.birth_date) # 12-1-2002
e.name = "a"
print(e.name) # A
print(e.birth_date) # 12-1-2002
```
In this  version of `Employee`, you turn `.name` and `.birth_date` into properties using the `@property` decorator. Now each attribute has a getter and a setter method named after the attribute itself. Note that the setter of `.name` turns the input name into uppercase letters. Similarly, the setter of `.birth_date` automatically converts the input date into a `date` object for you.

```python
>>> from employee import Employee

>>> john = Employee("John", "2001-02-07")

>>> john.name
'JOHN'

>>> john.birth_date
datetime.date(2001, 2, 7)

>>> john.name = "John Doe"
>>> john.name
'JOHN DOE'
```