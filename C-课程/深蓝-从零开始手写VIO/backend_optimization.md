

## SolveLinearSystem


```c++
/*
 * Solve Hx = b, we can use PCG iterative method or use sparse Cholesky
*/

// SLAM 问题采用舒尔补的计算方式
// step1: schur marginalization

int reserve_size = ordering_poses_;  //pose数x维度
int marg_size = ordering_landmarks_; //lm数x维度

// 获取对应矩阵块
MatXX Hmm = Hessian_.block(reserve_size, reserve_size, marg_size, marg_size);
MatXX Hpp = Hessian_.block(0, 0, reserve_size, reserve_size);

MatXX Hpm = Hessian_.block(0,reserve_size, reserve_size, marg_size);
MatXX Hmp = Hessian_.block(reserve_size,0, marg_size, reserve_size);

VecX bpp = b_.segment(0, reserve_size);
VecX bmm = b_.segment(reserve_size, marg_size);

// Hmm 是对角线矩阵，它的求逆可以直接为对角线块分别求逆
// 如果是逆深度，对角线块为1维的，则直接为对角线的倒数，这里可以加速
MatXX Hmm_inv(MatXX::Zero(marg_size, marg_size));
for (auto landmarkVertex : idx_landmark_vertices_) {
	//OrderingId为系数
    int idx = landmarkVertex.OrderingId() - reserve_size;      
    int dim = landmarkVertex.LocalDimension();
    Hmm_inv.block(idx, idx, dim, dim) = Hmm.block(idx, idx, dim, dim).inverse();
}

// 舒尔补
H_pp_schur_ = Hpp -  Hpm * Hmm_inv * Hmp;
b_pp_schur_ = bpp -  Hpm * Hmm_inv * bmm;

// step2: solve Hpp * delta_x = bpp
VecX delta_x_pp(VecX::Zero(reserve_size));
// PCG Solver 加上阻尼因子
for (ulong i = 0; i < ordering_poses_; ++i) {
    H_pp_schur_(i, i) += currentLambda_;
}
int n = H_pp_schur_.rows() * 2;                       // 迭代次数
delta_x_pp = PCGSolver(H_pp_schur_, b_pp_schur_, n);  
delta_x_.head(reserve_size) = delta_x_pp;
//step3: solve landmark
VecX delta_x_ll(marg_size);
delta_x_ll = Hmm_inv * (bmm - Hmp * delta_x_pp); //公式6
delta_x_.tail(marg_size) = delta_x_ll;
```

