# Profiling Analyzer

**ProfilingAnalyzer** class provides a full implementation of [Efficient Attacker Framework](https://cardis2021.its.uni-luebeck.de/papers/CARDIS2021_Picek.pdf).
This feature allows a more detailed evaluation of a neural network behavior for profiling side-channel analysis.

In profiling SCA, the evaluator or the implementer want to evaluate the security of a target device against a
strong adversary. When using a deep neural network as profiling model, one wants to understand how correct is the profiling
model. There are multiple objectives in this analysis:

- **Understanding overfitting**: to understand if a profiling model is actually overfitting due to small 
  amount of profiling traces used for training, it is necessary to evaluate the model for different amounts
  of profiling traces.
- **Understanding generalization of the model**: one way to verify the generalizing level of a profiling model is by 
  predicting multiple attack sets with the model. For all attack sets, guessing entropy is evaluated. To verify how what is 
  the model generalization, it is important to train this model for multiple amounts of profiling traces.
- **Impact of hyperparameters/learnability**: neural networks can be configured with infinite amount of hyperparameters combinations.
In a profiling SCA, it is important to verify how the search for different models impacts the attack performance (overfitting and 
  generalization).




