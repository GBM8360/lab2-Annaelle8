---
title: Cartesian undersampling
kernelspec:
  name: python3
  display_name: Python 3
---

## Clinical motivation

Acquiring all $k_y$ lines is time-consuming. Accelerated MRI does not acquired all the k-space lines to reduce the acquisition time by a factor $R$ (the acceleration factor), $R$ = 2, 4, 8 corresponds to acquiring around 50%, 25% and 13% of k-space lines respectively, covering the typical acceleration factors used in clinical accelerated MRI protocols. The missing line must then be recovered by the reconstruction algorithm. 

## Mask design
We use a standard cartesian undersampling mask along the phase readout $(k_y)$ direction {cite:p}`Griswold2002`:
- **Central k-space lines are always acquired:** they carry ~90% of the image energy and cannot be sacrificed. There are essential for stable reconstruction. They are called Autoalibration Signal (ACS)
- **Outer lines are randomly subsampled** at rate $1/R$ introduces incoherent aliasing

### Effect
Zeroing out k-space lines creates aliasing artifacts in the reconstructed image only correctable with knowledge of the acquisition geometry ([](#undersampling)).

:::{figure} #figUndersampling
:label: undersampling
Undersampling by a factor of $R$ = 1, 2, 4 ou 8 with a full acquuired center (ACS), reconstructed by zero-filling.
:::

:::{tip} Reading the figure
There is no motion here, so the top plot only shows the acquired $k_y$ lines as black dots along the time axis. As $R$ increases, fewer lines are acquired and the dots stop earlier: the acquisition time is shorter, you can see its value above the top plot. On the k-space, the missing lines appear as black rows, while the central band (ACS) is always fully sampled.
:::

The k-space was downsampled by a factor $R$ in the phase encoding direction by keeping every $R$ k-space line. This increases the sampling interval ${\Delta k}$ in that direction. Since $FOV = \frac{1}{\Delta k}$, the FOV is reduced by a factor $R$ in the downsampled direction. However, $k_{max}$ remains unchanged, so the spatial resolution is preserved. The magnitude image has therefore a smaller FOV with aliasing artefact (wrap-around). The resulting magnitude and phase images show aliasing artifacts due to the undersampling k-space

## Reconstruction

Sampling occur in an MRI acquisition when using an fast imaging sequence to reduce the acquisition time. If the undersampled k-space is directly reconstructed using an IFFT, the resulting image will contain aliasing artifacts as shown before. But reconstruction techniques such as GRAPPA or SENSE can be used to interpolate the missing k-space line allowing to approximately recover the reference image. 

:::{warning} Calculated $\neq $ Acquired
The image reconstruct using advanved technique to compute the missing information will never be  bitwise identical to a fuuly sampled acquisition of the same slice since the missing information are calculated and not acquired.
:::



