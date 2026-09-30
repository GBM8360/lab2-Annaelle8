---
title: Fast Gradient echo (GRE) imaging
kernelspec:
  name: python3
  display_name: Python 3
---


## Accelerated imaging

In practice, these effects do not occur in isolation. During a single MRI acquisition, the patient may breathe or move while the k-space is also being undersampled. These effects can therefore occur simultaneously and interact with one another, producing more complex artifact patterns [](#combined).

:::{figure} #figCombined
:label: combined
Combined simulation of respiratory motion, abrupt head rotation, and k-space undersampling. Each slider controls one of the effects independently. The parameter grids are smaller than in the previous figures because all combinations are precomputed.
:::

When several sources of corruption are combined, their effects appear simultaneously in the reconstructed image. For example, respiratory motion can introduce motion-related artifacts, head rotation can produce ghosting or spatial inconsistencies, and undersampling can produce aliasing artifacts. The resulting image can therefore contain several interacting artifact patterns.

This illustrates the complexity of an MRI acquisition: image quality depends not only on the imaging sequence and acquisition parameters, but also on the patient’s motion and the way k-space is sampled.

## Other considerations

Other physical and technical factors can also affect MRI acquisitions, including noise, B_0 inhomogeneities, and multi-coil signal reception. These effects can interact with motion and sampling artifacts and further affect the reconstructed image.

For instance, noise in MRI receiver coils is well modeled as additive complex Gaussian noise in k-space, with independent contributions to the real and imaginary components [](#noise):

$$ \tilde{K} = K + \sigma ( \mathcal{N}_\text{real} + j \mathcal{N}_\text{imag} ) $$ (noise)

This assumption is standard in MRI physics and holds prior to magnitude reconstruction.