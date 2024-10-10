### Question 3:
#### By default do django signals run in the same database transaction as the caller? Please support your answer with a code snippet that conclusively proves your stance. The code does not need to be elegant and production ready, we just need to understand your logic.

Yes, of course. Django signals are run by default in the same database transaction as the caller. This is to mean that all of the Django signal handlers-cached handlers like those connected to post_save, pre_save etc.-- will be performed within the context of the same transaction that has been initiated by the operation causing the signal-a model save, for instance. This can result in somewhat interesting behavior if a signal handler changes the database and an exception gets raised either within the handler or by the caller. The whole transaction rolls back in that case.
<br><br>
To prove this, I have used here a code snippet which demonstrates this behavior through the utilization of the transaction.atomic() control offered by Django for database transactions:
``` Python
from django.db import models, transaction
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.core.exceptions import ValidationError

# Example model
class MyModel(models.Model):
    name = models.CharField(max_length=100)

# Signal receiver function
@receiver(post_save, sender=MyModel)
def my_signal_handler(sender, instance, **kwargs):
    print("Signal handler is executing")
    # Intentionally raise an exception to simulate failure in the signal
    raise ValidationError("Error in signal handler!")

# Simulate saving an instance of the model
def save_model_instance():
    try:
        with transaction.atomic():  # Wrap the save in an atomic transaction
            instance = MyModel(name="Test")
            instance.save()
            print("Model instance saved")
    except ValidationError:
        print("Transaction rolled back due to error in signal handler")

# Run the save function
if __name__ == "__main__":
    save_model_instance()
```
Explanation:<br>
1. We define a simple MyModel that will have a name field.<br>
2. We define a signal receiver my_signal_handler, tied to the post_save signal of MyModel. In this handler we raise ValidationError deliberately to simulate failure in the signal handler.<br>
3. In save_model_instance, we wrap the operation of saving the model in a transaction.atomic() block, so that either everything-the signal handler operations included-is committed or nothing at all if there was an exception.<br>
4. If this exception is raised in the signal handler, then the whole transaction will roll back, and that MyModel instance will not be saved to the database.<br>
Conclusion:<br>
In particular, the fact that the whole transaction is rolled back because of an error in the signal handler confirms that Django signals run within the same database transaction as the caller. Since the save operation isn't committed if the signal handler fails, both prove to be part of the same transaction.
