# MTCS 102 — Chapter 7 Question Paper

## Domain-Specific Architectures

**Primary text:** John L. Hennessy, David A. Patterson, and Christos Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 7  
**Format:** 4 weeks × 15 questions = 60 questions  
**Difficulty:** Medium or High only  
**Solutions / answer key:** Not included

> **Question-counting rule:** One source exercise is exactly one question even if it contains several subparts. No textbook exercise has been split into multiple numbered questions.
>
> **Chapter-7 source rule:** GATE CSE does not provide enough direct TPU/NPU/DNN-accelerator/DSA PYQs to fill forty chapter-specific slots without forcing unrelated questions into this chapter. Therefore, Questions 1–10 each week use verified national-level previous-year questions, prioritizing **GATE DA** for DNN, matrix, optimization, probability, and ML workload primitives, and **GATE CSE** where arithmetic or parallel-performance questions transfer directly to accelerator architecture.
>
> **Book-exercise availability:** Chapter 7 contains **11 numbered textbook exercises (7.1–7.11)**. All eleven are used exactly once and kept intact. Since there are not twenty distinct Chapter-7 book exercises, the remaining class-discussion slots use relevant verified PYQs instead of inventing or splitting book exercises.
>
> **Wording:** PYQs and book exercises are reformatted/paraphrased for readability while preserving the numerical data and assessed task. No solutions are supplied.
>
> **Figures:** The paper avoids external figures wherever possible. Textbook Exercises 7.3–7.5 depend on Figures 7.33–7.35 in the primary text; those references are retained explicitly.

---

# Week 1 — Matrix Operations, GEMM Dataflows, and Linear-Algebra Primitives

## Verified Previous-Year Questions — Questions 1–10

### Q1. [GATE DA 2024 • Q13] — Medium

Consider

\[
M=
\begin{bmatrix}
2 & -1\\
3 & 1
\end{bmatrix}.
\]

Determine the eigenvalues of \(M\) and classify them correctly as real/repeated or a non-real complex-conjugate pair. Show the characteristic polynomial used.

---

### Q2. [GATE DA 2024 • Q35] — High

Let

\[
M=
\begin{bmatrix}
1&2&3\\
3&1&3\\
4&3&6
\end{bmatrix}.
\]

Find

\[
\det(M^2+12M).
\]

Do not expand the full product unless necessary; an eigenvalue/determinant identity may be used.

---

### Q3. [GATE DA 2024 • Q48] — High

Consider linear systems whose coefficient matrices have the following dimensions:

1. \(3\times3\),
2. \(2\times3\),
3. \(3\times2\).

For each dimensional case, determine which statements about the existence and uniqueness of solutions to \(Ax=b\) can be guaranteed from rank and dimension. Distinguish unique solution, infinitely many solutions, no solution, and dependence on the right-hand side \(b\).

---

### Q4. [GATE DA 2024 • Q49] — High

Let \(M\) be the matrix representing an **orthogonal projection** of vectors in \(\mathbb{R}^3\) onto a proper subspace.

Evaluate the following properties:

1. \(M^2=M\),
2. \(M^3=M\),
3. eigenvalues of \(M\) can only be \(0\) or \(1\),
4. the null-space dimension is determined by the dimension of the projected subspace.

Select all statements that must hold.

---

### Q5. [GATE DA 2024 • Q61] — High

Let

\[
u=
\begin{bmatrix}
1\\2\\3\\4\\5
\end{bmatrix},
\qquad
M=uu^T.
\]

Find the **sum of all singular values** of \(M\). Relate the rank-one structure of \(M\) to its non-zero singular value.

---

### Q6. [GATE DA 2025 • Q40] — High

Let \(x_1,x_2,x_3,x_4,x_5\in\mathbb{R}^{10}\) be mutually orthonormal vectors and define

\[
A=\sum_{i=1}^{5}x_i x_i^T.
\]

Determine the rank, possible eigenvalues/singular values, whether \(A\) is invertible, whether \(\det(A)=0\), and whether \(A\) behaves as a projection.

---

### Q7. [GATE DA 2026 • Q21] — Medium

Let

\[
M=
\begin{bmatrix}
\cos\theta&-\sin\theta\\
\sin\theta& \cos\theta
\end{bmatrix},
\qquad
\theta=\frac{2\pi}{5}.
\]

Which of the following is equal to \(M^{2026}\)?

1. \(M^2\)
2. \(M\)
3. \(M^{-1}\)
4. \(I_2\)

---

### Q8. [GATE DA 2026 • Q22] — Medium

Let

\[
S_1=\{x\in\mathbb{R}^3\mid x^Tx\le16\},
\]

and let \(S_2\) be any two-dimensional linear subspace of \(\mathbb{R}^3\).

Find the area of \(S_1\cap S_2\).

---

### Q9. [GATE DA 2026 • Q46] — High

Let \(\gamma_1,\gamma_2,\gamma_3\) be the eigenvalues of

\[
A=
\begin{bmatrix}
1&0&0\\
0&\cos t&\sin t\\
0&-\sin t&\cos t
\end{bmatrix},
\qquad t\in[-\pi,\pi].
\]

Find all values of \(t\) satisfying

\[
\gamma_1+\gamma_2+\gamma_3=1+\sqrt2.
\]

---

### Q10. [GATE DA 2026 • Q52] — High

Let

\[
M=I_n-\frac1n\mathbf{1}\mathbf{1}^T,
\qquad
\mathbf{1}=(1,1,\ldots,1)^T\in\mathbb{R}^n.
\]

Determine which statements are correct:

1. \(M^T=M\)
2. \(M^2=I_n\)
3. \(\operatorname{trace}(M)=n\)
4. \(M\) is a projection matrix

Explain the geometric action of \(M\).

---

## CLASS DISCUSSION — Questions 11–15

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

### Q14. [GATE DA 2026 • Q65] — CLASS DISCUSSION — High

Let

\[
A=I_n-\frac1n\mathbf{1}\mathbf{1}^T,
\qquad
S=\{x\in\mathbb{R}^n:\|x\|_2=1\}.
\]

Determine

\[
\max_{x\in S}x^TAx.
\]

Then interpret the maximization in terms of the eigenstructure of a projection operator and explain how eigenvalue bounds can be used when estimating the numerical range of matrix operations on an accelerator.

---

### Q15. [GATE DA 2024 • Q50] — CLASS DISCUSSION — High

Consider

\[
f(x)=\frac{x^4}{4}-\frac{2x^3}{3}-\frac{3x^2}{2}+1.
\]

Determine all stationary points and classify each as a local maximum, local minimum, or neither.

For class discussion, compare the arithmetic required for evaluating \(f(x)\), \(f'(x)\), and \(f''(x)\), and identify which computations can be fused or reused in a domain-specific datapath.

---
# Week 2 — DNN Layers, Activations, Attention, and Learning Workloads

## Verified Previous-Year Questions — Questions 1–10

### Q1. [GATE DA 2024 • Q18] — Medium

Match each machine-learning method to the most appropriate description.

| Method | Description |
|---|---|
| Principal Component Analysis | dimensionality reduction / feature extraction |
| Naive Bayes | generative probabilistic classifier |
| Logistic Regression | discriminative classifier |

Select the matching that correctly assigns all three.

---

### Q2. [GATE DA 2024 • Q19] — Medium

In a \(k\)-means clustering problem with three clusters, Cluster 3 currently contains

\[
v_1=[1,1],\qquad v_2=[-1,1].
\]

Which candidate is located at their centroid?

1. \([0,0]\)
2. \([0,2]\)
3. \([2,0]\)
4. \([0,1]\)

Show the centroid calculation.

---

### Q3. [GATE DA 2024 • Naive-Bayes parameter-count PYQ] — High

Consider a two-class classification problem with \(k\) **binary input attributes** and a Naive Bayes classifier.

Count the independent parameters required to specify:

1. the class prior distribution, and
2. all class-conditional Bernoulli distributions.

Give the total number of independent parameters as a function of \(k\).

---

### Q4. [GATE DA 2024 • Q33] — Medium

For the sigmoid function

\[
\sigma(x)=\frac{1}{1+e^{-x}},
\]

suppose \(\sigma(x)=0.4\). Find

\[
\frac{d\sigma(x)}{dx}
\]

at that point.

---

### Q5. [GATE DA 2025 • Q38] — Medium

Let

\[
\operatorname{ReLU}(x)=\max(x,0).
\]

Evaluate the statements:

1. ReLU is continuous everywhere.
2. ReLU is differentiable everywhere.
3. ReLU is not differentiable at \(x=0\).
4. \(\operatorname{ReLU}(x)=\operatorname{ReLU}(ax)\) for every real \(a\).

Select all correct statements.

---

### Q6. [GATE DA 2026 • Q11] — Medium

PCA reduces a 100-dimensional feature space to 10 dimensions.

What is the angle between the **first** and **tenth** principal components?

1. \(0^\circ\)
2. \(90^\circ\)
3. \(90^\circ<\theta\le180^\circ\)
4. \(0^\circ<\theta<90^\circ\)

---

### Q7. [GATE DA 2026 • Q23] — Medium

Match each task with an appropriate algorithm.

| Task | Algorithm |
|---|---|
| T1 — Clustering | A1 — Markov Chain Monte Carlo |
| T2 — Classification | A2 — K-Medoid |
| T3 — Sampling | A3 — Linear Discriminant Analysis |
| T4 — Feature extraction | A4 — Naive Bayes |

Identify the correct task-to-algorithm mapping.

---

### Q8. [GATE DA 2026 • Q29] — Medium

For a supervised-learning task, the scalar model is

\[
f_w(x)=wx.
\]

Stochastic gradient descent with learning rate \(0.10\) is used. At the end of iteration \(i\), \(w=10.00\). At iteration \(i+1\), \(x=10.00\).

Using the objective/update rule specified in the source question, determine the new value of \(w\), rounded to two decimal places.

---

### Q9. [GATE DA 2026 • Q37] — Medium

Which statement about **ridge regression** is correct?

1. Its regularizer is intended to correct a model that fits test data well but training data poorly.
2. It uses an \(L_1\)-norm penalty.
3. It is designed specifically to eliminate negative-valued parameters.
4. Its regularizer may increase model bias while reducing prediction variance.

---

### Q10. [GATE DA 2026 • Q56] — High

A fully connected feed-forward MLP has:

- 30 input neurons,
- 4 neurons in hidden layer 1,
- 3 neurons in hidden layer 2,
- 1 output neuron,
- **no bias parameters**.

Find the total number of learnable parameters.

---

## CLASS DISCUSSION — Questions 11–15

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

### Q14. [GATE DA 2026 • Q55] — CLASS DISCUSSION — High

Ridge regression uses

\[
y_{\text{pred}}=w^Tx,
\qquad
w=\begin{bmatrix}-3\\4\end{bmatrix},
\qquad
x=\begin{bmatrix}1\\2\end{bmatrix}.
\]

The true target is \(y_{\text{true}}=x_1+x_2\), the prediction error is measured by **mean absolute error**, and the regularization weight is \(0.20\).

Calculate the complete regularized loss for this instance using the ridge penalty specified by the source. Then identify the primitive operations that a training accelerator must execute for the prediction, loss, and regularization terms.

---

### Q15. [GATE DA 2026 • Q47] — CLASS DISCUSSION — High

A classifier is tested on 20 stories truly written by Author \(X\) and 10 stories truly written by Author \(Y\). Of the \(X\) stories, 6 are classified as \(Y\). Of the \(Y\) stories, 2 are classified as \(X\).

Determine overall accuracy, precision for both classes, and recall for both classes.

Then discuss why accelerator benchmarking should report workload-level quality/accuracy as well as raw throughput when reduced precision or approximate computation is used.

---
# Week 3 — Numerical Precision, Accelerator Throughput, and Memory Bandwidth

## Verified Previous-Year Questions — Questions 1–10

### Q1. [GATE CSE 2023 • Q35] — High

Two IEEE-754 single-precision floating-point values are stored as

\[
P=\texttt{0xC1800000},
\qquad
Q=\texttt{0x3F5C2EF4}.
\]

Determine the IEEE-754 single-precision representation of \(P\times Q\). Show sign, exponent, and significand handling.

---

### Q2. [GATE CSE 2025 • Set 2 • Q39] — High

Registers hold IEEE-754 single-precision encodings

```text
RX = 0xC1100000
RY = 0x40C00000
RZ = 0x41400000
```

Let the represented real values be \(X,Y,Z\). Determine which equalities are true:

1. \(4(X+Y)+Z=0\)
2. \(2Y-Z=0\)
3. \(4X+3Z=0\)
4. \(X+Y+Z=0\)

---

### Q3. [GATE CSE 2025 • Set 2 • Q22] — High

Booth multiplication is used for

```text
M = 1100110111101101
Q = 1010010010101010
```

Determine the total number of **addition and subtraction operations** performed by Booth's algorithm. Explain how transitions in the multiplier determine arithmetic operations.

---

### Q4. [GATE CSE 2026 • Set 1 • Q26] — Medium

Two IEEE-754 single-precision values are

```text
X = 0x35C00000
Y = 0x34A00000
```

Let \(Z=X+Y\). Determine the hexadecimal IEEE-754 representation of \(Z\).

---

### Q5. [GATE DA 2026 • Q44] — Medium

Let

\[
X\sim\operatorname{Bernoulli}(0.3),
\qquad
Y\sim N(0,100),
\]

with \(X\) and \(Y\) independent. Find

\[
\operatorname{Var}\big((2X-1)Y\big).
\]

---

### Q6. [GATE DA 2026 • Q53] — High

Let \(X_1,\ldots,X_n\) be independent standard normal random variables and

\[
\bar X=\frac1n\sum_{i=1}^{n}X_i.
\]

Evaluate the claims:

1. \(\sum_i X_i^2\) follows a chi-square distribution with \(n\) degrees of freedom.
2. \(\sum_i(X_i-\bar X)^2\) follows a chi-square distribution with \(n-1\) degrees of freedom.
3. \(X_1^2+X_n^2\) follows an exponential distribution with mean 2.
4. \((\sqrt n\,\bar X)^2\) follows a chi-square distribution with 2 degrees of freedom.

Select all correct statements.

---

### Q7. [GATE DA 2026 • Q64] — High

Let \(A\) be a \(5\times5\) random matrix whose 25 entries are independent Bernoulli variables with

\[
P(A_{ij}=1)=0.5.
\]

Find the probability that the sum of the second row is 3 **and** the sum of the third column is 3. Round to two decimal places. Pay attention to the element shared by that row and column.

---

### Q8. [GATE DA 2026 • Q34] — Medium

Let \(X\) be exponentially distributed with mean \(\lambda>0\). If

\[
P(X>5)=0.35,
\]

find

\[
P(X>10\mid X>5).
\]

Round to two decimal places.

---

### Q9. [GATE DA 2024 • Q56] — High

Let

\[
X\sim U[1,3],
\qquad
Y\sim U[2,4],
\]

independently. Find

\[
P(X\ge Y).
\]

Show the corresponding region in the \(X-Y\) plane.

---

### Q10. [GATE DA 2024 • Q59] — High

Suppose the joint density of \(X,Y\) is proportional to \(2xy\) over

\[
0<x<2,\qquad 0<y<x.
\]

Using the normalized conditional density implied by the source problem, find

\[
E[Y\mid X=1.5].
\]

---

## CLASS DISCUSSION — Questions 11–15

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

### Q14. [GATE DA 2026 • Q36] — CLASS DISCUSSION — High

Four points are

\[
P_1=[2,3,-1],\quad
P_2=[3,1,1],\quad
P_3=[5,-2,3],\quad
P_4=[3,3,3].
\]

Hierarchical agglomerative clustering uses **Manhattan distance**. Determine which pair merges first.

Then compare the hardware cost of Manhattan distance with Euclidean distance for a DSA: count the required subtractions, absolute-value operations, multiplications, additions, and square-root operations.

---

### Q15. [GATE DA 2026 • Q57] — CLASS DISCUSSION — High

A diagnostic test has

\[
P(+\mid D)=0.8,\qquad P(-\mid D)=0.2,
\]

\[
P(+\mid \bar D)=0.1,\qquad P(-\mid \bar D)=0.9,
\]

and disease prevalence \(P(D)=0.3\).

Find

\[
P(D\mid +).
\]

Then identify the multiply/add/divide operations required by Bayes inference and discuss how fixed-point or low-precision arithmetic could affect a probabilistic inference accelerator.

---
# Week 4 — DSA Scaling, Optimization, Economics, and Workload Fit

## Verified Previous-Year Questions — Questions 1–10

### Q1. [GATE CSE 2024 • Set 1 • Q55] — High

A program takes **100 ns** on a **2 GHz single-core** machine.

- 90% of the original execution time is fully parallelizable.
- Using each additional core adds **10 ns** of overhead.

Determine the number of cores that **minimizes total execution time**. Model the parallelizable portion as ideally divided among the selected cores and include the stated per-additional-core overhead.

---

### Q2. [GATE DA 2026 • Q12] — Medium

A classifier has:

- 10 output classes,
- 512-dimensional input vectors,
- 1000 total samples,
- the first 100 samples reserved for final testing.

Leave-One-Out Cross-Validation is applied to the remaining samples for model selection.

How many validation splits are generated?

1. 10
2. 512
3. 900
4. 1000

---

### Q3. [GATE DA 2026 • Q27] — High

Let

\[
f(x)=x^3-3x^2+2,
\qquad x\in(-1,3].
\]

Evaluate the source statements concerning the number/location of roots, whether \(x=2\) is a minimum, whether \(x=0\) is a maximum, and whether \(x=1\) is a root. Select all statements that are mathematically correct.

---

### Q4. [GATE DA 2024 • Probability PYQ — Consecutive Outcomes] — High

A sequence of independent fair trials is generated until the specified pattern of **two consecutive even outcomes** occurs.

Using the source experiment and its equiprobable outcomes, determine the expected number of trials required to observe the target pattern for the first time. Model the process with states representing whether the previous trial already satisfies the first condition in the pattern.

---

### Q5. [GATE DA 2024 • Q57] — High

A continuous random variable has the probability-density function specified in the GATE DA 2024 source question.

Determine the unknown normalization/parameter value and the requested probability/expectation quantity by:

1. normalizing the density,
2. writing the correct integration limits,
3. evaluating the requested statistic.

> **Organizer note:** Use the official GATE DA 2024 Q57 statement during final typesetting so that the exact piecewise density is preserved.

---

### Q6. [GATE DA 2024 • Q58] — High

Let \(T\) and \(S\) be events. The source gives

\[
P(\bar T)=0.6,\qquad
P(S\mid T)=0.3,\qquad
P(S\mid \bar T)=0.6.
\]

Determine the conditional probability requested in the original question using Bayes' rule and the law of total probability. Show the complete probability tree or equivalent algebra.

---

### Q7. [GATE DA 2024 • Q60] — High

Evaluate

\[
\lim_{x\to0}
\frac{\ln\!\left((1+x^2)\cos x\right)}{x^2},
\]

using a Taylor-series, standard-limit, or L'Hôpital-based derivation.

---

### Q8. [GATE DA 2024 • Q15] — Medium

For the differentiable function specified in the source GATE DA 2024 question, use the supplied first- and second-derivative information to determine whether the stated point can be classified as a local minimum.

State clearly which derivative condition is sufficient and which is only necessary.

> **Organizer note:** Preserve the exact source function/derivative values during the master-source audit.

---

### Q9. [GATE DA 2024 • Q37] — High

A piecewise function has the form

\[
f(x)=
\begin{cases}
-x, & x<-2,\\
ax^2+bx+c, & -2\le x\le2,\\
x, & x>2.
\end{cases}
\]

Determine \(a,b,c\) so that \(f\) is **continuous and differentiable** at both joining points \(x=-2\) and \(x=2\).

---

### Q10. [GATE DA 2025 • Nearest-Class-Mean PYQ] — High

A classifier represents each class by its **mean feature vector** and assigns a query \(x\in\mathbb{R}^d\) to the class whose mean is nearest under Euclidean distance.

Using the class means and query vector given in the source GATE DA 2025 question, determine the predicted class.

Then rewrite

\[
\|x-\mu_i\|_2^2
\]

so that terms common to all classes are removed, showing the dot-product form attractive for accelerator implementation.

> **Organizer note:** Retain the exact source vectors from the official GATE DA 2025 master paper during final print layout.

---

## CLASS DISCUSSION — Questions 11–15

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

### Q13. [GATE CSE 2024 • Set 1 • Q55, DSA Extension] — CLASS DISCUSSION — High

Revisit the core-count optimization problem from Question 1, but interpret each "core" as an accelerator tile.

The baseline program requires **100 ns**, 90% is parallelizable, and every added tile introduces **10 ns** of coordination overhead.

**(a)** Derive total time \(T(N)\).

**(b)** Find the optimal \(N\).

**(c)** Generalize the result for a parallel fraction \(p\) and per-tile overhead \(h\).

**(d)** Explain how on-chip network traffic, synchronization, memory bandwidth, and tile under-utilization change the ideal model for a real TPU/NPU.

---

### Q14. [GATE DA 2026 • Q45, Accelerator Interpretation] — CLASS DISCUSSION — High

Consider

\[
L=\lim_{n\to\infty}\sum_{k=0}^{n}e^{-n}\frac{n^k}{k!}.
\]

Determine \(L\).

Then discuss how this expression relates to a cumulative Poisson probability and why probabilistic kernels can stress accelerator support for exponentials, factorial/log-domain computation, normalization, and reductions.

---

### Q15. [GATE DA 2026 • Q53, Statistical-Kernel Extension] — CLASS DISCUSSION — High

For independent \(X_i\sim N(0,1)\), analyze the computational kernels required to evaluate

\[
\sum_i X_i^2,\qquad
\bar X,\qquad
\sum_i(X_i-\bar X)^2.
\]

Using the distributional results from the original GATE question, design a parallel reduction strategy for a DSA.

Your discussion must compare one-pass versus two-pass computation, numerical stability, tree reductions, partial sums, accumulator precision, and bandwidth versus arithmetic cost.

---

# Organizer Source Ledger

## Primary textbook

John L. Hennessy, David A. Patterson, and Christos Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 7, **Domain-Specific Architectures**, Case Studies and Exercises by Cliff Young, pp. 633–647.

### All Chapter 7 numbered exercises used

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

## Official GATE paper sources

Use the official master papers as the authoritative source during final printing/source audit:

- **GATE 2024 master question papers:** `https://gate2024.iisc.ac.in/question-papers/`
- **GATE 2025 master question papers:** `https://gate2025.iitr.ac.in/question-papers.html`
- **GATE 2026 master question papers:** `https://gate2026.iitg.ac.in/QPs-answer-keys.html`

Relevant papers are GATE DA and GATE CS for 2024, 2025, and 2026.

## Verification / selection notes

- GATE DA is used heavily because Chapter 7 is explicitly built around DNN and matrix-compute workloads, while GATE CSE has no stable TPU/NPU/DSA PYQ category.
- GATE CSE questions are retained where they test numerical representation, arithmetic, or quantitative parallel scaling that maps directly to accelerator design.
- All 11 available Chapter-7 book exercises are used exactly once.
- No textbook exercise is split into multiple numbered questions.
- No solution or answer key is included.
- Questions requiring textbook diagrams explicitly identify the required figure.
- A few PYQs whose long source parameter blocks were not safely reproduced are explicitly marked for insertion from the official paper during the final source audit.

# Completion Status for the Seven-Chapter Project

The seven numbered chapters in the selected textbook are:

1. Fundamentals of Quantitative Design and Analysis
2. Memory Hierarchy Design
3. Instruction-Level Parallelism and Its Exploitation
4. Data-Level Parallelism in Vector, SIMD, and GPU Architectures
5. Thread-Level Parallelism
6. Warehouse-Scale Architectures for Utility Computing
7. Domain-Specific Architectures

With this file, **all seven numbered chapters now have a question-paper draft**.

The textbook continues after Chapter 7 with appendices rather than additional numbered chapters:

- Appendix A — Instruction Set Principles
- Appendix B — Review of Memory Hierarchy
- Appendix C — Pipelining: Basic and Intermediate Concepts
- Appendix D — Storage Systems
- Appendix E — Embedded Systems
- Appendix F — Interconnection Networks
- Appendix G — Vector Processors in More Depth
- Appendix H — Hardware and Software for VLIW and EPIC
- Appendix I — Large-Scale Multiprocessors and Scientific Applications
- Appendix J — Computer Arithmetic
- Appendix K — Survey of Instruction Set Architectures
- Appendix L — Advanced Concepts on Address Translation
- Appendix M — Historical Perspectives and References

These appendices are **not required to complete the original Chapter 1–7 question-bank plan** unless the course instructor explicitly wants appendix-specific material.

## Recommended remaining project work

Before treating the complete seven-chapter bank as final, perform one master audit across Chapters 1–7:

1. verify every PYQ against the official master paper;
2. remove accidental duplicates across chapters unless repetition is intentional;
3. verify every textbook exercise number/page and every figure dependency;
4. confirm every weekly Q1–Q10 satisfies the chosen chapter-specific PYQ rule;
5. confirm all class-discussion questions are genuinely medium/high difficulty;
6. normalize formatting, labels, mathematics, and source-ledger style;
7. collect any required image assets before PDF generation;
8. only then proceed to the separate **solutions + weekly presentation** phase.
