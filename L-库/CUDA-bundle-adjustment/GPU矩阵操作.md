
## Device functions (template matrix and verctor operation)

### assignment operations
```cpp
using AssignOP = void(*)(Scalar*, Scalar);
__device__ inline void ASSIGN(Scalar* address, Scalar value) { *address = value; }
__device__ inline void ACCUM(Scalar* address, Scalar value) { *address += value; }
__device__ inline void DEACCUM(Scalar* address, Scalar value) { *address -= value; }
__device__ inline void ACCUM_ATOMIC(Scalar* address, Scalar value) { atomicAdd(address, value); }
__device__ inline void DEACCUM_ATOMIC(Scalar* address, Scalar value) { atomicAdd(address, -value); }
```

### dot product 

```cpp
// recursive dot product for inline expansion
template <int N>
__device__ inline Scalar dot_(const Scalar* a, const Scalar* b)
{
	return dot_<N - 1>(a, b) + a[N - 1] * b[N - 1];
}

template <>
__device__ inline Scalar dot_<1>(const Scalar* a, const Scalar* b) { return a[0] * b[0]; }

// recursive dot product for inline expansion (strided access pattern)
template <int N, int S1, int S2>
__device__ inline Scalar dot_stride_(const Scalar* a, const Scalar* b)
{
	static_assert(S1 == PDIM || S1 == LDIM, "S1 must be PDIM or LDIM");
	static_assert(S2 == 1 || S2 == PDIM || S2 == LDIM, "S2 must be 1 or PDIM or LDIM");
	return dot_stride_<N - 1, S1, S2>(a, b) + a[S1 * (N - 1)] * b[S2 * (N - 1)];
}

template <>
__device__ inline Scalar dot_stride_<1, PDIM, 1>(const Scalar* a, const Scalar* b) { return a[0] * b[0]; }
template <>
__device__ inline Scalar dot_stride_<1, LDIM, 1>(const Scalar* a, const Scalar* b) { return a[0] * b[0]; }
template <>
__device__ inline Scalar dot_stride_<1, PDIM, PDIM>(const Scalar* a, const Scalar* b) { return a[0] * b[0]; }
template <>
__device__ inline Scalar dot_stride_<1, LDIM, LDIM>(const Scalar* a, const Scalar* b) { return a[0] * b[0]; }

```



### matrix operation

<mark style="background: #FFB8EBA6;">例子</mark>

```cpp
// Hpp += = JPT*Ω*JP
// matrix(tansposed)-matrix product: C = AT*B
MatTMulMat<PDIM, MDIM, PDIM, ACCUM_ATOMIC>(JP, JP, Hpp.at(iP), omega);

// Hpl += = JPT*Ω*JL
// 
MatTMulMat<PDIM, MDIM, LDIM, ASSIGN>(JP, JL, Hpl.at(edge2Hpl[iE]), omega);
```


```cpp
// matrix(tansposed)-vector product: b = AT*x
template <int M, int N, AssignOP OP = ASSIGN>
__device__ inline void MatTMulVec(const Scalar* A, const Scalar* x, Scalar* b, Scalar omega)
{
#pragma unroll
	for (int i = 0; i < M; i++)
		OP(b + i, omega * dot_<N>(A + i * N, x));
}

// matrix(tansposed)-matrix product: C = AT*B
template <int L, int M, int N, AssignOP OP = ASSIGN>
__device__ inline void MatTMulMat(const Scalar* A, const Scalar* B, Scalar* C, Scalar omega)
{
#pragma unroll
	for (int i = 0; i < N; i++)
		MatTMulVec<L, M, OP>(A, B + i * M, C + i * L, omega);
}

// matrix-vector product: b = A*x
template <int M, int N, int S = 1, AssignOP OP = ASSIGN>
__device__ inline void MatMulVec(const Scalar* A, const Scalar* x, Scalar* b)
{
#pragma unroll
	for (int i = 0; i < M; i++)
		OP(b + i, dot_stride_<N, M, S>(A + i, x));
}

// matrix-matrix product: C = A*B
template <int L, int M, int N, AssignOP OP = ASSIGN>
__device__ inline void MatMulMat(const Scalar* A, const Scalar* B, Scalar* C)
{
#pragma unroll
	for (int i = 0; i < N; i++)
		MatMulVec<L, M, 1, OP>(A, B + i * M, C + i * L);
}

// matrix-matrix(tansposed) product: C = A*BT
template <int L, int M, int N, AssignOP OP = ASSIGN>
__device__ inline void MatMulMatT(const Scalar* A, const Scalar* B, Scalar* C)
{
#pragma unroll
	for (int i = 0; i < N; i++)
		MatMulVec<L, M, N, OP>(A, B + i, C + i * L);
}
```



### norm

```cpp
// squared L2 norm
template <int N>
__device__ inline Scalar squaredNorm(const Scalar* x) { return dot_<N>(x, x); }
template <int N>
__device__ inline Scalar squaredNorm(const Vecxd<N>& x) { return squaredNorm<N>(x.data); }

// L2 norm
template <int N>
__device__ inline Scalar norm(const Scalar* x) { return sqrt(squaredNorm<N>(x)); }
template <int N>
__device__ inline Scalar norm(const Vecxd<N>& x) { return norm<N>(x.data); }


```




