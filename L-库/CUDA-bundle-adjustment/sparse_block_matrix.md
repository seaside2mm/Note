

```c++

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

protected:

	Eigen::VectorXi outerIndices_, innerIndices_;
	int brows_, bcols_, nblocks_, outerSize_, innerSize_;
};
```

```c++

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

protected:

	Eigen::VectorXi outerIndices_, innerIndices_;
	int brows_, bcols_, nblocks_, outerSize_, innerSize_;
};
```

