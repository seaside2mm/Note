

```cpp
#include <cuda_runtime.h>
#include <cusparse.h>
#include <cusolverSp.h>
#include <cusolverSp_LOWLEVEL_PREVIEW.h>
```


```cpp
class SparseLinearSolver
{
public:

	using Ptr = std::unique_ptr<SparseLinearSolver>;
	static Ptr create();

	virtual void initialize(const HschurSparseBlockMatrix& Hsc) = 0;
	virtual bool solve(const Scalar* d_A, const Scalar* d_b, Scalar* d_x) = 0;

	virtual ~SparseLinearSolver();
};

```