# Team-selected best inference notebook

This folder publishes the inference notebook selected by the team as its best candidate. 
## Model and fusion configuration

| Branch | Contents | Weight |
|---|---|---:|
| Team | DINOv2-Small (3 seeds), EfficientNetV2-S (4 seeds), DINOv3-distilled ConvNeXt-Tiny (4 folds), newconv ConvNeXt-Tiny (4 folds); four family probabilities averaged equally inside the branch | 70% |
| Max | Max's all-window CoAtNet consensus bundle | 21% |
| R7 | Heliosli R7, arm `E_lrprior` | 9% |

The DINOv2 weight package contains seeds 42, 43, 44, and 45. The final inference uses only seeds **42, 43, and 44**; seed 45 is not used in the ensemble.

TTA follows the notebook configuration: DINOv2 seed42 and EfficientNetV2-S seed42 use -12°/0°/+12°; remaining seeds use 0°. DINOv3 uses only 0°. All four newconv folds use -12°/0°/+12°. The selected Team branch does not include S6, TinyViT, or RadImageNet ResNet50.

The final stage converts each branch's per-label predictions to percentile ranks, applies the configured weighted noisy-OR rank blend, then applies the sibling corrections:

- Lateral OA: subtract 0.10 × Medial OA rank.
- PF OA: subtract 0.10 × Medial OA rank.
- Synovitis: subtract 0.20 × Effusion rank.

If a branch fails for a study, its contribution is omitted for that study and the remaining configured weights are renormalized. The notebook writes `submission.csv`; it does not submit the file to Kaggle.

## Kaggle Input dependencies

Attach the following inputs in the Kaggle notebook:

- Competition data: `/kaggle/input/competitions/rsna-knee-abnormality-detection`
- DINOv2 seeds 42–44: `/kaggle/input/datasets/lihu6666/3seed-rsna-dinov2s-392-oai-mixdrop-45/rsna_dinov2s_392_oai_mix_unfreeze9`
- EfficientNetV2-S seeds 42–45: `/kaggle/input/datasets/lihu6666/4seed-rsna-effv2s-384-oai8649-mix58/rsna_effv2s_384_oai8649_mix58_42_seven`
- DINOv3 ConvNeXt-Tiny folds 0–3: `/kaggle/input/datasets/lihu6666/dinov3-convnext-tiny-lihu-of-fork-of-rsna-cd4e8a/rsna_dinov3_convnext_tiny_384_oai8649_mix58_42_seven_16ep_splitlr`
- newconv fold0/1: `/kaggle/input/datasets/lihu6666/convnext-tiny-384-oai8649-mix58-42-4fold/convnext_tiny_384_oai8649_mix58_42 fold12/rsna_convnext_tiny_384_oai8649_mix58_42_seven_graycopy24`
- newconv fold2/3: `/kaggle/input/datasets/lihu6666/convnext-tiny-384-oai8649-mix58-42-4fold/convnext_tiny_384_oai8649_mix58_42 fold23/rsna_convnext_tiny_384_oai8649_mix58_42_seven_graycopy24`
- Max bundle: `/kaggle/input/datasets/max6296/knee-coatnet-consensus-all-ftall-w56-bundle`
- R7 model package: `/kaggle/input/datasets/heliosli/rsna-knee-compact-r7cnxfsss`
- R7 inference/code package: `/kaggle/input/datasets/heliosli/rsna-orthofoundation-fold0-epoch7-20261004`

The R7 configuration may request the OFL-L initialization-weight dataset if the selected bundle manifest uses that backbone. Do not attach training caches or label CSVs for inference; this notebook reads test DICOMs and creates its inference cache under `/kaggle/temp` and `/kaggle/working`.

## Vendored code and weights

The Team runner and the Max/R7 orchestration are present in the notebook. At runtime, the Max bundle supplies its `src` package and optional offline wheelhouse, while the R7 datasets supply the compact model package and `cloud_code`. Those third-party code/weight assets are not copied into this repository: their Kaggle Input contents were not present in the local workspace, and their redistribution terms were not verified. The notebook retains the fixed Kaggle paths and fails with a clear missing-input error if a required dataset is not mounted.

## Validation status

Notebook JSON and Python syntax were checked locally. Full inference was not run here because the Kaggle competition DICOMs, GPU, and mounted checkpoint/code datasets are available only in the Kaggle runtime. The original Notebook is left untouched; this repository copy has no saved outputs.


## License and third-party terms

The team's original contributions in this repository are licensed under [CC BY-NC 4.0](LICENSE), to the extent the team has rights to license them. Third-party code, models, and weights remain subject to their own licenses. In particular, DINOv3-based checkpoints are subject to the [DINOv3 License](third_party/DINOv3_LICENSE.md), which must accompany distributions where applicable. The Kaggle dataset license field does not replace upstream terms.
