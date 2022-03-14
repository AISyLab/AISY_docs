# Creating Neural Network Models

The AISY Framework is a (Keras/TensorFlow) deep learning-based framework for profiled side-channel analysis.
One of the main advantages of using our framework is the easy definition and call of neural network models.

### Keras class support

The framework support Keras models written using either ```Sequential``` or ```Model``` classes.

### Custom Neural Networks File

For better code organization, we recommend writing all your (keras) neural network models in the
```custom/custom_models/neural_network.py``` file. The standard code is provided below:

```python
from tensorflow.keras.optimizers import *
from tensorflow.keras.layers import *
from tensorflow.keras.models import *


def cnn(classes, number_of_samples):
    model = Sequential()
    model.add(Conv1D(filters=8, kernel_size=20, strides=1, activation='relu', padding='valid', input_shape=(number_of_samples, 1)))
    model.add(Flatten())
    model.add(Dense(128, activation='relu', kernel_initializer='random_uniform', bias_initializer='zeros'))
    model.add(Dropout(0.5))
    model.add(Dense(128, activation='relu', kernel_initializer='random_uniform', bias_initializer='zeros'))
    model.add(Dense(classes, activation='softmax'))
    model.summary()
    optimizer = Adam(lr=0.001)
    model.compile(loss='categorical_crossentropy', optimizer=optimizer, metrics=['accuracy'])
    return model


def mlp(classes, number_of_samples):
    model = Sequential()
    model.add(Dense(200, activation='selu', input_shape=(number_of_samples,)))
    model.add(Dense(200, activation='selu'))
    model.add(Dense(200, activation='selu'))
    model.add(Dense(200, activation='selu'))
    model.add(Dense(classes, activation='softmax'))
    model.summary()
    optimizer = Adam(lr=0.001)
    model.compile(loss='categorical_crossentropy', optimizer=optimizer, metrics=['accuracy'])
    return model
``` 

Note the neural network functions must be defined with two basic method parameters:

- ```classes:``` number of classification classes. This is important in order to define the length of the output layer.
- ```number_of_samples:``` number of samples in each trace. This is important in order to define the length of the input layer.

Following, the defined neural network models can be easily called from the main script, as in the example below:

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
aisy.set_epochs(20)
aisy.set_neural_network(mlp)
aisy.run()

```

As shown in the above code, the neural network model is defined by setting the method:

```python
aisy.set_neural_network(mlp)
```

Note, however, that when the neural network model is set as above, the parameters ```classes``` and ```number_of_samples``` don't need to be 
passed. They will be automatically set according to the defined dataset and leakage model settings.

### Passing Custom Parameters to the Neural Network Definition

The user is also able to define and set a neural network model with special parameters, as in the example below, where we can create models 
with flexible hyper-parameters:

```python
import aisy_sca
from app import *
from custom.custom_models.neural_networks import *


def mlp(classes, number_of_samples, neuron, layer, activation, learning_rate):
    model = Sequential(name="my_mlp")
    for l_i in range(layer):
        if l_i == 0:
            model.add(Dense(neuron, activation=activation, input_shape=(number_of_samples,)))
        else:
            model.add(Dense(neuron, activation=activation))
    model.add(Dense(classes, activation=None))
    model.add(Activation(activation="softmax"))
    model.summary()
    optimizer = Adam(lr=learning_rate)
    model.compile(loss='categorical_crossentropy', optimizer=optimizer, metrics=['accuracy'])
    return model


aisy = aisy_sca.Aisy()
aisy.set_resources_root_folder(resources_root_folder)
aisy.set_database_root_folder(databases_root_folder)
aisy.set_datasets_root_folder(datasets_root_folder)
aisy.set_database_name("database_ascad.sqlite")
aisy.set_dataset(datasets_dict["ascad-variable.h5"])
aisy.set_aes_leakage_model(leakage_model="HW", byte=2)
aisy.set_batch_size(400)
aisy.set_epochs(40)
my_mlp = mlp(9, 1400, 200, 6, "relu", 0.001)
aisy.set_neural_network(my_mlp)
aisy.run()
```

Note that in this case, **it is necessary to pass all function parameters, including ```classes``` and ```number_of_samples```**.



