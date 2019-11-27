When simulating 2D systems under 3D periodic conditions, an artifact emerges from the slab-slab interactions. This interaction can be significant when there's net dipole in the system (*e.g.* electrified interface). A correction has been propsed to eliminate this undesired interaction.
$$
\begin{align*}
Q &= \sum q_i \\
\mu_z &= \sum q_i z_i \\
\mu_{z2} &= \sum q_i z_i^2 \\
E_{corr} &= \frac{2\pi}{V}(\mu_z^2-Q\mu_{z2}-\frac{Q^2l_z^2}{12}) \\
F_{z,i} &= -\frac{\part E_{corr}}{\part z_i} = -\frac{4\pi}{V}q_i(\mu_z-Qz_i)
\end{align*}
$$
