# Databases

## Database root folder

The root location of databases can be defined in two ways.

##### Defining database location in```app.py``` file

The root location of sqlite database is defined in ```app.py``` file as follows:

```python
databases_root_folder = "my_location/databases/"
```

The user is free to change this location.

##### Defining database location in script file 

The user can also easily set the database location in the main script, as in the example below:

```python
aisy = AisyAes()
aisy.set_database_root_folder("my_location/")
aisy.set_database_name("my_database.sqlite")
```

AISY framework makes use of SQLITE database. In the main script, the user enter the name of the *sqlite* database file. If file already
exists, then a new entry is stored for the deployed analysis. Otherwise, the *sqlite* file is created and stored in ```/databases``` folder. 

## Skipping Database Usage

By default, database is automatically set be used in the framework. However, users are free to skip the usage of databases in the AISY 
framework. To skip or avoid database storage, simply set the parameter ```save_database=False``` in the ```run``` method:

```python
aisy = AisyAes()
...
aisy.run(save_database=False)
```

## Tables

Database is based on [SQLAlchemy](https://www.sqlalchemy.org/) python package. Here we provide a list of tables and the column types from 
SQLAlchemy package.

##### Analysis
- ```id (Integer):``` primary key id for entry.
- ```datetime (DateTime):``` data and time for the entry.
- ```script (String):``` the name of script (script location: ```/scripts```).
- ```db_filename (String):``` the name of *sqlite* file.
- ```dataset (String):``` the name of evaluated SCA dataset.
- ```elapsed_time (Float):``` the elapsed time for the analysis.

##### HyperParameter
- ```id (Integer):``` primary key id for entry.
- ```hyper_parameters (JSON):``` dictionary containing list of hyperparameters.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### NeuralNetwork
- ```id (Integer):``` primary key id for entry.
- ```model_name (String):``` name of neural network model.
- ```description (String):``` description of neural network model.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### LeakageModel
- ```id (Integer):``` primary key id for entry.
- ```leakage_model (JSON):``` dictionary containing list of leakage model attributes.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### Metric
- ```id (Integer):``` primary key id for entry.
- ```value (Float):``` value of the metric.
- ```key_byte (Integer):``` target key byte related to the metric value.
- ```metric (String):``` name of the metric.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### KeyRank
- ```id (Integer):``` primary key id for entry.
- ```values (JSON):``` list of values.
- ```name (String):``` name of the key rank result.
- ```key_byte (Integer):``` target key byte related to the key rank.
- ```report_interval (Intger):``` report interval (step in the number of side channel traces or measurements) which key rank is stored.
- ```metric (String):``` name of the metric.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### SuccessRate
- ```id (Integer):``` primary key id for entry.
- ```values (JSON):``` list of values.
- ```name (String):``` name of the key rank result.
- ```key_byte (Integer):``` target key byte related to the success rate.
- ```report_interval (Intger):``` report interval (step in the number of side channel traces or measurements) which success rate is stored.
- ```metric (String):``` name of the metric.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### Visualization
- ```id (Integer):``` primary key id for entry.
- ```values (JSON):``` list of values.
- ```epoch (Integer):``` epoch value for visualization results. 
- ```key_byte (Integer):``` target key byte related to the visualization results.
- ```report_interval (Intger):``` report interval (step in the number of side channel traces or measurements) which visualization is stored.
- ```metric (String):``` name of the metric.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### HyperParameterSearch
- ```id (Integer):``` primary key id for entry.
- ```search_type (String):``` type of search. It can *grid_search* or *random_search*.
- ```hyper_parameters_settings (JSON):``` dictionary containing hyperparameters search settings.
- ```best_hyper_parameters (Integer, ForeignKey):``` id of HyperParameter table.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis table.

##### ConfusionMatrix
- ```id (Integer):``` primary key id for entry.
- ```y_pred (JSON):``` list of ```y_pred``` values.
- ```y_true (Integer):``` index of ```y_true``` label.
- ```key_byte (Integer):``` target key byte related to the results.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis Table.

##### ProbabilityRank
- ```id (Integer):``` primary key id for entry.
- ```ranks (JSON):``` list of ```ranks``` values (one row per ```key guess```).
- ```classes (Integer):``` number of classes in the classification.
- ```correct_key_byte (Integer):``` value of correct key byte.
- ```key_guess (Integer):``` value of key guess.
- ```key_byte (Integer):``` target key byte related to the results.
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
from sqlalchemy.orm import relationship
from aisy.sca_tables import Analysis, Base


def base():
    return Base


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

Firstly, in your script file (located at ```scripts/```), create the ```AisyAes()``` object and set *dataset folder*, *dataset name* and 
*database name*:

```python
aisy = AisyAes()
aisy.set_dataset("ascad-variable.h5")
aisy.set_database_name("database_ascad.sqlite")
```

Second, retrieve the ```db_inserts```:

```python
db_inserts = aisy.get_db_inserts()
``` 

Finally, insert your entries to your CustomTable as soon as you define the values to be inserted:

```python
new_insert = CustomTable(value1=10, value2=20, value3=30)
db_inserts.custom_insert(new_insert)
```

A full script example will look like this:
```python
from custom.custom_models.neural_networks import *
from aisy.sca_deep_learning_aes import AisyAes
from custom.custom_tables.tables import *

aisy = AisyAes()
aisy.set_datasets_root_folder("D:/traces/")
aisy.set_dataset("ascad-variable.h5")
aisy.set_database_name("database_ascad.sqlite")

db_inserts = aisy.get_db_inserts()

aisy.set_aes_leakage_model(leakage_model="HW", byte=2)
aisy.set_number_of_profiling_traces(10000)
aisy.set_number_of_attack_traces(1000)
aisy.set_batch_size(400)
aisy.set_epochs(50)
aisy.set_neural_network(mlp)

aisy.run()

new_insert = CustomTable(value1=10, value2=20, value3=30)
db_inserts.custom_insert(new_insert)
``` 