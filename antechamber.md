Setup atom type and AM1BCC charge with OpenBabel and Ambertools
```
obabel -:'Oc1ccccc1' -O step0.sdf -h --gen3d
```
```
antechamber -fi mdl -fo ac -i step0.sdf -o step1.ac -c bcc -s 2
```
