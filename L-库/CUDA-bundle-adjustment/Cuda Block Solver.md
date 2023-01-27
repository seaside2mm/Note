
## host buffers
```cpp
// graph components
std::vector<VertexP*> verticesP_;
std::vector<VertexL*> verticesL_;
std::vector<BaseEdge*> baseEdges_;
int numP_, numL_, nedges2D_, nedges3D_;
bool optimizeP_, optimizeL_;

// solution vectors
std::vector<Vec4d> qs_;
std::vector<Vec3d> ts_;
std::vector<Vec3d> Xws_;

// edge information
std::vector<Vec2d> measurements2D_;
std::vector<Vec3d> measurements3D_;
std::vector<Scalar> omegas_;
std::vector<PLIndex> edge2PL_;
std::vector<uint8_t> edgeFlags_;
std::vector<Scalar> chiSqs_;

// block matrices
HplSparseBlockMatrix Hpl_;
HschurSparseBlockMatrix Hsc_;
SparseLinearSolver::Ptr linearSolver_;
std::vector<HplBlockPos> HplBlockPos_;
int nHplBlocks_;

// camera parameters
std::vector<Vec5d> cameras_;

// robust kernels
RobustKernel kernels_[EDGE_TYPE_NUM];
```


## device buffers
```cpp
// solution vectors
GpuVec1d d_solution_, d_solutionBackup_;
GpuVec4d d_qs_;
GpuVec3d d_ts_, d_Xws_;

// edge information
GpuVec3d d_Xcs2D_, d_Xcs3D_;
GpuVec1d d_omegas2D_, d_omegas3D_;
GpuVec2d d_measurements2D_, d_errors2D_;
GpuVec3d d_measurements3D_, d_errors3D_;
GpuVec2i d_edge2PL2D_, d_edge2PL3D_;
GpuVec1b d_edgeFlags2D_, d_edgeFlags3D_;
GpuVec1i d_edge2Hpl_, d_edge2Hpl2D_, d_edge2Hpl3D_;
GpuVec1d d_chiSqs_, d_chiSqs2D_, d_chiSqs3D_;

// solution increments Δx = [Δxp Δxl]
GpuVec1d d_x_;
GpuPx1BlockVec d_xp_;
GpuLx1BlockVec d_xl_;

// coefficient matrix of linear system
// | Hpp  Hpl ||Δxp| = |-bp|
// | HplT Hll ||Δxl|   |-bl|
GpuPxPBlockVec d_Hpp_;
GpuLxLBlockVec d_Hll_;
GpuHplBlockMat d_Hpl_;
GpuVec3i d_HplBlockPos_;
GpuVec1d d_b_;
GpuPx1BlockVec d_bp_;
GpuLx1BlockVec d_bl_;
GpuPx1BlockVec d_HppBackup_;
GpuLx1BlockVec d_HllBackup_;

// schur complement of the H matrix
// HSc = Hpp - Hpl*inv(Hll)*HplT
// bSc = -bp + Hpl*inv(Hll)*bl
GpuHscBlockMat d_Hsc_;
GpuPx1BlockVec d_bsc_;
GpuLxLBlockVec d_invHll_;
GpuPxLBlockVec d_Hpl_invHll_;
GpuVec3i d_HscMulBlockIds_;

// conversion matrix storage format BSR to CSR
GpuVec1d d_HscCSR_;
GpuVec1i d_BSR2CSR_;

// camera parameters
GpuVec5d d_cameras_;

// temporary buffer
DeviceBuffer<Scalar> d_chi_;
GpuVec1i d_nnzPerCol_;

////////////////////////////////////////////////////////////////////////////////////
// statistics
////////////////////////////////////////////////////////////////////////////////////

std::vector<double> profItems_;
```