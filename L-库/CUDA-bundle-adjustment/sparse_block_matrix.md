
## SparseBlockMatrix ： 存储 CSR 数组信息


- `Eigen:: VectorXi outerIndices_, innerIndices_ ` : 根据 ORDER，分别存储非零元素的行/列索引.
- `int brows_, bcols_, nblocks_, outerSize_, innerSize_`

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
		outerIndices_.resize(outerSize_ + 1);
	}

	void resizeNonzeros(int nblocks)
	{
		nblocks_ = nblocks;
		innerIndices_.resize(nblocks);
	}

	int* outerIndices() { return outerIndices_.data(); }
	int* innerIndices() { return innerIndices_.data(); }
	const int* outerIndices() const { return outerIndices_.data(); }
	const int* innerIndices() const { return innerIndices_.data(); }

	int brows() const { return brows_; }
	int bcols() const { return bcols_; }
	int nblocks() const { return nblocks_; }
	int rows() const { return brows_ * BLOCK_ROWS; }
	int cols() const { return bcols_ * BLOCK_COLS; }

};
```



```cpp

struct HplBlockPos { int row, col, id; };

class HplSparseBlockMatrix : public SparseBlockMatrix<PDIM, LDIM, COL_MAJOR>
{
public:

	void constructFromBlockPos(std::vector<HplBlockPos>& blockpos);
};

class HschurSparseBlockMatrix : public SparseBlockMatrix<PDIM, PDIM, ROW_MAJOR>
{
public:

	void constructFromVertices(const std::vector<VertexL*>& verticesL);
	void convertBSRToCSR();

	const int* rowPtr() const { return rowPtr_.data(); }
	const int* colInd() const { return colInd_.data(); }
	const int* BSR2CSR() const { return BSR2CSR_.data(); }

	int nnzTrig() const { return nblocks_ * BLOCK_AREA; }
	int nnzSymm() const { return (2 * nblocks_ - brows_) * BLOCK_AREA; }
	int nmulBlocks() const { return nmultiplies_; }

private:

	int nmultiplies_;
	Eigen::VectorXi rowPtr_, colInd_, nnzPerRow_, BSR2CSR_;
};
```

