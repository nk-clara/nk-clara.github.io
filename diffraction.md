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

If you, like me, are interested in both music and physics, you will probably have heard that the timbre, or unique sound, of synthesizers can be altered by modulating a pure tone. Modulation involves changing the shape of a sound wave at a specific base frequency by overlaying it with another wave form, known as an envelope. 

**add image?**  

The key idea behind this Synth was to apply a modulation to musical notes (pure sine waves) that was based on the amplitude pattern produced when light diffracts through a specific aperture. Here, "amplitude pattern" refers to the electric field amplitude as a function of position along the screen. I chose the amplitude pattern instead of the intensity pattern for the envelope, as it produces clearer differences in timbre (since **|E|^2 is proportional to I**).

**add image that compares the two**  

Now, to create the synth I needed to: 

1. Determine the amplitude pattern for a specific aperture
2. Convert the amplitude pattern into an auditory waveform to modulate musical notes
3. Create an interactive Synth in Python to sonify and visualise this modulation


## Step 1: Determining the amplitude pattern

For a simple single slit, the electric field amplitude across the screen can be described by a continuous sinc function, which is easily applied in Python. 

**add image**

But, after encountering a video **add link** on Fourier optics, which used uncommon aperture shapes to produce intricate diffraction patterns, I wanted my Synth to work with *any* aperture (albeit in one spatial dimension). In turn, I needed a way to describe the amplitude pattern as a function of the aperture shape.

This is exactly what a Fourier transform does. 

It turns out the amplitude pattern produced by Fraunhofer diffraction is proportional to the Fourier transform of the aperture function, A(x):

**insert equation**

Here, A(x) describes at which points along aperture axis light can be transmitted. For example, the aperture function for a single slit is **add function**

Its Fourier transform gives the electric field amplitude pattern in spatial-frequency space. Since each spatial frequency corresponds to a specific point along the observation screen, we can convert our result into the desired coordinates, to obtain the amplitude pattern as a function screen position. The process is summarised as:

**shorthand symbols for each step**

To cover this concept more in depth, I created a [separate post](nk-clara.github.io/fourier) on why diffraction patterns are a Fourier transform of the aperture function.

## Step 2: Converting from optics to sound

Equipped with this tool, I needed to turn the amplitude pattern into a sound envelope. For this, I created an (arbitrary) scale that mapped the position coordinates along the screen onto a time basis. 

**add example/graph**


## Step 3: Building the Synth in Python

Now, all that was left was to was to evaluate the Fourier transform to obtain the amplitude pattern, and thus the sound envelope. To approach this computationally, I first re-expressed the integral as a discrete Fourier transform (DFT). Intuitively, this can be understood as breaking down the aperture into a finite number of samples  **maybe reexplain** :

**rexpress integral as discrete sum**

To evaluate this DFT in Python, I used a Fast-Fourier-Transform (included in the numpy.fft library), due to its relatively low computational time. 

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
