# Custom Callbacks

The user can easily add callbacks to the ```.fit``` Keras method. The process is done in two steps as explained below.

###### Step 1: Add Custom Callback

Add a custom callback class following the standard structure from the example below:

```python
class CustomCallback1(Callback):
    def __init__(self,
                 x_profiling, y_profiling, plaintexts_profiling,
                 x_validation, y_validation, plaintexts_validation,
                 x_attack, y_attack, plaintexts_attack,
                 param, leakage_model, key_rank_executions, key_rank_report_interval, key_rank_attack_traces,
                 *args):
        my_args = args[0]  # this line is mandatory
        self.param1 = my_args[0]
        self.param2 = my_args[1]

    def on_epoch_end(self, epoch, logs=None):
        print("Processing epoch {}".format(epoch))

    def on_train_end(self, logs=None):
        pass

    def get_param1(self):
        return self.param1

    def get_param2(self):
        return self.param2
``` 

The custom callback can be added to the main script or, as recommened for better code organization, to the file 
```custom/custom_callbacks/callbacks.py```.

In the example above, the ```CustomCallback1``` class expects two parameters, ```param1``` and ```param2```. In Step 2, we explain how to 
pass these two parameters in the custom callback.

Note that the custom callback **must** be provided with the following variables in the ```__init__``` function:

- ```x_profiling:``` profiling trace set.
- ```y_profiling:``` categorical profiling labels.
- ```plaintexts_profiling:``` profiling plaintext.
- ```x_validation:``` validation trace set.
- ```y_validation:``` categorical validation labels.
- ```plaintexts_validation:``` validation plaintexts.
- ```x_attack:``` attack trace set.
- ```y_attack:``` categorical attack labels.
- ```plaintexts_attack:``` attack plaintexts.
- ```param:``` target parameteres. It is a dictionary as in the example below:
```python
param = {
            "file": "ascad-variable.h5",
            "key": "00112233445566778899AABBCCDDEEFF",
            "key_offset": 16,
            "input_offset": 0,
            "data_length": 50,
            "first_sample": 0,
            "number_of_samples": 1400,
            "n_profiling": 100000,
            "n_attack": 1000,
            "classes": 9,
            "good_key": 34,
            "epochs": 50,
            "mini_batch": 400
        }
```
- ```leakage_model:``` leakage model parameters.: 
- ```key_rank_executions:``` number of key rank executions in the guessing entropy calculatioin.
- ```key_rank_report_interval:``` report trace interval in metric calculations (e.g., guessing entropy, success rate, etc.)
- ```key_rank_attack_traces:``` number of randomly selected traces from attack trace set to be used in each key rank calculation.
- ```*args:``` list of additional and optional parameters to be passed with the custom callback and to be treated inside the custom 
callback. 

###### Step 2: Using Custom Callback

The example below show how to use the custom callback in a script:

```python
from neural_networks.neural_networks import *
from commons.sca_aisy_aes import Aisy
from custom.custom_callbacks.callbacks import *

aisy = Aisy()
aisy.set_dataset("ascad-variable.h5")
aisy.set_database_name("database_ascad.sqlite")
aisy.set_aes_leakage_model(leakage_model="HW", byte=2)
aisy.set_number_of_profiling_traces(10000)
aisy.set_number_of_attack_traces(1000)
aisy.set_batch_size(400)
aisy.set_epochs(50)
aisy.set_neural_network(mlp)

param1 = [1, 2, 3]
param2 = "my_string"

custom_callbacks = [
    {
        "class": CustomCallback1,
        "name": "CustomCallback1",
        "parameters": [param1, param2]
    },
    {
        "class": CustomCallback2,
        "name": "CustomCallback2",
        "parameters": []
    }
]

aisy.run(
    callbacks=custom_callbacks
)

custom_callbacks = aisy.get_custom_callbacks()

custom_callback1 = custom_callbacks["CustomCallback1"]
print(custom_callback1.get_param1())
print(custom_callback1.get_param2())

custom_callback2 = custom_callbacks["CustomCallback2"]
```

Note how the structure for custom callback is declared (variable ```custom_callbacks```). For each custom callback, a dictionary with the 
following parameters must be defined:

- ```class:``` custom callback class.
- ```name:``` name of the custom callback class.
- ```parameters:``` list of parameters to be passed to the custom callback. If no parameters are passed, the item ```parameters``` in the 
dictionary of an element of ```custom_callbacks``` must be assigned with ```[]``` (empty list), as in the example below:
```python
custom_callbacks = [
    {
        "class": CustomCallback2,
        "name": "CustomCallback2",
        "parameters": []
    }
]
```

