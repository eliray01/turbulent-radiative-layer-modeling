# Modeling Turbulent Radiative Layer Dynamics Using Neural Operators

Forecasting the dynamics of `turbulent_radiative_layer_2D` with neural network models for physical fields. Given four previous time steps, the model predicts the next system state; quality is evaluated on both single-step prediction and 4-step autoregressive rollout.

## Visual rollout comparison

GIF animations of rollout for the `density` field on the same trajectory.

### FNO

| Baseline (FNO) | Delta_32_4 | Delta_64_4 | Full_64_4 |
| --- | --- | --- | --- |
| ![](plot_gifs_out/FNO/delta_gifs/baseline_model/traj000_density_pretrained_delta/sequence.gif) | ![](plot_gifs_out/FNO/delta_gifs/delta_32_4/traj000_density_delta_last_delta_model/sequence.gif) | ![](plot_gifs_out/FNO/delta_gifs/delta_64_4/traj000_density_delta_final_model_delta/sequence.gif) | ![](plot_gifs_out/FNO/full_gifs/full_64_4/traj000_density_full_final_model_full_frame/sequence.gif) |

Additional GIFs: `plot_gifs_out/FNO/delta_gifs/`, `plot_gifs_out/FNO/full_gifs/`

### ConvNextU-Net

| ConvNextU-Net_4_2_1_42_7 | ConvNextU-Net_4_2_1_48_7 | ConvNextU-Net_delta_4_2_1_42_7 |
| --- | --- | --- |
| ![](plot_gifs_out/U-net/full_gifs/unet_full_4_2_1_42_7/traj000_density_cnextunet_full_best_cnextunet_by_valid_rollout_vrmse/sequence.gif) | ![](plot_gifs_out/U-net/full_gifs/unet_full_4_2_1_48_7/traj000_density_cnextunet_full_best_cnextunet_by_valid_rollout_vrmse/sequence.gif) | ![](plot_gifs_out/U-net/delta_gifs/unet_delta_4_2_1_42_7/traj000_density_cnextunet_delta_best_cnextunet_delta_by_valid_rollout_vrmse/sequence.gif) |

Additional GIFs: `plot_gifs_out/U-net/delta_gifs/`, `plot_gifs_out/U-net/full_gifs/`

## Authors

- Student: Emil Fakhretdinov
- Advisor: Ruslan Kayumov

## Problem Setup

| Parameter | Value |
| --- | --- |
| Dataset | [`turbulent_radiative_layer_2D`](https://polymathic-ai.org/the_well/datasets/turbulent_radiative_layer_2D/) ([The Well](https://polymathic-ai.org/the_well/)) |
| Fields | `density`, `pressure`, `velocity_x`, `velocity_y` |
| Model input | 4 previous time steps |
| Evaluation modes | 1-step prediction, 4-step autoregressive rollout |
| Normalization | Z-score |

## Approach

Two architectural families are compared:

- **FNO** (Fourier Neural Operator) — baseline and a series of improved configurations with varying numbers of modes and layers
- **ConvNextU-Net** — convolutional architecture based on ConvNeXt blocks

For both architectures, two prediction modes are explored:

- **delta** — the model predicts the increment relative to the last input frame
- **full-frame** — the model predicts the full next state

Best model in this project: **`ConvNextU-Net_4_2_1_48_7`** (full-frame).

## Results

### Summary

| Metric | Baseline (FNO) | Best FNO | Best ConvNextU-Net |
| --- | --- | --- | --- |
| 1-step VRMSE ↓ | 0.5121 | 0.2799 (`Delta_64_4`) | **0.2173** (`ConvNextU-Net_4_2_1_48_7`) |
| 4-step rollout VRMSE ↓ | 1.6885 | 0.3411 (`Delta_32_4`) | **0.2905** (`ConvNextU-Net_4_2_1_48_7`) |

Compared to the best FNO configuration, ConvNextU-Net achieves ~22% improvement on 1-step VRMSE and ~15% on 4-step rollout. Compared to the baseline — ~58% and ~83% respectively.

For FNO, delta variants performed best; for ConvNextU-Net — full-frame. This suggests the architectures use temporal context differently.

### 1-step prediction

Sources: `results/FNO/Model Comparison (1-Step Prediction).csv`, `results/U-net/Model Comparison (1-Step Prediction).csv`

| Model | VRMSE ↓ | Rel L2 ↓ | R2 ↑ | RMSE ↓ |
| --- | --- | --- | --- | --- |
| Baseline (FNO) | 0.5121 | 0.5114 | 0.7207 | 0.5227 |
| Delta_16_4 | 0.2896 | 0.2892 | 0.9096 | 0.2957 |
| Delta_32_4 | 0.2816 | 0.2812 | 0.9137 | 0.2876 |
| Full_16_4 | 0.3090 | 0.3086 | 0.8969 | 0.3155 |
| Full_32_4 | 0.2997 | 0.2993 | 0.9019 | 0.3061 |
| Full_32_5 | 0.3079 | 0.3075 | 0.8964 | 0.3145 |
| Full_64_4 | 0.2973 | 0.2969 | 0.9031 | 0.3037 |
| Delta_64_4 | 0.2799 | 0.2795 | 0.9144 | 0.2859 |
| ConvNextU-Net_delta_4_2_1_42_7 | 0.2491 | 0.2487 | 0.9311 | 0.2545 |
| ConvNextU-Net_4_2_1_42_7 | 0.2221 | 0.2219 | 0.9463 | 0.2268 |
| ConvNextU-Net_4_2_1_48_7 | **0.2173** | **0.2170** | **0.9482** | **0.2220** |

### 4-step autoregressive rollout

Sources: `results/FNO/Model Comparison (Rollout 4-step).csv`, `results/U-net/Model Comparison (Rollout 4-step).csv`

| Model | VRMSE ↓ | Rel L2 ↓ | R2 ↑ | RMSE ↓ | MAE ↓ |
| --- | --- | --- | --- | --- | --- |
| Baseline (FNO) | 1.6885 | 1.6864 | -2.1406 | 1.7192 | 0.5966 |
| Delta_16_4 | 0.3483 | 0.3478 | 0.8696 | 0.3552 | 0.1376 |
| Delta_32_4 | 0.3411 | 0.3406 | 0.8741 | 0.3479 | 0.1307 |
| Full_16_4 | 0.3624 | 0.3618 | 0.8588 | 0.3695 | 0.1520 |
| Full_32_4 | 0.3550 | 0.3544 | 0.8638 | 0.3620 | 0.1390 |
| Full_32_5 | 0.3594 | 0.3588 | 0.8604 | 0.3665 | 0.1436 |
| Full_64_4 | 0.3522 | 0.3517 | 0.8658 | 0.3592 | 0.1341 |
| Delta_64_4 | 0.3416 | 0.3411 | 0.8734 | 0.3484 | 0.1269 |
| ConvNextU-Net_delta_4_2_1_42_7 | 0.3198 | 0.3193 | 0.8889 | 0.3263 | 0.1153 |
| ConvNextU-Net_4_2_1_42_7 | 0.2912 | 0.2908 | 0.9081 | 0.2971 | 0.1084 |
| ConvNextU-Net_4_2_1_48_7 | **0.2905** | **0.2901** | **0.9083** | **0.2963** | **0.1071** |

### Per-field comparison: 1-step VRMSE

Sources: `results/FNO/Per-field Comparison (1-Step).csv`, `results/U-net/Per-field Comparison (1-Step).csv`

| Model | density | pressure | velocity_x | velocity_y |
| --- | --- | --- | --- | --- |
| Baseline (FNO) | 0.3346 | 0.6925 | 0.4643 | 0.5568 |
| Delta_16_4 | 0.1551 | 0.3681 | 0.3028 | 0.3323 |
| Delta_32_4 | 0.1423 | 0.3624 | 0.2954 | 0.3262 |
| Full_16_4 | 0.1661 | 0.3979 | 0.3207 | 0.3513 |
| Full_32_4 | 0.1502 | 0.3939 | 0.3110 | 0.3436 |
| Full_32_5 | 0.1547 | 0.4044 | 0.3181 | 0.3545 |
| Full_64_4 | 0.1459 | 0.3908 | 0.3085 | 0.3438 |
| Delta_64_4 | 0.1381 | 0.3595 | 0.2954 | 0.3267 |
| ConvNextU-Net_delta_4_2_1_42_7 | 0.1083 | 0.3190 | 0.2703 | 0.2987 |
| ConvNextU-Net_4_2_1_42_7 | 0.1092 | 0.2727 | 0.2423 | 0.2644 |
| ConvNextU-Net_4_2_1_48_7 | **0.1021** | **0.2714** | **0.2371** | **0.2587** |

### Per-field comparison: 4-step rollout VRMSE

Sources: `results/FNO/Per-field Comparison (Rollout).csv`, `results/U-net/Per-field Comparison (Rollout).csv`

| Model | density | pressure | velocity_x | velocity_y |
| --- | --- | --- | --- | --- |
| Baseline (FNO) | 1.9597 | 1.4643 | 0.9464 | 2.3837 |
| Delta_16_4 | 0.1929 | 0.4444 | 0.3532 | 0.4027 |
| Delta_32_4 | 0.1818 | 0.4391 | 0.3478 | 0.3958 |
| Full_16_4 | 0.2014 | 0.4659 | 0.3657 | 0.4165 |
| Full_32_4 | 0.1929 | 0.4615 | 0.3562 | 0.4093 |
| Full_32_5 | 0.1942 | 0.4653 | 0.3616 | 0.4164 |
| Full_64_4 | 0.1903 | 0.4588 | 0.3522 | 0.4076 |
| Delta_64_4 | 0.1800 | 0.4440 | 0.3465 | 0.3960 |
| ConvNextU-Net_delta_4_2_1_42_7 | 0.1656 | 0.4084 | 0.3259 | 0.3794 |
| ConvNextU-Net_4_2_1_42_7 | 0.1503 | 0.3658 | 0.3037 | 0.3452 |
| ConvNextU-Net_4_2_1_48_7 | **0.1478** | **0.3676** | **0.3047** | **0.3419** |

### Error growth over rollout horizon

Sources: `results/FNO/Error Growth (Rollout Stability).csv`, `results/U-net/Error Growth (Rollout Stability).csv`

| Model | Step 1 | Step 2 | Step 3 | Step 4 |
| --- | --- | --- | --- | --- |
| Baseline | 0.5098 | 0.7238 | 1.1606 | 2.9894 |
| Delta_16_4 | 0.2870 | 0.3337 | 0.3670 | 0.3947 |
| Delta_32_4 | 0.2787 | 0.3260 | 0.3598 | 0.3885 |
| Full_16_4 | 0.3065 | 0.3475 | 0.3793 | 0.4070 |
| Full_32_4 | 0.2968 | 0.3398 | 0.3727 | 0.4005 |
| Full_32_5 | 0.3048 | 0.3454 | 0.3762 | 0.4022 |
| Full_64_4 | 0.2942 | 0.3378 | 0.3692 | 0.3976 |
| Delta_64_4 | 0.2770 | 0.3258 | 0.3607 | 0.3906 |
| ConvNextU-Net_delta_4_2_1_42_7 | 0.2473 | 0.3014 | 0.3409 | 0.3735 |
| ConvNextU-Net_4_2_1_42_7 | 0.2209 | 0.2701 | 0.3103 | 0.3466 |
| ConvNextU-Net_4_2_1_48_7 | **0.2160** | **0.2694** | **0.3109** | **0.3471** |

## Conclusions

`ConvNextU-Net_4_2_1_48_7` (full-frame) outperforms all tested FNO configurations across every key metric — on both 1-step prediction and 4-step rollout, including per-field analysis across all four fields. The model also shows the smallest error growth as the rollout horizon increases.

## Repository structure

```
msc_hse/
├── dataset.ipynb              # data loading and sample preparation
├── data_explore.ipynb         # EDA and trajectory visualization
├── models_eval.ipynb          # model and metric comparison
├── evaluate.py                # pretrained FNO baseline evaluation
├── autoregressive_pretrained_fno.py   # FNO rollout and visualization
├── autoregressive_cnextunet.py        # ConvNextU-Net rollout and visualization
├── training_scripts/
│   ├── train_fno_delta.py
│   ├── train_fno_full_frame.py
│   ├── train_cnextunet_delta.py
│   └── train_cnextunet_full_frame.py
├── plot_functions/
│   ├── plot_metrics.py
│   └── plot_folder_gifs.py
├── results/
│   ├── FNO/                   # FNO metric tables
│   └── U-net/                 # ConvNextU-Net metric tables
├── plot_gifs_out/             # rollout GIF animations
└── models_trained/            # saved model weights
```

## Quick start

### Installation

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

The dataset is downloaded automatically via [`the_well`](https://github.com/PolymathicAI/the_well) on the first run of training or evaluation scripts. By default, data is saved to `./data/`.

### Training

```bash
# FNO
python training_scripts/train_fno_delta.py
python training_scripts/train_fno_full_frame.py

# ConvNextU-Net
python training_scripts/train_cnextunet_delta.py
python training_scripts/train_cnextunet_full_frame.py
```

### Rollout and visualization

```bash
# FNO (including pretrained baseline)
python autoregressive_pretrained_fno.py

# ConvNextU-Net (best model)
python autoregressive_cnextunet.py --trained-model-dir models_trained/unet_full_4_2_1_48_7
```

### Baseline evaluation

```bash
python evaluate.py
```

For detailed result analysis, see the notebooks `dataset.ipynb`, `data_explore.ipynb`, and `models_eval.ipynb`.
