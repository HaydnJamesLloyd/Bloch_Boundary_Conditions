# Bloch_Boundary_Conditions
The MD simulation code for Stockmayer Fluids under Bloch Boundary Conditions.

Bloch Boundary Conditions were developed to decouple the wavelengths/wavevectors that can be studied from the geometry of the system. Under Periodic Boundary Conditions (PBCs), the wavevectors to be studied must be on the reciprocal lattice, and so studying $\lim k \to 0$ requires very large simulation cells.

Bloch Boundary Conditions can be defined similarly to PBCs, but any multipole within the unit cell lattice vector $\mathbf{R}$ from the simulation cell gain a scaling factor $\exp(\text{i} \mathbf{k} \cdot \mathbf{R})$. The wavevector $\mathbf{k}$ defines the Bloch Boundary Conditions, and for uniqueness can be chosen to be within the First Brillouin Zone of the reciprocal lattice. Then, one can simulate the simulation cell in a similar fashion to PBCs.

Under Bloch Boundary Conditions, the electrostatic energy for a system of charges is given by: 
```math
E(\mathbf{k}) = \frac{1}{2}\sideset{}{'}\sum_{\mathbf{R}} \sum_{i, j} q_i q_j \frac{\text{e}^{\text{i} \mathbf{k} \cdot \mathbf{R}}}{\lvert \mathbf{r}_{ij} - \mathbf{R} \lvert},
```
where the sum over $i$ and $j$ is over particles in the simulation cell only.
We define the Bloch Summation to be the effective electrostatic pair potential betwtext{e}n two charges in the simulation cell as
```math
S(\mathbf{r},\mathbf{k}) = \sideset{}{'}\sum_{\mathbf{R}} \frac{\text{e}^{\text{i} \mathbf{k} \cdot \mathbf{R}}}{\lvert \mathbf{r} - \mathbf{R} \lvert}
```
which depends only on the wavevector and geometry of the lattice. Note this is a Bloch function, and so this gives responses at wavevectors $\{ \mathbf{k} + \mathbf{G} \}$, i.e. shifted by a wavevector that we control.

The above expression does not converge for 3D space, and so most be manipulated in a way analogous to the Ewald summation for PBCs. Thus, this can be calculated practically using:
```math
S(\mathbf{r},\mathbf{k}) = \frac{4\uppi}{V} \sum_{\mathbf{G}} \frac{\exp(-\lvert\mathbf{k}+\mathbf{G}\lvert^2/4\alpha) 	\text{e}^{\text{i}[\mathbf{k}+\mathbf{G}]\cdot \mathbf{r}}}{\lvert \mathbf{k}+\mathbf{G} \lvert^2}  - 2(\alpha/\pi)^{3/2}\text{e}^{\text{i} \mathbf{k} \cdot \mathbf{R}}\delta_{rR} + \sum_{\mathbf{R}} \frac{	\text{e}^{	\text{i} \mathbf{k} \cdot \mathbf{R}}\text{erfc}(\sqrt{\alpha} r)}{r}.
```
The equivalent equation for dipoles can be derived by making the transformation $q_i \to -\mu_i \cdot \nabla_i$, and so
```math
\underline{S}_\mu(\mathbf{r},\mathbf{k}) =\frac{4\uppi}{V} \sum_{\mathbf{G}} \frac{(\mathbf{k}+\mathbf{G}) \otimes (\mathbf{k}+\mathbf{G})}{\lvert \mathbf{k}+\mathbf{G} \lvert^2} \text{e}^{\text{i}(\mathbf{k}+\mathbf{G})\cdot\mathbf{r}}\exp(-\frac{\lvert \mathbf{k}+\mathbf{G}\lvert^2}{4\alpha}) \\
        - \frac{4\alpha^{3/2}}{3\sqrt{\uppi}} \underline{\text{I}} \sum_{\mathbf{R}} \text{e}^{\text{i} \mathbf{k}\cdot\mathbf{R}} \delta_{\mathbf{rR}} \\
        +\sideset{}{'}\sum_{\mathbf{R}} \text{e}^{\text{i} \mathbf{k}\cdot\mathbf{R}} \left[\underline{\text{I}} B(\lvert \mathbf{r}-\mathbf{R} \lvert) - (\mathbf{r}-\mathbf{R}) \otimes (\mathbf{r}-\mathbf{R}) C(\lvert\mathbf{r}-\mathbf{R}\lvert)\right].
```
where the functions $B(r)$ and $C(r)$ are the same as in the Ewald summation for dipoles:
```math
B(r) = \text{erfc}(\sqrt{\alpha}r)/r^3 + 2(\alpha/\pi)^{1/2} \frac{\exp(-\alpha r^2)}{r^2},
```
and
```math
C(r) = 3\text{erfc}(\sqrt{\alpha}r)/r^5 + 2(\alpha/\pi)^{1/2} (2\alpha + 3/r^2) \frac{\exp(-\alpha r^2)}{r^2},
```
From this expression, one can then calculate the energy of a system of dipoles as
```math
E(\mathbf{k}) = \frac{1}{2} \sum_{i,j} \mu_I^T \underline{S}_\mu(\mathbf{r},\mathbf{k}) \mu_j.
```
