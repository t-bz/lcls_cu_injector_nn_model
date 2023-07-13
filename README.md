# lcls_cu_injector_nn_model
Stores the files corresponding to the LCLS Cu injector NN surrogate model and example notebooks illustrating how to load and use the model.

## Model Description
The model was trained by Auralee to predict beam properties at OTR2 using injector PVs for LCLS. See the examples below for more information.

## Environment
```shell
conda env create -f environment.yml
```

## Examples
* Load information: [load_info.ipynb](load_info.ipynb)
* Load as PyTorch module: [load_torch_model.ipynb](load_torch_model.ipynb)
* Load as LUME module: [load_lume_model.ipynb](load_torch_model.ipynb)
