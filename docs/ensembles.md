# Ensembles

When a specific deep learning model is not able to provide strong enough results in a given task, the *ensemble* of multiple models may be 
able to boost the performance of a profiled side-channel attack scenario.

The ensemble feature provided in the AISY framework combines the prediction (output class probabilities) of multiple deep learning models. 
To use the feature, the user must define hyperparemeter search (random search or grid search). This way, the framework return the ensemble 
prediction of all searched models as well as the ensemble results for N best models.

For more information about ensembles in profiled SCA, please refer to [this paper](https://tches.iacr.org/index.php/TCHES/article/view/8686/8245). 

## Validation set for ensembles

As explained above, when ensemble are considered in the AISY framework, the user set what is the minimal number of models to ensemble in 
order to produce metric results based on the ensemble of those models. This way, the framework will need a *validation set* (which is 
different from the *attack set*) to validate all search models and select the N best ones. 

Therefore, the amount of traces set as *attack traces* will be split into two halves, where the first half will be used as the validation 
set and the second half will be used as the attack set.    

## Example Code

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
    "neural_network": "mlp",
    "hyper_parameters_search": {
        'neurons': [100, 200],
        'layers': [2, 3],
        'learning_rate': [0.001, 0.0001],
        'activation': ["relu", "selu"]
    },
    "metric": "guessing_entropy",
    "stop_condition": False,
    "stop_value": 1.0,
    "train_after_search": True
}

aisy.run(
    key_rank_attack_traces=500,
    grid_search=grid_search,
    ensemble=[10]
)
```




