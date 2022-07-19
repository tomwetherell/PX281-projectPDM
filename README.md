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

### Formalising the PDM method

Consider a light-curve with $N$ observations, firstly we compute the mean flux value, $\bar{f}$. We then fold at a trial period $p$ and binned the fluxes into $M$ phase bins. For each bin we compute the mean flux, $f_j$, and count the number of points, $n_j$, that fall into it. We then compute a measure of variance of mean fluxes in the phase bins about the global mean:

$$A = \sum_{j=1}^{M} n_j\left(\bar{f_j} - \bar{f}\right)^2$$

We also compute the sum of all of the variances of the individual bins:

$$E = \sum_{j=1}^{M}\sum_{k=1}^{n_j}\left(f_{kj}-\bar{f_j}\right)^2$$

where $f_{kj}$ is the $k$th data value in the $j$th bin. Lastly we compute the ratio of these two values thus:

$$S=\frac{(N-M) A}{(M-1) E}$$

The basic point here is that this $S$ statistic is larger when the variance of the data points in the individual phase bins ($E$) is smaller, and when the variance of the binned mean fluxes ($A$) is larger. This generally corresponds to the case where the trial period is close to the true period, _or an integral multiple or fraction of it_.

### Automating the period search

Now that we have a statistic intended to measure how "good" a given trial period is we can automate the search. The simplest technique is to generate a list of trial periods $P_i$ between a defined lower and upper limit and a chosen sampling, then to compute the value of $S(P_i)$. The result is usually referred to as a periodogram (in astronomy circles at least!).

We need to make sensible choices for the minimum and maximum trial period and the sampling interval. Much of this is guided by experience but there are a couple of useful rules of thumb based on information we have about how _TESS_ gathers data. The satellite surveys the sky by pointing at a specific patch for $\sim$27 days before moving on (actually two pointings of $\sim$13 days with a day in between to download data to Earth). This means that typically we will get light-curves of a maximum of 27 days duration. We need to see at least two full cycles of our periodic signal so we can be sure we have measured the period correctly, so there is little point in searching for periods longer than 13 days. Secondly _TESS_ acquires a data point every 30 minutes so for a minimum period there is no point in going shorter than a few hours. For the sampling interval you need to sample finely enough that you don't miss a best period, but not so long that the search consumes too much CPU time. A few hundred thousand samples is usually more than enough.

The second important choice to be made is the number of phase bins ($M$) to use. This is something of a black art, and is something that you will need to experiment with. Typically 10 to 30 works well, but it really depends on the shape of the folded lightcurve.
