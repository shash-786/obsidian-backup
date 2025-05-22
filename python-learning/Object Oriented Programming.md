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

