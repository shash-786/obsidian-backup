In Python, the Method Resolution Order (MRO) defines the order in which base classes are searched when looking for a method or attribute in a class hierarchy.
```python
class Employee:
    def __init__(self, name):
        self.name = name
    def show(self):
        return f'Hey my name is {self.name}'

class Profession:
    def __init__(self, prof):
        self.prof = prof
    def show(self):
        print(f'Hey my profession is {self.prof}')
        
class Person(Employee, Profession):
    def __init__(self, name, prof):
        self.name = name
        self.prof = prof

p = Person("A", "Banker")
print(p.show())
print(p.__mro__)
'''
Hey my name is A
(<class '__main__.Person'>, <class '__main__.Employee'>,<class'__main__.Profession'>, <class 'object'>)
'''
```

