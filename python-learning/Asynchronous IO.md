To grasp the fundamentals of Async Python, you need to understand two essential keywords: `async` and `await`.

- `async`: The `async` keyword is used to define asynchronous functions. Functions defined with `async def` can be paused and resumed, allowing other tasks to run in the meantime.
- `await`: The `await` keyword is used within `async` functions to pause execution until a specific asynchronous task is complete. It's essential to remember that you can only use `await` within an `async` function.

## What happens when the program “awaits” functions?

When python _awaits_ a function, it will yield the CPU usage (_GIL_ Global Interpreter Lock) to any other async function waiting for it. As generally the _awaits_ happen in I/O operations it makes sense to allow other pieces of the program to use the CPU while waiting.

```python
import asyncio
import time

async def say_after(delay, what):
    print(f"[{time.strftime('%X')}] Starting '{what}' (delay: {delay}s)") # Added for clarity
    await asyncio.sleep(delay)
    print(f"[{time.strftime('%X')}] Finished '{what}'") # Added for clarity

async def main():
    print(f"[{time.strftime('%X')}] Program started.")

    # asyncio.gather runs coroutines concurrently.
    # It takes coroutine objects as arguments.
    # It effectively schedules them as independent tasks for the event loop.
    await asyncio.gather(
        say_after(5, 'hello'),
        say_after(5, 'world')
    # Ready task [say_after(5, 'hello'), say_after(5, 'world')]
    )

    print(f"[{time.strftime('%X')}] Program finished.")

asyncio.run(main())
'''
[06:14:38] Program started.
[06:14:38] Starting 'hello' (delay: 5s)
[06:14:38] Starting 'world' (delay: 5s)
[06:14:43] Finished 'hello'
[06:14:43] Finished 'world'
[06:14:43] Program finished.
'''

```