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

If you, like me, are interested in both music and physics, you will probably have heard that the timbre, or unique sound, of synthesizers can be altered by modulating a pure tone. Modulation involves changing the shape of a sound wave at a specific base frequency by overlaying it with another wave form. **add image?**  The key idea behind this Synth was to apply a modulation to musical notes (pure sine waves) that was based on the waveform of the electric field amplitude at the diffraction screen.

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

We define our aperture through a function A(x), which indicates where along the axis z = 0 light can be transmitted. For example, for a single slit, the aperture function is **insert function**.

Now assume we have a plane wave of light incident on an aperture. By Huygen's principle, every point along a wavefront acts as a point source of secondary spherical wavelets. The electric field at a particular point X on the screen is the sum (or superposition) of all incident wavelets.

To reach this point, each wavelet travels through a slightly different path length, accumulating different propagation phases. To visualise the acquired phases, consider a phasor assigned to each point along the aperture. In the far field (the Fraunhofer region), the phase distribution is approximately linear in x. Visually, this means the phasors rotate with a fixed spatial frequency, **kx**. Thus, the phasor orientation can be expressed as **e^-ikxx**. 

**add diagram of phasors along the aperture**

The aperture function now determines which of these phasors actually contribute to the field: where A(x)=0, there is no transmitted wavelet, and where A(x)=/0, the corresponding phasor contributes to the sum. Thus, the electric field at our chosen point X is the vector sum of the phasors selected by the aperture. 

**insert integral**

Due to geometry, the phase distribution along the aperture varies for all points X along the screen. In other words, each position on the screen can be associated with a particular value of kx, so that the integral is equivalently expressed as:

**insert integral**

This is the Fourier transform of A(x). 
  

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
