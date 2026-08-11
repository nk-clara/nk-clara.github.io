---
layout: default
title: Diffraction Synthesizer 
---

# Diffraction Synthesizer: The Sounds of Fraunhofer Diffraction

** add image**

## Overview

While studying diffraction, I learned that Fraunhofer diffraction can be mathematically described by a Fourier transform, an operation which is used to break any function down into its constituent frequencies. I was intrigued, since I had previously only encountered Fourier transforms as method for audio signal processing, i.e. breaking down sounds into their constituent frequencies. This provoked me to think about what parallels could be drawn between optical and audio spectra, which ultimately led to the Diffraction Synthesizer.

In this project, I aimed at creating an instrument in Python that could sonify diffraction patterns by using their unique shapes to modulate a pure tone and produce various timbres. Throughout, I also sought to familiarise myself with the fundamentals of Fourier transforms, FFTs, and signal processing in Python.



## Background

If you, like me, are interested in both music and physics, you will probably have heard that the timbre, or unique sound, of synthesizers can be altered by modulating a pure tone.

<img width="445" height="225" alt="telecom-principles-0116" src="https://github.com/user-attachments/assets/dd0ed849-4fdc-42bb-84e4-39bfab425039" />

*Source: https://www.technologyuk.net/telecommunications/telecom-principles/amplitude-modulation.shtml*

The key idea behind this Synth is to perform amplitude modulation on musical notes, where the carrier signal is a pure sine and the modulating signal is based on the amplitude pattern produced when light diffracts through a specific aperture. Here, "amplitude pattern" refers to the electric field amplitude as a function of position along the observation screen. I chose the amplitude pattern instead of the intensity pattern as the basis for this modulating signal, as it produces clearer differences in timbre. (Since $|E|^2 \propto I $, the intensity pattern drops much more quickly).

<img width="445" height="225" alt="Screenshot 2026-08-11 at 09 38 20" src="https://github.com/user-attachments/assets/c9a7d96c-0e46-48a1-acbe-db6ebea2ae59" />
<img width="445" height="225" alt="Screenshot 2026-08-11 at 09 38 34" src="https://github.com/user-attachments/assets/14ac248e-fc85-4333-a043-91286086b929" />

## Determining the amplitude pattern

For a simple single slit in one dimension, the amplitude pattern can be described by a sinc function (i.e. a function of the form $\frac{sin(x)}{x}$), which is easily applied in Python. But, after encountering a [video on Fourier optics](https://www.youtube.com/watch?v=Y9FZ4igNxNA), which used uncommon aperture shapes to produce intricate diffraction patterns, I wanted my Synth to work with *any* aperture (albeit restricted to one spatial dimension, so that the amplitude pattern could still be mapped onto a time base later). In turn, I needed a way to describe the amplitude pattern as a function of the aperture shape.

This is exactly what a Fourier transform does. 

It turns out the amplitude pattern produced by Fraunhofer (far-field) diffraction is proportional to the Fourier transform of the aperture function, A(x):

$\ F(k_x) \propto \int_{-\infty}^{\infty} A(x)e^{-ik_xx}\ dx \$

Here, A(x) describes at which points along aperture axis light can be transmitted. For example, for a single slit of width $a$:

$$
A(x) =
\begin{cases}
1, & -\dfrac{a}{2} \leq x \leq \dfrac{a}{2},\\
0, & \text{otherwise}.
\end{cases}
$$

so that the transform could be equivalently expressed as:

$$\ F(k_x) \propto \int_{-a/2}^{a/2} e^{-ik_xx}\ dx \$$


Here, $k_x$ denotes the x-component of the wave-vector associated with a particular observation direction, and $F(k_x)$ encodes the amplitude of the outgoing electric field for each $k_x$. What does this mean? 

At the aperture, each transmitting point acts as a source of secondary wavelets, by Huygen's principle. Beyond the aperture, the electric field at any given point in space is the superposition of all secondary wavelets at that point. In the Fraunhofer (far-field) region, the resulting field can be described as a collection of plane waves, each with a wave-vector $\mathbf{k}$, travelling in different directions towards the screen.

<img width="275" height="148" alt="Screen Shot 2022-02-18 at 7 49 25 PM" src="https://github.com/user-attachments/assets/27daf644-ba6f-4ded-ae1b-7dd58b86e270" />
https://phys.libretexts.org/Bookshelves/Electricity_and_Magnetism/Essential_Graduate_Physics_-_Classical_Electrodynamics_%28Likharev%29/08%3A_Radiation_Scattering_Interference_and_Diffraction/8.06%3A_Fresnel_and_Fraunhofer_Diffraction_Patterns 

The x-component of the wave-vector, $k_x$, describes the spatial phase variation of a particular plane wave along the x-axis. This phase variation determines how the contributions from each point source along the aperture interfere, and therefore determines the amplitude of the outgoing plane wave in that direction. Thus, $F(k_x)$ encodes the amplitude of each outgoing plane wave associated with a particular spatial frequency.

Since a wave-vector describes the propagation direction of a plane wave, each $k_x$ can be associated with a specific observation angle, $\theta$, and thus a specific position $X$ along the screen. In the Fraunhofer regime:

n the Fraunhofer regime:

$$
X = d\tan\theta \approx \theta d,
$$

where $d$ is the perpendicular distance from the aperture to the screen, and

$$
k_x = k\sin\theta \approx \theta k.
$$

where $k$ is the magnitude of the wave-vector $\mathbf{k}$ and is given by $k = \frac{2\pi}{\lambda}$. 

Combining these yields:

$$
k_x = \frac{kX}{d}.
$$

Thus, we can change the coordinates of our transform to determine the amplitude pattern as a function of position along the screen:

$$
F(X) \propto \int_{-\infty}^{\infty} A(x)e^{-i\frac{kX}{d}x}\ dx
$$


## Evaluating the Fourier transform in Python

To evaluate this Fourier transform in Python, I used a Fast-Fourier-Transform (included in the numpy.fft library), due to its relatively low computational time. This required the integral to be re-expressed as a discrete Fourier transform (DFT):

**rexpress integral as discrete sum**

To begin with, I created 

## Creating the modulating signal


1. Restrict the region of the screen to be used for the envelope. The region most useful in distinguishing different patterns is near the central maximum, after which amplitude variations are less significant.
2. Define the length of the carrier signal (in seconds).
3. Define a mapping between the selected position range onto the time range. 


Then, I defined a function to convert the FFT output into a time basis. 
- I encountered several issues with playing audios in python, also with the modulation.
- I thought the issue was with my coding, but using AI to debug showed that the issue was actually in my choices for the sampling parameters (I was getting confused between frequency bin size, the number of samples taken across the aperture, and what zero padding was).
- Struggled with selecting the relevant region of the diffraction pattern, but came across the concept of "masks".


Finally, I built the interactive element using Pygame. 
- Initially struggled to figure out the best library for both sonification and graphing, because I couldn't figure out how to map the graph onto a Pygame surface (had AI write that function).



## Results

Add explanation of how to use the synth.


## What I Learned


## Extensions




## Source Code

(https://github.com/nk-clara/diffraction-synth)
