---
layout: default
title: Diffraction Synthesizer 
---

# Diffraction Synthesizer: The Sounds of Fraunhofer Diffraction

** add image**

## Overview

While studying diffraction, I learned that diffraction patterns can be mathematically described by a Fourier transform, an operation which is used to break any function down into its constituent frequencies. I was intrigued, since I had previously only encountered Fourier transforms as method for audio signal processing, i.e. breaking down sounds into their constituent frequencies. This provoked me to think about what parallels could be drawn between optical and audio spectra, which ultimately led to the Diffraction Synthesizer.

In this project, I aimed at creating an instrument in Python that could sonify diffraction patterns by using their unique shapes to modulate a pure tone and produce various timbres. Throughout, I also sought to familiarise myself with the fundamentals of Fourier transforms, FFTs, and signal processing in Python.



## Background

**add Intro about diffraction**
- mention the difference between electric field amplitude and intensity, and specify which will be used! (i.e. electric field waveform refers to its amplitude)
- 

If you, like me, have any interest in both music and physics, you will probably have heard that the timbre, or unique sound, of synthesizers can be altered by modulating a pure tone. Modulation involves changing the shape of a sound wave at a specific base frequency by overlaying it with another wave form. **add image?**  The key idea behind this Synth was to apply a modulation to musical notes (pure sine waves) that was based on the waveform of the electric field amplitude at the diffraction screen.

This led to three primary steps to building the Synth:

1. Determine the electric field waveform at the position of screen given a specific aperture
2. Convert the optical (electric field) waveform into an auditory waveform to modulate musical notes
3. Create an interactive Synth in Python to sonify and visualise this modulation


## Step 1: Determining the electric field waveform

For a simple single slit, the electric field at the screen can be described by a continuous sinc function, which is easily applied in Python. 

**add image**

But, after encountering a video **add link** on Fourier optics, which used apertures in the shaped of letters to produce more intricate diffraction patterns, I wanted my Synth to work with *any* aperture. This means I needed a tool that determined the waveform of the electric field aperture for a given aperture shape: the Fourier transform.

**But, how exactly is the electric field waveform at the screen a Fourier transform of the aperture?**

We start by defining the coordinates of our setup. Let x be direction parallel to the aperture and the screen, and z be the perpendicular direction, so that the aperture is at z = 0 and the screen is at z = d.

**add diagram**

Now assume we have a plane wave of light incident on the aperture. We can define an aperture function, A(x), that determines at which points along z = 0, light is actually be transmitted. For example, for a single slit, the aperture function is **insert function**.

By Huygen's principle, every point along the wavefront at which A(x) =/ 0 acts as a point source of secondary spherical wavelets. Given that the electric field amplitude decays with 1/r, the electric field from each wavelet can be represented as **insert equation**. Therefore, the field at a screen position is simply the sum of contributions from the whole aperture, represented by the integral

At a particular observation angle, the electric field at the screen is simply the sum of contributions from each point source.

- Each of secondary wavelets has accumulated a different phase to reach the screen, and whether they interfere to produce a dark or bright fringe depends on the phase distribution across the aperture. 
- In the far field, this phase variation is approximately linear with position along the aperture, with a scaling dependent on the observation angle to the screen / position along the screen.
- **add diagram of phasors along the aperture to visualise how the phase ramp is really just dictated by a spatial frequency**
- Since phase along the aperture varies periodically, it can be expressed by the complex factor ...

- From this, the contribution from a point source at z = 0 can be expressed as A(x) * phase factor * decaying complex waveform, and the total contribution is the integral of this for all x **add integral**.

- This is the Fourier transform! Essentially, we can think of this as mapping whether the aperture overlaps with the specific frequency of phase variation along x associated with a point on the screen. **add diagram**




## Step 2: Converting from optics to sound

Equipped with this tool, I needed to turn the optical waveform into something that could be mapped onto a sound wave. For this, I created an (arbitrary) scale that mapped the position coordinates along the screen onto a time basis. 

...


## Step 3: Building the Synth in Python

- I applied the FFT to an aperture function in python and visualised the effect of changing parameters in the spatial domain. Then I got around to converting the electric field pattern to a time-based domain.
- Encountered several issues with playing audios in python, also with the modulation. I thought the issue was with my coding, but using AI to debug showed that the issue was actually in my choices for the sampling parameters (I was getting confused between frequency bin size, the number of samples taken across the aperture, and what zero padding was). Struggled with selecting the relevant region of the diffraction pattern, but came across the concept of "masks".
- Built the interactive element using Pygame. Initially struggled to figure out the best library for both sonification and graphing, because I couldn't figure out how to map the graph onto a Pygame surface (had AI write that function).



## Results

Add explanation of how to use the synth.


## What I Learned


## Extensions




## Source Code

(https://github.com/nk-clara/diffraction-synth)
