**There is a singularity in the force calculation for an angle at 0 or 180 degree, because of the `cos(theta)` at denominator.**

This singularity disappers if the derivative of energy nearby 0 or 180 degree is zero.
Otherwise, this singularity can be bypassed by a numerical trick: setting the denominator to a small value at 0 or 180 degree.
For example, OpenMM implements it like this:
```
angleForce.cc
```

In an issue, OpenMM claims that it uses `arcsin()` to calculate the angle for better precision, but the code in `angleForce.cc` still uses `arccos()`
https://github.com/openmm/openmm/issues/3515

**Other than the singularity, there is also a corner in the force for an angle at 0 or 180 degree, because of the sawtooth shape of bond angle.**

The corner disappears only if the derivative of energy nearby 0 or 180 degree is zero.

This is the reason SDK CGFF fails sometimes. The equilibrium value for angle is around 170 degree and the force constant is small, which makes it easy for three atoms be colienar.

GROMACS has proposed a functional form for linear angle, which works perfectly for linear molecule like CO2 or nitrile. http://doi.org/10.1063/5.0015184
But be careful, the argument about setting angle to 178 degree in the introduction of this article is incorrect.
It may decrease the probability of reaching 180 degree, but it creates a corner in the force that doesn't exist in the first place.

**Summary**
1. For a truly linear molecule, linear potential proposed by GROMACS will work. In theory, harmonic potential will also work, but it seems not all MD engines handle it properly.
2. For near linear molecule with soft angle, be very careful. Probally the harmonic cosine potential will work, but I haven't tested it.

**Read further**
http://doi.org/10.1002/jcc.540130508
