## Lambdas

A Python lambda function is a small, anonymous function that you can define in a single line. It is useful when you need a short function for a quick operation without defining it using `def`.

`lambda arguments: expression`

```python
square = lambda x: x * x
print(square(5))  # Output: 25
```

## Map

```python
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x * x, numbers))
print(squared)  # Output: [1, 4, 9, 16, 25]
```

## Filter

```python
numbers = [1, 2, 3, 4, 5, 6]
even_numbers = list(filter(lambda x: x % 2 == 0, numbers))
print(even_numbers)  # Output: [2, 4, 6]
```

## Reduce

```python
numbers = [1, 2, 3, 4, 5, 6]
total_numbers = reduce(lambda x, y : x + y, numbers)
print(total_numbers) # 21
```

