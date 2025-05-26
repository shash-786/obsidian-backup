```python
import threading
import time

def crawl(link, delay=3):
	#print(f"crawl started for {link}")
	time.sleep(delay) # Blocking I/O (simulating a network request)	
	# print(f"crawl ended for {link}")

links = [ "https://python.org", "https://docs.python.org", "https://peps.python.org",]
# Start threads for each link

threads = []
for link in links:
	# Using `args` to pass positional arguments and `kwargs` for keyword arguments
	# t = threading.Thread(target=crawl, args=(link,), kwargs={"delay": 2})
	t = threading.Thread(target=crawl, args=(link,2))	
	threads.append(t)

# Start each thread
t1 = time.perf_counter()
for t in threads:
	t.start()
	
# Wait for all threads to finish
for t in threads:
	t.join()
t2 = time.perf_counter()

print(f'threads took {t2 - t1} seconds to excute and finish')

t1 = time.perf_counter()
for link in links:
	crawl(link, 2)
t2 = time.perf_counter()
print(f'normal execution took {t2 - t1} seconds to excute and finish')

'''
OUTPUT:
threads took 2.00094297600117 seconds to excute and finish
normal execution took 6.00107571099943 seconds to excute and finish
'''
```