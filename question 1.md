### Question 1
#### By default are django signals executed synchronously or asynchronously? Please support your answer with a code snippet that conclusively proves your stance. The code does not need to be elegant and production ready, we just need to understand your logic.

By default Django signals are executed synchronously. This means the signal handler function runs immediately after the signal is sent, in the same request-response cycle and doing all work withing this time frame.
Here is a code example showing synchronous execution —
``` Python
from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import MyModel

@receiver(post_save, sender=MyModel)
def my_handler(sender,   
 instance, **kwargs):
    # This handler will be executed immediately after a MyModel instance is saved
    print("Signal handler executed synchronously")
```
This means that in this example, my_handler will be called right after a MyModel object has been saved. No delay, no asynchronous processing nothing;
