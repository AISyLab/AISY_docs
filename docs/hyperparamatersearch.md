# Hyperparameter Search

Hyperparameter search a fundamental procedure in the application of deep learning to profiled side-channel analysis.
AISY Framework provides easy and efficient functionalities for hyperparameters search for MLP and CNN models. 

## Random Search

Hyperparameter random search is the most common choice for finding efficient deep neural network architectures.
Due to the very large search space, it is very likely that we have no enough computation power (and time) to cover all the search space.
AISY Framework provides easy solution to run random search for profiled side-channel attacks.

```python
import aisy_sca
from app import *

aisy = aisy_sca.Aisy()
aisy.set_resources_root_folder(resources_root_folder)
aisy.set_database_root_folder(databases_root_folder)
aisy.set_datasets_root_folder(datasets_root_folder)
aisy.set_database_name("database_ascad.sqlite")
aisy.set_dataset(datasets_dict["ascad-variable.h5"])
aisy.set_aes_leakage_model(leakage_model="HW", byte=2)
aisy.set_batch_size(400)
aisy.set_epochs(10)

# for each hyper-parameter, specify the min, max and step or the possible options
random_search = {
    "neural_network": "cnn",
    "hyper_parameters_search": {
        'conv_layers': {"min": 1, "max": 2, "step": 1},
        'kernel_1': {"min": 2, "max": 8, "step": 1},
        'kernel_2': {"min": 2, "max": 8, "step": 1},
        'stride_1': {"min": 5, "max": 10, "step": 5},
        'stride_2': {"min": 5, "max": 10, "step": 5},
        'filters_1': {"min": 8, "max": 32, "step": 4},
        'filters_2': {"min": 8, "max": 32, "step": 4},
        'pooling_type_1': ["Average", "Max"],
        'pooling_type_2': ["Average", "Max"],
        'pooling_size_1': {"min": 1, "max": 1, "step": 1},
        'pooling_size_2': {"min": 1, "max": 1, "step": 1},
        'pooling_stride_1': {"min": 1, "max": 1, "step": 1},
        'pooling_stride_2': {"min": 1, "max": 1, "step": 1},
        'neurons': {"min": 100, "max": 1000, "step": 100},
        'layers': {"min": 2, "max": 3, "step": 1},
        'learning_rate': [0.001, 0.0009, 0.0008, 0.0007, 0.0006, 0.0005, 0.0004, 0.0003, 0.0002, 0.0001],
        'activation': ["relu", "selu", "elu", "tanh"],
        'epochs': {"min": 5, "max": 5, "step": 1},
        'batch_size': {"min": 100, "max": 1000, "step": 100},
        'optimizer': ["Adam", "RMSprop", "Adagrad", "Adadelta", "SGD"]
    },
    "metric": "guessing_entropy",
    "stop_condition": False,
    "stop_value": 1.0,
    "max_trials": 10,
    "train_after_search": True
}

aisy.run(
    random_search=random_search,
    key_rank_attack_traces=500
)
```

As specified in the above examples, we need to pass a dictionary with the following parameters:

- ```neural_network:``` type of neural network. It can be ```"mlp"``` or ```cnn```.
- ```hyper_parameters_search:``` dictionary containing the hyper-parameters to be search. Hyperparameters for MLP can be:
    - ```neurons```: dictionary containing the ranges for the number of neurons to be searched in dense layers. Example: ```'neurons': {"min": 10, "max": 1000, "step": 10}```,
    - ```layers```: dictionary containing the ranges for the number of dense layers. Example: ```'layers': {"min": 1, "max": 10, "step": 1}```,
    - ```learning_rate```: list containing the options for learning rate. Example: ```'learning_rate': [0.001, 0.0001]```,
    - ```activation```: list containing the options for activation functions. Example: ```'activation': ["relu", "selu"]```.
    
    Hyperparameters for CNN are the same as for MLP, expect for convolution and pooling layers:
    
    - ```conv_layers:``` dictionary containing the ranges for the number of convolution layers. Example: ```'conv_layers': {"min": 1, "max": 2, "step": 1}```,
    - ```kernel_1:``` dictionary containing the ranges for the kernel size in the ```conv_layer_1```. Example: ```'kernel_1': {"min": 2, "max": 8, "step": 1}```,
    - ```kernel_2:``` dictionary containing the ranges for the kernel size in the ```conv_layer_2```. Example: ```'kernel_2': {"min": 2, "max": 8, "step": 1}```,
    - ```stride_1:``` dictionary containing the ranges for the stride size in the ```conv_layer_1```. Example: ```'stride_1': {"min": 1, "max": 2, "step": 1}```,
    - ```stride_2:``` dictionary containing the ranges for the stride size in the ```conv_layer_2```. Example: ```'stride_2': {"min": 1, "max": 2, "step": 1}```,
    - ```filters_1:``` dictionary containing the ranges for the number of filters in the ```conv_layer_1```. Example: ```'filters_1': {"min": 8, "max": 16, "step": 8}```,
    - ```filters_2:``` dictionary containing the ranges for the number of filters in the ```conv_layer_2```. Example: ```'filters_2': {"min": 8, "max": 16, "step": 8}```,
    - ```pooling_type_1:``` list containing the options for pooling type in the ```pooling_layer_1```. Example: ```'pooling_type_1': ["Average", "Max"]```,
    - ```pooling_type_2:``` list containing the options for pooling type in the ```pooling_layer_2```. Example: ```'pooling_type_2': ["Average", "Max"]```,
    - ```pooling_size_1:``` dictionary containing the ranges for the pooling size in the ```pooling_layer_1```. Example: ```'pooling_size_1': {"min": 1, "max": 2, "step": 1}```,
    - ```pooling_size_2:``` dictionary containing the ranges for the pooling size in the ```pooling_layer_2```. Example: ```'pooling_size_2': {"min": 1, "max": 2, "step": 1}```,
    - ```pooling_stride_1:``` dictionary containing the ranges for the pooling stride in the ```pooling_layer_1```. Example: ```'pooling_stride_1': {"min": 1, "max": 2, "step": 1}```,
    - ```pooling_stride_2:``` dictionary containing the ranges for the pooling stride in the ```pooling_layer_2```. Example: ```'pooling_stride_2': {"min": 1, "max": 2, "step": 1}```,
    
Note that ```activation``` is set for convolution and dense layers. The structure of randomly generated CNNs follows the sequence with: 
```conv_layer_1 -> pool_layer_1 -> batch_normalization_layer_1 ->conv_layer_2 -> pool_layer_2 -> batch_normalization_layer_2 -> ...``` where 
a ```BatchNormalization``` layer is defined after the pooling layer.  

Besides those specific hyperparameters for MLPs and CNNs, the user can also search for number of epochs and batch size:
    
- ```epochs:``` dictionary containing the ranges for the epochs. Example: ```'epochs': {"min": 50, "max": 150, "step": 50}```.
- ```batch_size:``` dictionary containing the ranges for the batch size. Example: ```'batch_size': {"min": 100, "max": 4000, "step": 100}```.
- ```optimizer:``` list of options for optimizers (must be Strings).

## Grid Search

To perform a hyperparameter grid search, the main script needs to defined the ranges to be searched. 
The hyperparameter grid search in the AISY Framework supports two types of neural networks: MLP and CNN.
Below we provide examples.

MLP example: 

```python
from aisy.sca_aisy_aes import AisyAes

aisy = AisyAes()
aisy.set_dataset("ascad-variable.h5")
aisy.set_database_name("database_ascad.sqlite")
aisy.set_aes_leakage_model(leakage_model="HW", byte=2)
aisy.set_number_of_profiling_traces(100000)
aisy.set_number_of_attack_traces(2000)
aisy.set_batch_size(400)
aisy.set_epochs(50)

# for each hyper-parameter, specify the options in the grid search
grid_search = {
    "neural_network": "mlp",
    "hyper_parameters_search": {
        'neurons': [100, 200],
        'layers': [3, 4],
        'learning_rate': [0.001, 0.0001],
        'activation': ["relu", "selu"],
        'optimizer': ["Adam", "SGD"]
    },
    "metric": "guessing_entropy",
    "stop_condition": False,
    "stop_value": 1.0,
    "train_after_search": True
}

aisy.run(
    grid_search=grid_search
)
```

CNN example:

```python
import aisy_sca
from app import *

aisy = aisy_sca.Aisy()
aisy.set_resources_root_folder(resources_root_folder)
aisy.set_database_root_folder(databases_root_folder)
aisy.set_datasets_root_folder(datasets_root_folder)
aisy.set_database_name("database_ascad.sqlite")
aisy.set_dataset(datasets_dict["ascad-variable.h5"])
aisy.set_aes_leakage_model(leakage_model="HW", byte=2)
aisy.set_batch_size(400)
aisy.set_epochs(20)

# for each hyper-parameter, specify the options in the grid search
grid_search = {
    "neural_network": "cnn",
    "hyper_parameters_search": {
        'conv_layers': [1, 2],
        'kernel_1': [4, 8],
        'kernel_2': [2, 4],
        'stride_1': [1],
        'stride_2': [1],
        'filters_1': [8, 16],
        'filters_2': [8, 16],
        'pooling_type_1': ["Average", "Max"],
        'pooling_type_2': ["Average", "Max"],
        'pooling_size_1': [1, 2],
        'pooling_size_2': [1, 2],
        'pooling_stride_1': [1, 2],
        'pooling_stride_2': [1, 2],
        'neurons': [100, 200],
        'layers': [3, 4],
        'learning_rate': [0.001],
        'activation': ["selu", "elu"],
        'optimizer': ["Adam", "SGD"]
    },
    "metric": "guessing_entropy",
    "stop_condition": False,
    "stop_value": 1.0,
    "train_after_search": True
}

aisy.run(
    grid_search=grid_search,
    key_rank_attack_traces=500
)
```

As specified in the above examples, we need to pass a dictionary with the following parameters:

- ```neural_network:``` type of neural network. It can be ```"mlp"``` or ```cnn```.
- ```hyper_parameters_search:``` dictionary containing the hyper-parameters to be search. Hyperparameters for MLP can be:
    - ```neurons```: list containing the options for the number of neurons to be searched in dense layers. Example: ```'neurons': [100, 200]```,
    - ```layers```: list containing the options for the number of dense layers. Example: ```'layers': [3, 4]```,
    - ```learning_rate```: list containing the options for learning rate. Example: ```'learning_rate': [0.001, 0.0001]```,
    - ```activation```: list containing the options for activation functions. Example: ```'activation': ["relu", "selu"]```.
    
    Hyperparameters for CNN are the same as for MLP, expect for convolution and pooling layers:
    
    - ```conv_layers:``` list containing the options for the number of convolution layers. Example: ```'conv_layers': [1, 2]```,
    - ```kernel_1:``` list containing the options for the kernel size in the ```conv_layer_1```. Example: ```'kernel_1': [4, 8]```,
    - ```kernel_2:``` list containing the options for the kernel size in the ```conv_layer_2```. Example: ```'kernel_2': [2, 4]```,
    - ```stride_1:``` list containing the options for the stride size in the ```conv_layer_1```. Example: ```'stride_1': [1, 2]```,
    - ```stride_2:``` list containing the options for the stride size in the ```conv_layer_2```. Example: ```'stride_2': [1, 2]```,
    - ```filters_1:``` list containing the options for the number of filters in the ```conv_layer_1```. Example: ```'filters_1': [8, 16]```,
    - ```filters_2:``` list containing the options for the number of filters in the ```conv_layer_2```. Example: ```'filters_2': [8, 16]```,
    - ```pooling_type_1:``` list containing the options for pooling type in the ```pooling_layer_1```. Example: ```'pooling_type_1': ["Average", "Max"]```,
    - ```pooling_type_2:``` list containing the options for pooling type in the ```pooling_layer_2```. Example: ```'pooling_type_2': ["Average", "Max"]```,
    - ```pooling_size_1:``` list containing the options for the pooling size in the ```pooling_layer_1```. Example: ```'pooling_size_1': [1, 2]```,
    - ```pooling_size_2:``` list containing the options for the pooling size in the ```pooling_layer_2```. Example: ```'pooling_size_2': [1, 2]```,
    - ```pooling_stride_1:``` list containing the options for the pooling stride in the ```pooling_layer_1```. Example: ```'pooling_stride_1': [1, 2]```,
    - ```pooling_stride_2:``` list containing the options for the pooling stride in the ```pooling_layer_2```. Example: ```'pooling_stride_2': [1, 2]```,
    
Note that ```activation``` is set for convolution and dense layers. The structure of randomly generated CNNs follows the sequence with: 
```conv_layer_1 -> pool_layer_1 -> batch_normalization_layer_1 ->conv_layer_2 -> pool_layer_2 -> batch_normalization_layer_2 -> ...``` where 
a ```BatchNormalization``` layer is defined after the pooling layer.  

Besides those specific hyperparameters for MLPs and CNNs, the user can also search for number of epochs and batch size:
    
- ```epochs:``` list containing the options for the epochs. Example: ```'epochs': [50, 100, 150]```.
- ```batch_size:``` list containing the option for the batch size. Example: ```'batch_size': [100, 200]```.
- ```optimizer:``` list of options for optimizers (must be Strings).