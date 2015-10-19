<!-- 
.. title: Asyncio First Look
.. slug: asyncio-first-look
.. date: 2015-10-19 16:08:39 UTC+02:00
.. tags: Networking
.. category: Python
.. link: 
.. description: 
.. type: text
-->

## Definitions

* A Good explanation for [_Threading_](http://stackoverflow.com/a/5201906/2536357)
* "A piece of code is _thread-safe_ if it functions correctly during simultaneous execution by multiple threads." (Wikipedia). For a discussion about how thread-safety is strictly connected to scenarios in which specific objectives are pursued(accessing/modifying nutable data-structures for example), see [here](http://blogs.msdn.com/b/ericlippert/archive/2009/10/19/what-is-this-thing-you-call-thread-safe.aspx). The author sustains that thread-safety is strictly related to the tasks the software performs. It can be, or not, thread-safe related to what it is performing, and not just generically thread-safe.  

### Generators coroutines and async coroutines
_from [Pydocs](https://docs.python.org/3/library/asyncio-task.html)_:

"Coroutines used with __asyncio__ may be implemented using the __async def__ statement, or by using __generators__."

"Generator-based coroutines use the __yield from__ syntax introduced in [PEP 380](http://www.python.org/dev/peps/pep-0380), instead of the original yield syntax."

#### Disambiguation
* The __function__ that defines a coroutine (a function definition using async def or decorated with @asyncio.coroutine). If disambiguation is needed we will call this a coroutine function (iscoroutinefunction() returns True).
    
* The __object__ obtained by calling a coroutine function. This object represents a computation or an I/O operation (usually a combination) that will complete eventually. If disambiguation is needed we will call it a coroutine object (iscoroutine() returns True).

#### Basics
"Calling a coroutine does not start its code running. Calling a coroutine does not start its code running – the coroutine object returned by the call doesn’t do anything until you schedule its execution."

Things a coroutine can do:

_result = await future_ or _result = yield from future_ – suspends the coroutine until the future is done, then returns the future’s result, or raises an exception, which will be propagated. (If the future is cancelled, it will raise a CancelledError exception.) Note that tasks are futures, and everything said about futures also applies to tasks.

_result = await coroutine_ or _result = yield from coroutine_ – wait for another coroutine to produce a result (or raise an exception, which will be propagated). The coroutine expression must be a call to another coroutine.

_return expression_ – produce a result to the coroutine that is waiting for this one using await or yield from.

_raise exception_ – raise an exception in the coroutine that is waiting for this one using await or yield from.

#### Example: Two async coroutines
```
import asyncio

async def compute(x, y):
    print("Compute %s + %s ..." % (x, y))
    await asyncio.sleep(1.0)
    return x + y

async def print_sum(x, y):
    result = await compute(x, y)
    print("%s + %s = %s" % (x, y, result))

loop = asyncio.get_event_loop()
loop.run_until_complete(print_sum(1, 2))
loop.close()
```
* `await compute` makes `print_sum` to wait for the coroutine to complete.

* The loop is created by the `BaseEventLoop.run_until_complete()` method when it gets a <u>coroutine object instead of a task.</u>

* see [BaseEventLoop](https://docs.python.org/3/library/asyncio-eventloop.html)

### Future
Construct used for synchronization in concurrent operations. "... describe an object that acts as a proxy for a result that is initially unknown, usually because the computation of its value is yet incomplete."(Wikipedia)

Return a result or an exception when it's done. If invoked can raise different exceptions defining its actual state (None, InvalidStateError, CancelledError). Can have a callback to be run when it's done. "`result()` and `exception()` methods do not take a timeout argument and raise an exception when the future isn’t done yet."

### Task
"A task is responsible for executing a coroutine object in an event loop. If the wrapped coroutine yields from a future, the task suspends the execution of the wrapped coroutine and waits for the completition of the future. When the future is done, the execution of the wrapped coroutine restarts with the result or the exception of the future."

"Event loops use cooperative scheduling: an event loop only runs one task at a time. Other tasks may run in parallel if other event loops are running in different threads. While a task waits for the completion of a future, the event loop executes a new task."

<u>Important:</u> "Don’t directly create Task instances: use the `ensure_future()` function or the `BaseEventLoop.create_task()` method."
