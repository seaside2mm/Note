
```cpp
void initialize() override
{
	solver_.initialize(vertexMapP_, vertexMapL_, edges2D_, edges3D_, kernels_);

	stats_.clear();
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
			solver_.buildStructure();

		const double iniF = solver_.computeErrors();
		F = iniF;

		solver_.buildSystem();
		
		if (iteration == 0)
			lambda = tau * solver_.maxDiagonal();

		int q = 0;
		double rho = -1;
		for (; q < maxq && rho < 0; q++)
		{
			solver_.push();

			solver_.setLambda(lambda);

			const bool success = solver_.solve();

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