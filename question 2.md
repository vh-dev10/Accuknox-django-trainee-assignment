### Question 2
####  Do django signals run in the same thread as the caller? Please support your answer with a code snippet that conclusively proves your stance. The code does not need to be elegant and production ready, we just need to understand your logic.

Now, by default Django signals run in the same thread as the caller. Which means that at the time of raising a signal, the connected signal handlers are executed in synchrony in the same thread as the signal raising code.
Here is a code snippet to demonstrate this:
``` Python
import threading
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.db import models

# Example model
class MyModel(models.Model):
    name = models.CharField(max_length=100)

# Signal receiver function
@receiver(post_save, sender=MyModel)
def my_signal_handler(sender, instance, **kwargs):
    print(f"Signal handler thread: {threading.current_thread().name}")

# Simulate saving an instance of the model
def save_model_instance():
    print(f"Caller thread: {threading.current_thread().name}")
    instance = MyModel(name="Test")
    instance.save()

# Run the save function
if __name__ == "__main__":
    save_model_instance()

```
Explanation:<br>
1 We define a simple class named MyModel containing a name attribute.<br>
2. Create a signal receiver called my_signal_handler that's tied to the post_save signal of MyModel<br>
3. In the my_signal_handler function, we print the current thread name so that the calling thread might be matched.<br>
4. We print the current thread name in the save_model_instance method just before saving an instance of MyModel.<br>
5. Running the above program will ensure that the output reflects the fact that both caller and the signal handler run in the same thread.<br>
