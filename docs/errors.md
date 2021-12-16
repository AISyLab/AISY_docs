# Errors

###### Error 1: neural network model is not defined.


###### ERROR 2: Dataset 'filename' not specified. Please use ```set_filename``` method to specify it.


###### ERROR 3: Dataset 'key' not specified. Please use ```set_key``` method to specify it.


###### ERROR 4 : Dataset 'first_sample' not specified. Please use ```set_first_sample``` method to specify it.


###### ERROR 5: Dataset 'number_of_samples' not specified. Please use ```set_number_of_samples``` method to specify it.


###### ERROR 6: Dataset 'number_of_profiling_traces' not specified. Please use ```set_number_of_profiling_traces``` method to specify it.


###### ERROR 7: Dataset 'number_of_attack_traces' not specified. Please use ```set_number_of_attack_traces``` method to specify it.


###### ERROR 8: ensemble feature requires the 'number_of_attack_traces' >= 2 x key_rank_attack_traces.


###### ERROR 9: early stopping feature requires the 'number_of_attack_traces' >= 2 x key_rank_attack_traces.


###### ERROR 10: Dataset format not supported.


###### ERROR 11: when grid search has a stop condition, ensembles can't be applied.


###### ERROR 12: number of models for ensembles can't be larger than maximum number of trials in random search.


###### ERROR 13: when random search has a stop condition, ensembles can't be applied.


###### ERROR 14: grid search feature requires the 'number_of_attack_traces' >= 2 x key_rank_attack_traces.


###### ERROR 15: random search feature requires the 'number_of_attack_traces' >= 2 x key_rank_attack_traces.


###### ERROR 16: to be able to reproduce analysis results, user must set random states for GE and SR with set_ge_sr_random_states() method


###### ERROR 17: to be able to reproduce analysis results, user must set random states for ensembles for GE and SR with set_ge_sr_random_states_ensembles() method


###### ERROR 18: to be able to reproduce analysis results, user must set random states for hyper-parameters search for GE and SR with set_ge_sr_random_states_search() method


###### ERROR 19: to be able to reproduce analysis results, user must set random states for early stopping for GE and SR with set_ge_sr_random_states_early_stopping() method