# HyperFast : Instant Classification for Tabular Data

[HyperFast](https://openreview.net/forum?id=VRBhaU8IDz) is a hypernetwork designed for fast classification of tabular data, capable of scaling to large datasets. Utilizing a meta-trained hypernetwork, HyperFast generates a dataset-specific target network in a single forward pass, eliminating the need for time-consuming model training.


## Installation
:exclamation: Install the new release:

HyperFast can be installed from PyPI using

```bash
pip install hyperfast==1.0.0
```

This package contains example code to run HyperFast. Ensure that you are using Python 3.9 or later to run this project.
When using HyperFast for the first time, the model weights will be downloaded automatically. The model weights are also available [here](https://figshare.com/articles/software/hyperfast_ckpt/24749838). 



## Usage

Here's a quick example on how to use HyperFast's scikit-learn-like interface:

```python
import torch
import numpy as np
from hyperfast import HyperFastClassifier
from sklearn.metrics import accuracy_score

# Load your dataset
X_train, y_train = np.load("data/hapmap1_X_train.npy"), np.load( "data/hapmap1_y_train.npy")
X_test, y_test = np.load("data/hapmap1_X_test.npy"), np.load("data/hapmap1_y_test.npy")

# Set the device
device = torch.device('cuda:0' if torch.cuda.is_available() else 'cpu')

# Initialize HyperFast
model = HyperFastClassifier(device=device)

# Generate a target network and make predictions
model.fit(X_train, y_train)
predictions = model.predict(X_test)

accuracy = accuracy_score(y_test, predictions)
print(f"Accuracy: {accuracy * 100:.2f}%")
```


**Warning**: 
* Set ``cat_features`` as the list of indices of the categorical features in the dataset (if any), now in ``__init__``.
* The current default hyperparameters provide medium speed-accuracy performance.
* For the fastest inference (but less accurate) set ``n_ensemble=1`` and ``optimization=None``.
* For slower but most accurate predictions, optimize the parameters of HyperFast for each dataset. In this case, we recommend the following search space:

```python
param_grid = {
    'n_ensemble': [1, 4, 8, 16, 32],
    'batch_size': [1024, 2048],
    'nn_bias': [True, False],
    'optimization': [None, 'optimize', 'ensemble_optimize'],
    'optimize_steps': [1, 4, 8, 16, 32, 64, 128],
    'seed': list(range(1, 10))
}
```

## License

This project is under the CC BY-NC 4.0 license. See [LICENSE](LICENSE) for details.


## Cite us

If you use HyperFast in your research, please cite our [paper](https://openreview.net/forum?id=VRBhaU8IDz):

```latex
@inproceedings{bonet2023hyperfast,
  title={HyperFast: Instant Classification for Tabular Data},
  author={Bonet, David and Montserrat, Daniel Mas and Gir{\'o}-i-Nieto, Xavier and Ioannidis, Alexander},
  booktitle={NeurIPS 2023 Second Table Representation Learning Workshop},
  year={2023}
}
```
