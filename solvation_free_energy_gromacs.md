### Generate simulation files

- With whatever tools you like, build a box of solvents and put one solute molecue inside, generate *gro* and *top* files for GROMACS


- Write two *mdp* files for equilibriation and production run.  
  * Notice that *dt*, *nsteps*, *pcoupl*, *tau-p* and *nstdhdl* are different for eq and prod runs
  * Be careful about *init_lambda_state*
  * *vdw_lamdbas* and *coul_lambdas* should be carefully chosen for different solutes and solvents. Though the follwing set of lambdas is applicable for most cases.
```ini
; eq.mdp

integrator              = sd       ; use stochastic intergrator for free energy calculation
dt                      = 0.001    ; 0.001 ps timestep for equilibriation
nsteps                  = 300000   ; 300 ps is enough for most solvents
; Nonbonded interactions
rlist                   = 1.2
coulombtype             = PME
rcoulomb                = 1.2
vdw-type                = Cut-off
rvdw                    = 1.2
DispCorr                = EnerPres
; Temperature coupling
tcoupl                  = no
tc-grps                 = System
tau-t                   = 1.0
ref-t                   = 298
; Pressure coupling
pcoupl                  = Berendsen  ; Berendsen for eq and Parrinello-Rahman for prod
tau-p                   = 0.5        ; 0.5 ps for Berendsen and 2.0 ps for Parrinello-Rahman
compressibility         = 4.5e-05
ref-p                   = 1.0
; Constraints
constraints             = h-bonds
constraint-algorithm    = LINCS

; Free energy
free-energy             = yes
couple-moltype          = benzene  ; the molecule you want to solvate
couple-intramol         = no
couple-lambda0          = none     ; no interaction between solute and solvents when lambda=0
couple-lambda1          = vdw-q    ; full interaction between solute and solvents when lambda=1
init_lambda_state       = $lambda  ; will be replaced with a integer listed below later
; lambda_states           0    1    2    3    4    5    6    7    8    9    10   11   12   13   14   15   16   17   18   19
vdw_lambdas             = 0.   0.1  0.2  0.25 0.3  0.35 0.4  0.45 0.5  0.6  0.7  0.8  1.   1.   1.   1.   1.   1.   1.   1.
coul_lambdas            = 0.   0.   0.   0.   0.   0.   0.   0.   0.   0.   0.   0.   0.   0.3  0.6  0.7  0.8  0.9  0.95 1.
nstdhdl                 = 0        ; donot calculate dhdl during equilibration
sc-alpha                = 0.5
sc-power                = 1
sc-sigma                = 0.3
```

```ini
; prod.mdp

integrator              = sd       ; use stochastic intergrator for free energy calculation
dt                      = 0.002    ; 0.002 ps timestep for production run
nsteps                  = 500000   ; 1000 ps is more than enough for most solvents
; Nonbonded interactions
rlist                   = 1.2
coulombtype             = PME
rcoulomb                = 1.2
vdw-type                = Cut-off
rvdw                    = 1.2
DispCorr                = EnerPres
; Temperature coupling
tcoupl                  = no
tc-grps                 = System
tau-t                   = 1.0
ref-t                   = 298
; Pressure coupling
pcoupl         = Parrinello-Rahman  ; Berendsen for eq and Parrinello-Rahman for prod
tau-p                   = 2         ; 0.5 ps for Berendsen and 2.0 ps for Parrinello-Rahman
compressibility         = 4.5e-05
ref-p                   = 1.0
; Constraints
constraints             = h-bonds
constraint-algorithm    = LINCS

; Free energy
free-energy             = yes
couple-moltype          = benzene   ; the molecule you want to solvate
couple-intramol         = no
couple-lambda0          = none      ; no interaction between solute and solvents when lambda=0
couple-lambda1          = vdw-q     ; full interaction between solute and solvents when lambda=1
init_lambda_state       = $lambda   ; will be replaced with a integer listed below later
; lambda_states           0    1    2    3    4    5    6    7    8    9    10   11   12   13   14   15   16   17   18   19
vdw_lambdas             = 0.   0.1  0.2  0.25 0.3  0.35 0.4  0.45 0.5  0.6  0.7  0.8  1.   1.   1.   1.   1.   1.   1.   1.
coul_lambdas            = 0.   0.   0.   0.   0.   0.   0.   0.   0.   0.   0.   0.   0.   0.3  0.6  0.7  0.8  0.9  0.95 1.
nstdhdl                 = 50        ; calculate dhdl every 0.1 ps
sc-alpha                = 0.5
sc-power                = 1
sc-sigma                = 0.3
```

- Generate directories and files for different lambdas. In this example, there are 20 lambda points
```bash
for i in {00..19}; do mkdir lambda-$i; done
for i in {00..19}; do cp conf.gro topol.top lambda-$i; done
for i in {00..19}; do sed "s/\$lambda/$i/" _eq.mdp > lambda-$i/_eq.mdp; done
for i in {00..19}; do sed "s/\$lambda/$i/" _prod.mdp > lambda-$i/_prod.mdp; done
```

### Run simulations for all lambdas

### Analyze results
```bash
# calculate free energy with BAR method, draw histogram to make sure there's sufficient overlap between neighbour lambda points
gmx bar -f lambda-*/prod.xvg -o -oi -oh
```