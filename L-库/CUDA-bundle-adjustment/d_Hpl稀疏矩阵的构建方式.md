
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


第二步：buildSystem 中的constructQuadraticFormKernel

```cpp
// Hpl += = JPT*Ω*JL
MatTMulMat<PDIM, MDIM, LDIM, ASSIGN>(JP, JL, Hpl.at(edge2Hpl[iE]), omega);
```