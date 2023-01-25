
- MatView：对一块设备内存，进行数据读取 
- Matx：创建一块内存，并进行矩阵元素的读取

```cpp
template <typename T, int ROWS, int COLS>
struct MatView
{
	__device__ inline T& operator()(int i, int j) { return data[j * ROWS + i]; }
	__device__ inline MatView(T* data) : data(data) {}
	T* data;
};


template <typename T, int ROWS, int COLS>
struct Matx
{
	using View = MatView<T, ROWS, COLS>;
	__device__ inline T& operator()(int i, int j) { return data[j * ROWS + i]; }
	__device__ inline operator View() { return View(data); }

	T data[ROWS * COLS];
};

using MatView3x3d = MatView<Scalar, 3, 3>;

```

