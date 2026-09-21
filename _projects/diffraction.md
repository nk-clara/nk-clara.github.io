---
layout: default
title: Diffraction Synthesizer
date: 08-08-2026
description: Sonifying diffraction patterns using FFTs in python
---

# Diffraction Synthesizer: The Sounds of Fraunhofer Diffraction

<figure class="post-figure hero-figure">
  <img alt="Screenshot 2026-08-11 at 23 40 56" src="https://github.com/user-attachments/assets/22c1fc47-562f-4868-9d56-42245ecf104b" />
</figure>

<div class="synth-demo">
  <video controls poster="PATH_TO_A_THUMBNAIL.png">
    <source src="PATH_TO_YOUR_SCREEN_RECORDING.mp4" type="video/mp4">
    Your browser doesn't support embedded video — see the source code below to run it locally.
  </video>
</div>

## Summary

While studying diffraction, I learned that Fraunhofer diffraction can be mathematically described by a Fourier transform, an operation which is used to break any function down into its constituent frequencies. I was intrigued, since I had previously only encountered Fourier transforms as method for audio signal processing, i.e. breaking down sounds into their constituent frequencies. This provoked me to think about what parallels could be drawn between optical and audio spectra, which ultimately led to the Diffraction Synthesizer.

<figure class="post-figure">
  <img alt="Screenshot 2026-08-11 at 23 32 43" src="https://github.com/user-attachments/assets/af14e59f-4777-4706-b673-011f09fdfee1" />
  <figcaption>Source: <a href="http://labman.phys.utk.edu/phys136core/modules/m9/diffraction.html">labman.phys.utk.edu</a></figcaption>
</figure>

In this project, I aimed at creating an instrument in Python that could sonify diffraction patterns by using their unique shapes to modulate a pure tone and produce various timbres. Throughout, I also sought to familiarise myself with the fundamentals of Fourier transforms, FFTs, and signal processing in Python.

## Background

If you, like me, are interested in both music and physics, you will probably have heard that the timbre, or unique sound, of synthesizers can be altered by modulating a pure tone.

<figure class="post-figure">
  <img alt="telecom-principles-0116" src="https://github.com/user-attachments/assets/dd0ed849-4fdc-42bb-84e4-39bfab425039" />
  <figcaption>Source: <a href="https://www.technologyuk.net/telecommunications/telecom-principles/amplitude-modulation.shtml">technologyuk.net</a></figcaption>
</figure>

The key idea behind this Synth is to perform amplitude modulation on musical notes, where the carrier signal is a pure sine and the modulating signal is based on the amplitude pattern produced when light diffracts through a specific aperture. Here, "amplitude pattern" refers to the electric field amplitude as a function of position along the observation screen. I chose the amplitude pattern instead of the intensity pattern as the basis for this modulating signal, as it produces clearer differences in timbre. (Since $$ \lvert E \rvert^2 \propto I $$, the intensity pattern drops much more quickly).

<div class="image-row">
  <figure class="post-figure">
    <img alt="Screenshot 2026-08-11 at 09 38 20" src="https://github.com/user-attachments/assets/c9a7d96c-0e46-48a1-acbe-db6ebea2ae59" />
  </figure>
  <figure class="post-figure">
    <img alt="Screenshot 2026-08-11 at 09 38 34" src="https://github.com/user-attachments/assets/14ac248e-fc85-4333-a043-91286086b929" />
  </figure>
</div>

## Determining the amplitude pattern

For a simple single slit in one dimension, the amplitude pattern can be described by a sinc function (i.e. a function of the form $$\frac{sin(x)}{x}$$), which is easily applied in Python. But, after encountering a [video on Fourier optics](https://www.youtube.com/watch?v=Y9FZ4igNxNA), which used uncommon aperture shapes to produce intricate diffraction patterns, I wanted my Synth to work with *any* aperture (albeit restricted to one spatial dimension, so that the amplitude pattern could still be mapped onto a time base later). In turn, I needed a way to describe the amplitude pattern as a function of the aperture shape.

This is exactly what a Fourier transform does.

It turns out the amplitude pattern produced by Fraunhofer (far-field) diffraction is proportional to the Fourier transform of the aperture function, A(x):

$$
F(k_x) \propto \int_{-\infty}^{\infty} A(x)e^{-ik_xx}\ dx
$$

Here, A(x) describes at which points along aperture axis light can be transmitted. For example, for a single slit of width $$a$$:

$$
A(x) =
\begin{cases}
1, & -\dfrac{a}{2} \leq x \leq \dfrac{a}{2},\\
0, & \text{otherwise}.
\end{cases}
$$

so that the transform could be equivalently expressed as:

$$
F(k_x) \propto \int_{-a/2}^{a/2} e^{-ik_xx}\ dx
$$

Here, $$k_x$$ denotes the x-component of the wave-vector associated with a particular observation direction, and $$F(k_x)$$ encodes the amplitude of the outgoing electric field for each $$k_x$$. What does this mean?

At the aperture, each transmitting point acts as a source of secondary wavelets, by Huygen's principle. Beyond the aperture, the electric field at any given point in space is the superposition of all secondary wavelets at that point. In the Fraunhofer (far-field) region, the resulting field can be described as a collection of plane waves, each with a wave-vector $$\mathbf{k}$$, travelling in different directions towards the screen.

<figure class="post-figure">
  <img alt="Screen Shot 2022-02-18 at 7 49 25 PM" src="https://github.com/user-attachments/assets/27daf644-ba6f-4ded-ae1b-7dd58b86e270" />
  <figcaption>Source: <a href="https://phys.libretexts.org/Bookshelves/Electricity_and_Magnetism/Essential_Graduate_Physics_-_Classical_Electrodynamics_%28Likharev%29/08%3A_Radiation_Scattering_Interference_and_Diffraction/8.06%3A_Fresnel_and_Fraunhofer_Diffraction_Patterns">phys.libretexts.org</a></figcaption>
</figure>

The x-component of the wave-vector, $$k_x$$, describes the spatial phase variation of a particular plane wave along the x-axis. This phase variation determines how the contributions from each point source along the aperture interfere, and therefore determines the amplitude of the outgoing plane wave in that direction. Thus, $$F(k_x)$$ encodes the amplitude of each outgoing plane wave associated with a particular spatial frequency.

Since a wave-vector describes the propagation direction of a plane wave, each $$k_x$$ can be associated with a specific observation angle, $$\theta$$, and thus a specific position $$X$$ along the screen. In the Fraunhofer regime:

$$
X = d\tan\theta \approx \theta d,
$$

where $$d$$ is the perpendicular distance from the aperture to the screen, and

$$
k_x = k\sin\theta \approx \theta k.
$$

where $$k$$ is the magnitude of the wave-vector $$\mathbf{k}$$ and is given by $$k = \frac{2\pi}{\lambda}$$.

Combining these yields:

$$
k_x = \frac{kX}{d}.
$$

Thus, we can change the coordinates of our transform to determine the amplitude pattern as a function of position along the screen:

$$
F(X) \propto \int_{-\infty}^{\infty} A(x)e^{-i\frac{kX}{d}x}\ dx
$$

## Evaluating the Fourier transform in Python

To evaluate this Fourier transform in Python, I used a Fast-Fourier-Transform (included in the numpy.fft library), due to its relatively low computational time. This required the integral to be re-expressed as a discrete Fourier transform (DFT) that the FFT can operate on:

$$
\sum_{n=-m}^{m} A(n\Delta x)\, e^{-i\frac{kX}{d}(n\Delta x)}\ \Delta x
$$

Here, $$\Delta x$$ refers to the interval size for each sample, $$n\Delta x$$ expresses the x-position of the sample, and $$m$$ indicates the total number of samples taken along the aperture on each side of $$x = 0$$.

In this discrete framework, I defined an aperture function in terms of an input number of slits, at a fixed separation from each other. Padding is added as a parameter to increase the x-range sampled across the aperture. This ensures a sufficient number of samples taken given a fixed interval size $$\Delta x$$.

```python
def aperture_function(N,a,b,padding):
    """
    Define a one-dimensional aperture centered at x = 0.

    Parameters
        N: number of slits
        a: slit width
        b: slit separation
        padding: determines how far the sample range extends beyond the aperture

    Returns
        x: x-coordinates (in meteres) of the samples taken along the aperture axis
        aperture: the aperture function as a boolean array

    Note:   currently only works for single and double slit apertures
            (with a transmission of either 0 or 1 at a given x-coordinate)

    """

    # Defining a sample range along the aperture axis, centered at x = 0 (meters)
    x_range = padding*a # [m]
    dx = a/100 # [m]
    x = np.arange(-x_range/2,x_range/2,dx)

    # Determining the aperture function

    if N == 1:
        aperture = np.abs(x) < a/2
    elif N % 2 == 0:
        aperture = np.zeros(len(x), dtype=bool)
        
        slit_centers = np.arange(-(N-1)*b/2,(N+1)*b/2,b)

        for centre in slit_centers:
            aperture = aperture | (np.abs(x - centre) < a/2)
    else:
        aperture = np.zeros(len(x), dtype=bool)

        slit_centers = np.arange(-(N-1)*b/2,(N+1)*b/2,b)

        for centre in slit_centers:
            aperture = aperture | (np.abs(x - centre) < a/2)

    return x, aperture
```

The following function then executes a FFT on the aperture function. The cutoff parameter is used to extract only the range of spatial frequencies corresponding to significant amplitudes, close to the central maximum (at $$x = 0$$) along the screen.

```python
def aperture_fft(x,aperture,l,d,cutoff):
    
    # Calculate magnitude of wavevector [rad/m]
    k = 2 * np.pi / l 

    # Defining discrete spatial frequency bins for the FFT (rad/m)
    kx = 2 * np.pi * fft.fftshift(fft.fftfreq(len(x),np.diff(x)[0]))

    # Complex electric field at screen from FFT (arbitrary units)
    E = fft.fftshift(fft.fft(aperture))

    # Normalized electric field amplitude (arbitrary units)
    ampl = np.abs(E)/np.max(np.abs(E))

    # Converting spatial frequencies into x-positions along the screen (meters)
    X =  (kx * d / k)

    # Selecting a relevant range of the screen to observe
    mask = np.abs(X) < cutoff
    
    return X[mask], ampl[mask]
```

## Creating the modulating signal

Following this, I defined a function to build a modulating sine-wave based on the diffraction pattern. This was based on an arbitrary scaling between positional coordinates of the diffraction pattern and time coordinates of an audio signal. Since the previous FFT function calculates a normalised electric field amplitude, this directly translates to an audio amplitude.

Notably, the cutoff that determines the relevant range of results from the FFT is defined as the product of the scaling paramter and the duration of the modulating signal. This ensures every mapping and modulation is audibly significant.

```python
def modulate_sound(x,aperture,l,d,duration,carrier_freq,scale):
    """
    Amplitude modulation of a pure sine based on a Fraunhofer amplitude pattern

    Parameters
        x: x-coordinates (in meteres) of the samples taken along the aperture axis
        aperture: takes an array containing the aperture function values
        duration: duration in seconds of the audio
        l: wavelength of light used for Fraunhofer amplitude pattern
        d: distance from aperture to observation screen for Fraunhofer amplitude pattern
        carrier_freq: frequency of the carrier signal
        scale: determines how the spatial basis is mapped onto a time basis

    Returns

        time_x: array of discrete times at which the modulated amplitude is calculated
        modulated: amplitude of the modulated sine wave (arbitrary units, from 0 to 1)
        sample_rate: number of amplitude samples per unit time

    """

    # Use the space-to-time scaling to determine the
    # relevant range of the Fraunhofer amplitude pattern
    cutoff = scale * duration # [m]

    # Compute FFT
    X, ampl = aperture_fft(x,aperture,l,d,cutoff)
    
    # Setting time as the x-variable
    time_x = np.linspace(0,duration,len(X))
    
    # Amplitude modulation of a pure sine
    pure_sine = np.sin(2 * np.pi * carrier_freq * time_x)
    modulated = pure_sine * ampl

    # Calculating the sample rate 
    sample_rate = int(len(time_x) / duration)

    return modulated, sample_rate
```

## Building the Game in PyGame

Finally, I designed the synth layout, and built the interactive element using PyGame and Sounddevice. I initially struggled to figure out the best library for both sonification and graphing, and I specifically couldn't figure out how to map the graph onto a PyGame surface (here, I had ChatGPT write up a function). Even with a working sound library, I encountered issues with the "smoothness" of the modulated sound, which ChatGPT traced to the interval size and padding value chosen. In other words, I had to spend some time manually fine-tuning the parameters for the amplitude function to ensure the FFT yielded spatial frequencies spaced closely enough to produce a "smooth" diffraction pattern.

Try for yourself: <div class="code-download">
  <a href="{{ '/assets/diffraction_synth.py' | relative_url }}" download>⬇ Download diffraction_synth.py</a>
  <p class="synth-note">This runs locally via PyGame and Sounddevice, so it can't run directly in the browser. Click the buttons to select the respective slit properties, press <kbd>Space</kbd> to change the number of slits in the aperture, and the press the letter keys to play notes.</p>
</div>


