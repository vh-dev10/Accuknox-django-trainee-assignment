## Topic: Custom Classes in Python
### Description: You are tasked with creating a Rectangle class with the following requirements:
####  1.An instance of the Rectangle class requires length:int and width:int to be initialized.
####  2.We can iterate over an instance of the Rectangle class 
####  3.When an instance of the Rectangle class is iterated over, we first get its length in the format: {'length': <VALUE_OF_LENGTH>} followed by the width {width: <VALUE_OF_WIDTH>}


``` Python
class Rectangle:
    def __init__(self, length, width):
        self.length = length
        self.width = width

    def __iter__(self):
        self.index = 0
        return self

    def __next__(self):
        if self.index == 0:
            self.index += 1
            return {'length': self.length}
        elif self.index == 1:
            self.index += 1
            return {'width': self.width}
        else:
            raise StopIteration

# Example usage
rect = Rectangle(5, 3)

for item in rect:
    print(item)
```
### Example output
``` Output
{'length': 5}
{'width': 3}
```
