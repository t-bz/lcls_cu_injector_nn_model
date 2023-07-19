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
* Load as LUME module: [load_lume_model.ipynb](load_lume_model.ipynb)
* Load as LUME module with EPICS PVs: [load_lume_model_epics.ipynb](load_lume_model_epics.ipynb)

## Notes about working with EPICS PV values

### OTR2 / OTR3

The surrogate model was trained on predictions for OTR2. However, the OTR2 screen has been broken for a number of months and is currently unavailable for measurements. OTR3 can be used instead as in theory, there shouldn't be too much difference between the two. There are a number of quadrupoles between the two but generally these aren't changed and should stay relatively consistent. The PV for OTR3 replaces 571 with 621 to become `OTRS:IN20:621:XRMS` etc.

We don't yet have a confirmed time for when OTR2 will be back in action.

### Unmeasured Input PVs

Some of the input features used as features of the model are not available in EPICS. These include:

#### `distgen:t_dist:length:value`

This is the **pulse length** within the simulation. There has been some discussion about creating a PV to record the pulse length but for now, a reference value of 1.8550514181818183 (PV units) or 3.06083484 (sim units) is used by default.

#### `L0B_scale:voltage`

As demonstrated by the train input min and max values in [model.json](info/model.json), this value was treated as a constant when training the surrogate model. However in reality, it's PV value `ACCL:IN20:400:L0B_ADES` shows a distribution of values. If it was to be used in the model for predictions, the error would increase dramatically and therefore any measured values from EPICS are overwritten by the value seen during training, scaled to PV units.

#### `distgen:total_charge:value`

As above, the **charge value** was constant in the training dataset but it's PV value `FBCK:BCI0:1:CHRG_S` shows a distribution of values. Measured values from EPICS are overwritten by the value seen during training, scaled to PV units.

#### `distgen:r_dist:sigma_xy:value`

The value for the **beam size** (r_dist) is not measured directly in EPICS but we do measure the XRMS and YRMS value of the beam. We use these PVs (`CAMR:IN20:186:XRMS` and `CAMR:IN20:186:YRMS`) to calculate a value for the beam size using the formula:

```python
r_dist = np.sqrt(data["CAMR:IN20:186:XRMS"].values ** 2 + data["CAMR:IN20:186:YRMS"].values ** 2)
```

We call this computed PV `CAMR:IN20:186:R_DIST`. Therefore, when pulling data from the archive, this step needs to be completed in any data processing.
