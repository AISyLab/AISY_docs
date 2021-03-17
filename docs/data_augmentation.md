# Data Augmentation

AISY Framework also allows easy configuration of data augmentation techniques during model training.
Data augmentation is common machine learning technique and it is widely applied in side-channel analysis in order to improve model 
learnability. Basically, data augmentation allows small modifications in side-channel traces during training, which makes the model to reduce
overfitting and, as a consequence, to improve generalization.

### Creating Data Augmentation Methods

THe user may define data augmentation methods in any location. 
For better code organization, we recommend to write the methods in the file ```custom/custom_data_augmentation/data_augmentation.py``` 
Code below provides two examples of data augmentation methods: ```data_augmentation_shifts``` and ```data_augmentation_gaussian_noise```:

```python
import random
import numpy as np


def data_augmentation_shifts(data_set_samples, data_set_labels, batch_size, input_layer_shape):
    ns = len(data_set_samples[0])

    while True:

        x_train_shifted = np.zeros((batch_size, ns))
        rnd = random.randint(0, len(data_set_samples) - batch_size)
        x_mini_batch = data_set_samples[rnd:rnd + batch_size]

        for trace_index in range(batch_size):
            x_train_shifted[trace_index] = x_mini_batch[trace_index]
            shift = random.randint(-5, 5)
            if shift > 0:
                x_train_shifted[trace_index][0:ns - shift] = x_mini_batch[trace_index][shift:ns]
                x_train_shifted[trace_index][ns - shift:ns] = x_mini_batch[trace_index][0:shift]
            else:
                x_train_shifted[trace_index][0:abs(shift)] = x_mini_batch[trace_index][ns - abs(shift):ns]
                x_train_shifted[trace_index][abs(shift):ns] = x_mini_batch[trace_index][0:ns - abs(shift)]

        if len(input_layer_shape) == 3:
            x_train_shifted_reshaped = x_train_shifted.reshape((x_train_shifted.shape[0], x_train_shifted.shape[1], 1))
            yield x_train_shifted_reshaped, data_set_labels[rnd:rnd + batch_size]
        else:
            yield x_train_shifted, data_set_labels[rnd:rnd + batch_size]


def data_augmentation_gaussian_noise(data_set_samples, data_set_labels, batch_size, input_layer_shape):
    ns = len(data_set_samples[0])

    while True:

        x_train_augmented = np.zeros((batch_size, ns))
        rnd = random.randint(0, len(data_set_samples) - batch_size)
        x_mini_batch = data_set_samples[rnd:rnd + batch_size]

        noise = np.random.normal(0, 1, ns)

        for trace_index in range(batch_size):
            x_train_augmented[trace_index] = x_mini_batch[trace_index] + noise

        if len(input_layer_shape) == 3:
            x_train_augmented_reshaped = x_train_augmented.reshape((x_train_augmented.shape[0], x_train_augmented.shape[1], 1))
            yield x_train_augmented_reshaped, data_set_labels[rnd:rnd + batch_size]
        else:
            yield x_train_augmented, data_set_labels[rnd:rnd + batch_size]
```

As we can see, the method **must** be created with four input parameters:

- ```data_set_samples:```: array containing the profiling traces.
- ```data_set_labels:```: array containing the profiling *categorical* labels.
- ```batch_size:```: integer defining the batch size.
- ```input_layer_shape:```: shape of the input layer.

When data augmentation is considered, the model training is done with ```fit_generator``` method from Keras.

### Calling Data Augmentation in the Main Script

To pass data augmentation to the ```run``` method, user must enter two parameter in a list, as in the example below:

```python
import aisy_sca
from app import *
from custom.custom_models.neural_networks import *
from custom.custom_data_augmentation.data_augmentation import *

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
aisy.run(data_augmentation=[data_augmentation_gaussian_noise, 100])
```

The first parameter indicates the name of the data augmentation method, as defined in the ```custom/custom_data_augmentation/data_augmentation.py``` file.
The second parameter indicates the number of data augmentation iterarions in a single epochs. Each iteration will apply data augmentation 
method to a defined amount of profiling traces. In our examples above, each iteration randomly select a number of profiling traces equivalent 
to the batch size and applies the modifications. 