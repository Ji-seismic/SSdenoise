# Dip-aligned self-supervised seismic denoising

Code and releasable swell-noise examples for "Dip-aligned structural constraints for suppression of partially coherent seismic noise using self-supervised learning".

This repository contains only the main method: the attention-enhanced structured blind-spot network and adaptive dip-aligned second-order directional regularization. Benchmark methods and the company-owned OVT/scattered-noise dataset are excluded. The latter cannot be redistributed.

## Run

Use Python 3.12. Install PyTorch appropriate for your CUDA installation, then:

```sh
python -m pip install -r requirements.txt
python -m jupyterlab
```

Open and run all cells of a notebook. CUDA is recommended; CPU is supported but full-section training is expensive. Each example trains from scratch on its noisy observation; pretrained weights are not needed.

| Notebook | Input | Settings retained from experiment |
| --- | --- | --- |
| syn_swell/synthetic.ipynb | data.mat: d, dn (640 x 640) | 400 epochs; lr 0.001; TV maximum 2; ramp 300; first-order PWD |
| syn_swell/curved.ipynb | data_2.mat: d, dn (992 x 480) | 400 epochs; lr 0.001; TV maximum 4; ramp 300; second-order PWD |
| real_swell/real.ipynb | d_swell_real.mat: dbp, first 288 traces (1536 x 288) | 300 epochs; lr 0.001; TV maximum 0.5; ramp 200; first-order PWD |

All use seed 42, a 50-epoch warmup, dip updates every 50 epochs, and six PWD iterations. Data are transposed for the network; saved results are in time-by-trace orientation. Both network dimensions must be multiples of 32. Outputs are written into each example's outputs directory.

## Reproduction details

The extracted network and training routines retain the original experiment behavior. Synthetic outputs select the maximum clean-reference SNR across training epochs. The clean data are never used in the optimization loss, but this best-epoch selection is an oracle evaluation choice. Real-data outputs select the minimum total loss, including its changing regularization weight. Predictions are captured before the corresponding optimizer step, as in the source notebooks. Hardware and package versions can affect numerical reproducibility.

The real input uses the existing dbp array without additional normalization. The synthetic notebook retains the original normalization order (the clean data already have unit peak amplitude). The curved example is trained without amplitude normalization.

data_manifest.json records SHA-256 hashes of the original data files. The real MAT file contains both d and dbp; only dbp is used by the example. The author has confirmed that this real swell dataset can be redistributed.

## Attribution

The shift/crop/convolution building blocks retain code originating from Nick Luiken and Matteo Ravasi, KAUST (2022), as credited in the original project. No new redistribution license is asserted here; upstream licensing and the author's code/data licensing must be resolved before public release.
