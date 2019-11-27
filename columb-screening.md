### Tang Toennies Damping

$$
f_{n}(r) = 1-c_{ij} \mathrm{e}^{-b_{ij}r} \sum_{k=0}^{n} \frac{(b_{ij} r)^{k}}{k !} \\
\chi = \frac{q_i q_j}{4\pi \epsilon_0} \\
\alpha = \frac{1}{r} \\
\alpha^\prime = -\frac{1}{r^2} \\
\beta =  c_{ij}\mathrm{e}^{-b_{ij}r} \\
\beta^\prime = - c_{ij} b_{ij} \mathrm{e}^{-b_{ij}r} \\
\gamma = \sum_{k=0}^{n} \frac{b_{ij}^k r^{k}}{k !} \\
\gamma^\prime = \sum_{k=1}^{n} \frac{b_{ij}^{k} r^{k-1}}{(k-1) !}\\
U_{corr} = \chi \alpha \beta \gamma \\
F_{corr} = -\frac{\part U}{\part r} = -\chi (\alpha^\prime \beta \gamma + \alpha \beta^\prime \gamma + \alpha \beta \gamma^\prime)
$$

