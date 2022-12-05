---
layout: post
title:  "CARS Noise: PMT Ripple and Modelocking"
date:   2022-12-04
categories: optics
---

Since I got CARS imaging "working" in a general sense in the previous weeks, this week was about optimizing the image quality by dealing with some sources of noise. In particular, when I finally got the imaging working I was puzzled by a certain type of noise I was seeing that I could not easily locate the source of. Although, in hindsight it seems fairly trivial. In this post I will discuss two types of noise that you might encounter due to periodic oscillations in the PMT power or in the beam intensity due to suboptimal modelocking of the laser source. I won't mention anything on dark noise, shot noise or electronic "salt and pepper" noise. 

## PMT Ripple

Hamamatsu does a good job of describing the root cause of PMT ripple noise and how to deal with it. From [Chapter 8](https://www.hamamatsu.com/content/dam/hamamatsu-photonics/sites/documents/99_SALES_LIBRARY/etd/PMT_handbook_v3aE-Chapter8.pdf) of the Hamamatsu PMT Handbook:

![Hamamatsu - PMT Handbook - Ripple Noise](/images/cars-noise/hamamatsu_pmt_handbook_ripple_noise.png)

Great! Simply from reading the PMT manual, we have gained two methods of dealing with PMT ripple noise. One extra point for the RTFM crowd. But what does ripple noise look like? From the ScanImage documentation section on [PMT Ripple Noise](https://docs.scanimage.org/Solutions/PMT%2Bnoise.html) we get a visualization of how this manifests in a particular image.

![ScanImage Docs - PMT Ripple Noise Demonstration](/images/cars-noise/scanimage_pmt_ripple.png)

Wonderful, now we can recognize when we are seeing PMT ripple noise and how to remedy it. 


## Modelocking Noise

Now, what if your noise looks like this?

![Modelocking noise in AXN image](/images/cars-noise/axn_modelocking_noise.jpg)

This is one of the first images I took of astaxanthin (AXN) from the microscope. Notice how we don't have the checkerboard pattern as seen in PMT ripple noise. It also does not depend on signal intensity or control voltage applied to the PMT, so that rules out PMT ripple. There is still a periodicity to the noise, but it seems less consistent and manifests as dark diagonal lines in the otherwise normal image. Next I thought that maybe the DAQ was dropping samples in a semi-periodic manner, but testing the DAQ ruled that out. After that I went over the entire system checking components that may produce a periodic signal. Naturally the laser came up. For CARS to happen, you need ultra-fast laser pulses with a pulse duration on the same order as the interaction time. This means that we need pico- or femto-second pulses arriving at the sample periodically. In our setup we have a Spectra-Physics Tsunami delivering ~190 fs pulses tuned to 800 nm with a 80 MHz repetition rate. For the imaging I used a sample rate of about 50 MHz, so the 80 MHz pulses shouldn't be causing the lines we're seeing unless some portion of the pulses are not properly mode locked. By setting up a spectrometer to monitor a small portion of the laser output, we can see if the pulses are mode locked based on if the spectrum looks Gaussian or not. Lo and behold, the laser was not modelocking properly. There seems to be some region between full CW breakthrough—where no CARS can occur—and proper mode locking. By changing the wavelength a bit to find a mode locking sweet spot, I was able to get the noise to disappear. This can be seen in the image below.

![Comparison between image with optimal and suboptimal modelocking](/images/cars-noise/modelocking_noise_comparison.jpg)

In the top left, we can see a spectrum that doesn't show any clear CW breakthrough, but is also clearly not properly mode locked, as is shown in the bottom left. It's possible that we had some sort of periodic CW breakthrough that the spectrometer couldn't pick up, but was visible in the image. By optimizing the modelocking at 800 nm, the noise went away. 