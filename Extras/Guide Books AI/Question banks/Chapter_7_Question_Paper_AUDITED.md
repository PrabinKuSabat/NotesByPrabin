# MTCS 102 — Chapter 7 Question Paper

## Domain-Specific Architectures

**Primary text:** John L. Hennessy, David A. Patterson, and Christos Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 7  
**Format:** 4 weeks × 15 questions = 60 questions  
**Difficulty:** Medium or High only  
**Solutions / answer key:** Not included

> **Question-counting rule:** One source exercise is exactly one question even if it contains several subparts. No textbook exercise has been split into multiple numbered questions.
>
> **Chapter-7 source rule — audited exception:** GATE CSE does not provide forty direct TPU/NPU/DNN-accelerator/DSA PYQs. To preserve relevance, Questions 1–10 use verified **GATE DA + GATE CSE** PYQs that test matrix, DNN, numerical-representation, probability, optimization, and workload primitives relevant to DSAs. The PYQ itself is kept distinct from any architectural interpretation. Architectural extensions are labelled `ORGANIZER EXTENSION` and do not count as PYQs.
>
> **Book-exercise availability:** Chapter 7 contains **11 numbered textbook exercises (7.1–7.11)**. All eleven are used exactly once and kept intact. Since there are not twenty distinct Chapter-7 book exercises, the remaining class-discussion slots use relevant verified PYQs instead of inventing or splitting book exercises.
>
> **Wording:** The audited PYQ blocks preserve the source task type, numerical data, and MCQ/MSQ alternatives. Method hints and accelerator-specific additions have been removed from PYQ blocks. Any added architectural analysis is placed only in explicitly labelled organizer extensions. No solutions are supplied.
>
> **Figures:** The paper avoids external figures wherever possible. Textbook Exercises 7.3–7.5 depend on Figures 7.33–7.35 in the primary text; those references are retained explicitly.

---

# Week 1 — Matrix Operations, GEMM Dataflows, and Linear-Algebra Primitives

## Verified Previous-Year Questions — Questions 1–10

### Q1. [GATE DA 2024 • Q13] — Medium

Consider
\[
M=\begin{bmatrix}2&-1\\3&1\end{bmatrix}.
\]
Which statement is true?

1. The eigenvalues of \(M\) are non-negative and real.
2. The eigenvalues of \(M\) form a complex-conjugate pair.
3. One eigenvalue is positive and real and the other is zero.
4. One eigenvalue is non-negative and real and the other is negative and real.

### Q2. [GATE DA 2024 • Q35] — High

Let
\[
M=\begin{bmatrix}1&2&3\\3&1&3\\4&3&6\end{bmatrix}.
\]
Find
\[
\det(M^2+12M).
\]

### Q3. [GATE DA 2024 • Q48] — High

Which statements are true? **Select all that apply.**

1. There exist \(M\in\mathbb{R}^{3\times3}\), \(p,q\in\mathbb{R}^3\) such that \(Mx=p\) has a unique solution and \(Mx=q\) has infinitely many solutions.
2. There exist \(M\in\mathbb{R}^{3\times3}\), \(p,q\in\mathbb{R}^3\) such that \(Mx=p\) has no solution and \(Mx=q\) has infinitely many solutions.
3. There exist \(M\in\mathbb{R}^{2\times3}\), \(p,q\in\mathbb{R}^2\) such that \(Mx=p\) has a unique solution and \(Mx=q\) has infinitely many solutions.
4. There exist \(M\in\mathbb{R}^{3\times2}\), \(p,q\in\mathbb{R}^3\) such that \(Mx=p\) has a unique solution and \(Mx=q\) has no solution.

### Q4. [GATE DA 2024 • Q49] — High

Let \(U\) be a subspace of \(\mathbb{R}^3\) and let \(M\in\mathbb{R}^{3\times3}\) be the matrix of the projection onto \(U\).

Which statements are true? **Select all that apply.**

1. If \(\dim U=1\), then the null space of \(M\) is one-dimensional.
2. If \(\dim U=2\), then the null space of \(M\) is one-dimensional.
3. \(M^2=M\).
4. \(M^3=M\).

### Q5. [GATE DA 2024 • Q61] — High

Let
\[
u=\begin{bmatrix}1\\2\\3\\4\\5\end{bmatrix},\qquad M=uu^T.
\]
If \(\sigma_1,\ldots,\sigma_5\) are the singular values of \(M\), find
\[
\sum_{i=1}^{5}\sigma_i.
\]

### Q6. [GATE DA 2025 • Q40] — High

Let \(x_1,\ldots,x_5\) be orthonormal vectors in \(\mathbb{R}^{10}\), and define
\[
A=\sum_{i=1}^{5}x_i x_i^T.
\]
Which statements are correct? **Select all that apply.**

1. The singular values of \(A\) are also eigenvalues of \(A\).
2. The singular values of \(A\) are either 0 or 1.
3. \(\det(A)=1\).
4. \(A\) is invertible.

### Q7. [GATE DA 2026 • Q21] — Medium

Let
\[
M=\begin{bmatrix}\cos\theta&-\sin\theta\\\sin\theta&\cos\theta\end{bmatrix},\qquad \theta=\frac{2\pi}{5}.
\]
Which option equals \(M^{2026}\)?

1. \(M^2\)
2. \(M\)
3. \(M^{-1}\)
4. \(I_2\)

### Q8. [GATE DA 2026 • Q22] — Medium

Let
\[
S_1=\{x\in\mathbb{R}^3:x^Tx\le16\},
\]
and let \(S_2\) be a two-dimensional linear subspace of \(\mathbb{R}^3\).

What is the area of \(S_1\cap S_2\)?

1. \(16\pi\)
2. \(4\pi\)
3. \(4\pi^2\)
4. \(16\pi^2\)

### Q9. [GATE DA 2026 • Q46] — High

Let \(\gamma_1,\gamma_2,\gamma_3\) be the eigenvalues of
\[
A=\begin{bmatrix}1&0&0\\0&\cos t&\sin t\\0&-\sin t&\cos t\end{bmatrix},\qquad t\in[-\pi,\pi].
\]
Find the set of values of \(t\) satisfying
\[
\gamma_1+\gamma_2+\gamma_3=1+\sqrt2.
\]

1. \(\{\pi/3,-\pi/4\}\)
2. \(\{\pi/4,-\pi/3\}\)
3. \(\{\pi/4,-\pi/4\}\)
4. \(\{\pi/3,-\pi/3\}\)

### Q10. [GATE DA 2026 • Q52] — High

Let
\[
M=I_n-\frac1n\mathbf{1}\mathbf{1}^T,
\qquad \mathbf{1}=(1,1,\ldots,1)^T.
\]
Which statements are correct? **Select all that apply.**

1. \(M^T=M\)
2. \(M^2=I_n\)
3. \(\operatorname{trace}(M)=n\)
4. \(M\) is a projection matrix

### Q11. [BOOK • Chapter 7 • Exercise 7.1 • pp. 633–635] — CLASS DISCUSSION — High

Consider the chapter's conventional row-major GEMM computation \(C=AB\), where \(A\) is \(M\times K\), \(B\) is \(K\times N\), and \(C\) is \(M\times N\):

```c
for (int i = 0; i < M; ++i)
    for (int j = 0; j < N; ++j)
        for (int k = 0; k < K; ++k)
            c[i][j] += a[i][k] * b[k][j];
```

Complete **all parts of Exercise 7.1**. Your analysis must include:

- asymptotic operation count and storage when \(M=N=K\);
- how operational intensity changes as matrix dimensions grow;
- row-major memory-access behavior of \(A\), \(B\), and \(C\);
- why the access to \(B\) is problematic in the straightforward loop order;
- the effect of transposing \(B\) to \(B^T\);
- the exercise's comparison with the hypothetical matrix-compute hardware described in the source.

Keep operation counts and memory traffic distinct.

---

### Q12. [BOOK • Chapter 7 • Exercise 7.2 • pp. 635–636] — CLASS DISCUSSION — High

Rewrite matrix multiplication using alternative primitive operations.

**(a)** Starting from the transposed-\(B\) GEMM form, replace the scalar MAC loop with a software **dot product**. State how many times the dot routine is called.

**(b)** Assume the only hardware dot-product primitive is fixed at length two, `dot2`. Rewrite the computation using `dot2`, determine how many calls are required, and explain how an odd value of \(K\) must be handled.

**(c)** Rewrite GEMM using a **SAXPY** primitive

\[
y\leftarrow \alpha x+y.
\]

For each decomposition, identify which operand is naturally reused and what buffering/dataflow a DSA would favor.

---

### Q13. [BOOK • Chapter 7 • Exercise 7.3 • pp. 636–637] — CLASS DISCUSSION — High

Use an **outer-product** decomposition for GEMM.

**(a)** Rewrite GEMM so that each step invokes an outer-product primitive rather than a scalar MAC. You may redesign the primitive's software interface if necessary.

**(b)** Redraw the matrix-multiplication prism in **Figure 7.33** to illustrate separately how the computation is grouped into dot products, SAXPY operations, and outer products.

For each representation, state the principal reuse direction and the corresponding implication for accelerator-local storage.

> **Required textbook material:** Figure 7.33.

---

### Q14. [ORGANIZER EXTENSION • Projection Operators and Accelerator Numerical Range] — CLASS DISCUSSION — High

Consider the centering/projection matrix
\[
A=I_n-\frac1n\mathbf{1}\mathbf{1}^T.
\]
For unit-norm inputs, analyze the range of \(x^TAx\), the eigenvalues of \(A\), and the subspaces on which the quadratic form attains its extrema. Then discuss how these spectral bounds can be used to choose accumulator width or scaling for a fixed-point matrix accelerator.

### Q15. [ORGANIZER EXTENSION • based on GATE DA 2024 Q50] — CLASS DISCUSSION — High

Consider
\[
f(x)=\frac{x^4}{4}-\frac{2x^3}{3}-\frac{3x^2}{2}+1.
\]

**(a)** Find and classify all stationary points.  
**(b)** Compare operation counts for evaluating \(f(x)\), \(f'(x)\), and \(f''(x)\).  
**(c)** Identify reusable subexpressions and propose a small fused datapath for evaluating all three quantities.  
**(d)** Discuss the trade-off between extra parallel multipliers and time-multiplexed arithmetic.

# Week 2 — DNN Layers, Activations, Attention, and Learning Workloads

## Verified Previous-Year Questions — Questions 1–10

### Q1. [GATE DA 2024 • Q18] — Medium

Match Column 1 with Column 2.

| Column 1 | Column 2 |
|---|---|
| (p) Principal Component Analysis | (i) Discriminative Model |
| (q) Naive Bayes Classification | (ii) Dimensionality Reduction |
| (r) Logistic Regression | (iii) Generative Model |

1. p-iii, q-i, r-ii
2. p-ii, q-i, r-iii
3. p-ii, q-iii, r-i
4. p-iii, q-ii, r-i

### Q2. [GATE DA 2024 • Q19] — Medium

Euclidean-distance \(k\)-means clustering is run on 100 points with \(k=3\). If
\[
\begin{bmatrix}1\\1\end{bmatrix}
\quad\text{and}\quad
\begin{bmatrix}-1\\1\end{bmatrix}
\]
are both in cluster 3, which point is **necessarily** also in cluster 3?

1. \([0,0]^T\)
2. \([0,2]^T\)
3. \([2,0]^T\)
4. \([0,1]^T\)

### Q3. [GATE DA 2024 • Q20] — Medium

A dataset has \(K>2\) binary-valued attributes and a two-class classification target. How many independent parameters must be estimated to learn a Naive Bayes classifier?

1. \(2^K+1\)
2. \(2K+1\)
3. \(2^{K+1}+1\)
4. \(K^2+1\)

### Q4. [GATE DA 2024 • Q33] — Medium

Let
\[
f(x)=\frac{1}{1+e^{-x}}.
\]
At a point where \(f(x)=0.4\), find \(f'(x)\), rounded to two decimal places.

### Q5. [GATE DA 2025 • Q38] — Medium

For \(\operatorname{ReLU}(x)=\max(x,0)\), which statements are correct? **Select all that apply.**

1. ReLU is continuous everywhere.
2. ReLU is differentiable everywhere.
3. ReLU is not differentiable at \(x=0\).
4. \(\operatorname{ReLU}(x)=\operatorname{ReLU}(ax)\) for every real \(a\).

### Q6. [GATE DA 2026 • Q11] — Medium

PCA reduces a 100-dimensional feature space to 10 dimensions. What is the angle between the first and tenth principal components?

1. \(0^\circ\)
2. \(90^\circ\)
3. \(90^\circ<\theta\le180^\circ\)
4. \(0^\circ<\theta<90^\circ\)

### Q7. [GATE DA 2026 • Q23] — Medium

Match each task with an appropriate algorithm.

| Task | Algorithm |
|---|---|
| T1 — Clustering | A1 — Markov Chain Monte Carlo |
| T2 — Classification | A2 — K-Medoid |
| T3 — Sampling | A3 — Linear Discriminant Analysis |
| T4 — Feature Extraction | A4 — Naive Bayes |

Which matching is correct?

1. T1:A4, T2:A3, T3:A1, T4:A2
2. T1:A2, T2:A4, T3:A1, T4:A3
3. T1:A3, T2:A4, T3:A1, T4:A2
4. T1:A4, T2:A2, T3:A1, T4:A3

### Q8. [GATE DA 2026 • Q29] — Medium

For a supervised-learning task, the objective being minimized is
\[
f_w(x)=wx,
\]
where \(w,x\in\mathbb{R}\). Stochastic Gradient Descent uses learning rate **0.10**. At the end of iteration \(i\), \(w=10.00\). The input for iteration \(i+1\) is \(x=10.00\).

Find \(w\) at the end of iteration \(i+1\), rounded to two decimal places.

### Q9. [GATE DA 2026 • Q37] — Medium

Which statement about ridge regression is true?

1. Its regularizer is used to correct a model that performs well on test data but poorly on training data.
2. Its regularizer uses the \(L_1\) norm.
3. Ridge regression aims to eliminate parameters having negative values.
4. Its regularizer may increase bias while reducing prediction variance.

### Q10. [GATE DA 2026 • Q56] — High

A fully connected feed-forward MLP has 30 input neurons, hidden layers of 4 and 3 neurons, and one output neuron. There are **no bias parameters**.

Find the total number of learnable parameters.

### Q11. [BOOK • Chapter 7 • Exercise 7.4 • pp. 637–641] — CLASS DISCUSSION — High

Use the AlexNet structure in **Figure 7.34** and complete the **entire Exercise 7.4**.

Your work must include:

**(a)** Number of weights in each convolutional layer and the number of weights used to produce one output activation.

**(b)** Derivation of the activation counts of the convolutional layers, reconciling them with the values given in the figure/caption.

**(c)** For the sixth, seventh, and eighth layers, whose computation is split across two GPUs: data exchanged between the GPUs, weights resident on each GPU, and operation count for one image.

**(d)** Determine where spatial padding is required for strided convolutions and max pooling so that the stated activation dimensions are obtained.

**(e)** Evaluate and interpret SoftMax for four identical logits equal to \(2.0\), \([1,2,3,4]\), a length-four one-hot vector, and a vector with two large and two small values. Then explore scaling all logits, adding a common offset, and changing relative differences.

**(f)** Generate random 1000-element logit vectors from uniform, Gaussian, and exponential distributions; compute SoftMax; plot the output CDFs; determine how many inputs account for 50%, 95%, and 99% of probability mass.

**(g)** Rewrite SoftMax to avoid numerical overflow/instability.

> **Required textbook material:** Figure 7.34.

---

### Q12. [BOOK • Chapter 7 • Exercise 7.5 • p. 641] — CLASS DISCUSSION — High

Use the attention interpretation associated with **Figure 7.35**.

**(a)** Randomly choose a four-dimensional vector and construct an **orthogonal basis** of four vectors that includes it. Unit-length normalization is not required, but every distinct pair must have dot product zero.

**(b)** If one key vector is exactly twice another key vector, determine the ratio of their dot products with the same query.

**(c)** If one key is the negation of another, determine the relation between their dot products and explain what the negative score does after the SoftMax stage.

Connect the result to the use of dot products as similarity measures in attention hardware.

> **Required textbook material:** Figure 7.35.

---

### Q13. [BOOK • Chapter 7 • Exercise 7.6 • pp. 642–643] — CLASS DISCUSSION — High

Study 4-bit integer representations.

**(a)** Enumerate all bit patterns and represented values for both unsigned and two's-complement 4-bit integers.

**(b)** For two's-complement values, derive the bit-level operation/formula that maps \(x\) to \(-x\), excluding the unrepresentable negation of \(-8\).

**(c)** Determine the output bit width required to represent the exact sum of any two 4-bit inputs for each relevant representation.

**(d)** Determine the exact product width for two 4-bit unsigned values.

**(e)** Design the interpretation/sign-extension logic required for one multiplier to correctly accept unsigned × unsigned, signed × signed, and signed × unsigned. Explain what must change at the multiplier inputs and/or product interpretation.

---

### Q14. [ORGANIZER EXTENSION • based on GATE DA 2026 Q55] — CLASS DISCUSSION — High

For a two-feature ridge-regression datapath, use
\[
w=\begin{bmatrix}-3\\4\end{bmatrix},\qquad x=\begin{bmatrix}1\\2\end{bmatrix}.
\]

Design a datapath that can evaluate the prediction \(w^Tx\), an absolute-error term, and an \(L_2\)-regularization term. Count multiplies, additions, absolute-value operations, and accumulator width requirements. Then compare serial, two-lane SIMD, and fully parallel implementations.

### Q15. [ORGANIZER EXTENSION • Classification Metrics under Reduced Precision] — CLASS DISCUSSION — High

A classifier is tested on 20 samples of class \(X\) and 10 samples of class \(Y\). Six \(X\) samples are classified as \(Y\), and two \(Y\) samples are classified as \(X\).

**(a)** Compute the confusion matrix, accuracy, per-class precision, and per-class recall.  
**(b)** Suppose an accelerator uses lower precision and changes exactly two previously correct classifications to incorrect ones. Determine the possible range of overall accuracy.  
**(c)** Explain why accelerator evaluation should report quality metrics together with throughput, latency, energy, and numerical precision.

# Week 3 — Numerical Precision, Accelerator Throughput, and Memory Bandwidth

## Verified Previous-Year Questions — Questions 1–10

### Q1. [GATE CSE 2023 • Q35] — High

IEEE-754 single-precision values are encoded as
\[
P=\texttt{0xC1800000},\qquad Q=\texttt{0x3F5C2EF4}.
\]
Which encoding represents \(P\times Q\)?

1. `0x404C2EF4`
2. `0x405C2EF4`
3. `0xC15C2EF4`
4. `0xC14C2EF4`

### Q2. [GATE CSE 2025 • Set 2 • Q39] — High

Registers contain IEEE-754 single-precision values

```text
RX = 0xC1100000
RY = 0x40C00000
RZ = 0x41400000
```

Let the represented real numbers be \(X,Y,Z\). Which equalities are true? **Select all that apply.**

1. \(4(X+Y)+Z=0\)
2. \(2Y-Z=0\)
3. \(4X+3Z=0\)
4. \(X+Y+Z=0\)

### Q3. [GATE CSE 2025 • Set 2 • Q22] — High

Booth multiplication is used with

```text
M = 1100110111101101
Q = 1010010010101010
```

Find the total number of addition and subtraction operations performed by Booth's algorithm.

### Q4. [GATE CSE 2026 • Set 1 • Q26] — Medium

Two IEEE-754 single-precision values are

```text
X = 0x35C00000
Y = 0x34A00000
```

Let \(Z=X+Y\). Which hexadecimal encoding represents \(Z\)?

1. `35C80000`
2. `35CC0000`
3. `35E80000`
4. `35EC0000`

### Q5. [GATE DA 2026 • Q44] — Medium

Let \(X\sim\operatorname{Bernoulli}(0.3)\) and \(Y\sim N(0,100)\), independently. What is
\[
\operatorname{Var}((2X-1)Y)?
\]

1. 100
2. 90
3. 49
4. 21

### Q6. [GATE DA 2026 • Q53] — High

Let \(X_1,\ldots,X_n\) be independent standard normal random variables and
\[
\bar X=\frac1n\sum_{i=1}^nX_i.
\]
Which statements are correct? **Select all that apply.**

1. \(\sum_i X_i^2\) is chi-square with \(n\) degrees of freedom.
2. \(\sum_i(X_i-\bar X)^2\) is chi-square with \(n-1\) degrees of freedom.
3. \(X_1^2+X_n^2\) is exponential with mean 2.
4. \((\sqrt n\,\bar X)^2\) is chi-square with 2 degrees of freedom.

### Q7. [GATE DA 2026 • Q64] — High

A \(5\times5\) matrix has independent Bernoulli entries with \(P(A_{ij}=1)=0.5\). Find the probability that the sum of the second row is 3 **and** the sum of the third column is 3. Round to two decimal places.

### Q8. [GATE DA 2026 • Q34] — Medium

Let \(X\) be exponentially distributed with mean \(\lambda>0\). If
\[
P(X>5)=0.35,
\]
find
\[
P(X>10\mid X>5),
\]
rounded to two decimal places.

### Q9. [GATE DA 2024 • Q56] — High

Let \(X\sim U[1,3]\) and \(Y\sim U[2,4]\) independently. Find
\[
P(X\ge Y),
\]
rounded to three decimal places.

### Q10. [GATE DA 2024 • Q59] — High

The joint density of \(X,Y\) is
\[
f_{X,Y}(x,y)=
\begin{cases}
2xy,&0<x<2,\ 0<y<x,\\
0,&\text{otherwise}.
\end{cases}
\]
Find
\[
E[Y\mid X=1.5].
\]

### Q11. [BOOK • Chapter 7 • Exercise 7.7 • pp. 643–645] — CLASS DISCUSSION — High

Analyze several **4-bit floating-point formats**.

**(a)** For a format with 1 sign bit, 1 fraction bit, and 2 exponent bits, enumerate all representable real values using the exercise's normal/subnormal exponent convention.

**(b)** Plot the positive representable values on a real-number line and explain the purpose of **subnormal values**.

**(c)** Repeat for a format with 1 sign bit, 0 fraction bits, and 3 exponent bits, and explain how multiplication can be implemented efficiently for this format.

**(d)** Analyze a format with 1 sign bit, 3 fraction bits, and 0 exponent bits.

**(e)** Analyze a format with 1 sign bit, 2 fraction bits, and 1 exponent bit, and explain what expressive range the exponent bit adds relative to part (d).

Compare range, precision, multiplier complexity, and suitability for a DNN accelerator.

---

### Q12. [BOOK • Chapter 7 • Exercise 7.8 • p. 645] — CLASS DISCUSSION — High

The first TPU uses a \(256\times256\) systolic array and has peak throughput of approximately **92 TOPS**. Assume each systolic cell performs one multiply-accumulate per cycle and one MAC counts as two operations. The source gives approximately **30 GB/s** of weight-memory bandwidth for one-byte weights.

**(a)** Infer the TPU clock rate.

**(b)** Determine the time required to load a **64 KiB** tile of one-byte weights.

**(c)** Determine the required steady-state **weight reuse / arithmetic intensity** for both peak compute and peak memory bandwidth to be reached simultaneously.

For TPU v4 lite, use two HBM stacks, **614 GB/s** aggregate memory bandwidth, four \(128\times128\) systolic arrays treated for this exercise as one \(256\times256\) resource, and bf16 weights of **2 bytes** each.

**(d)** Determine the load time for 64 Ki weights and the arithmetic intensity required to balance peak memory bandwidth and peak compute.

Explain why DNN accelerators invest heavily in on-chip reuse.

---

### Q13. [BOOK • Chapter 7 • Exercise 7.9 • pp. 645–646] — CLASS DISCUSSION — High

Use the chapter's H100 and Cerebras WSE-2 specifications.

For H100 SXM, use approximately:

- 132 SMs,
- 367 TFLOP/s FP32 peak,
- 1979 TFLOP/s bf16 tensor-core peak,
- peak clocks near 2 GHz.

For WSE-2, use:

- 40 GB on-chip SRAM,
- 20 PB/s on-chip bandwidth,
- 12 × 100-Gb/s external Ethernet links.

Complete all parts:

**(a)** From FP32 throughput and clock rate, infer the approximate number of FP32 functional units per SM and in the complete H100.

**(b)** Use the bf16 tensor-core peak to estimate the relative quantity/effective throughput of tensor-core arithmetic resources compared with conventional SIMD FP32 resources.

**(c)** GPT-3 has **175 billion parameters**. At one byte per parameter, determine how long it would take to stream one model copy to the WSE-2 over its external network interface.

Identify whether the limiting rate is on-chip bandwidth or off-chip ingress bandwidth.

---

### Q14. [ORGANIZER EXTENSION • Distance Metrics in a DSA] — CLASS DISCUSSION — High

For two \(d\)-dimensional vectors, compare hardware for Manhattan distance and squared Euclidean distance.

**(a)** Count arithmetic primitives required by each metric.  
**(b)** Propose a \(k\)-lane SIMD datapath for both.  
**(c)** Identify the reduction-tree depth.  
**(d)** Compare fixed-point overflow requirements.  
**(e)** Explain when avoiding square-root evaluation changes neither nearest-neighbor ordering nor classification result.

### Q15. [ORGANIZER EXTENSION • Bayes-Inference Datapath] — CLASS DISCUSSION — High

A binary diagnostic model uses
\[
P(+\mid D)=0.8,\quad P(+\mid\bar D)=0.1,\quad P(D)=0.3.
\]

Design a datapath to compute the posterior \(P(D\mid+)\). Compare:

- direct probability-domain arithmetic,
- fixed-point arithmetic with a shared divider,
- log-domain arithmetic for products of many likelihood terms.

Quantify the operations and discuss precision, normalization, and throughput trade-offs.

# Week 4 — DSA Scaling, Optimization, Economics, and Workload Fit

## Verified Previous-Year Questions — Questions 1–10

### Q1. [GATE DA 2026 • Q45] — High

Evaluate
\[
L=\lim_{n\to\infty}\sum_{k=0}^{n}e^{-n}\frac{n^k}{k!}.
\]

1. \(0.5\)
2. \(1\)
3. \(0\)
4. \(e^{-1}\)

### Q2. [GATE DA 2026 • Q12] — Medium

A classifier dataset contains 1000 samples. The first 100 are reserved for final testing. Leave-One-Out Cross-Validation is used on the remaining samples for model selection.

How many validation splits are generated?

1. 10
2. 512
3. 900
4. 1000

### Q3. [GATE DA 2026 • Q27] — High

Let
\[
f(x)=x^3-3x^2+2,\qquad x\in(-1,3].
\]
Which statements are correct? **Select all that apply.**

1. \(f(x)\) has exactly two roots in \([-0.9,0]\).
2. \(f(x)\) has a minimum at 2 only.
3. \(f(x)\) has a maximum at 0 only.
4. \(f(x)\) has a root at 1.

### Q4. [GATE DA 2024 • Q36] — High

A fair six-sided die is thrown independently until **two consecutive even outcomes** are observed. What is the expected number of throws?

1. 2
2. 4
3. 6
4. 8

### Q5. [GATE DA 2024 • Q57] — High

Let \(X\) have exponential density
\[
f_X(x)=\begin{cases}\lambda e^{-\lambda x},&x\ge0,\\0,&\text{otherwise},\end{cases}
\qquad \lambda>0.
\]
If
\[
5E(X)=\operatorname{Var}(X),
\]
find \(\lambda\), rounded to one decimal place.

### Q6. [GATE DA 2024 • Q58] — High

Let \(T,S\) be events with
\[
P(\bar T)=0.6,\qquad P(S\mid T)=0.3,\qquad P(S\mid\bar T)=0.6.
\]
Find \(P(T\mid S)\), rounded to two decimal places.

### Q7. [GATE DA 2024 • Q60] — High

Evaluate
\[
\lim_{x\to0}\frac{\ln((1+x^2)\cos x)}{x^2}.
\]

### Q8. [GATE DA 2024 • Q15] — Medium

For any twice-differentiable function \(f:\mathbb{R}\to\mathbb{R}\), suppose
\[
f'(x^*)=0,\qquad f''(x^*)>0.
\]
Then \(f\) necessarily has a ______ at \(x=x^*\).

1. local minimum
2. global minimum
3. local maximum
4. global maximum

### Q9. [GATE DA 2024 • Q37] — High

Let
\[
f(x)=\begin{cases}
-x,&x<-2,\\
ax^2+bx+c,&-2\le x\le2,\\
x,&x>2.
\end{cases}
\]
Which values make \(f\) continuous and differentiable?

1. \(a=1/4,b=0,c=1\)
2. \(a=1/2,b=0,c=0\)
3. \(a=0,b=0,c=0\)
4. \(a=1,b=1,c=-4\)

### Q10. [GATE DA 2025 • Q45] — High

A two-class problem in \(\mathbb{R}^d\) has class means \(\mu_{red}\) and \(\mu_{green}\). A classifier computes
\[
f(x)=\|\mu_{red}-x\|^2-\|\mu_{green}-x\|^2
\]
and assigns `red` if \(f(x)<0\), and `green` otherwise.

Which statements are correct? **Select all that apply.**

1. \(x=0\) is assigned `green` if \(\|\mu_{red}\|<\|\mu_{green}\|\).
2. \(f\) is a linear function of \(x\).
3. \(f(x)=w^Tx+b\), where \(w,b\) depend on the two class means.
4. \(f\) is a quadratic polynomial in \(x\).

### Q11. [BOOK • Chapter 7 • Exercise 7.10 • pp. 646–647] — CLASS DISCUSSION — High

Analyze the economics of an NVIDIA H100 using the complete source exercise.

Use:

- cloud rental price in 2024: approximately **$2–$4 per hour**,
- approximate purchase price: **$30,000**,
- H100 TDP: **700 W**,
- electricity: **$0.10–$0.20/kWh**.

Complete all parts:

**(a)** Annualize continuous rental revenue.

**(b)** If hardware purchase were the only vendor cost, calculate purchase-price payback time.

**(c)** Calculate the worst-case annual electricity bill.

**(d)** Recalculate payback after electricity and identify other lifecycle/TCO costs.

**(e)** Assume those additional operating costs equal the electricity cost; calculate payback again.

**(f)** Assume an 8-year useful life. Estimate profit, then repeat under a model in which rental rates approximately halve every two years as new GPU generations arrive.

**(g)** Replace the unrealistic 100% utilization assumption with a defensible utilization model. Discuss differences between training and inference utilization and incorporate CPU-host CAPEX/OPEX, staffing, infrastructure, financing/cost of capital, idle capacity, failures, and service time.

If the simple rental economics no longer appear plausible, explain what missing factors could reconcile them.

---

### Q12. [BOOK • Chapter 7 • Exercise 7.11 • p. 647] — CLASS DISCUSSION — High

The first Anton molecular-dynamics system simulated approximately a **64 Å × 64 Å × 64 Å** box while the physical computer can be approximated as a box roughly **1 m** on a side.

A simulation step represents approximately **2.5 fs** of simulated time and requires about **10 μs** of wall-clock time. The physical model effectively requires global interaction/synchronization on an outer step.

Complete all parts:

**(a)** Calculate the spatial expansion factor from simulated space to physical hardware.

**(b)** Calculate the temporal slowdown factor from simulated time to wall-clock time.

**(c)** Explain why the two factors are surprisingly similar. Use the speed-of-light constraint on both the simulated physical system and the real machine.

**(d)** Estimate the fastest plausible synchronization/simulation step for a warehouse-scale machine with characteristic physical size \(10^2\) m and \(10^3\) m. Extend the argument qualitatively to a geographically distributed cloud.

Explain why physical scale can become an architectural limit even when arithmetic throughput continues to increase.

---

### Q13. [ORGANIZER EXTENSION • DSA Tile Scaling] — CLASS DISCUSSION — High

A domain-specific accelerator executes a workload in **100 ns** on one tile. **90%** of the work is ideally partitionable, while every additional tile adds **10 ns** of coordination overhead to the total execution time.

**(a)** Derive \(T(N)\).  
**(b)** Find the tile count that minimizes execution time.  
**(c)** Generalize for parallel fraction \(p\) and per-added-tile overhead \(h\).  
**(d)** Extend the model to include a shared-memory-bandwidth ceiling.  
**(e)** Explain how network-on-chip contention and synchronization alter the optimum.

### Q14. [ORGANIZER EXTENSION • Probabilistic Kernel Mapping] — CLASS DISCUSSION — High

Consider the Poisson cumulative kernel
\[
F(n)=\sum_{k=0}^{n}e^{-n}\frac{n^k}{k!}.
\]

Design an accelerator-oriented implementation that avoids explicit factorial overflow. Compare recurrence-based term generation, log-domain computation, and table/approximation approaches. Analyze parallel reduction, normalization, exponentiation support, numerical error, and throughput.

### Q15. [ORGANIZER EXTENSION • Stable Statistical Reductions] — CLASS DISCUSSION — High

For a stream \(X_1,\ldots,X_n\), a DSA must compute
\[
\sum_iX_i^2,\qquad \bar X,\qquad \sum_i(X_i-\bar X)^2.
\]

Compare one-pass, two-pass, pairwise/tree, and Welford-style implementations. Discuss numerical stability, accumulator precision, reduction depth, partial-state size, memory bandwidth, reproducibility, and scalability across multiple accelerator tiles.

# Organizer Source Ledger — Audited

## Primary textbook

Hennessy, Patterson, and Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 7, **Domain-Specific Architectures**, pp. 633–647.

### All Chapter-7 numbered exercises used

| Week-Q | Exercise | Page(s) | Main architectural focus |
|---|---:|---:|---|
| W1-Q11 | 7.1 | 633–635 | GEMM complexity, operational intensity, locality |
| W1-Q12 | 7.2 | 635–636 | Dot / dot2 / SAXPY decomposition |
| W1-Q13 | 7.3 | 636–637 | Outer-product dataflow |
| W2-Q11 | 7.4 | 637–641 | AlexNet layers, SoftMax, multi-GPU data |
| W2-Q12 | 7.5 | 641 | Attention and dot-product similarity |
| W2-Q13 | 7.6 | 642–643 | Integer representations and multiplier design |
| W3-Q11 | 7.7 | 643–645 | Reduced-precision floating point |
| W3-Q12 | 7.8 | 645 | TPU systolic array, bandwidth and arithmetic intensity |
| W3-Q13 | 7.9 | 645–646 | H100 arithmetic resources and WSE bandwidth |
| W4-Q11 | 7.10 | 646–647 | H100 CAPEX/OPEX and accelerator economics |
| W4-Q12 | 7.11 | 647 | Anton, synchronization and physical scaling |

## Authoritative GATE papers

- GATE DA 2024 official master paper: https://gate2024.iisc.ac.in/question-papers/
- GATE DA 2025 official master paper: https://gate2025.iitr.ac.in/question-papers.html
- GATE DA/CS 2026 official master papers: https://gate2026.iitg.ac.in/QPs-answer-keys.html

Indexed cross-checks used for repaired 2025 questions include:

- GATE DA 2025 Q40: https://gateoverflow.in/460982/gate-da-2025-question-40
- GATE DA 2025 Q45: https://gateoverflow.in/460977/gate-da-2025-question-45

## Audit notes

- GATE DA 2024 Q13, Q18–Q20, Q35–Q37, Q48–Q50, and Q56–Q61 were restored to their source task type and alternatives/data.
- Method hints were removed from PYQ blocks.
- The duplicated GATE CSE 2024 core-count question was removed because Chapter 1 is frozen as its canonical placement.
- Every architectural transformation beyond a PYQ is now explicitly labelled `ORGANIZER EXTENSION`.
- All 11 available numbered Chapter-7 book exercises remain included exactly once.
