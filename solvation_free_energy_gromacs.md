### Generate simulation files

- With whatever tools you like, build a box of solvents and put one solute molecue inside, generate *gro*, and *top* files for GROMACS


- Write *mdp* file suitable for the underlaying force field and solvation free enegry calculation
```ini
integrator              = sd  ; use stochastic intergrator for free energy calculation
dt                      = 0.002
nsteps                  = 1000000  ; 2 ns is more than enough for most solvents
; Output control
nstlog                  = 1000
nstenergy               = 100
; Neighbor searching
cutoff-scheme           = verlet
rlist                   = 1.2
; Electrostatics
coulombtype             = PME
rcoulomb                = 1.2
; VdW
vdw-type                = Cut-off
rvdw                    = 1.2
DispCorr                = EnerPres
; Temperature coupling
tcoupl                  = no
tc-grps                 = System
tau-t                   = 1.0
ref-t                   = 298
; Pressure coupling
Pcoupl                  = Berendsen  ; use Parranello-Rahman if you are picky
tau-p                   = 0.5
compressibility         = 4.5e-05
ref-p                   = 1.0
; Velocity generation
gen-vel                 = yes
; Bonds
constraints             = h-bonds
constraint-algorithm    = LINCS

; free energy
free-energy             = yes
couple-moltype          = benzene  ; the molecule you want to solvate
couple-intramol         = no
couple-lambda0          = none  ; no interaction between solute and solvents when lambda=0
couple-lambda1          = vdw-q  ; full interaction between solute and solvents when lambda=1
init_lambda_state       = $lambda  ; will be replaced with a integer listed below later
; lambda_states           0   1   2   3   4   5   6   7   8   9   10  11
vdw_lambdas             = 0.0 0.2 0.3 0.4 0.6 0.8 1.0 1.0 1.0 1.0 1.0 1.0
coul_lambdas            = 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.3 0.6 0.8 0.9 1.0
calc_lambda_neighbors   = 2  ; if larger than 1, MBAR algorithm is used for BAR analysis
nstdhdl                 = 50
sc-alpha                = 0.5
sc-power                = 1
sc-sigma                = 0.3
```

- Generate a series of files for different lambda points
```bash
for i in {00..11}; do mkdir lambda-$i; done
for i in {00..11}; do cp conf.gro topol.top lambda-$i; done
for i in {00..11}; do sed "s/\$lambda/$i/" _fe.mdp > lambda-$i/_fe.mdp; done
for i in {00..11}; do cd lambda-$i; gmx grompp -f _fe.mdp -o fe; cd ..; done
```

### Run simulations

### Analyze results
```bash
# calculate free energy with BAR method, draw histogram to make sure there's sufficient overlap between neighbour lambda points. Ignore the first 200 ps of data
gmx bar -f lambda*/fe.xvg -o -oi -oh -b 200
```

