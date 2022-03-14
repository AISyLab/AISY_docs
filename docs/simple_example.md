# Simple Example

The following example shows how simple it is to break [ASCAD Random Keys](https://github.com/ANSSI-FR/ASCAD/tree/master/ATMEGA_AES_v1/ATM_AES_v1_variable_key) 
dataset with AISY Framework. The example considers the dataset ascad_variable.h5 provided in the original
ASCAD Github repository. This dataset is a trimmed version of side-channel traces containing 1400 features
that represent the processing of third Sbox operation during first encryption round. Note that this operation
is masked with first-order Boolean masking scheme (see [Ref](https://eprint.iacr.org/2018/053.pdf)).


## Code

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
