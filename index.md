---
title: MRI breathing-induced artifacts & accelerated imaging
description: An interactive book built with MyST
---

## About this book

Respiratory motion during MRI acquisition introduces artifacts in k-space that degrade image quality. This is particularly critical for brain and spinal cord imaging. This book aim to simulate different artifacts as motion corruption, rotation and breathing-induced motion corruption directly in k-space for accelerated MRI. Indeed, accelerated MRI is oine of the resarche subject in MRI field but comes also with it's proper artifact. This book aim to simulate alle this type of artifact and see how they interect togather.

Rather than working in image space, the model operates on complex k-space data (real + imaginary channels), which is more faithful to the actual acquisition process and allows correction before reconstruction.

:::{note}
Built with [MyST Markdown](https://mystmd.org): Markdown for the prose, Jupyter
notebooks for the computation, one `myst.yml` for the configuration, and a GitHub
Action that rebuilds and republishes on every push.
:::
