---
date: 星期三, 一月 25日 2023, 10:17:28 上午
modified: 星期二, 二月 14日 2023, 5:06:51 下午
---

# SparseBlockMatrix ： 存储 CSR/CSC 数组长度信息
[[SparseCore#^3e0a1f]]

- `Eigen:: VectorXi outerIndices_, innerIndices_ ` : 根据 ORDER，分别存储非零元素的行/列索引.
- `int brows_, bcols_` :  块矩阵的行列数目
- `nblocks_, outerSize_, innerSize_ ` : 行/列索引数组的长度

```cpp

template <int _BLOCK_ROWS, int _BLOCK_COLS, int ORDER>
class SparseBlockMatrix
{
public:

	static const int BLOCK_ROWS = _BLOCK_ROWS;
	static const int BLOCK_COLS = _BLOCK_COLS;
	static const int BLOCK_AREA = BLOCK_ROWS * BLOCK_COLS;

	void resize(int brows, int bcols)
	{
		brows_ = brows;
		bcols_ = bcols;
		
		outerSize_ = ORDER == ROW_MAJOR ? brows : bcols;
		innerSize_ = ORDER == ROW_MAJOR ? bcols : brows;
		//
		outerIndices_.resize(outerSize_ + 1);
	}

	void resizeNonzeros(int nblocks)
	{
		nblocks_ = nblocks;
		//
		innerIndices_.resize(nblocks);
	}

	int rows() const { return brows_ * BLOCK_ROWS; }
	int cols() const { return bcols_ * BLOCK_COLS; }
};
```


# HplSparseBlockMatrix   根据 Pos 构建 Hpl 矩阵
将 COO 格式
`struct HplBlockPos { int row, col, id; };`

1. 根据 row 排序 blockpos
2. nnzPerCol 数组存储中每一列不为 0 的数目
3. 向 outerIndices_存储列索引 [[SparseCore#^3e0a1f]] 
	```cpp
	bcolPtr[0] = 0;
	for (int c = 0; c < bcols_; c++)
		bcolPtr[c + 1] = bcolPtr[c] + nnzPerCol[c];
	nblocks_ = bcolPtr[bcols_];
	```
4. 向 innerIndices_存储每一行的索引
	```cpp
	for (const auto& pos : blockpos)
		browInd[nnzPerCol[pos.col]++] = pos.row;
	```


```cpp
struct HplBlockPos { int row, col, id; };

class HplSparseBlockMatrix : public SparseBlockMatrix<PDIM, LDIM, COL_MAJOR>
{
public:
	void constructFromBlockPos(std::vector<HplBlockPos>& blockpos);
};
```




# HschurSparseBlockMatrix 构建 schur 补矩阵

$$
{\bf H}_{\rm sc} = {\bf H}_{\rm pp} − {\bf H}_{\rm pl} {\bf H}_{\rm ll}^{-1} {\bf H}_{\rm pl}^{\rm T}
$$
[[视觉slam十四讲批注#^b0yq74vmk8u|H 矩阵的非对角块处的非零元素对应着相机和路标的关联。那么，进行了Schur 消元后 S 的稀疏性是否具有物理意义呢？答案是有的。此处我们不加以证明地说，S 矩阵的非对角线上的非零矩阵块，表示了该处对应的两个相机变量之间存在着共同观测的路标点，有时候称为共视（Co-visibility）。反之，如果该块为零，则表示这两个相机没有共同观测。]]

## API
`int nmultiplies_` : 两两 poseVertex 间数目，应该为 `n!` 
`Eigen::VectorXi rowPtr_, colInd_` : CSR 的索引，此处 row 为外，col 为内
`Eigen::VectorXi nnzPerRow_` : 每行非零元素个数
`Eigen::VectorXi BSR2CSR_` : 转换的索引对应关系


```cpp
// 矩阵大小 PDIM x PDIM
// brows_, bcols_= PDIM
class HschurSparseBlockMatrix : public SparseBlockMatrix<PDIM, PDIM, ROW_MAJOR>
{
public:
	void constructFromVertices(const std::vector<VertexL*>& verticesL);
	void convertBSRToCSR();

	const int* rowPtr() const { return rowPtr_.data(); }
	const int* colInd() const { return colInd_.data(); }
	const int* BSR2CSR() const { return BSR2CSR_.data(); }

	// 所有块的所需元素(Scalar)个数。
	int nnzTrig() const { return nblocks_ * BLOCK_AREA; }
	// 整个schur对称矩阵所需元素(Scalar)个数
	int nnzSymm() const { return (2 * nblocks_ - brows_) * BLOCK_AREA; }
	int nmulBlocks() const { return nmultiplies_; }
};
```


## constructFromVertices

> 只保存每个矩阵块的索引，即 BSR 格式。而每个矩阵块的维度为 PDIM。

1. 创建 BlockPos 数组
2. 逐个将 verticesL 被连接的边的 poseVertex index 统计
	```cpp
	for (auto vL : verticesL)
	    indices.clear();
		for (const auto e : vL->edges)
			indices.push_back(e->poseVertex()->iP);
	```
3. 准备所需数据放入 BlockPos 中，也就是全部 PoseVertex 之间两两 id 信息
	```cpp
	for (int i = 0; i < indices.size(); i++)
	{
		const int rowId = indices[i];  //pose id
		// std::vector<uint8_t> map(brows_ * bcols_, 0);
		// Hsc矩阵(dense情况)每一行
		uint8_t* ptrMap = map.data() + rowId * bcols_;
		for (int j = i; j < indices.size(); j++)
		{
			const int colId = indices[j];
			if (!ptrMap[colId])
			{
				blockpos.push_back({ rowId, colId });
				ptrMap[colId] = 1;
			}
			countmul++;
		}
	}
	```
4. 根据 col 排序 blockpos
5. nnzPerRow_ 数组存储中每一行不为 0 的数目，向 browPtr_ 存储行索引。同HplSparseBlockMatrix
	```cpp
	for (int i = 0; i < nblocks_; i++)
	    nnzPerRow_[blockpos[i].row]++;
	    
	browPtr_[0] = 0;
	for (int r = 0; r < brows_; r++)
		browPtr_[r + 1] = browPtr_[r] + nnzPerRow_[r];
    ```
6. 向 bcolInd_ 存储每一列的索引
	```cpp
	for (int i = 0; i < nblocks_; i++)
	{
		const int rowId = blockpos[i].row;
		const int colId = blockpos[i].col;
		const int k = nnzPerRow_[rowId]++;
		bcolInd_[k] = colId;
	}
	```


## convertBSRToCSR

将 constructFromVertices 步骤储存 BSR 格式转换成 CSR 格式

> BSR ref :  [[Sparse Matrices · Matt Eding#Block Sparse Row]]

<iframe src="https://docs.nvidia.com/cuda/cusparse/#block-compressed-sparse-row-format-bsr" allow="fullscreen" allowfullscreen="" style="height: 342px; width: 766px; aspect-ratio: 16 / 9;"></iframe>


[[视觉slam十四讲批注#^dwcnmvs1lk9|图 10-11 以 S 矩阵中前 4 ×4 个矩阵块为例，这区域当中的矩阵块 S14, S24 不为零，表示相机 C4 和相机 C1 和 C2 之间有共同观测的点。而 S34 为零则表示 C3 和 C4 之间没有共同观测的路标]]

1. 计算 CSR 中每一行非零数目
	```cpp
	// 遍历行索引，对每一行的所有矩阵块
	for (int blockRowId = 0; blockRowId < brows_; blockRowId++)
	{
		// 得到每一行的起始块矩阵的索引，遍历一行的矩阵块数目
		for (int i = browPtr_[blockRowId]; i < browPtr_[blockRowId + 1]; i++)
		{
			//块矩阵的列索引
			const int blockColId = bcolInd_[i];
			// 换算到CSR格式中索引位置
			const int dstColId0 = blockColId * PDIM; 
			const int dstRowId0 = blockRowId * PDIM;
			// 计算每行非零数 
			if (blockRowId == blockColId)
			{
				//对块每一行数目加上维度
				for (int dr = 0; dr < PDIM; dr++)
					nnzPerRow_[dstRowId0 + dr] += PDIM;
			}
			else
			{
				// schur补是对称的，一次加上两个
				for (int dr = 0; dr < PDIM; dr++)
					nnzPerRow_[dstRowId0 + dr] += PDIM;
				for (int dc = 0; dc < PDIM; dc++)
					nnzPerRow_[dstColId0 + dc] += PDIM;
			}
		}
	}
	```
2. 计算 CSR 中行起始位置在列索引数组中的位置
	```cpp
		for (int r = 0; r < drows; r++)
			rowPtr_[r + 1] = rowPtr_[r] + nnzPerRow_[r];
	```
3. 重复步骤 1 的双重循环，遍历每行每列的块矩阵，得到在 BSR 列索引的位置
	```cpp
	//块矩阵的列索引
	const int blockColId = bcolInd_[i];
	// 换算到CSR格式中索引位置
	const int dstColId0 = blockColId * PDIM; 
	const int dstRowId0 = blockRowId * PDIM;
	// 块矩阵的索引i
	int srck = i * PDIM * PDIM;
	// 同样，根据对称对角只算一次
	if (blockRowId == blockColId)
	{
		for (int dc = 0; dc < PDIM; ++dc)
		{
			for (int dr = 0; dr < PDIM; ++dr)
			{
				// 在CSR格式中索引位置
				const int colId = dstColId0 + dc;
				const int rowId = dstRowId0 + dr;
				
				const int dstk = nnzPerRow_[rowId]++;
				colInd_[dstk] = colId;
				BSR2CSR_[dstk] = srck;
				srck++;
			}
		}
	}
	else
	{
		for (int dc = 0; dc < PDIM; ++dc)
		{
			for (int dr = 0; dr < PDIM; ++dr)
			{
				const int colId0 = dstColId0 + dc;
				const int rowId0 = dstRowId0 + dr;
	
				const int colId1 = rowId0;
				const int rowId1 = colId0;
	
				const int dstk0 = nnzPerRow_[rowId0]++;
				const int dstk1 = nnzPerRow_[rowId1]++;
	
				colInd_[dstk0] = colId0;
				colInd_[dstk1] = colId1;
				BSR2CSR_[dstk0] = srck;
				BSR2CSR_[dstk1] = srck;
				srck++;
			}
		}
	}
	```
