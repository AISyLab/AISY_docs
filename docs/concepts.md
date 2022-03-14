# Concepts

AISY Framework v0.2 works in a specific setting of *known-key side-channel analysis*. 
Therefore, for each analysis, the user must clearly provide with dataset definitions what is the 
correct key of the target device. Note, however, that automatic labelling is only supported for AES128
cipher. For other ciphers, the labelling process must be provided by the user as custom dataset.

### AISY Framework Flow

Figure below illustrates the main framework flow. In blue, we depict the operations that are basic 
to the framework (those that are always execute). In light orange, we depict the optional features.
The user settings (which are provided in the main script) are shown in yellow. The "conditional point" indicates the moment when the framework
check a conditional statement in order to verify if a functionality is executed or not. 

![Screenshot](images/framework_structure.png)

### Main Functionalities for Profiling Model Creation

Figure belows illustrates the main functionalities implemented in AISY framework regarding profiling model
creation. Basically, a user can deploy a single process or hyperparameter search process. 
For single process, user can train a single model or multiple models on the same dataset and 
leakage model. This means that in the main script, the user can enter multiple neural networks 
to be trained by the framework in a single run. 
In case of hyperparameter search process, user can select between grid or random search.

Two additional functionalities are provided with the framework: **ProfilingAnalyzer** and **Ensemble**.
Profiling Analyzer can be executed in all situations (single model, multi-model or hyperparameter search).
Ensemble requires multiple models to be provided by the user, and the ensemble is built from user-defined 
amount of models.

![Screenshot](images/framework_functionalities.png)


