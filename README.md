# Project Specification

### Searching for periodic signals in light-curves to find variable stars and exoplanets

Many stars exhibit periodic changes in brightness. For some single stars this is intrinsic (similar to the Solar cycle seen in our own Sun but much more rapid) and may be due to large star spots on the stellar surface, or to pulsations which can cause significant changes in temperature and radius. Other stars exist in binary systems, as the perceived brightness as seen from Earth will vary as they mutually occlude each other as they proceed in the orbits.

More excitingly for a small fraction of stars that host their own planets there may be a chance alignment between the orbital plane of the exoplanet and our line-of-sight to the star. In these cases the exoplanet will periodically pass between the star and us, blocking a small fraction of light (typically <1%). By taking very high-precision light-curves of many stars and searching for small periodic signals we can discover new exoplanets. 

Included in the 'data' sub-directory are several data files containing light-curves obtained using the NASA satellite _TESS_ (Transiting Exoplanet Sky Survey). A quick note on nomenclature - a light-curve is what astronomers call a photometric time-series, ie. a series of brightness / flux measurements taken at specific times.

The attached files contain the following columns:
- time, in days
- flux (brightness) in arbtrary units
- flux error (1$\sigma$) in those same units

You should be able to read these files using numpy functions you are familiar with. The values of the time stamps are quite large, so for help with plotting, etc., it is best to subtract the minimum time value from the time array after reading the file.

Here we will use a technique called Phase Dispersion Minimization (PDM) to identify the periods of the handful of variable stars and exoplanets in the _TESS_ light-curves.

At it's core this method relies on the concept of _phase-folding,_ which works as follows. Assume you have a light-curve of a target star whose brightness varies in some periodic fashion with a period $P_{\rm true}$, and that that light-curve contains series of sample times $t_i$ and measured brightness $f_i$. For a given trial period, $P_{\rm guess}$, you can compute a phase value $\phi_i = (t_i\mod P_{\rm guess}) / P_{\rm guess}$. You can plot $f_i$ against $\phi_i$, and if you have guessed the period correctly you will see the flux measurements from each cycle line up nicely and overlay each other. If you have guessed the wrong period, you will generally just get a scattered mess.

What PDM does is to attempt to compute a single statistical  measure of how closely the data points line up and overlay as a function of phase. This is not as simple as it sounds, as real data contains noise from various sources, eg. counting statistics, and various instrumental and environmental influences on the satellite. The data you will be using here have been quite thorougly cleaned to remove most (but not all) of these effects.
