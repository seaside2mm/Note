---
date: 星期一, 一月 23日 2023, 9:24:55 上午
modified: 星期四, 二月 9日 2023, 3:00:38 下午
---


#  fixed_vector 创建 CPU / GPU 内存的数组

- 拷贝一个指针指向的数据

```cpp
using Vec2d = Vec<Scalar, 2>;
template <int N>
using Vecxd = Vec<Scalar, N>;
```

```cpp
#define HOST_DEVICE __host__ __device__ inline
template <typename T, int N>
struct Vec
{
	//将一块其他格式的数组指针的数据进行拷贝，用Vec管理。
	//比如vector的data，或者eigen的data
    HOST_DEVICE Vec(const T* values) { for (int i = 0; i < N; i++) data[i] = values[i]; }
    
    T data[N];
}
```


#  device_buffer 用于包装 GPU 数据的指针

- `T* data_` :  数据首地址
- `size_t size_, capacity_` : 实际数据大小，持有内存空间大小
- `bool allocated_` ： 是否分配

```cpp
//T类型的GPU数组
template <typename T>
using GpuVec = DeviceBuffer<T>;

using GpuVec1d = GpuVec<Scalar>;
using GpuVec2d = GpuVec<Vec2d>;
using GpuVec1i = GpuVec<int>;
using GpuVec1b = GpuVec<uint8_t>;


template <int N>
using GpuVecxd = GpuVec<Vecxd<N>>;
```
```mermaid
graph TD
%%此图示由列表文本转换而成！%%





```
```cpp
template <typename T>
class DeviceBuffer
{
public:
	// 分配GPU内存,未初始化
	void allocate(size_t count)
	{
		if (data_ && capacity_ >= count)
			return;

		destroy();
		CUDA_CHECK(cudaMalloc(&data_, sizeof(T) * count));
		capacity_ = count;
		allocated_ = true;
	}


	void resize(size_t size)
	{
		allocate(size);
		size_ = size;
	}
	// 映射一块内存，相当于浅拷贝
	void map(size_t size, void* data)
	{
		data_ = (T*)data;
		size_ = size;
		allocated_ = false;
	}
	
	void assign(size_t size, const void* h_data)
	{
		resize(size);
		upload((T*)h_data);
	}

	void upload(const T* h_data) // cudaCopy
	void download(T* h_data) const 
	void fillZero() //cuda Memset
};

```

#  device_matrix


## BlockPtr：  连续矩阵块的索引

-  `T* data_ ` ：指向一块 GPU 内存，一般是连续矩阵块

``` cpp
template <typename T, int BLOCK_ROWS, int BLOCK_COLS>
class BlockPtr
{
	static const int BLOCK_AREA = BLOCK_ROWS * BLOCK_COLS;
	__device__ T* at(int i) { return data_ + i * BLOCK_AREA; }
	__device__ const T* at(int i) const { return data_ + i * BLOCK_AREA; }
};

using PxPBlockPtr = BlockPtr<Scalar, PDIM, PDIM>;
using LxLBlockPtr = BlockPtr<Scalar, LDIM, LDIM>;
using PxLBlockPtr = BlockPtr<Scalar, PDIM, LDIM>;
using Px1BlockPtr = BlockPtr<Scalar, PDIM, 1>;
using Lx1BlockPtr = BlockPtr<Scalar, LDIM, 1>;
```


## DeviceBlockVector：Dense 矩阵管理的外部接口

进一步管理 DeviceBuffer，作为外部使用接口。注意类中实际创建了一个 T 类型数据，而 BLOCK_AREA 的数组需要计算管理。

- `DeviceBuffer<T> values_` :  创建了 T 类型的数据，用于提供 GPU 的起始地址 
- `int size_` :  block 数

```cpp
static const int BLOCK_AREA = BLOCK_ROWS * BLOCK_COLS;
//每个矩阵块的开始地址
using BlockPtrT = BlockPtr<T, BLOCK_ROWS, BLOCK_COLS>; 

// 注意此处以下的Hpp等由于是对角矩阵，构建时并不是一个完整的矩阵，而是类似稀疏矩阵，只将对角元素按照id顺序放入数组中。
// Block: [ Jpp1, Jpp2, Jpp3, Jpp4, ...]
using GpuPxPBlockVec = DeviceBlockVector<Scalar, PDIM, PDIM>;
using GpuLxLBlockVec = DeviceBlockVector<Scalar, LDIM, LDIM>;
using GpuPxLBlockVec = DeviceBlockVector<Scalar, PDIM, LDIM>;
using GpuPx1BlockVec = DeviceBlockVector<Scalar, PDIM, 1>;
using GpuLx1BlockVec = DeviceBlockVector<Scalar, LDIM, 1>;

```

```cpp
template <typename T, int BLOCK_ROWS, int BLOCK_COLS>
class DeviceBlockVector
{
public:

	void resize(int size)
	{
		size_ = size;
		values_.resize(size * BLOCK_AREA);
	}

	void map(int size, T* data)
	{
		size_ = size; //block num
		// buffer中所需的一维数组：size * BLOCK_AREA
		values_.map(size * BLOCK_AREA, data);
	}

	//使用DeviceBuffer内部API
    void copyTo(DeviceBlockVector& rhs) const     
    void fillZero()

	operator BlockPtrT() const { return BlockPtrT((T*)values_.data()); }
};

```


## DeviceBlockMatrix：稀疏矩阵的外部接口


- `DeviceBuffer<T> values_` ： GPU 内存
- `DeviceBuffer<int> outerIndices_, innerIndices_` ： 稀疏矩阵索引数组
- `int rows_, cols_` : 矩阵大小
- `int nnz_` ：  non zeros 数
- `int outerSize_, innerSize_` ： 索引数组长度

> [!info] 
> 稀疏矩阵定义查看： [[SparseCore]]
>   ROW_MAJOR 则是 CSR 

> [!faq] 
> 由于类内部定义了 `BlockPtrT`, 可以使用内部类的 `Hpl.at(edge2Hpl[iE])` 


```c++
static const int BLOCK_AREA = BLOCK_ROWS * BLOCK_COLS;
//每个矩阵块的开始地址
using BlockPtrT = BlockPtr<T, BLOCK_ROWS, BLOCK_COLS>; 

using GpuHplBlockMat = DeviceBlockMatrix<Scalar, PDIM, LDIM, COL_MAJOR>;
using GpuHscBlockMat = DeviceBlockMatrix<Scalar, PDIM, PDIM, ROW_MAJOR>;
```


``` c++
template <typename T, int BLOCK_ROWS, int BLOCK_COLS, int ORDER>
class DeviceBlockMatrix
{
public:
	DeviceBlockMatrix(int rows, int cols) : nnz_(0) { resize(rows, cols); }

	void resize(int rows, int cols)
	{
		rows_ = rows;
		cols_ = cols;
		outerSize_ = ORDER == ROW_MAJOR ? rows : cols;
		innerSize_ = ORDER == ROW_MAJOR ? cols : rows;
		outerIndices_.resize(outerSize_ + 1); // 
	}

	void resizeNonZeros(int nnz)
	{
		nnz_ = nnz;
		values_.resize(nnz * BLOCK_AREA);
		innerIndices_.resize(nnz);
	}
	//将cpu的SparseBlockMatrix的索引数据传到gpu
	void upload(const T* values, const int* outerIndices, const int* innerIndices)
	{
		if (values)
			values_.upload(values);
		if (outerIndices)
			outerIndices_.upload(outerIndices);
		if (innerIndices)
			innerIndices_.upload(innerIndices);
	}

	void download(T* values, int* outerIndices, int* innerIndices) const
	
	// ?
	operator BlockPtrT() const { return BlockPtrT((T*)values_.data()); }

};
```

## GpuVecAny： 

``` c++
class GpuVecAny
{
public:

	GpuVecAny() : ptr_(nullptr) {}
	template <typename T> GpuVecAny(const GpuVec<T>& vec) : ptr_((void*)&vec) {}
	template <typename T> GpuVec<T>& getRef() const { return *((GpuVec<T>*)ptr_); }
	template <typename T> const GpuVec<T>& getCRef() const { return *((GpuVec<T>*)ptr_); }

private:

	void* ptr_;
};
```

