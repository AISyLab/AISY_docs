# Simple Example

Breaking ASCAD Random Keys with AISY Framework.

## Code

```python
from neural_networks.neural_networks import *
from commons.sca_aisy_aes import Aisy


aisy = Aisy()
aisy.set_dataset("ascad-variable.h5")
aisy.set_database_name("database_ascad.sqlite")
aisy.set_aes_leakage_model(leakage_model="HW", byte=2)
aisy.set_number_of_profiling_traces(200000)
aisy.set_number_of_attack_traces(1000)
aisy.set_batch_size(400)
aisy.set_epochs(50)
aisy.set_neural_network(mlp)

aisy.run()
```
