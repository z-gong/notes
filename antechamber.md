Setup atom type and AM1BCC charge with OpenBabel and Ambertools
```
obabel -:'Oc1ccccc1' -O step0.mol2 -h --gen3d
```
```
antechamber -fi mol2 -fo ac -i step0.mol2 -o step1.ac -c bcc -s 2
```
