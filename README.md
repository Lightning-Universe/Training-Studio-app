# Lightning HPO

Lightning provides the most pythonic implementation for Scalable Hyperparameter Tuning.

This library relies on [Optuna](https://optuna.readthedocs.io/en/stable/) for providing state-of-the-art sampling hyper-parameters algorithms and efficient trial pruning strategies.

### Installation

```bash
git clone https://github.com/PyTorchLightning/lightning-hpo.git
pip install -e .
```

### How to use

The only provided classes are: `BaseObjective` and `Optimizer`.

```py
import optuna
from lightning_hpo import BaseObjective, Optimizer

class MyCustomObjective(BaseObjective):

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.best_model_path = None

    def on_after_run(self, result):
        self.best_model_score = float(result["best_model_score"])

    @staticmethod
    def distributions():
        return {"learning_rate": optuna.distributions.LogUniformDistribution(0.0001, 0.1)}


component = Optimizer(
    script_path=`{RELATIVE_PATH_TO_YOUR_SCRIPT}`,
    total_trials=100,
    simultaneous_trials=5,
    objective_work_cls=MyCustomObjective,
)
```

### Example

```bash
python -m lightning run app app.py
```


### Customize your HPO training with Optuna advanced algorithms

TODO [Hyperband paper](http://www.jmlr.org/papers/volume18/16-558/16-558.pdf)

```python
import optuna

Optimizer(
    study=optuna.create_study(
        direction="maximize",
        pruner=optuna.pruners.HyperbandPruner(
            min_resource=1, max_resource=n_train_iter, reduction_factor=3
    ),
)
```

```bash
python -m lightning run app app_hyperband.py --cloud
```

Learn more [here](https://optuna.readthedocs.io/en/stable/tutorial/10_key_features/003_efficient_optimization_algorithms.html?highlight=hyperband#activating-pruners)
