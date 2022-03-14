# Welcome to AISY Framework - Deep Learning for Side-Channel Analysis

AISY framework is a python-based framework that allows efficient and scalable applications of deep learning to profiling side-channel analysis (SCA). This project was implemented as a result 
of several years of research on deep learning and side-channel analysis by AisyLab at TU Delft (The Netherlands).

### Example script

AISY Framework allows very easy execution of deep learning in profiled side-channel attacks.
Here is an example of all the code that is needed to run a profiled SCA attack on the third key byte 
(index 2) of an AES implementation from well-known ASCAD dabatase:

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

By running the above script, the framework implements automated dataset loading, database 
configuration, leakage model (labeling) definitions, profiling and attack phases. By default, the 
framework computes Guessing Entropy and Success Rate metrics.

Next, we list all main advantages of AISY Framework for deep learning-based SCA research.

### Integrated SQLite Database

AISY Framework comes with the option to store all analysis results in a SQLite database. Standard libraries are implemented in the framework
and users can easily add custom tables to the project. The creation of custom tables does not require any specific background knowlegde on 
databases.  

### Web Application 

AISY Framework is built on top of the [Flask](https://flask.palletsprojects.com/en/1.1.x/) python-based web framework. A web application is 
integrated with a web-based user interface.

The web application provides a user-friendly way to visualize analysis, plots, results and tables. Note, 
however that the interface is only intended to provide an easy way to visualize results and keep them organized
on databases. From the web interface, the user cannot run scripts or manipulate analysis settings (this will
appear in future versions of the framework).

### Reproducible results with one-click script generation

In the Web Application, an user can generate the full script that was used to produce results stored in the database. This is particularly 
important when the user wishes to reproduce results after he or she changed the script and don't keep track of the changes. Another advantage of this feature can be seen when 
an user shares the database with a second user. The latter generate all the scripts from original database.  

### State-of-the-art deep learning-based SCA 

AISY Framework brings state-of-the-art deep learning-based side-channel attacks. We follow recent publications and keep the framework 
updated accordingly. The current version is 0.2 and it that already provides state-of-the-art features such as custom loss functions, 
hyperparameter search, visualization, custom metrics and data augmentation. 


### Installation

The AISY framework can be cloned from our github page:
```
git clone https://github.com/AISyLab/AISY_Framework.git
cd AISY_framework
pip install -r requirements.txt
```

## Framework layout

    custom/                               # folder containing customized definitions 
        custom_callbacks/callbacks.py     # file with user callbacks
        custom_metrics/                   # each .py file contain a custom metric
        custom_models/neural_networks.py  # file to insert user neural networks (keras models)
        custom_loss/                      # each .py file contain a custom loss
        custom_datasets/datasets.py       # file with dataset details
        custom_data_augmentation/         # file containing data augmentation method
        custom_tables/tables.py           # file containing custom sqlite tables 
    resources/                            # folder to store user resources (it will be created when first analysis is executed)
        databases/                        # .sqlite database files with project information and analysis results
        figures/                          # .png figure generated from user
        models/                           # .h5 models
        npz/                              # .npz files with project information and analysis results 
    scripts/                              # folder to store main user scripts
    webapp/                               # flask web application files (html, js, css)
    app.py                                # main flask application
    
## Main Features

- SCA Metrics (guessing entropy and success rate)
- Gradient Visualization
- Data Augmentation 
- Grid Search
- Random Search
- Early Stopping
- Ensemble
- Custom Metrics
- Custom Callbacks
- Confusion Matrix
- Easy Neural Network Definitions
- Data Augmentation
- GUI - plots, tables
- Automatically generate scripts 
- Fully reproducible scripts   


## Running Scripts

The project structure is provided in a way that all scripts are placed inside ```scripts``` folder. To run a python file script
(e.g., ```script_aes.py```), the user might use an IDE (like PyCharm, where we simply right-click in the script file and then *run*) or,
more likely, from command line. To run scripts from command line, user must follow one of the two options:
 
1) Place the script inside project root folder.
2) Place the script inside ```scripts``` folder and use **sys** python package to indicate the project root folder, as in the example below:

```python
import sys
sys.path.append('my_path/AISY_framework')

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

Then, simply put the root path location of your AISY_framework project and run the script: ```python scripts/script_aes.py```.
 

## Starting the WebApp

The Web Application is built on top of Flask web framework. To start the web homepage and visualize project results, simply run the 
following command on the terminal: 

```
cd AISY_Framework/
flask run
```   
