It's literal meaning is to ‘decorate’ function objects. To ‘decorate’ here means if you want to perform some extra functionality that supplements the original function, but isn’t necessarily part of the core logic of that function.

**Types of decorators:**

1. Decorator without arguments (2 levels)
2. Decorator with arguments (3 levels)

```python
def simple_decorator(func):  
	def wrapper(name):  
		print("Before the function call")  
		# Call the original function and store the result  
		say = func(name)  
		print("After the function call")  
		# Return the result  
		return say  
return wrapper  
  
@simple_decorator  
def say_hello(name):  
	print("Function being excuted")  
	return f"Hello {name}!"  
  
# Call the decorated function  
print(say_hello('Shashank'))

'''
Before the function call  
Function being excuted  
After the function call  
Hello Shashank!
'''
```



```python
def repeat_decorator(times):  
	def decorator(func):  
		def wrapper(name):  
			for _ in range(times):  
				print("Before the function call")  
				result = func(name)  
				print("After the function call")  
			return result  
	return wrapper  
return decorator  
  
@repeat_decorator(times=3)  
def say_hello(name):  
	print("Function being excuted")  
	return f"Hello {name}!"  
  
# Call the decorated function  
print(say_hello('Shashank'))

'''
Before the function call  
Function being excuted  
After the function call  
Before the function call  
Function being excuted  
After the function call  
Before the function call  
Function being excuted  
After the function call  
Hello Shashank!
'''
```
