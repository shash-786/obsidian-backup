Direct assignment of a list to a variable will cause both of the variables to be pointing to the same list.

```python
colors: list = ['red', 'blue', 'green']
new_colors: list = colors

new_colors[1] = 'yellow'
print(colors) # red yellow green
colors[1] = 'blue'

# SEPARATE COPY 
new_colors: list = colors.copy()
new_colors[1] = 'yellow'
print(colors) # red blue green
```
