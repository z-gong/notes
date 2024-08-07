### Bond
```math
\begin{aligned}
E &= \frac{1}{2}k(r_{ij}-r_0)^2 \\
\mathbf{F_i} &= -\frac{\partial E}{\partial \mathbf{r_i}}
= -k(r_{ij}-r_0)\frac{\partial \lVert\mathbf{r_{ij}}\rVert}{\partial \mathbf{r_i}}
= k(r_{ij}-r_0)\frac{\mathbf{r_{ij}}}{\lVert\mathbf{r_{ij}}\rVert}
\end{aligned}
```

### Angle
```math
\begin{aligned}
E &= \frac{1}{2}k(\theta-\theta_0)^2 \\
\theta &= \arccos{\frac{\mathbf{r_{ji}}\cdot \mathbf{r_{jk}}}{r_{ji} r_{jk}}} \\
C &= \cos{\theta} \\
S &= \sin{\theta} \\
\mathbf{F_i} &= -\frac{\partial E}{\partial \mathbf{r_i}} 
= -k(\theta-\theta_0)\frac{\partial \theta}{\partial \mathbf{r_i}}
= k(\theta-\theta_0) \frac{1}{S} \frac{\partial C}{\partial \mathbf{r_i}} \\
\frac{\partial C}{\partial \mathbf{r_i}} &=\frac{1}{r_{jk}} \partial \frac{\mathbf{r_{ji}} \cdot \mathbf{r_{jk}}}{r_{ji}} / \partial \mathbf{r_i}
=\frac{1}{r_{jk} r_{ji}^2} \left (\mathbf{r_{jk}} \cdot r_{ji} - \mathbf{r_{ji}} \cdot \mathbf{r_{jk}} \cdot \frac{\mathbf{r_{ji}}}{r_{ji}} \right) \\
\frac{\partial C}{\partial \mathbf{r_i}}  &= - \frac{C}{r_{ji}^2}\mathbf{r_{ji}} + \frac{1}{r_{ji} r_{jk}} \mathbf{r_{jk}}
\end{aligned}
```

### Dihedral
```math
\begin{aligned}
E &= k_1(1+\cos{\phi})+k_2(1-\cos{2\phi})+k_3(1+\cos{3\phi})+k_4(1-\cos{4\phi}) \\
E &= (2k_2+k_1+k_3) + (3k_3-k_1) C + (8k_4-2k_2) C^2 - 4k_3 C^3 - 8k_4 C^4 \\
C &= \cos{\phi} \\
S &= \sin{\phi} \\
\end{aligned}
```

### Tang-Toennies Damping

```math
\begin{aligned}
f_{n}(r) &= 1-c_{ij} \mathrm{e}^{-b_{ij}r} \sum_{k=0}^{n} \frac{(b_{ij} r)^{k}}{k !} \\
\chi &= \frac{q_i q_j}{4\pi \epsilon_0} \\
\alpha &= \frac{1}{r} \\
\alpha^\prime &= -\frac{1}{r^2} \\
\beta &=  c_{ij}\mathrm{e}^{-b_{ij}r} \\
\beta^\prime &= - c_{ij} b_{ij} \mathrm{e}^{-b_{ij}r} \\
\gamma &= \sum_{k=0}^{n} \frac{b_{ij}^k r^{k}}{k !} \\
\gamma^\prime &= \sum_{k=1}^{n} \frac{b_{ij}^{k} r^{k-1}}{(k-1) !}\\
U_{\mathrm{corr}} &= \chi \alpha \beta \gamma \\
F_{\mathrm{corr}} &= -\frac{\partial U}{\partial r} = -\chi (\alpha^\prime \beta \gamma + \alpha \beta^\prime \gamma + \alpha \beta \gamma^\prime)
\end{aligned}
```