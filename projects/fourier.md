---
layout: default
title: fourier
---

# Why is a diffraction pattern a Fourier transform of the aperture function?


## Intro to main fourier concepts

##

We start by defining the coordinates of our setup. Let x be direction parallel to the aperture and the screen, and z be the perpendicular direction, so that the aperture is at z = 0 and the screen is at z = d.

**add diagram**

We define our aperture through a function A(x), which indicates where along the axis z = 0 light can be transmitted. For example, for a single slit, the aperture function is **insert function**.

Now assume we have a plane wave of light incident on an aperture. By Huygen's principle, every point along a wavefront acts as a point source of secondary spherical wavelets. The electric field at a particular point X on the screen is the sum (or superposition) of all incident wavelets transmitted through the aperture.

To reach this point, each wavelet travels through a slightly different path length, accumulating different propagation phases. To visualise the acquired phases, consider a phasor assigned to each point along the aperture. In the far field (the Fraunhofer region), the phase distribution is approximately linear in x. Visually, this means the phasors rotate with a fixed spatial frequency, **kx**. ... **e^-ikxx**. 

**add diagram of phasors along the aperture**

The aperture function now determines which of these phasors actually contribute to the field: where A(x)=0, there is no transmitted wavelet, and where A(x)=/0, the corresponding phasor contributes to the sum. Thus, the electric field at our chosen point X is the vector sum of the phasors selected by the aperture. 

**insert integral**

Due to geometry, the phase distribution along the aperture varies for all points X along the screen. In other words, each position on the screen can be associated with a particular value of kx, so that the integral is equivalently expressed as:

**insert integral**

This is the Fourier transform of A(x). 
  
