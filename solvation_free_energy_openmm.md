# Solvation Free Energy via Alchemical Decoupling with OpenMM

Solvation free energy (excess chemical potential) computed by alchemically decoupling
a single molecule from its environment using OpenMM. Two-phase protocol: Coulomb
interactions are turned off first, then LJ interactions via soft-core potential.

## Lambda Schedule

Two-phase decoupling: Coulomb first (safe without soft-core because LJ repulsion
is still fully on), then LJ with soft-core.

```
lambda index:    0    1    2    3    4    5    ...   n-1
lambda_coul:   1.0  0.75  0.5  0.25  0.0  0.0  ...  0.0
lambda_vdw:    1.0  1.0  1.0  1.0  1.0  0.9  ...  0.0
```

~1/3 of windows for Coulomb, ~2/3 for LJ. Spacing is uniform within each phase.
Soft-core parameter: alpha=0.5 (hardcoded).

## Force Decomposition

The OpenMM system is assumed to contain the following nonbonded forces (bonded
forces are unchanged by alchemical modification and omitted here). The approach
described below does not cover Mie potentials, shifted LJ, Drude polarizable
models, or virtual site charges.


| Force                      | Computes                         | Per-particle params       |
|----------------------------|----------------------------------|---------------------------|
| NonbondedForce (PME)       | Coulomb (real+reciprocal+self)   | charge, sigma=1.0, eps=0  |
| NonbondedForce exceptions  | 1-4 Coulomb (chargeProd) + 1-2/1-3 exclusions (zero) | chargeProd |
| CustomNonbondedForce (LJ)  | A(type1,type2)*invR6^2 - B(type1,type2)*invR6 | type (int) |
| CustomBondForce (1-4 LJ)   | C*eps*((sigma/r)^n - (sigma/r)^m) | eps, sigma, n, m         |

Coulomb is handled entirely by NonbondedForce (PME). LJ is handled by
CustomNonbondedForce with tabulated A/B parameters indexed by atom type. This
separation is what makes alchemical modification straightforward.

## Alchemical Modification

Let M = set of atom indices belonging to the decoupled molecule.
Let env = all other atoms.

### Coulomb decoupling (Phase 1: lambda_coul 1 -> 0)

**Scale M's charges in NonbondedForce:**

    q_i -> lambda_coul * q_i    for i in M

This correctly scales M-env Coulomb as lambda_coul. However, M-M beyond-1-4
Coulomb (which should stay constant) scales as lambda_coul^2.

**Correction CustomBondForce for M-M beyond-1-4 Coulomb:**

    V_correction = (1 - lambda_coul^2) * ONE_4PI_EPS0 * qq / r

    for each beyond-1-4 pair (i,j) in M:
        qq = q_i * q_j

At lambda=1: correction=0, PME gives full q_i*q_j/r (normal).
At lambda=0: PME gives 0, correction gives q_i*q_j/r (intramolecular preserved).
At intermediate: PME gives lam^2*qq/r, correction gives (1-lam^2)*qq/r, total = qq/r.

**1-4 exceptions are unaffected:** OpenMM exceptions use stored chargeProd directly,
not particle charges. The exception mechanism also corrects reciprocal space exactly.

### LJ decoupling (Phase 2: lambda_vdw 1 -> 0)

**Restrict original CustomNonbondedForce to non-alchemical pairs:**

    original_cnb.addInteractionGroup(env_atoms, env_atoms)
    original_cnb.addInteractionGroup(mol_atoms, mol_atoms)

M-M LJ stays at full strength. Exclusions (1-2/1-3/1-4) already handled.

**New alchemical CustomNonbondedForce for M-env pairs (soft-core):**

    V = lambda_vdw * (A(type1,type2)*invR6_sc^2 - B(type1,type2)*invR6_sc)
    invR6_sc = 1 / (alpha*(1-lambda_vdw)^2 * S(type1,type2) + r^6)

    S(i,j) = sigma_ij^6 = A/B when B>0, else 0

    alch_lj.addInteractionGroup(mol_atoms, env_atoms)

Must copy the same exclusion list as all other forces. Same cutoff distance. LRC enabled.

**1-4 LJ (CustomBondForce):** contains only intramolecular pairs, no lambda scaling.

### Why two CustomNonbondedForce instead of one?

OpenMM's select() evaluates both branches for every pair. A single force would
compute the expensive soft-core expression for all env-env pairs (vast majority)
for nothing. Two forces: bulk pairs get the simple expression, only the ~N_mol * N_env
alchemical pairs pay the soft-core cost. Extra kernel launch is negligible.

## Final Force Table

| Force                          | Interaction groups     | Lambda?              |
|--------------------------------|------------------------|----------------------|
| NonbondedForce (PME)           | all pairs (unchanged)  | M charges * lam_coul |
| NonbondedForce exceptions      | 1-4 Coulomb + 1-2/1-3 exclusions (unchanged) | No    |
| Correction CustomBondForce     | M-M beyond-1-4 only   | lam_coul             |
| Original CustomNonbondedForce  | (env, env) + (M, M)   | No                   |
| Alchemical CustomNonbondedForce| (M, env) soft-core     | lam_vdw, LRC enabled |
| 1-4 CustomBondForce            | all 1-4 LJ (unchanged)| No                   |

## MBAR Energy Evaluation

At each frame during production, evaluate dU to every other lambda window by
switching global parameters and updating M's charges in NonbondedForce:

    for k in range(n_lambda):
        context.setParameter('lambda_coul', schedule[k].coul)
        context.setParameter('lambda_vdw', schedule[k].vdw)
        for idx in mol_atoms:
            nbforce.setParticleParameters(idx, q_orig[idx] * schedule[k].coul, 1.0, 0.0)
        nbforce.updateParametersInContext(context)
        U[k] = context.getState(getEnergy=True).getPotentialEnergy()
    dU[k] = U[k] - U[current_window]

## Known Issue: LRC Offset with Interaction Groups

Adding interaction groups to a CustomNonbondedForce changes how OpenMM computes
the long-range correction (LRC), even when the groups cover all pairs. This produces
a constant energy offset at lambda=(1,1) vs the unmodified reference system.

At fixed volume, this offset depends only on particle counts and types, cancels
in all dU evaluations, and does not affect MBAR results. However, in NPT
simulations the altered LRC affects barostat acceptance, slightly perturbing
the sampled volume distribution. The effect is typically small but not zero.
