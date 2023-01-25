

```cpp
template <int N>
using Vecxd = Vec<Scalar, N>;

template <int N>
using GpuVecxd = GpuVec<Vecxd<N>>;

using PxPBlockPtr = BlockPtr<Scalar, PDIM, PDIM>;
using LxLBlockPtr = BlockPtr<Scalar, LDIM, LDIM>;
using PxLBlockPtr = BlockPtr<Scalar, PDIM, LDIM>;
using Px1BlockPtr = BlockPtr<Scalar, PDIM, 1>;
using Lx1BlockPtr = BlockPtr<Scalar, LDIM, 1>;
```



```cpp


struct LessRowId
{
	__device__ bool operator()(const Vec3i& lhs, const Vec3i& rhs) const
	{
		if (lhs[0] == rhs[0])
			return lhs[1] < rhs[1];
		return lhs[0] < rhs[0];
	}
};

struct LessColId
{
	__device__ bool operator()(const Vec3i& lhs, const Vec3i& rhs) const
	{
		if (lhs[1] == rhs[1])
			return lhs[0] < rhs[0];
		return lhs[1] < rhs[1];
	}
};

```



```cpp
template <typename T, int ROWS, int COLS>
struct MatView
{
	__device__ inline T& operator()(int i, int j) { return data[j * ROWS + i]; }
	__device__ inline MatView(T* data) : data(data) {}
	T* data;
};

template <typename T, int ROWS, int COLS>
struct ConstMatView
{
	__device__ inline T operator()(int i, int j) const { return data[j * ROWS + i]; }
	__device__ inline ConstMatView(const T* data) : data(data) {}
	const T* data;
};

template <typename T, int ROWS, int COLS>
struct Matx
{
	using View = MatView<T, ROWS, COLS>;
	using ConstView = ConstMatView<T, ROWS, COLS>;
	__device__ inline T& operator()(int i, int j) { return data[j * ROWS + i]; }
	__device__ inline T operator()(int i, int j) const { return data[j * ROWS + i]; }
	__device__ inline operator View() { return View(data); }
	__device__ inline operator ConstView() const { return ConstView(data); }
	T data[ROWS * COLS];
};

using MatView3x3d = MatView<Scalar, 3, 3>;
using ConstMatView3x3d = ConstMatView<Scalar, 3, 3>;

```