Python strings are "immutable" which means they cannot be changed after they are created (Java strings also use this immutable style). Since strings can't be changed, we construct *new* strings as we go to represent computed values. So for example the expression ('hello' + 'there') takes in the 2 strings 'hello' and 'there' and builds a new string 'hellothere'.

```
x : str = r'x\nx'
print(str) 

# OUTPUT:  x\nx

x : str = 'x\nx'
print(str)

'''
OUTPUT:
x
x
'''
```
The `r''` shows the raw string literal meaning the escape characters will be shown as it is.

## String Methods

- s.lower(), s.upper() -- returns the lowercase or uppercase version of the string

- s.strip() -- returns a string with whitespace removed from the start and end

- s.isalpha()/s.isdigit()/s.isspace()... -- tests if all the string chars are in the various character classes

- s.startswith('other'), s.endswith('other') -- tests if the string starts or ends with the given other string

- s.find('other') -- searches for the given other string (not a regular expression) within s, and returns the first index where it begins or -1 if not found

- s.replace('old', 'new') -- returns a string where all occurrences of 'old' have been replaced by 'new'

- s.split('delim') -- returns a list of substrings separated by the given delimiter. The delimiter is not a regular expression, it's just text. 'aaa,bbb,ccc'.split(',') -> ['aaa', 'bbb', 'ccc']. As a convenient special case s.split() (with no arguments) splits on all whitespace chars.

- s.join(list) -- opposite of split(), joins the elements in the given list together using the string as the delimiter. e.g. '---'.join(['aaa', 'bbb', 'ccc']) -> aaa---bbb---ccc

## String Splicing Methods

"Hello"
- s[-1] is 'o' -- last char (1st from the end)
- s[-4] is 'e' -- 4th from the end
- s[:-3] is 'He' -- going up to but not including the last 3 chars.
- s[-3:] is 'llo' -- starting with the 3rd char from the end and extending to the end of the string.

It is a neat truism of slices that for any index n, `s[:n] + s[n:] == s`. This works even for n negative or out of bounds. Or put another way s[:n] and s[n:] always partition the string into two string parts, conserving all the characters. As we'll see in the list section later, slices work with lists too.



