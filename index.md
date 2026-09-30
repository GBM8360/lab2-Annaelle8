---
title: MRI breathing-induced artifacts & accelerated imaging
description: An interactive book built with MyST
---

## About this book

Patient motion during an MRI acquisition corrupts k-space and degrades image quality.
This is particularly critical in brain and spinal cord imaging, where the structures of
interest are small.

This book simulates the most common sources of artifacts directly in k-space, in a cartesian gradient echo (GRE) acquisition:

- rigid head motion: modelled as an in-plane rotation occurring during the scan;
- breathing-induced motion: modelled as a periodic translation driven by a realistic respiratory signal;
- undersampling: used in accelerated MRI to shorten the acquisition.

Accelerated MRI is an active research topic: it reduces scan time and therefore the opportunity for motion, but it introduces artifacts of its own. The last part of the book combines all three effects to show how they interact.

:::{tip} Every figure is interactive
Move the sliders to change the rotation angle, the motion amplitude, the breathing rate, or the acceleration factor, and see the effect on k-space and on the reconstructed image.
:::

:::{note}
Built with [MyST Markdown](https://mystmd.org): Markdown for the prose, Jupyter
notebooks for the computation, one `myst.yml` for the configuration, and a GitHub
Action that rebuilds and republishes on every push.
:::
