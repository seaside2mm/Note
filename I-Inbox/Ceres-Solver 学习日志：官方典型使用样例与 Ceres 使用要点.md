---
url: https://www.cnblogs.com/dzyBK/p/13934514.html
title: Ceres-Solver 学习日志：官方典型使用样例与 Ceres 使用要点
date: 2023-01-11 10:55:24
tag: 
summary: 
---


![](https://img2020.cnblogs.com/blog/705780/202011/705780-20201116172121328-741442575.png)

![](https://img2020.cnblogs.com/blog/705780/202011/705780-20201105225842733-1682641902.png)

**6.** **使用样例**

         提供三个官方使用样例，封装为三个类：

         (1)AboutPowellEquation：四个残差模型、每个残差模型仅一个残差项、LM 算法。

         (2)AboutCurveFitting：一个残差模型、此残差模型对应多个残差项、LM 算法。

         (3)AboutRosenbrock：两个残差模型、每个残差模型仅一个残差项、梯度下降法、可配置为其它优化算法测试收敛性。

         以下是详细代码，依赖于 C++14、Ceres 和 Spdlog。

![](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)

![](https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

```
  1 #include <ceres/ceres.h>
  2 #include <spdlog/spdlog.h>
  3 using namespace std;
  4 
  5 class AboutPowellEquation //CeresBasicDemo: multiple residual models + no input vector
  6 {
  7 public:
  8     static void TestMe(int argc = 0, char** argv = 0)
  9     {
 10         double param[4] = { 3.0, -1.0, 0.0, 1.0 };
 11         ceres::Problem problem;
 12         problem.AddResidualBlock(new ceres::AutoDiffCostFunction<AboutPowellEquation, 4, 4>(new AboutPowellEquation), NULL, param);
 13 
 14         ceres::Solver::Options options;
 15         options.minimizer_progress_to_stdout = true;
 16         ceres::Solver::Summary summary;
 17         ceres::Solve(options, &problem, &summary);
 18 
 19         cout << summary.FullReport() << endl;
 20         spdlog::info("x1={:.9f}, x2={:.9f} x3={:.9f} x4={:.9f}", param[0], param[1], param[2], param[3]);
 21     }
 22 
 23 public:
 24     template <typename tp> bool operator()(const tp* const param, tp* residual) const
 25     {
 26         tp x1 = param[0];
 27         tp x2 = param[1];
 28         tp x3 = param[2];
 29         tp x4 = param[3];
 30         residual[0] = x1 + 10. * x2;
 31         residual[1] = sqrt(5.) * (x3 - x4);
 32         residual[2] = (x2 - 2. * x3) * (x2 - 2. * x3);
 33         residual[3] = sqrt(10.) * (x1 - x4) * (x1 - x4);
 34         return true;
 35     }
 36 };
 37 
 38 class AboutCurveFitting //CeresBasicDemo: single residual model + multiple input vectors
 39 {
 40 public:
 41     static void TestMe(int argc = 0, char** argv = 0)
 42     {
 43         vector<double> xys =
 44         {
 45             0.000000e+00, 1.133898e+00,
 46             7.500000e-02, 1.334902e+00,
 47             1.500000e-01, 1.213546e+00,
 48             2.250000e-01, 1.252016e+00,
 49             3.000000e-01, 1.392265e+00,
 50             3.750000e-01, 1.314458e+00,
 51             4.500000e-01, 1.472541e+00,
 52             5.250000e-01, 1.536218e+00,
 53             6.000000e-01, 1.355679e+00,
 54             6.750000e-01, 1.463566e+00,
 55             7.500000e-01, 1.490201e+00,
 56             8.250000e-01, 1.658699e+00,
 57             9.000000e-01, 1.067574e+00,
 58             9.750000e-01, 1.464629e+00,
 59             1.050000e+00, 1.402653e+00,
 60             1.125000e+00, 1.713141e+00,
 61             1.200000e+00, 1.527021e+00,
 62             1.275000e+00, 1.702632e+00,
 63             1.350000e+00, 1.423899e+00,
 64             1.425000e+00, 1.543078e+00,
 65             1.500000e+00, 1.664015e+00,
 66             1.575000e+00, 1.732484e+00,
 67             1.650000e+00, 1.543296e+00,
 68             1.725000e+00, 1.959523e+00,
 69             1.800000e+00, 1.685132e+00,
 70             1.875000e+00, 1.951791e+00,
 71             1.950000e+00, 2.095346e+00,
 72             2.025000e+00, 2.361460e+00,
 73             2.100000e+00, 2.169119e+00,
 74             2.175000e+00, 2.061745e+00,
 75             2.250000e+00, 2.178641e+00,
 76             2.325000e+00, 2.104346e+00,
 77             2.400000e+00, 2.584470e+00,
 78             2.475000e+00, 1.914158e+00,
 79             2.550000e+00, 2.368375e+00,
 80             2.625000e+00, 2.686125e+00,
 81             2.700000e+00, 2.712395e+00,
 82             2.775000e+00, 2.499511e+00,
 83             2.850000e+00, 2.558897e+00,
 84             2.925000e+00, 2.309154e+00,
 85             3.000000e+00, 2.869503e+00,
 86             3.075000e+00, 3.116645e+00,
 87             3.150000e+00, 3.094907e+00,
 88             3.225000e+00, 2.471759e+00,
 89             3.300000e+00, 3.017131e+00,
 90             3.375000e+00, 3.232381e+00,
 91             3.450000e+00, 2.944596e+00,
 92             3.525000e+00, 3.385343e+00,
 93             3.600000e+00, 3.199826e+00,
 94             3.675000e+00, 3.423039e+00,
 95             3.750000e+00, 3.621552e+00,
 96             3.825000e+00, 3.559255e+00,
 97             3.900000e+00, 3.530713e+00,
 98             3.975000e+00, 3.561766e+00,
 99             4.050000e+00, 3.544574e+00,
100             4.125000e+00, 3.867945e+00,
101             4.200000e+00, 4.049776e+00,
102             4.275000e+00, 3.885601e+00,
103             4.350000e+00, 4.110505e+00,
104             4.425000e+00, 4.345320e+00,
105             4.500000e+00, 4.161241e+00,
106             4.575000e+00, 4.363407e+00,
107             4.650000e+00, 4.161576e+00,
108             4.725000e+00, 4.619728e+00,
109             4.800000e+00, 4.737410e+00,
110             4.875000e+00, 4.727863e+00,
111             4.950000e+00, 4.669206e+00
112         };
113 
114         vector<double> xs, ys;
115         for (int i = 0; i < (int)xys.size(); i += 2)
116         {
117             xs.push_back(xys[i]);
118             ys.push_back(xys[i + 1]);
119         }
120 
121         double param[2] = { 0.0, 0.0 };
122         ceres::Problem problem;
123         problem.AddResidualBlock(new ceres::AutoDiffCostFunction<AboutCurveFitting, ceres::DYNAMIC, 2>(new AboutCurveFitting((int)xs.size(), xs.data(), ys.data()), (int)xs.size()), NULL, param);
124 
125         ceres::Solver::Options options;
126         options.minimizer_progress_to_stdout = true;
127         ceres::Solver::Summary summary;
128         ceres::Solve(options, &problem, &summary);
129 
130         cout << summary.FullReport() << endl;
131         spdlog::info("x1={:.9f} x2={:.9f}", param[0], param[1]);
132     }
133 
134 public:
135     int count;
136     double* xs;
137     double* ys;
138     AboutCurveFitting(int count0, double* xs0, double* ys0) : count(count0), xs(xs0), ys(ys0) {}
139 
140 public:
141     template <typename tp> bool operator()(const tp* const param, tp* residual) const
142     {
143         tp m = param[0];
144         tp c = param[1];
145         for (int k = 0; k < count; ++k) residual[k] = ys[k] - exp(m * xs[k] + c);
146         return true;
147     }
148 };
149 
150 class AboutRosenbrock //CeresApplication: GradientDescent, ConjugateGradient, BFGS, LBFGS, LM, DL
151 {
152 public:
153     static void TestMe(int argc = 0, char** argv = 0)
154     {
155         double param[2] = { 3.0, -1.0 };
156         ceres::Problem problem;
157         problem.AddResidualBlock(new ceres::AutoDiffCostFunction<AboutRosenbrock, 2, 2>(new AboutRosenbrock), NULL, param);
158 
159         ceres::Solver::Options options;
160         options.minimizer_type = ceres::LINE_SEARCH;
161         options.line_search_direction_type = ceres::STEEPEST_DESCENT;
162         options.minimizer_progress_to_stdout = true;
163         ceres::Solver::Summary summary;
164         ceres::Solve(options, &problem, &summary);
165 
166         cout << summary.FullReport() << endl;
167         spdlog::info("x1={:.9f}, x2={:.9f}", param[0], param[1]);
168     }
169 
170 public:
171     template <typename tp> bool operator()(const tp* const param, tp* residual) const
172     {
173         tp x = param[0];
174         tp y = param[1];
175         residual[0] = 1. - x;
176         residual[1] = 10. * y - 10. * x * x;
177         return true;
178     }
179 };
180 
181 int main(int argc, char** argv)
182 {
183     spdlog::set_pattern("%v");
184     AboutPowellEquation::TestMe(argc, argv);
185     spdlog::info("\n\n\n\n\n\n");
186     AboutCurveFitting::TestMe(argc, argv);
187     spdlog::info("\n\n\n\n\n\n");
188     AboutRosenbrock::TestMe(argc, argv);
189     std::getchar();
190     return 0;
191 }

```

View Code