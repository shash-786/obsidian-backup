```python
foods = list()
while True:
 food = input("What food do you like?: ")
 if food == "quit":
	 break
 foods.append(food)
```

Python's walrus operator := *allows you to assign a value to a variable within an expression, combining assignment and use in a single step*

```python
foods = list()
while (food := input("What food do you like?: ")) != "quit":
    foods.append(food)
```