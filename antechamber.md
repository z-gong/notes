Setup atom type and AM1BCC charge with OpenBabel and Ambertools
```
obabel -:'Oc1ccccc1' -O step0.sdf -h --gen3d
antechamber -fi mdl -fo ac -i step0.sdf -o step1.ac -c bcc -nc 0 -s 2
```

Strange behavior: antechamber consider NC=C as aromatic and assigned following atom types
```
ATOM      1  N1  MOL     1       1.047  -0.083  -0.057 -0.827600        nh
ATOM      2  C1  MOL     1       2.398  -0.037  -0.104  0.030000        c2
ATOM      3  C2  MOL     1       3.123   0.748  -0.902 -0.326000        c2
ATOM      4  H1  MOL     1       0.577  -0.713   0.583  0.379800        hn
ATOM      5  H2  MOL     1       0.494   0.512  -0.662  0.379800        hn
ATOM      6  H3  MOL     1       2.890  -0.718   0.588  0.129000        h4
ATOM      7  H4  MOL     1       2.656   1.433  -1.599  0.116500        ha
ATOM      8  H5  MOL     1       4.207   0.719  -0.873  0.116500        ha
```
