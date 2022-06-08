# Custom Callbacks

The user can easily add callbacks to the ```.fit``` Keras method. The process is done in two steps as explained below.

###### Step 1: Add Custom Callback

Add a custom callback class following the standard structure from the example below:

```python
class CustomCallback1(Callback):
    def __init__(self, dataset, settings, args):
        super().__init__()
        self.param1 = args["param1"]
        self.param2 = args["param2"]

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

These are the parameters set in the *\_\_init\_\_* method:

- ```dataset:``` dataset object.
- ```settings:``` dictionary containing analysis configuratioins.
- ```args:``` additional dictionary with custom arguments.

The ```dataset``` object contains the following arrays:

- ```dataset.x_profiling``` array with profiling traces
- ```dataset.x_attack``` array with attack traces
- ```dataset.x_validation``` array with validation traces
- ```dataset.y_profiling``` array with profiling categorical labels
- ```dataset.y_attack``` array with attack categorical labels
- ```dataset.y_validation``` array with validation categorical labels
- ```dataset.plaintext_profiling``` array with profiling plaintext
- ```dataset.plaintext_attack``` array with attack plaintext
- ```dataset.plaintext_validation``` array with validation plaintext
- ```dataset.ciphertext_profiling``` array with profiling ciphertext
- ```dataset.ciphertext_attack``` array with attack ciphertext
- ```dataset.ciphertext_validation``` array with validation ciphertext
- ```dataset.key_profiling``` array with profiling key
- ```dataset.key_attack``` array with attack key
- ```dataset.key_validation``` array with validation key

The ```settings:``` dictionary has the following structure:

```python
settings = {
    "key_rank_attack_traces": 1000,
    "key_rank_report_interval": 1,
    "key_rank_executions": 100,
    "leakage_model": {
        "leakage_model": "HW",
        "bit": 0,
        "byte": 0,
        "round": 1,
        "round_first": 1,  # for Hamming Distance
        "round_second": 1,  # for Hamming Distance
        "cipher": "AES128",
        "target_state": "Sbox",
        "target_state_first": "Sbox",  # for Hamming Distance
        "target_state_second": "Sbox",  # for Hamming Distance
        "direction": "Encryption",
        "attack_direction": "input"
    },
    "datasets_root_folder": "",
    "database_root_folder": "",
    "resources_root_folder": "",
    "database_name": "",
    "filename": "",
    "first_sample": 0,
    "number_of_samples": 700,
    "number_of_profiling_traces": 50000,
    "number_of_attack_traces": 10000,
    "key": "00112233445566778899AABBCCDDEEFF",
    "good_key": 22,
    "batch_size": 400,
    "epochs": 100,
    "classes": 256,
    "models": {
        "0": {
            "model_name": "my_model",
            "method_name": "mlp_best",
            "seed": 123456,
            "model": None,
            "index": 0
        } 
    },
    ...
}
```

The ```settings:``` dictionary also contain information related to hyperparameters search.

###### Step 2: Using Custom Callback

The example below show how to use the custom callback in a script:

```python
import aisy_sca
from app import *
from custom.custom_models.neural_networks import *

aisy = aisy_sca.Aisy()
aisy.set_resources_root_folder(resources_root_folder)
aisy.set_database_root_folder(databases_root_folder)
aisy.set_datasets_root_folder(datasets_root_folder)
aisy.set_database_name("database_ascad.sqlite")
aisy.set_dataset(datasets_dict["ascad-variable.h5"])
aisy.set_aes_leakage_model(leakage_model="HW", byte=2)
aisy.set_batch_size(400)
aisy.set_epochs(10)
aisy.set_neural_network(mlp)

param1 = [1, 2, 3]
param2 = "my_string"

custom_callbacks = [
    {
        "class": "custom.custom_callbacks.callbacks.CustomCallback1",
        "name": "CustomCallback1",
        "parameters": {
            "param1": [1, 2, 3],
            "param2": "my_string"
        }
    },
    {
        "class": "custom.custom_callbacks.callbacks.CustomCallback2",
        "name": "CustomCallback2",
        "parameters": {}
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

- ```class:``` path to custom callback class.
= ```name:``` name for the custom callback.
- ```parameters:``` list of parameters to be passed to the custom callback. If no parameters are passed, the item ```parameters``` in the 
dictionary of an element of ```custom_callbacks``` must be assigned with ```[]``` (empty list), as in the example below:
```python
custom_callbacks = [
    {
        "class": "custom.custom_callbacks.callbacks.CustomCallback2",
        "name": "CustomCallback2",
        "parameters": []
    }
]
```

