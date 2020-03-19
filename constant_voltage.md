## Siepmann \& Sprik method
Assume the electrodes are perfect conductor with the infinite dielectric constant.
In other words, there is no electric field inside the electrodes in equilibrium.
In the framework of classical electrostatics
$$
\begin{aligned}
  \Phi (\mathbf{r} ) &=& \int d \mathbf{r}^{\prime} \frac{\rho_{\mathrm{electrodes}}\left(\mathbf{r}^{\prime}\right)+\rho_{\mathrm{electrolytes}}\left(\mathbf{r}^{\prime}\right)}{\left|\mathbf{r}-\mathbf{r}^{\prime}\right|}
\end{aligned}
$$
Under constant voltage, following constraints should be satisfied
$$
\begin{aligned}
  \Phi^+ &=& \Delta \Phi+ \Phi_{\mathrm{ref}} \\
  \Phi^- &=& \Phi_{\mathrm{ref}} \\
  \sum q_i^+ + \sum q_i^- &=& 0
\end{aligned}
$$
The charges on each electrode atoms thus can be solved iteratively at every simulation step.

Alternatively, extended Lagrangian approached can be utilized to get rid of the iteration.
Treating the electrode charges as dynamic variables, the extended Hamiltonian $\mathcal{H}$ is given by
$$
\begin{aligned}
  \mathcal{H} = T + U_{bonded} + U_{vdW} + U_{elec} + T_q - \sum_i q_i^+\Phi^+ - \sum_i q_i^-\Phi^-
\end{aligned}
$$
where $T$ is the kinetic energy of atoms and $T_q$ is the fake kinetic energy of electrode charges.
The fake force on an electrode charge is thus
$$
\begin{aligned}
  F_{q,i} = -\frac{\partial \mathcal{H}}{\partial q_i} = -\frac{\partial U_{elec}}{\partial q_i} + \Phi^{\pm}
\end{aligned}
$$

> In literature, the electrode charges are described as Gaussian charges instead of point charges.
> It is claimed that Gaussian charge is more robust to be solved iteratively.

The iterative approach has been implemented in LAMMPS by Wang et. al.
During the constant voltage simulation, the electrolytes are modeled by polarizable force field, whereas the electrode atoms are modeled by non-polarizable force field.
This is because the charges of the electrode atoms are solved at every steps to enforce the constant voltage constraints, which has already considered the polarization of electrolytes.

## 2-D Image charge method
Image charge method is an relatively efficient way to enforce constant voltage during simulation for parallel plate electrodes.
The method proposed by Petersen \textit{et. al.}\cite{Petersen2012} is used in this work.
Considering the two electrodes as mirrors, each atomic charge in electrolytes creates two oppositely signed image charges with respect to the two mirrors.
The image charge will induce higher-order image charges.
These higher-order image charges and the applied voltage drop are treated effectively by spread charges uniformly on the electrodes
$$
    q_{\mathrm{cathode}}=\sum_{i}^{N_{\mathrm{electrolytes}}} \frac{q_{i} \left(z_{i}-z_{\mathrm{cathode}}\right)}{D}+\frac{V_{0} A \epsilon_{0}}{D} \\
    q_{\mathrm{anode}}=\sum_{i}^{N_{\mathrm{electrolytes}}} \frac{q_{i}\left(z_{\mathrm{anode}}-z_{i}\right)}{D}-\frac{V_{0} A \epsilon_{0}}{D}
$$
where $q_i$ is the charge of electrolytes atoms, $z_i$, $z_{\mathrm{cathode}}$ and $z_{\mathrm{anode}}$ are the $z$ coordinates of electrolyte atoms, cathode and anode, $D$ is the distance between two electrodes, $A$ is the area of electrodes in $xy$ plane, $V_0$ is the applied voltage drop.
In a simulation work on the water confined between two Pt electrodes, it has been shown that Peterson's method produces almost identical liquid structures compared with Siepmann \& Sprik's method.\cite{Matsumi2017}

## 3-D Image charge method
