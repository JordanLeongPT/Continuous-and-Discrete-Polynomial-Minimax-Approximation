\textbf{Repository title:} Continuous and Discrete Polynomial Minimax Approximation (Proj 8)

\medskip
This project implements and compares continuous and discrete minimax polynomial approximation on the interval $[-1,1]$. We study three representative target functions,
\[
  f_1(x) = e^x,\qquad
  f_2(x) = |x|,\qquad
  f_3(x) = \sin(20x),
\]
and construct degree-$n$ Chebyshev approximants in the $\ell_\infty$ sense.

On the continuous side, we use a numerically stable Remez algorithm with Chebyshev/DCT initial guesses, dense monitoring grids, and optional Lawson polishing. The code includes equioscillation checks and spectral-extrema search to locate alternation points and verify Chebyshev optimality. For smooth functions ($e^x$ and $\sin(20x)$), the computed minimax error reaches machine precision for moderate degrees; for the non-smooth function $|x|$, the error decays at the optimal rate $\mathcal{O}(1/n)$, matching the Bernstein constant.

On the discrete side, we formulate the $\ell_\infty$ approximation problem on equispaced samples as a linear program and solve it with the HiGHS optimizer, followed by Lawson active-set iterations when they further reduce the supremum norm. We report results for both clean data and noisy data with bounded perturbations of size $\delta = 10^{-3}$, and compare the optimal error $t^*$, the noisy error $t^*_{\text{noisy}}$, and the robust upper bound $t^*_{\text{noisy}} + \delta$. The experiments illustrate the convergence behavior, numerical stability, and noise robustness of minimax approximation for smooth vs.\ non-smooth targets and for different polynomial degrees and sample sizes.
