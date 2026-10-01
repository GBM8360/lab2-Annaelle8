---
title: Breathing-induced artifacts
kernelspec:
  name: python3
  display_name: Python 3
---

## Why simulate breathing?

In cartesian gradient echo (GRE) imaging, k-space is filled one $k_y$ line per TR. An image with $N_{ky}$ phase-encoding lines therefore takes about $N_{ky} \cdot TR$ seconds to acquire. 

The patient keeps breathing during this time, so successive $k_y$ lines are acquired at different moments of the respiratory cycle. During the respiratory cycle the anatomy move due to the expansion of the lungs, also created $B_0$ field inhomogenities.

The reconstruction assumes that every line describes the same object. When inconsistencies between lines appear as the object at a slightly different positions, it introduces motion ghosting artifacts in the reconstructed image.

## MRI acquisition simulation

We assume a GRE sequence with sequential cartesian acquisition where k-space is acquired line by line, with one line per TR from the top
of k-space to the bottom. The $n$-th $k_y$ line is acquired at [](#eqLineTime):

$$ t_n(k_y) = n \cdot TR + TE, \qquad n = 0, 1, \dots, N_{ky} - 1 $$ (eqLineTime)

:::{note} A deliberately simple model
This represents a simplified single-shot line-by-line acquisition model and does not reproduce an exact timing of a real GRE sequence. The objective of this model was to introduce temporally respiratory displacements across k-space in order to simulate breathing-related motion artifacts during image reconstruction, we do not simulate multi-coil acquisition and field inhomogeneity. This model correctly captures the key physical phenomenon: successive k-space lines are acquired at different respiratory phases, which is the source of motion artifacts.
:::

## Respiratory signal

We generate a physiologically realistic respiratory trace with [NeuroKit2](https://neuropsychology.github.io/NeuroKit/), using the `breathmetrics` method {cite:p}`Makowski2021`. Compared with a pure sinusoid, this trace has asymmetric inspiration and expiration, pauses and cycle-to-cycle variability. The trace is centred (zero mean) and normalised to $[-1, 1]$, then scaled by the motion amplitude $A$ [](#eqDisplacement):

$$ d(t) = A \cdot r(t), \qquad r(t) \in [-1, 1] $$ (eqDisplacement)

The signal is then sampled to exactly $N_{ky}$ points (one per $k_y$ line) using linear interpolation, so each line gets a displacement value $d_n = d(t_n)$ corresponding to its acquisition time.

## Breathing motion corruption

We choose two simulation parameters, the amplitude and frequency of the respiratory cycle, because it's theme that defined the respiratory cycle:

- **Motion amplitude A:** The motion amplitude `A` (in pixels, assuming 1 mm isotropic resolution) is chosen to span the range of respiratory spinal cord displacement measured *in vivo*. Studies have shown that breathing induces spinal cord displacements of up to 10 mm {cite:p}`Verma2014` in the antorior-posterior direction at 3T. At 1 mm/px resolution, this translates to A = 1–10 px.

- **Respiratory rate f:** The normal respiratory rate for adults at rest ranges from 12 to 20 breaths/min {cite:p}`Sapra2026`. 


## Fourier shift theorem
A spatial shift of $d$ pixels in the image domain along $y$ is equivalent to a linear phase modulation in k-space along $k_y$, the phase encoding direction [](#eqShift):

$$ \mathcal{F}\{I(x, y - d)\}(k_x, k_y) = \mathcal{F}\{I\}(k_x, k_y)\, e^{-i 2\pi k_y d} $$ (eqShift)

This property allows us to simulate motion efficiently without shifting the image for each $k_y$ line an recomputing a 2D FFT for each k-space line. Instead of shifting the image repeatedly, we apply a line-dependent phase ramp in k-space and perform a single inverse FFT.

The resulting k-space has a different phase on each line, which, after IFFT, produces the characteristic ghosting artifacts seen in motion-corrupted MRI.

## Breathing-induced artifacts simulation

The figure [](#respiration) below applies the model at the reference k-space.

:::{figure} #figRespiration
:label: respiration
Breathing-induced motion artifact. Top: respiratory displacement along $y$, sampled at
the acquisition time of each $k_y$ line. Bottom: k-space magnitude, reconstructed
magnitude and reconstructed phase. The sliders set the motion amplitude $A$ and the
respiratory rate $f$.
:::

:::{tip} Reading the figure
The top plot shows the simulated respiratory displacement (in pixels) over time. Each black dot is one acquired $k_y$ line, placed at the displacement the anatomy had when that line was acquired. The spread of the dots is what corrupts the image: the more
they vary from one line to the next, the stronger the ghosts.
:::

:::{tip} Things to try
- Set $A = 0$ to see the reference image, then increase it. The ghosts become more
  intense, but they stay concertrate inside the brain approximatively at the same position.
- Keep $A$ fixed and change $f$. The ghosts move in the PE direction.
:::


## Where do the ghosts appear?

For a periodic motion, the position of the ghosts can be predicted. Consider a
sinusoidal breathing pattern of rate $f$ and amplitude $A$,
$d(t) = A \sin(2\pi f t)$. Using the Jacobi–Anger expansion, the phase factor applied
to each line becomes

$$ e^{-i 2\pi k_y A \sin(2\pi f t_n)} = \sum_{m=-\infty}^{+\infty} J_m(2\pi k_y A)\, e^{-i 2\pi m f t_n} $$ (eqJacobiAnger)

where $J_m$ is the Bessel function of the first kind of order $m$. 

Since $t_n$ grows linearly with the line index, each term $e^{-i 2\pi m f t_n}$ is a linear phase ramp along $k_y$. By the shift theorem, it produces a copy of the object translated along $y$. The intensity of the ghosts is governed by $J_m(2\pi k_y A)$, which grows with the amplitude $A$.

This relation summarise the physics of the figure below:

- the amplitude $A$ sets how much intense leaks into the ghosts;
- the respiratory rate $f$ and the acquisition time $t_n$ set where the ghosts appear.

A real respiratory trace is not a perfect sinusoid, so its ghosts are less sharply defined but still behaves the same way on average.

## Limitations

This model captures the main mechanism of breathing ghosts, but it simplifies reality
in several ways:

- **Rigid translation and in-plane motion only.** The whole image moves as one block. In vivo, the chest and
  abdomen move much more than the other tissues. Especially true for the brain which further from the lungs than the spinal cord for instance not mooving just B_0 field inhomogenities.
- **No field changes.** Breathing also changes the magnetic field $B_0$, because air in the lungs has a different magnetic susceptibility from tissue.
  These fluctuations add a phase error to each line, even when the tissus (brain or spinal cord) itself barely moves. In spinal cord imaging they are a major source of respiratory artifacts {cite:p}`Verma2014`, and could be added to this model as an extra phase term per line.
- **Sequential ordering.** Real sequences may use other orderings, such as centric or interleaved, or respiratory gating, which redistribute or suppress the ghosts.
