[CUDAによるバンドル調整の高速化とソースコード公開]( https://proc-cpuinfo.fixstars.com/2020/10/cuda-bundle-adjustment/ )

<iframe src="https://proc-cpuinfo.fixstars.com/2020/10/cuda-bundle-adjustment/" width=100% height="500px" frameborder="0" scrolling="auto"></iframe>

# 流程

- Initialize Optimizer 

- Build Structure 
	- 未迭代之前，首先构建 $Hx=b$ 这样一个线性系统。


* Compute Error

* Build System 
	* 每次迭代求解之前，都需要调用 buildSystem 函数来初始化。 

* Schur Complement

正定対称矩阵，コレスキー分解 ${\bf H}_{\rm sc}={\bf L}{\bf L}^{\rm T}$
$$
{\bf H}_{\rm sc} = {\bf H}_{\rm pp} − {\bf H}_{\rm pl} {\bf H}_{\rm ll}^{-1} {\bf H}_{\rm pl}^{\rm T}
$$

* Symbolic Decomposition

* Numerical Decomposition

* Update Solution 
	* 把得到的 $Δx$ 对应的状态变量进行更新 

<iframe src=" https://raw.githubusercontent.com/seaside2mm/github-photos/master/images/20221021184218.png" width=100% height="500px" frameborder="0" scrolling="auto">
</iframe>


核心步骤是实现在 `CudaBlockSolver` 类中。
```cpp
void initialize() override
{
	//Step 1
	solver_.initialize(vertexMapP_, vertexMapL_, edges2D_, edges3D_, kernels_);
}
```

```cpp
void optimize(int niterations) override
{
	const int maxq = 10;
	const double tau = 1e-5;

	double nu = 2;
	double lambda = 0;
	double F = 0;

	// Levenberg-Marquardt iteration
	for (int iteration = 0; iteration < niterations; iteration++)
	{
		if (iteration == 0)
			//Step 2
			solver_.buildStructure();
		//Step 3
		const double iniF = solver_.computeErrors();
		F = iniF;
		//Step 4
		solver_.buildSystem();
		
		if (iteration == 0)
			lambda = tau * solver_.maxDiagonal();

		int q = 0;
		double rho = -1;
		for (; q < maxq && rho < 0; q++)
		{
			solver_.push();

			solver_.setLambda(lambda);
		    //Step 5
			const bool success = solver_.solve();
		    //Step 6
			solver_.update();

			const double Fhat = solver_.computeErrors();
			const double scale = solver_.computeScale(lambda) + 1e-3;
			rho = success ? (F - Fhat) / scale : -1;

			if (rho > 0)
			{
				lambda *= clamp(attenuation(rho), 1./3, 2./3);
				nu = 2;
				F = Fhat;
				break;
			}
			else
			{
				lambda *= nu;
				nu *= 2;
				solver_.restoreDiagonal();
				solver_.pop();
			}
		}

		stats_.push_back({ iteration, F });

		if (q == maxq || rho <= 0 || !std::isfinite(lambda))
			break;
	}

	solver_.finalize();
	solver_.getChiSqs(chiSqs_);
	solver_.getTimeProfile(timeProfile_);
}

```