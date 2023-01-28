

# Problem 类

## MakeHessian

```cpp
// 直接构造大的 H 矩阵
MatXX H(MatXX::Zero(size, size));
VecX b(VecX::Zero(size));

for (auto &edge: edges_) {

	edge.second->ComputeResidual();
	edge.second->ComputeJacobians();

	auto jacobians = edge.second->Jacobians();
	auto verticies = edge.second->Verticies();

	
	for (size_t i = 0; i < verticies.size(); ++i) {
		auto v_i = verticies[i];
		// Hessian 里不需要添加它的信息，也就是它的雅克比为 0
		if (v_i->IsFixed()) continue;    

		auto jacobian_i = jacobians[i];
		ulong index_i = v_i->OrderingId();
		ulong dim_i = v_i->LocalDimension();

		MatXX JtW = jacobian_i.transpose() * edge.second->Information();
		
		for (size_t j = i; j < verticies.size(); ++j) {
			auto v_j = verticies[j];

			if (v_j->IsFixed()) continue;

			auto jacobian_j = jacobians[j];
			ulong index_j = v_j->OrderingId();
			ulong dim_j = v_j->LocalDimension();

			assert(v_j->OrderingId() != -1);
			MatXX hessian = JtW * jacobian_j;
			// 所有的信息矩阵叠加起来
			H.block(index_i, index_j, dim_i, dim_j).noalias() += hessian;
			if (j != i) {
				// 对称的下三角
				H.block(index_j, index_i, dim_j, dim_i).noalias() += hessian.transpose();
			}
		}
		b.segment(index_i, dim_i).noalias() -= JtW * edge.second->Residual();
	}

}
Hessian_ = H;
b_ = b;
t_hessian_cost_ += t_h.toc();


//    Eigen::JacobiSVD<Eigen::MatrixXd> svd(H, Eigen::ComputeThinU | Eigen::ComputeThinV);
//    std::cout << svd.singularValues() <<std::endl;

if (err_prior_.rows() > 0) {
	b_prior_ -= H_prior_ * delta_x_.head(ordering_poses_);   // update the error_prior
}
Hessian_.topLeftCorner(ordering_poses_, ordering_poses_) += H_prior_;
b_.head(ordering_poses_) += b_prior_;

delta_x_ = VecX::Zero(size);  // initial delta_x = 0_n;
```

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
int n = H_pp_schur_.rows() * 2;  // 迭代次数
delta_x_pp = PCGSolver(H_pp_schur_, b_pp_schur_, n);  
delta_x_.head(reserve_size) = delta_x_pp;

//step3: solve landmark
VecX delta_x_ll(marg_size);
delta_x_ll = Hmm_inv * (bmm - Hmp * delta_x_pp); //公式6
delta_x_.tail(marg_size) = delta_x_ll;
```

