# Welcome to AISY Framework - Deep Learning for Side-Channel Analysis

AISY framework is a python-based framework that allows efficient and scalable applications of deep learning to side-channel attacks (SCA). This project was implemented as a result 
of several years of research on deep learning and side-channel analysis by AisyLab at TU Delft (The Netherlands).

### Why you should consider AISY Framework for Deep Learning-based SCA

###### Reason 1: Easy to use

AISY Framework allows very easy execution of deep learning in profiled side-channel attacks.
Here is an example of all the code that is needed to run a profiled SCA attack on key byte 2 of an AES implementation from well-known ASCAD 
dabatase:

```python
from custom.custom_models.neural_networks import *
from aisy.sca_deep_learning_aes import AisyAes

aisy = AisyAes()
aisy.set_database_name("database_ascad.sqlite")
aisy.set_dataset("ascad-variable.h5")
aisy.set_aes_leakage_model(leakage_model="HW", byte=2)
aisy.set_batch_size(400)
aisy.set_epochs(50)
aisy.set_neural_network(mlp)

aisy.run()
```

###### Reason 2: Easy Integrated Database

AISY Framework comes with the option to store all analysis results in a SQLite database. Standard libraries are implemented in the framework
and users can easily add custom tables to the framework. The creation of custom tables does not require any specific background knowlegde on 
databases. 

*In future releases, our framework will provide* **remote** *database implementation.*

###### Reason 3: Web Application 

AISY Framework is built on top of the [Flask](https://flask.palletsprojects.com/en/1.1.x/) python-based web framework. A web application is 
integrated with a web-based user interface.

The web application provides a user-friendly way to visualize analysis, plots, results and tables.  

###### Reason 4: One-Click Script Generation

In the Web Application, an user can generate the full script that was used to produce results stored in the database. This is particularly 
important when the user wishes to reproduce results after he or she changed the script. Another advantage of this feature can be seen when 
an user shares the database with a second user. The latter generate all the scripts from database.  

###### Reason 5: State-of-the-Art Side-Channel Analysis 

AISY Framework brings state-of-the-art deep learning-based side-channel attacks. We follow recent publications and keep the framework 
updated accordingly. The version 0.1 already provides state-of-the-art features such as ensembles, hyperparameter search, visualization, 
data augmentation and special SCA metrics. 

###### Reason 6: Team Work 

AISY Framework allows easy and efficient team work. The structure is done in a way that multiple users can easily share results and 
contribute together to analysis. By sharing the database, users in different locations can visualization results without the need of sending
multiple email with files, figures and code.  

### Installation

The AISY framework can be cloned from our github page:
```
git clone https://github.com/AISyLab/AISY_Framework.git
``` 

## Framework layout

    aisy/                                 # The AISY framework main functions and classes
    api/api.py                            # API for SCA functionalities.
    custom/                               # folder containing customized definitions 
        custom_callbacks/callbacks.py     # file with user callbacks
        custom_metrics/                   # each .py file contain a custom metric
        custom_models/neural_networks.py  # file to insert user neural networks (keras models) 
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

- SCA Metrics
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


## Running Scripts

The project structure is provided in a way that all scripts are placed inside ```scripts``` folder. To run a python file script
(e.g., ```script_aes.py```), the user might use an IDE (like PyCharm, where we simply right-click in the script file and then *run*) or,
more likely, from command line. To run script from command line, user must follow one of the two options:
 
1) Place the script inside project root folder.
2) Place the script inside ```scripts``` folder and use **sys** python package to indicate the project root folder, as in the example below:

```python
import sys
sys.path.append('my_path/AISY_framework')

from custom.custom_models.neural_networks import *
from aisy.sca_deep_learning_aes import AisyAes

aisy = AisyAes()
aisy.set_database_name("database_ascad.sqlite")
aisy.set_dataset("ascad-variable.h5")
aisy.set_aes_leakage_model(leakage_model="HW", byte=2)
aisy.set_batch_size(400)
aisy.set_epochs(50)
aisy.set_neural_network(mlp)

aisy.run()
```

Then, simply put the path location of your AISY_framework project and run the script: ```python scripts/script_aes.py```.
 

## Starting the WebApp

The Web Application is built on top of Flask web framework. To start the web homepage and visualize project results, simply run the 
following command on the terminal: 

```
cd AISY_Framework/
flask run
```   
