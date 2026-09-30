---
title: Motion artifact
kernelspec:
  name: python3
  display_name: Python 3
---

## Head motion

Patient motion is one of the most common sources of artifacts in MRI. Because k-space is acquired over time, a moving patient does not generate a consistent k-space throughout the acquisition. Instead, different portions of k-space may correspond to different positions or orientations of the anatomy.

Motion can involve rotations and translations along different axes. These movements may occur simultaneously and can vary in amplitude and timing, leading to complex complex artifact in the reconstructed image.


## Effect of rotation on k-space

A rotation of the object in the image domain results in a corresponding rotation of its k-space representation by the same angle.

Therefore, if the patient’s head rotates during the acquisition, the k-space lines acquired before and after the movement correspond to different head positions. Combining these lines produces a k-space that is no longer consistent with the initial object configuration [](#headMotion).

:::{figure} #figHeadMotion
:label: headMotion
Simulation of an abrupt in-plane head rotation during k-space acquisition. The first slider controls the position in k-space, and therefore the acquisition time, at which the motion occurs. The second slider controls the rotation angle, from $0^\circ$ to $25^\circ$.
:::

:::{tip} Reading the figure
The top plot shows the head rotation angle (in degrees) during the acquisition. The step marks the moment the head turns, and each black dot is one acquired $k_y$ line. On the k-space, the red line shows the same moment: lines above it were acquired before the rotation, lines below it after.
:::

The reconstructed image combines information from the original and rotated head positions. Because these k-space lines are not mutually consistent, the reconstruction contains motion-induced artifacts. The effect is strongly dependent on the position of the motion within the acquisition. When the motion occurs late in the acquisition, most of the k-space may already have been acquired from the original position, so the rotation of the brain may be less or no apparent in the magnitude image. Conversely, when the motion occurs early, a larger fraction of k-space represents the rotated position, making the rotation more visible in the magnitude image.

The motion also affects the phase image. Because k-space data are complex-valued, combining data acquired from different head positions introduces phase inconsistencies. These inconsistencies produce spatially structured phase variations even when the corresponding anatomical displacement is no or less apparent in the magnitude image.

## Simulation of head motion

To illustrate this effect, we simulate an abrupt in-plane rotation during the acquisition.

At a given point during the acquisition, the kspace is rotated by an angle $\theta$, by multiplying each sample real and imaginary part by this angle. The final k-space is computing by keeping the k-space lines acquired before the motion from the original head position, while the lines acquired after the motion are replaced by the corresponding lines from the rotated position kspace.

The resulting k-space therefore contains data acquired from two different head positions.

Finally, an IFFT is applied to this modified k-space to reconstruct the image.


:::{note}
This simulation uses an abrupt rotation. Real MRI motion artifacts can be different because the patient can move several time during on acquisition with differnet angle.
:::