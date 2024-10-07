### Django Signals

---

#### Question 1: By default, are Django signals executed synchronously or asynchronously?

Django signals are executed **synchronously** by default. Here's a simple code snippet to demonstrate this:

```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from time import sleep

@receiver(post_save)
def simple_signal(sender, **kwargs):
    print("Signal started")
    sleep(3)  # Sleep for 3 seconds
    print("Signal finished")

print("Saving model")
post_save.send(sender=None)
print("Save finished")
```

**Expected Output:**

```
Saving model
Signal started
(delayed for 3 seconds)
Signal finished
Save finished
```

Here, you can see the `post_save` signal blocks the execution of "Save finished" until the signal has completed, proving it runs synchronously.

---

#### Question 2: Do Django signals run in the same thread as the caller?

Yes, Django signals run in the **same thread** as the caller by default. Here's a simple demonstration:

```python
import threading
from django.db.models.signals import post_save
from django.dispatch import receiver

@receiver(post_save)
def thread_check_signal(sender, **kwargs):
    print(f"Signal running in thread: {threading.current_thread().name}")

print(f"Caller running in thread: {threading.current_thread().name}")
post_save.send(sender=None)
```

**Expected Output:**

```
Caller running in thread: MainThread
Signal running in thread: MainThread
```

This shows both the caller and the signal handler are running in the same thread (`MainThread`), confirming that signals run in the same thread by default.

---

#### Question 3: Do Django signals run in the same database transaction as the caller?

Yes, Django signals run in the **same database transaction** by default. Here’s a basic example that shows this:

```python
from django.db import transaction
from django.db.models.signals import post_save
from django.dispatch import receiver

@receiver(post_save)
def transaction_check_signal(sender, **kwargs):
    if transaction.get_connection().in_atomic_block:
        print("Signal is in the same transaction")
    else:
        print("Signal is outside the transaction")

with transaction.atomic():
    print("Inside transaction block")
    post_save.send(sender=None)
```

**Expected Output:**

```
Inside transaction block
Signal is in the same transaction
```

This proves that the signal is running within the same transaction block as the caller.

---

### Custom Classes in Python

#### Task: Create a `Rectangle` class with the requirements mentioned.

Here’s a simple example of a `Rectangle` class that you can iterate over to get the length and width:

```python
class Rectangle:
    def __init__(self, length: int, width: int):
        self.length = length
        self.width = width

    # Allow iteration over the object to return length and width
    def __iter__(self):
        yield {'length': self.length}
        yield {'width': self.width}


rect = Rectangle(5, 10)

# Iterating over the rectangle object
for dimension in rect:
    print(dimension)
```

**Expected Output:**

```
{'length': 5}
{'width': 10}
```

This basic class initializes with `length` and `width`, and when you iterate over it, it returns each dimension as a dictionary.

---
