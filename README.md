# ModelTES

[![Build Status](https://travis-ci.org/ggggggggg/ModelTES.jl.svg?branch=master)](https://travis-ci.org/ggggggggg/ModelTES.jl)

This package is intended to simulate TES microcalorimeter pulses in both the linear and non-linear regime. It's not really
intended for wide use, but feel free to play with it if you want. There may be no support, but can try opening and issue and
hope.

Here is an example of what you can do

```julia
using ModelTES, PyPlot
# Create a high-E TES design
stdTES = ModelTES.highEpix()
# Create a Biased TES from the 48 nanohentry Holmes paramters with 0.2*Rn resistance
tes =  ModelTES.pholmes(48e-9, 0.20)
# Create a Biased TES from the 48 nanohentry Holmes paramters with 0.4*Rn resistance
tes2 = ModelTES.pholmes(48e-9, 0.40)
# Integrate a pulse with 12000 samples, 1e-7 second spacing, 1000 eV energy, 2000 presamples
out = rk8(12000,1e-7, tes, 1000, 2000);
# Integrate a pulse with 12000 samples, 1e-7 second spacing, 1000 eV energy, 2000 presamples from the higher biased version of the same tes
out2 = rk8(12000,1e-7, tes2, 1000, 2000);
# Get all the linear parameters for the irwin hilton model
lintes = IrwinHiltonTES(tes)
# Calculate the noise and the 4 components in the IrwinHilton model
f = logspace(0,6,100);
n,n1,n2,n3,n4 = noise(lintes, f);

# Calculate a stochastic noise 1000 eV pulse with 12000 samples and 2000 presmples
outstochastic = stochastic(12000,1e-7, tes, 1000,2000);



figure()
title("stochastic pulse and noiseless pulse")
plot(1e3*times(out), 1e6*out.I,".k")
plot(1e3*times(outstochastic), 1e6*outstochastic.I,".r")
xlabel("Time (ms)"); ylabel("Current (\$\\mu\$A)");
figure()
title("pulses with different bias points")
plot(1e3*times(out), 1e6*out.I)
plot(1e3*times(out2), 1e6*out2.I)
xlabel("Time (ms)"); ylabel("Current (\$\\mu\$A)");
figure()
loglog(f,n1,label="TES electrical")
loglog(f,n2,label="Load electrical")
loglog(f,n3,label="Thermal fluctuation")
loglog(f,n4,label="Amplifier")
loglog(f,n,label="Total noise PSD", lw=2)
xlabel("Frequency (Hz)"); ylabel("Noise Power (A\$^2\$/Hz)")
legend(loc="best")
```
