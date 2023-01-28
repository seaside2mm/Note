
第 0 步： 

```c
```
第一步：buildStructure

```cpp
// build Hpl block matrix structure
d_Hpl_.resize(numP_, numL_);
d_Hpl_.resizeNonZeros(nHplBlocks_);
2
d_HplBlockPos_.assign(nHplBlocks_, HplBlockPos_.data());
d_nnzPerCol_.resize(numL_ + 1);
d_edge2Hpl_.resize(baseEdges_.size());

gpu::buildHplStructure(d_HplBlockPos_, d_Hpl_, d_edge2Hpl_, d_nnzPerCol_);

```


```cpp
void buildHplStructure(GpuVec3i& blockpos, GpuHplBlockMat& Hpl, GpuVec1i& indexPL, GpuVec1i& nnzPerCol)
{
	const int nblocks = Hpl.nnz();
	const int block = 1024;
	const int grid = divUp(nblocks, block);
	int* colPtr = Hpl.outerIndices();
	int* rowInd = Hpl.innerIndices();

	auto ptrBlockPos = thrust::device_pointer_cast(blockpos.data());
	thrust::sort(ptrBlockPos, ptrBlockPos + nblocks, LessColId());

	CUDA_CHECK(cudaMemset(nnzPerCol, 0, sizeof(int) * (Hpl.cols() + 1)));
	nnzPerColKernel<<<grid, block>>>(blockpos, nblocks, nnzPerCol);
	exclusiveScan(nnzPerCol, colPtr, Hpl.cols() + 1);
	setRowIndKernel<<<grid, block>>>(blockpos, nblocks, rowInd, indexPL);
}
```


第二步：buildSystem 中的 constructQuadraticFormKernel

```cpp
// Hpl += = JPT*Ω*JL
MatTMulMat<PDIM, MDIM, LDIM, ASSIGN>(JP, JL, Hpl.at(edge2Hpl[iE]), omega);
```