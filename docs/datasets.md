# Datasets

## Open-source AES datasets

The standard version of AISY Framework comes with the definitions of open-source side-channel analysis datasets.

- [ASCAD Fixed Key](https://github.com/ANSSI-FR/ASCAD/tree/master/ATMEGA_AES_v1/ATM_AES_v1_fixed_key)  
- [ASCAD Random Keys](https://github.com/ANSSI-FR/ASCAD/tree/master/ATMEGA_AES_v1/ATM_AES_v1_variable_key)
- [AES_HD](http://aisylabdatasets.ewi.tudelft.nl/)
- [AES_HD_ext](http://aisylabdatasets.ewi.tudelft.nl/)

The user can download the datasets by running the following commands in a terminal:

### Windows 10
```
curl.exe -o aes_hd.h5 http://aisylabdatasets.ewi.tudelft.nl/aes_hd.h5
curl.exe -o aes_hd_ext.h5 http://aisylabdatasets.ewi.tudelft.nl/aes_hd_ext.h5
curl.exe -o ches_ctf.h5 http://aisylabdatasets.ewi.tudelft.nl/aes_hd.h5
curl.exe -o ches_ctf.h5 http://aisylabdatasets.ewi.tudelft.nl/aes_hd_ext.h5

```

### Linux
```
wget http://aisylabdatasets.ewi.tudelft.nl/aes_hd.h5
wget http://aisylabdatasets.ewi.tudelft.nl/aes_hd_ext.h5
wget http://aisylabdatasets.ewi.tudelft.nl/aes_hd.h5
wget http://aisylabdatasets.ewi.tudelft.nl/aes_hd_ext.h5
```

## Creating datasets for AISY Framework

#### .h5 datasets

In the academic community, the most used datasets in deep learning-based SCA are [ASCAD](https://github.com/ANSSI-FR/ASCAD) trace sets from 
[ANSSI (France) github repository](https://github.com/ANSSI-FR). 

The authors of ASCAD repository delivered [source code](https://github.com/ANSSI-FR/ASCAD/blob/master/ASCAD_generate.py)
to generate ```.h5``` datasets according to their specific format. The lines 127-145 from file ```ASCAD_generate.py``` provide an example of 
how to generate ```.h5``` datasets. 
*If the user wants to process datasets in the ```.h5``` format, AISY Framework expects files generated according to ASCAD database.*

The only limitation found in ```ASCAD_generate.py``` code is that it does not generate ciphertexts in the metadata field. Therefore, we 
modified the code in order to overcome this limitation (note that we don't provide ```masks``` in the metadata):

```python
out_file = h5py.File('new_dataset.h5', 'w')

profiling_traces_group = out_file.create_group("Profiling_traces")
attack_traces_group = out_file.create_group("Attack_traces")

profiling_traces_group.create_dataset(name="traces", data=train_samples, dtype=train_samples.dtype)
attack_traces_group.create_dataset(name="traces", data=test_samples, dtype=test_samples.dtype)

metadata_type_profiling = np.dtype([("plaintext", profiling_plaintext.dtype, (len(profiling_plaintext[0]),)),
                          ("ciphertext", profiling_ciphertext.dtype, (len(profiling_ciphertext[0]),)),
                          ("key", profiling_key.dtype, (len(profiling_key[0]),))
                          ])
metadata_type_attack = np.dtype([("plaintext", attack_plaintext.dtype, (len(attack_plaintext[0]),)),
                          ("ciphertext", attack_ciphertext.dtype, (len(attack_ciphertext[0]),)),
                          ("key", attack_key.dtype, (len(attack_key[0]),))
                          ])

profiling_metadata = np.array([(profiling_plaintext[n], profiling_ciphertext[n], profiling_key[n]) for n, k in
                               zip(profiling_index, range(0, len(train_samples)))], dtype=metadata_type_profiling)
profiling_traces_group.create_dataset("metadata", data=profiling_metadata, dtype=metadata_type_profiling)

attack_metadata = np.array([(attack_plaintext[n], attack_ciphertext[n], attack_key[n]) for n, k in
                            zip(attack_index, range(0, len(test_samples)))], dtype=metadata_type_attack)
attack_traces_group.create_dataset("metadata", data=attack_metadata, dtype=metadata_type_attack)

out_file.flush()
out_file.close()
```
 
Here is an example of how to read ```.h5``` dataset in the ASCAD format:

```python
in_file = h5py.File("my_location/my_dataset.h5", "r")
# reading trace samples
profiling_samples = numpy.array(in_file['Profiling_traces/traces'], dtype=numpy.float64)
attack_samples = numpy.array(in_file['Attack_traces/traces'], dtype=numpy.float64)
# reading trace plaintexts
profiling_plaintext = in_file['Profiling_traces/metadata']['plaintext']
attack_plaintext = in_file['Attack_traces/metadata']['plaintext']
# reading trace ciphertexts
profiling_ciphertext = in_file['Profiling_traces/metadata']['ciphertext']
attack_ciphertext = in_file['Attack_traces/metadata']['ciphertext']
# reading trace keys
profiling_key = in_file['Profiling_traces/metadata']['key']
attack_key = in_file['Attack_traces/metadata']['key']
```

#### .npz datasets

```.npz``` dataset format will be available in future releases.

## Dataset root folder

The root location of datasets can be defined in two ways.

##### Defining datasets location in```app.py``` file

The root location of datasets is defined in ```app.py``` file as follows:

```python
datasets_root_folder = "my_location/datasets/"
```

The user is free to change this location.

##### Defining database location in script file 

The user can also easily set the dataset location in the main script, as in the example below:

```python
aisy = AisyAes()
aisy.set_datasets_root_folder("my_location/datasets/")
aisy.set_dataset("ascad-variable.h5")
```
    
## Datasets Specifications

As for the location, specifications for the datasets can be done either in the main script or, as recommended, in a dictionary containing 
six specifications for all datasets in ```custom/custom_datasets/datasets.py``` file. The possible specifications are:

- ```"file_name":``` string setting the file name of the dataset. Example: ```"filename": "ascad-variable.h5"```. 
- ```"key": ``` hex string defining the dataset key. Example: ```"key": "00112233445566778899AABBCCDDEEFF"```.
- ```"first_sample": ``` integer defining the index of the first sample in each trace to be analysed. Example: ```"first_sample": 0```. 
- ```"number_of_samples": ``` integer defining the number of samples in each trace to be analysed. Example: ```"number_of_samples": 700```.
- ```"number_of_profiling_traces": ``` integer defining the number of profiling traces. Example: ```"number_of_profiling_traces": 200000```. 
- ```"number_of_attack_traces": ``` integer defining the number of attack traces. Example: ```"number_of_attack_traces": 10000```. 

If the user decides to set the dataset specification in ```custom/custom_datasets/datasets.py``` file, the dataset is called from the main 
script according to the name defined in the dictionary. In the main script:

```python
aisy = AisyAes()
aisy.set_dataset("ascad-variable.h5")
```

And in the dictionary in ```custom/custom_datasets/datasets.py```:

```python
datasets_dict = {
    "ascad-variable.h5": {
        "filename": "ascad-variable.h5",
        "key": "00112233445566778899AABBCCDDEEFF",
        "first_sample": 0,
        "number_of_samples": 1400,
        "number_of_profiling_traces": 100000,
        "number_of_attack_traces": 1000
    }
}
```

Alternatively, the user can completely ignore ```custom/custom_datasets/datasets.py``` file and set all the specifications in the main script:

```python
aisy = aisy_sca.Aisy()
aisy.set_dataset_filename("ascad-variable.h5")
aisy.set_key("00112233445566778899AABBCCDDEEFF")
aisy.set_number_of_profiling_traces(200000)
aisy.set_number_of_attack_traces(10000)
aisy.set_first_sample(0)
aisy.set_number_of_samples(1400)
```

Note that if the user decides to avoid the datasets definitions from ```datasets_dict```, it is strictly necessary to provide all six specifications.
  