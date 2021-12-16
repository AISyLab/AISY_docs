# Saving to .npz files

AISY Framework also allows to save analysis results to .npz files. This is automatically done when the user sets the option ```save_to_npz``` in the the ```run()``` method from the main script. Below, we provide an example code of how to save results to a .npz file.

```python
from custom.custom_models.neural_networks import *
from aisy.sca_deep_learning_aes import AisyAes

aisy = AisyAes()
aisy.set_database_name("database_ascad.sqlite")
aisy.set_dataset("ascad-variable.h5")
aisy.set_aes_leakage_model(leakage_model="HW", byte=2)
aisy.set_batch_size(400)
aisy.set_epochs(20)
aisy.set_neural_network(mlp)
aisy.run(
    save_to_npz=["aes_attack"]
)
``` 

At the end of the analysis execution, the framework saves a .npz file, ```aes_attack.npz``` in ```resources/npz``` folder. The .npz file has the following attributes:

- ```metrics_profiling:``` list of dictionaries containing metrics associated to profiling set. Version 0.1 of the AISY framework saves accuracy and loss. 
- ```metrics_validation:``` list of dictionaries containing metrics associated to validation set. Validation set is considered when early stopping is set.
- ```metrics_attack:``` list of dictionaries containing metrics associated to attack set. When validation set is not used, these metrics starts with "val".
- ```guessing_entropy:``` vector contaning guessing entropy evolution values withe respect to the number of attack traces. 
- ```success_rate:``` vector contaning success rate evolution values withe respect to the number of attack traces.
- ```model_weights:``` structure containing model weights obtained with ```model.get_weights()``` method from Keras library.
- ```input_gradients_epoch:``` list of input gradient vectors. Each element of the list is a vector contaning the input gradients (the vector has the same dimension of input layer) for specific processed epoch.
- ```input_gradients_sum:``` vector containing the sum of all input gradients from all processed epochs.
- ```settings:``` dictionary containing the settings defined in the script.
- ```hyperparameters:``` some hyperparameters associated to the training process (does not include all neural network hyperparameters).
- ```leakage_model:``` dictionary containing the definitions of the leakage model.

The code below provides an example of how to read the saved .npz file:

```python
import numpy as np

npz_file = np.load("../resources/npz/aes_attack.npz", allow_pickle=True)

# obtaining guessing entropy
guessing_entropy = npz_file["guessing_entropy"]
print(guessing_entropy)

# obtaining success_rate
success_rate = npz_file["success_rate"]
print(success_rate)

# obtaining metric for profiling set (accuracy, loss)
metrics_profiling = npz_file["metrics_profiling"]
print(metrics_profiling)

# obtaining metric for attack set (val_accuracy, val_loss)
metrics_attack = npz_file["metrics_attack"]
print(metrics_attack)

# obtaining metric for validation set (val_accuracy, val_loss and custom metrics)
metrics_validation = npz_file["metrics_validation"]
print(metrics_validation)

# obtaining trained weights from model
model_weights = npz_file["model_weights"]
print(model_weights)

# obtaining input gradient for each trained epoch (when visualization feature is set)
input_gradients_epoch = npz_file["input_gradients_epoch"]
print(input_gradients_epoch)

# obtaining sum of input gradient for all trained epochs (when visualization feature is set)
input_gradients_sum = npz_file["input_gradients_sum"]
print(input_gradients_sum)

# obtaining the analysis settings
settings = npz_file["settings"]
print(settings)

# obtaining hyperparameters
hyperparameters = npz_file["hyperparameters"]
print(hyperparameters)

# obtaining leakage model
leakage_model = npz_file["leakage_model"]
print(leakage_model)
```