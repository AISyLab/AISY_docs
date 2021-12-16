# Custom datasets

## Basic h5 dataset format

The main **Aisy** class framework only supports datasets in .h5 format for the current version. The dataset in .h5 format must be structured
according to the format createb by ANSSI in ASCAD database. The .h5 file must contain traces and metadata fields, as follows:

- Profiling traces: [*Profiling_traces/traces*]: 2D array containing profiling traces. The format of the array must be *nb_traces x
  nb_samples*. Here, *samples* mean the amount of data points (or features) in a trace.
- Attack traces: [*Attack_traces/traces*]: this field should contain a 2D array with attack traces. The format of the array must be *
  nb_traces x nb_samples*.
- Profiling Metadata: [*Profiling_traces/metadata*]: metadata contains all 2D arrays for *keys*, *plaintext* and *ciphertext*. Note that,
  depending on the leakage model plaintext or ciphertext are not necessary. If metadata contains *mask* values, they will automatically
  discarded by the main **Aisy** class when creating labels.
- Attack Metadata: [*Attack_traces/metadata*]: similarly, this field contains metadata for attack set.

Below, user can find an example of how to create a .h5 dataset that can be interpreted by the main **Aisy** class:

```python
import numpy as np
import h5py

""" create dummy profiling traces array with 10000 traces, each one containing 100 samples """
profiling_traces = np.random.rand(10000, 100)

""" create dummy attack traces array with 1000 traces, each one containing 100 samples """
attack_traces = np.random.rand(1000, 100)

""" create dummy plaintexts, ciphertexts and keys for profiling set: array with 10000 rows x 16 bytes """
plaintexts_profiling = np.random.randint(0, 256, (10000, 16))
ciphertexts_profiling = np.random.randint(0, 256, (10000, 16))
keys_profiling = np.random.randint(0, 256, (10000, 16))

""" create dummy plaintexts, ciphertexts and keys for attack set: array with 1000 rows x 16 bytes """
plaintexts_attack = np.random.randint(0, 256, (1000, 16))
ciphertexts_attack = np.random.randint(0, 256, (1000, 16))
keys_attack = np.random.randint(0, 256, (1000, 16))

nb_profiling_traces = 10000
nb_attack_traces = 1000
profiling_index = [i for i in range(nb_profiling_traces)]
attack_index = [i for i in range(nb_attack_traces)]

out_file = h5py.File('new_dataset.h5', 'w')

profiling_traces_group = out_file.create_group("Profiling_traces")
attack_traces_group = out_file.create_group("Attack_traces")

""" create fields for profiling and attack sets """
profiling_traces_group.create_dataset(name="traces", data=profiling_traces, dtype=profiling_traces.dtype)
attack_traces_group.create_dataset(name="traces", data=attack_traces, dtype=attack_traces.dtype)

""" create metadata fields for profiling and attack sets"""
metadata_type_profiling = np.dtype([("plaintext", plaintexts_profiling.dtype, (nb_profiling_traces,)),
                                    ("ciphertext", ciphertexts_profiling.dtype, (nb_profiling_traces,)),
                                    ("key", keys_profiling.dtype, (nb_profiling_traces,))
                                    ])
profiling_metadata = np.array([(plaintexts_profiling[n], ciphertexts_profiling[n], keys_profiling[n]) for n in
                               zip(profiling_index)], dtype=metadata_type_profiling)

metadata_type_attack = np.dtype([("plaintext", plaintexts_attack.dtype, (nb_attack_traces,)),
                                 ("ciphertext", ciphertexts_attack.dtype, (nb_attack_traces,)),
                                 ("key", keys_attack.dtype, (nb_attack_traces,))
                                 ])
profiling_traces_group.create_dataset("metadata", data=profiling_metadata, dtype=metadata_type_profiling)

attack_metadata = np.array([(plaintexts_attack[n], ciphertexts_attack[n], keys_attack[n]) for n in
                            zip(attack_index)], dtype=metadata_type_attack)
attack_traces_group.create_dataset("metadata", data=attack_metadata, dtype=metadata_type_attack)

out_file.flush()
out_file.close()
```

For sanity checks, the user may verify if the .h5 file contain specific field in metadata.
The next example shows how to verify if *ciphertext* field is located in profiling metadata:

```python
import h5py

h5_file = h5py.File("my_dataset.h5", "r")
if "ciphertext" in h5_file['Profiling_traces/metadata'].dtype.fields:
    print("ciphertext field is in metadata for profiling traces.")
```

##### h5py package

To avoid incopatibility issues, AISY framework recommends to use h5py version 2.10.0, as specified in requirements.txt file.

## Creating **Dataset** object

After reading a .h5 file, AISY framework automatically generates an internal **Dataset** object. 
This object is manipulated by the classes and functions that uses dataset information.

As specified in this documentation, AISY framework automatically generates dataset labels from plaintext (or ciphertexts)
and keys from .h5 metadata. However, the current version of the framework only supports labels generation for AES128-related datasets.
In case the user wants to label the dataset with an alternative concept (e.g., by taking into consideration **masks** available to the user),
it is possible to create a **Dataset** object and provided it to the main **Aisy** class before running the analysis.

The following code provides an example to generate a **Dataset** object:

```python
import aisy_sca
from app import *
from custom.custom_models.neural_networks import *
from aisy_sca.datasets.Dataset import Dataset
from tensorflow.keras.utils import to_categorical
import numpy as np

""" set profiling, validation and attack traces"""
x_profiling = np.random.rand(10000, 100)
x_attack = np.random.rand(1000, 100)
x_validation = np.random.rand(1000, 100)

""" set profiling, validation and attack labels"""
y_profiling = to_categorical(np.random.randint(0, 256, 10000), num_classes=256)
y_attack = to_categorical(np.random.randint(0, 256, 1000), num_classes=256)
y_validation = to_categorical(np.random.randint(0, 256, 1000), num_classes=256)

""" create list of key guesses for attack and validation sets """
labels_key_guess_validation_set = np.random.randint(0, 256, (256, 1000))
labels_key_guess_attack_set = np.random.randint(0, 256, (256, 1000))

""" create dataset object """
new_dataset = Dataset(x_profiling, y_profiling, x_attack, y_attack, x_validation, y_validation)

new_dataset_dict = {
    "filename": "new_dataset.h5",
    "key": "4DFBE0F27221FE10A78D4ADC8E490469",
    "first_sample": 0,
    "number_of_samples": 100,
    "number_of_profiling_traces": 10000,
    "number_of_attack_traces": 1000
}

aisy = aisy_sca.Aisy()
aisy.set_resources_root_folder(resources_root_folder)
aisy.set_database_root_folder(databases_root_folder)
aisy.set_datasets_root_folder(datasets_root_folder)
aisy.set_database_name("database_ascad_variable.sqlite")

""" User must set dataset object and value of good key for GE and SR """
aisy.set_classes(256)
aisy.set_good_key(224)
aisy.set_dataset(new_dataset_dict, dataset=new_dataset)
aisy.set_labels_key_guesses_attack_set(labels_key_guess_attack_set)
aisy.set_labels_key_guesses_validation_set(labels_key_guess_validation_set)

aisy.set_batch_size(400)
aisy.set_epochs(10)
aisy.add_neural_network(mlp)
aisy.run()
```

Note in the above example that the labels (*y_profiling, y_validation, y_attack*) are generated before the method **run()** from main **Aisy** class is
called.
