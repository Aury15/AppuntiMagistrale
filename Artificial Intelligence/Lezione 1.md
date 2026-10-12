$$
\boxed{
\begin{array}{l}
\textbf{Batch Perceptron} \\[8pt]
\textbf{input:} \quad \text{A training set } (\mathbf{x}_1, y_1), \dots, (\mathbf{x}_m, y_m) \\
\textbf{initialize:} \quad \mathbf{w}^{(1)} = (0, \dots, 0) \\[4pt]
\textbf{for } t = 1, 2, \dots \\
\quad \textbf{if } (\exists\, i \text{ s.t. } y_i \langle \mathbf{w}^{(t)}, \mathbf{x}_i \rangle \le 0) \textbf{ then} \\
\quad\quad \mathbf{w}^{(t+1)} = \mathbf{w}^{(t)} + y_i \mathbf{x}_i \quad \color{red}\triangleright\ \mathbf{w}^{(t)} \text{ is "WRONG" on } (\mathbf{x}_i, y_i) \\
\quad \textbf{else} \\
\quad\quad \textbf{output } \mathbf{w}^{(t)}
\end{array}
}
$$

> [!algorithm] Batch Perceptron
> **input:** A training set $(\mathbf{x}_1, y_1), \dots, (\mathbf{x}_m, y_m)$
> **initialize:** $\mathbf{w}^{(1)} = (0, \dots, 0)$
> 
> **for** $t = 1, 2, \dots$
> > **if** $(\exists\, i \text{ s.t. } y_i \langle \mathbf{w}^{(t)}, \mathbf{x}_i \rangle \le 0)$ **then**
> > > $\mathbf{w}^{(t+1)} = \mathbf{w}^{(t)} + y_i \mathbf{x}_i$  *(nota: $\mathbf{w}^{(t)}$ sbaglia su $(\mathbf{x}_i, y_i)$)*
> > 
> > **else**
> > > **output** $\mathbf{w}^{(t)}$
