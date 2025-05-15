```python
count = {"a": 1, "b": 2, "c": 3}
for key in count:
	print(key) # a b c d

for key, val in count.items():
	print(key, val) # a 1 b 2 c 3
```

```python
#PRINTING VALUES in DICTIONARY
h = {}
h['word'] = 'garfield'
h['count'] = 42
s = 'I want %(count)d copies of %(word)s' % h
# 'I want 42 copies of garfield'

dict = {}
dict['a'] = 'alpha'
dict['g'] = 'gamma'
dict['o'] = 'omega'

print(dict) ## {'a': 'alpha', 'o': 'omega', 'g': 'gamma'}
## Get the .keys() list:
print(dict.keys())  ## dict_keys(['a', 'o', 'g'])

## Likewise, there's a .values() list of values
print(dict.values())  ## dict_values(['alpha', 'omega', 'gamma'])

## Common case -- loop over the keys in sorted order,
## accessing each key/value
for key in sorted(dict.keys()):
print(key, dict[key])
```

```python
# FILE HANDLING
f = open('foo.txt', 'rt', encoding='utf-8')
for line in f:   
	print(line, end='')    ## end='' so print does not add an end-of-line char
## since 'line' already includes the end-of-line.
f.close()

# with 'with' operator the file is closed on it's own
with open('foo.txt', r) as f:
	for line in f:
		print(line, end = ' ')
```
