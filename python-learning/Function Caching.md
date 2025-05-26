Decorator to wrap a function with a memoizing callable that saves up to the _maxsize_ most recent calls. It can save time when an expensive or I/O bound function is periodically called with the same arguments.
```python
import time
from functools import lru_cache

@lru_cache(maxsize = None)
def func(n):
	time.sleep(5)
	return n * 10

init = time.time()
print(func(5))
print(func(15))
print(func(25))
print(f'first 3 functions took {time.time() - init} seconds')

init = time.time()
print(func(5))
print(func(15))
print(func(25))
print(f'Last 3 functions took {time.time() - init} seconds')

print(func.cache_info())

'''
**OUTPUT**

50
150
250
first 3 functions took 15.000447750091553 seconds

50
150
250
Last 3 functions took 2.384185791015625e-06 seconds

CacheInfo(hits=3, misses=3, maxsize=None, currsize=3)
'''
```