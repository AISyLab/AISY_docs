# Databases

The AISY database package must be installed together with the framework files. In ```requirements.txt```, python package
aisy-database ```aisy-database 0.2.0 ``` is already set.

## Database root folder

The root location of databases can be defined in two ways.

##### 1. By dfining database location in```app.py``` file

The root location of sqlite database is defined in ```app.py``` file as follows:

```python
databases_root_folder = "my_location/databases/"
```

The user is free to change this location.

##### 2. By defining database location in script file

The user can also easily set the database location in the main script, as in the example below:

```python
aisy = aisy_sca.Aisy()
aisy.set_database_root_folder("my_location/")
aisy.set_database_name("my_database.sqlite")
```

AISY framework makes use of SQlite database. In the main script, the user enter the name of the ```.sqlite``` database file. If file already
exists, then new entries from the current analysis are stored for the existing tables. Otherwise, the ```.sqlite``` file is created and
stored in ```resources/databases``` folder. Note that ```resources``` folder is only generated once the first analysis is executed.

## Tables

Database code is implemented from open-source [SQLAlchemy](https://www.sqlalchemy.org/) python package. Here we provide a list of tables (
and the column types from SQLAlchemy package) that are generate in each
```.sqlite``` file.

##### Analysis

- ```id (Integer):``` primary key id for entry.
- ```datetime (DateTime):``` data and time for the entry.
- ```db_filename (String):``` the name of *sqlite* file.
- ```dataset (String):``` the name of evaluated SCA dataset.  
- ```settings (JSON): ``` dictionary of analysis settings.
- ```elapsed_time (Float):``` the elapsed time for the analysis.
- ```deleted (Boolean):``` indicates if entry is deleted by the user.

##### HyperParameter

- ```id (Integer):``` primary key id for entry.
- ```hyper_parameters (JSON):``` dictionary containing list of hyperparameters.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### NeuralNetwork

- ```id (Integer):``` primary key id for entry.
- ```model_name (String):``` name of neural network model.
- ```description (String):``` description of neural network model.
- ```hyperparameters_id (Integer, ForeignKey):``` id from Hyperparameters Table.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### LeakageModel

- ```id (Integer):``` primary key id for entry.
- ```leakage_model (JSON):``` dictionary containing list of leakage model attributes.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### Metric

- ```id (Integer):``` primary key id for entry.
- ```values (JSON):``` value of the metric.
- ```label (String):``` name of the metric.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### GuessingEntropy

- ```id (Integer):``` primary key id for entry.
- ```values (JSON):``` list of values.
- ```label (String):``` name of the guessing entropy result.
- ```report_interval (Intger):``` report interval (step in the number of side channel traces or measurements) which key rank is stored.
- ```metric (String):``` name of the metric.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### SuccessRate

- ```id (Integer):``` primary key id for entry.
- ```values (JSON):``` list of values.
- ```label (String):``` name of the success rate result.
- ```report_interval (Intger):``` report interval (step in the number of side channel traces or measurements) which success rate is stored.
- ```metric (String):``` name of the metric.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### Visualization

- ```id (Integer):``` primary key id for entry.
- ```values (JSON):``` list of values.
- ```epoch (Integer):``` epoch value for visualization results.
- ```label (String):``` metric label.
- ```hyperparameters_id (Integer, ForeignKey):``` id from Hyperparameters Table.  
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### HyperparameterMetric

- ```id (Integer):``` primary key id for entry.
- ```metric_id (Integer, ForeignKey):``` id from Metric Table.  
- ```hyperparameters_id (Integer, ForeignKey):``` id from Hyperparameters Table.  
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### HyperParameterSuccessRate

- ```id (Integer):``` primary key id for entry.
- ```success_rate_id (Integer, ForeignKey):``` id from SuccessRate Table.  
- ```hyperparameters_id (Integer, ForeignKey):``` id from Hyperparameters Table.  
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### HyperParameterGuessingEntropy

- ```id (Integer):``` primary key id for entry.
- ```guessing_entropy_id (Integer, ForeignKey):``` id from SuccessRate Table.  
- ```hyperparameters_id (Integer, ForeignKey):``` id from Hyperparameters Table.  
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### ConfusionMatrix

- ```id (Integer):``` primary key id for entry.
- ```y_pred (JSON):``` list of ```y_pred``` values.
- ```y_true (Integer):``` index of ```y_true``` label.
- ```hyperparameters_id (Integer, ForeignKey):``` id from Hyperparameters Table.  
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### ProbabilityRank

- ```id (Integer):``` primary key id for entry.
- ```ranks (JSON):``` list of ```ranks``` values (one row per ```key guess```).
- ```classes (Integer):``` number of classes in the classification.
- ```correct_key_byte (Integer):``` value of correct key byte.
- ```key_guess (Integer):``` value of key guess.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### RandomStatesHyperparameter

- ```id (Integer):``` primary key id for entry.
- ```random_states (JSON)```: list of random states
- ```label (String):``` random state label.
- ```index (Integer```: random state index.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.


## Adding Custom Tables

User can also add custom SQLite tables to the analysis. The process is rather simple and requires the following steps:

###### Step 1: Define custom table in ```custom/custom_tables/tables.py```

In the ```custom/custom_tables/tables.py``` file, enter a custom SQLite table structure following the SQLAlchemy package structure. Below,
we provide an example of ```CustomTable``` class with the following columns:

- ```id (Integer):``` primary key id for entry.
- ```value1 (Integer):``` column with an integer value.
- ```value2 (Integer):``` column with an integer value.
- ```value3 (Integer):``` column with an integer value.
- ```datetime (DateTime):``` data and time for the entry.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis table.

Here is the code for the table:

```python
class CustomTable(Base):
    __tablename__ = 'custom_table'
    id = Column(Integer, primary_key=True)
    value1 = Column(Integer)
    value2 = Column(Integer)
    value3 = Column(Integer)
    datetime = Column(DateTime, default=datetime.datetime.utcnow)
    analysis_id = Column(Integer, ForeignKey(Analysis.id))
    analysis = relationship(Analysis)

    def __repr__(self):
        return "<CustomTable(id=%d)>" % self.id
```

The full code for ```custom/custom_tables/tables.py``` will look like this:

```python
import datetime
from sqlalchemy import *
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship
from aisy.sca_tables import Analysis
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

Base = declarative_base()


def base():
    return Base


def start_custom_tables(database_name):
    engine = create_engine('sqlite:///{}'.format(database_name), echo=False)
    base().metadata.create_all(engine)


def start_custom_tables_session(database_name):
    engine = create_engine('sqlite:///{}'.format(database_name), echo=False)
    return sessionmaker(bind=engine)()


class CustomTable(Base):
    __tablename__ = 'custom_table'
    id = Column(Integer, primary_key=True)
    value1 = Column(Integer)
    value2 = Column(Integer)
    value3 = Column(Integer)
    datetime = Column(DateTime, default=datetime.datetime.utcnow)
    analysis_id = Column(Integer, ForeignKey(Analysis.id))
    analysis = relationship(Analysis)

    def __repr__(self):
        return "<CustomTable(id=%d)>" % self.id

```

###### Step 2: Inserting entries to your custom table

First, in your script file (located at ```scripts/```), create the ```Aisy()``` object and set *dataset folder*, *dataset name* and
*database name* and run the analysis as usual (look at the imports):

```python
import aisy_sca
from app import *
from custom.custom_models.neural_networks import *
from custom.custom_tables.tables import *

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

Next, start the custom table session:

```python
start_custom_tables(databases_root_folder + "database_ascad.sqlite")
session = start_custom_tables_session(databases_root_folder + "database_ascad.sqlite")
```

Then, insert your entries to your CustomTable as soon as you define the values to be inserted:

```python
new_insert = CustomTable(value1=10, value2=20, value3=30, analysis_id=aisy.settings["analysis_id"])
session.add(new_insert)
session.commit()
```

A full script example will look like this:

```python
import aisy_sca
from app import *
from custom.custom_models.neural_networks import *
from custom.custom_tables.tables import *

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

start_custom_tables(databases_root_folder + "database_ascad.sqlite")
session = start_custom_tables_session(databases_root_folder + "database_ascad.sqlite")

new_insert = CustomTable(value1=10, value2=20, value3=30, analysis_id=aisy.get_analysis_id())
session.add(new_insert)
session.commit()
``` 