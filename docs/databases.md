# Databases

The AISY database package must be installed together with the framework files. In ```requirements.txt```, python package
aisy-database ```aisy-database 0.2.0 ``` is already set.

## Database root folder

The root location of databases can be defined in two ways.

##### 1. By defining database location in```app.py``` file

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