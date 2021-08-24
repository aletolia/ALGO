# Global Optimization Toolbox User's Guide

## Introducing Global Optimization Toolbox Functions

Global Optimization Toolbox Product Description

求解多个最大值、多个最小值和非光滑优化问题 Global Optimization Toolbox 提供了搜索包含多个最大值或最小值的问题的全局解的函数。 工具箱求解器包括代理、模式搜索、遗传算法、粒子群、模拟退火、多起点和全局搜索。 您可以将这些求解器用于优化问题，其中目标或约束函数是连续的、不连续的、随机的、不具有导数的，或者包括模拟或黑盒函数。 对于具有多个目标的问题，您可以使用遗传算法或模式搜索求解器识别帕累托前沿。

您可以通过调整选项以及针对适用的求解器自定义创建、更新和搜索功能来提高求解器的效率。 您可以将自定义数据类型与遗传算法和模拟退火求解器结合使用，以表示标准数据类型不易表达的问题。 混合函数选项可让您通过在第一个求解器之后应用第二个求解器来改进解决方案。

主要特点 

• 具有较长目标函数执行时间和边界约束的问题的代理求解器 

• 具有线性、非线性和边界约束的单目标和多目标问题的模式搜索求解器 

• 具有线性、非线性、边界和整数约束的问题的遗传算法 

• 用于线性、非线性和边界约束问题的多目标遗传算法 

• 用于边界约束的粒子群求解器 

• 用于边界约束的模拟退火求解器 

• 用于具有线性、非线性和边界约束的平滑问题的多起点和全局搜索求解器

#### Comparison of Six Solvers

**Function to Optimize**

此示例显示如何使用六个求解器最小化 Rastrigin 的函数。 每个求解器都有自己的特点。 这些特性导致不同的解决方案和运行时间。 在第 1-9 页的“比较语法和解决方案”中检查的结果可以帮助您为自己的问题选择合适的求解器。

Rastrigin 的函数有许多局部最小值，全局最小值在 (0,0)：
$$
Ras(x)=20+x_1^2+x_2^2-10(cos(2\pi x_1)+cos(2\pi x_2))
$$
通常您不知道目标函数的全局最小值的位置。 为了展示求解器如何寻找全局解，此示例在远离全局最小值的点 [20,30] 周围启动所有求解器。

rastriginsfcn.m 文件实现了 Rastrigin 的功能。 该文件随 Global Optimization Toolbox 软件一起提供。 这个例子使用了具有更大吸引力盆地的 Rastrigin 函数的缩放版本。 有关信息，请参阅第 1-19 页的“景点盆地”。

```matlab
rf2 = @(x)rastriginsfcn(x/10);
```

![image-20210823171235993](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210823171235993.png)

此示例使用 fminunc（Optimization Toolbox™ 求解器）、模式搜索和全局搜索的默认设置来最小化 rf2。 该示例还使用具有非默认选项的 ga 和particleswarm 从点[20,30] 周围的初始种群开始。 由于 surrogateopt 需要有限边界，因此该示例在每个变量中使用下限为 -70 和上限为 130 的 surrogateopt。

**Six Solution**

• 第 1-4 页的“fminunc” 

• 第 1-5 页的“patternsearch” 

• 第 1-6 页的“ga” 

• 第 1-7 页的“particleswarm”

• 第 1-8 页的“surrogateopt” 

• 第 1-8 页的“GlobalSearch” 第 1-8 页

**fminunc**

要使用 fminunc 优化工具箱求解器求解优化问题，请输入：

```matlab
rf2 = @(x)rastriginsfcn(x/10); % objective
x0 = [20,30]; % 起点远离最小值
[xf,ff,flf,of] = fminunc(rf2,x0)
```

fminunc 返回

```matlab
优化完成，因为梯度的大小小于函数容差的默认值
xf =
   19.8991 29.8486
ff =
   12.9344
flf =
   1
of =
   struct with fields:
      iterations: 3
        funcCount: 15
     stepsize: 1.7776e-06
  lssteplength: 1
    firstorderopt: 5.9907e-09
      algorithm: 'quasi-newton'
    message: 'Local minimum found.…'
```

• xf 是最小化点。

• ff 是目标值 rf2 在 xf 处的值。

• flf 是退出标志。 退出标志为 1 表示 xf 是局部最小值。

• of 是输出结构，它描述了导致解的 fminunc 计算

**patternsearch**

要使用模式搜索全局优化工具箱求解器求解优化问题，请输入：

```matlab
rf2 = @(x)rastriginsfcn(x/10); % objective
x0 = [20,30]; % start point away from the minimum
[xp,fp,flp,op] = patternsearch(rf2,x0)
```

模式搜索返回

```matlab
Optimization terminated: mesh size less than options.MeshTolerance.
xp =
19.8991 -9.9496
fp =
4.9748
flp =
1
op =
struct with fields:
function: @(x)rastriginsfcn(x/10)
problemtype: 'unconstrained'
pollmethod: 'gpspositivebasis2n'
maxconstraint: []
searchmethod: []
iterations: 48
funccount: 174
```

• xp 是最小化点。

• fp 是物镜rf2 在xp 处的值。

• flp 是退出标志。 退出标志为 1 表示 xp 是局部最小值。

• op 是输出结构，它描述了导致解决方案的模式搜索计算。

这些结果是典型的： 

• fminunc 快速到达其起始盆地内的局部解，但根本不在该盆地外探索。  fminunc 有一个简单的调用语法。

• 模式搜索比 fminunc 需要更多的函数评估，并搜索多个盆地，得出比 fminunc 更好的解决方案。 模式搜索调用语法与 fminunc 相同。

• ga 比模式搜索需要更多的函数评估。 一个偶然的机会，它找到了一个更好的解决方案。 在这种情况下，ga 找到了一个接近全局最优值的点。  ga 是随机的，所以它的结果会随着每次运行而变化。  ga 有一个简单的调用语法，但是要在 [20,30] 附近设置初始种群还需要额外的步骤。

•particleswarm 比 ga 需要更少的函数评估，但比模式搜索更多。 在这种情况下，particleswarm 找到了一个目标函数值低于模式搜索但高于 ga 的点。 由于particleswarm 是随机的，它的结果会随着每次运行而变化。  particleswarm 有一个简单的调用语法，但要在 [20,30] 附近有一个初始种群需要额外的步骤。

• surrogateopt 在达到函数求值限制时停止，对于二变量问题，默认值为 200。  surrogateopt 有一个简单的调用语法，但需要有限的界限。surrogateopt 试图找到一个全局解决方案，并且在这种情况下成功了。  surrogateopt 中的每个函数计算都比大多数其他求解器花费更长的时间，因为 surrogateopt 作为其算法的一部分执行许多辅助计算。

• GlobalSearch 运行采用与 ga 和particlewarm 相同数量级的函数评估，搜索许多盆地，并得出一个好的解决方案。 在这种情况下，GlobalSearch 找到了全局最优解。 设置 GlobalSearch 比设置其他求解器更复杂。 如示例所示，在调用 GlobalSearch 之前，您必须创建 GlobalSearch 对象（示例中的 gs）和问题结构（problem）。然后，您使用 gs 和问题调用 run 方法。 有关如何运行 GlobalSearch 的更多详细信息，请参阅“GlobalSearch 和 MultiStart 的工作流程”（第 3-3 页）。

**具有非光滑问题的求解器行为**

这个例子显示了为优化问题选择合适的求解器的重要性。 它还表明，单点不平滑可能会导致 Optimization Toolbox™ 求解器出现问题。

通常，求解器决策表提供了关于哪种求解器可能最适合您的问题的指导。 对于平滑问题，请参见“优化决策表”。 对于非光滑问题，请首先参阅第 1-23 页的“选择求解器的表”，有关详细信息，请参阅第 1-24 页的“全局优化工具箱求解器特性”。

**具有单个非光滑点的函数**

函数 $f (x) = |  |x||^{1/2}$ 在点 0 处是不光滑的，这是最小化点。 这是使用 4-D 点的矩阵范数的 2-D 图

![image-20210823172043206](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210823172043206.png)

此示例对 2×6 矩阵 x 使用矩阵范数。 矩阵范数涉及奇异值分解，不如欧几里得范数平滑。 参见“矩阵的 2-范数”。

**使用模式搜索最小化**

模式搜索是推荐的尝试非光滑问题的第一个求解器。 请参见第 1-23 页的“选择求解器的表”。 从非零 2×6 矩阵 x0 开始模式搜索，并尝试定位 $f$ 的最小值。 对于此尝试以及所有其他尝试，请使用默认求解器选项。

返回应接近零的解、目标函数值（同样应接近零）以及所采用的函数评估次数。

```matlab
fun = @(x)norm([x(1:6);x(7:12)])^(1/2);
x0 = [1:6;7:12];
rng default
x0 = x0 + rand(size(x0))
x0 = 2×6
1.8147 2.1270 3.6324 4.2785 5.9575 6.1576
7.9058 8.9134 9.0975 10.5469 11.9649 12.9706
[xps,fvalps,eflagps,outputps] = patternsearch(fun,x0)
```

模式搜索达到了一个很好的解决方案，正如退出标志 1 所证明的那样。但是，它需要超过 10,000 次函数评估才能收敛。

**使用 fminsearch 最小化**

文档指出 fminsearch 有时可以处理不连续性，所以这是一个合理的选择

```matlab
[xfms,fvalfms,eflagfms,outputfms] = fminsearch(fun,x0);
退出：已超过最大函数评估次数 - 增加 MaxFunEvals 选项.
Current function value: 3.197063

```

函数返回

```matlab
xfms = 2×6
2.2640 1.1747 9.0693 8.1652 1.7367 -1.2958
3.7456 1.2694 0.2714 -3.7942 3.8714 1.9290
fvalfms = 3.1971
eflagfms = 0
ans = 2401
```

使用默认选项， fminsearch 在收敛到解决方案之前用完函数评估。退出标志 0 表示缺乏收敛。 报告的解决方案很差。

**使用粒子群**

建议将particleswarm 作为下一个尝试的求解器。 请参见第 1-26 页上的“在非光滑问题的求解器之间进行选择”。

```matlab
[xpsw,fvalpsw,eflagpsw,outputpsw] = particleswarm(fun,12);
Optimization ended: relative change in the objective value
over the last OPTIONS.MaxStallIterations iterations is less than OPTIONS.FunctionTolerance.
xpsw,fvalpsw,eflagpsw,outputpsw.funccount
xpsw = 1×12
10-12 ×
-0.0386 -0.1282 -0.0560 0.0904 0.0771 -0.0541 -0.1189 0.1290 -0.0032 0.0165 fvalpsw = 4.5222e-07
eflagpsw = 1
ans = 37200
```

particleswarm 找到了比模式搜索更准确的解决方案，但需要超过 35,000 次函数评估。 退出标志 1 表示解决方案是好的。

**使用遗传算法**

ga 是一种流行的求解器，但不建议将其作为第一个尝试的求解器。 看看它在这个问题上的效果如何。

```matlab
[xga,fvalga,eflagga,outputga] = ga(fun,12);
Optimization terminated: average change in the fitness value less than options.FunctionTolerance.
xga,fvalga,eflagga,outputga.funccount
xga = 1×12
-0.0061 -0.0904 0.0816 -0.0484 0.0799 -0.1925 0.0048 0.3581 0.0848 0.0232 fvalga = 0.6257
eflagga = 1
ans = 65190
```

ga 没有找到像模式搜索或粒子群那样好的解决方案，并且需要大约两倍于粒子群的函数评估。 在这种情况下，退出标志 1 具有误导性。

结果总结 

选择合适的求解器可以得到更好、更快的结果。 此摘要显示了结果的差异性。 如果目标函数值大于 0.1，则解质量为“差”，如果值小于 0.01，则为“好”，否则为“中等”

![image-20210823175754461](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210823175754461.png)

虽然particleswarm 实现了最低的目标函数值，但它的功能评估是模式搜索的三倍多，是 fmincon 的 30 多倍。  fmincon 通常不推荐用于非光滑问题。 在这种情况下它是有效的，但这种情况只有一个非光滑点。

#### What Is Global Optimization?

**Local vs. Global Optimization**

优化是寻找使函数最小化的点的过程。 更具体地说： 

• 函数的局部最小值是函数值小于或等于附近点的值但可能大于远处点的值的点。

• 全局最小值是函数值小于或等于所有其他可行点的值的点。

![image-20210823175951547](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210823175951547.png)

通常，Optimization Toolbox 求解器会找到局部最优解。  （这个局部最优可以是全局最优。）他们在起点的吸引力盆地中找到最优。 如需更多信息，请参阅第 1-19 页的“景点盆地”。

相比之下，Global Optimization Toolbox 求解器旨在搜索多个吸引域。 它们以多种方式进行搜索： 

• GlobalSearch 和 MultiStart 生成多个起点。 然后他们使用局部求解器在起点的吸引力盆地中找到最优解。

• ga 使用一组起始点（称为总体）并从总体中迭代生成更好的点。 只要初始种群覆盖了几个盆地，ga就可以考察几个盆地。

• 与 ga 一样，particlewarm 使用一组起点。 粒子群可以一次检查多个盆地，因为它的种群多样化。

• simulannealbnd 执行随机搜索。 通常，如果 simulannealbnd 比前一点好，则接受该点。  simulannealbnd 有时会接受更糟糕的点，以便到达不同的盆地。

• 模式搜索在接受其中一个之前查看多个相邻点。 如果一些相邻的点属于不同的盆地，模式搜索本质上是一次在多个盆地中查找。

• surrogateopt 从边界内的准随机抽样开始，寻找一个小的目标函数值。 surrogateopt 使用一个评价函数，该函数部分地优先考虑远离评估点的点，这是尝试达到全局解决方案。 在它无法改善当前点后，surrogateopt 重置，使其再次在范围内广泛采样。 重置是 surrogateopt 搜索全局解决方案的另一种方式。

**Basins of Attraction**

如果目标函数 $f(x)$ 是平滑的，向量 $–∇f(x)$ 指向 $f(x)$ 下降最快的方向。 最速下降方程，即
$$
\frac d{dt}x(t)=-∇f(x(t))
$$
产生一条路径 $x(t)$，随着 $t$ 变大，该路径达到局部最小值。 通常，彼此接近的初始值 $x(0)$ 给出趋向于相同最小值点的最陡下降路径。 最速下降的吸引力盆地是导致相同局部最小值的一组初始值。

下图显示了两个一维最小值。 该图以不同的线型显示了不同的吸引力盆地，并用箭头显示了最陡峭的下降方向。 对于此图和后续图，黑点代表局部最小值。 每条最陡的下降路径，从点 $x(0)$​ 开始，到达包含 $x(0)$ 的盆地中的黑点。

![image-20210823180652793](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210823180652793.png)

下图显示了最速下降路径在更多维度上如何变得更加复杂

![image-20210823180707659](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210823180707659.png)

下图显示了更复杂的路径和吸引力盆地。

![image-20210823180718932](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210823180718932.png)

约束可以将一盆吸引力分成几部分。 例如，考虑最小化 $y$ ：

• $y ≥ |x|$  

• $y ≥ 5 – 4(x–2)2$。

该图显示了两个吸引力盆地和最终点。

![image-20210823180829973](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210823180829973.png)

最陡的下降路径是直线下降到约束边界。 从约束边界开始，最陡的下降路径沿着边界向下行进。 最终点是 $(0,0)$​ 或 $(11/4,11/4)$，具体取决于初始 $x$ 值是高于还是低于 2。

**选择求解器的表**

根据问题特征和您想要的解决方案类型选择求解器。 第 1-27 页上的“求解器特性”包含更多信息，可帮助您确定最合适的求解器。 该表给出了适用于大多数问题的建议。

| 问题类型                                                     | 推荐求解器                                  |
| ------------------------------------------------------------ | ------------------------------------------- |
| 平滑（目标两次可微），并且您想要一个局部解决方案             | 一个合适的优化工具箱求解器； 见“优化决策表” |
| 平滑（目标两次可微），并且您想要全局解决方案或多个局部解决方案 | GlobalSearch 或 MultiStart                  |
| 不平滑，并且您需要局部解决方案                               | 模式搜索                                    |
| 不平滑，您需要全局解决方案或多个局部解决方案                 | surrogateopt 或具有多个初始点的模式搜索 x0  |

当您在每个组件上都有有限边界 lb 和 ub 时，要在多个点开始模式搜索，请尝试：

```matlab
x0 = lb + rand(size(lb)).*(ub - lb);
```

许多其他求解器提供不同的求解算法，包括遗传算法求解器 ga 和粒子群求解器。 如果推荐的求解器在您的问题上表现不佳，请尝试其中的一些。 有关详细信息，请参阅第 1-24 页的“全局优化工具箱求解器特性”。

#### Global Optimization Toolbox Solver Characteristics

**Solver Choices**

本节介绍全局优化工具箱解算器的特性。本节包括更有效地取得成果的建议。
要获得更好或更快的解决方案，请首先尝试通过设置适当的选项或边界来调整第1-23页上推荐的解算器。如果结果不令人满意，请尝试其他解决方案。

| 期望的解决方案                 | 平滑目标和约束条件                                           | 非光滑目标或约束                                             |
| ------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 单一局部解                     | 优化工具箱函数；参见“优化决策表”                             | FminBind、模式搜索、fminsearch、ga、particleswarm、simulannealbnd、代理选项 |
| 多个局部解决方案               | GlobalSearch, MultiStart                                     | patternsearch、ga、particleswarm、simulannealbnd或代理选项从多个初始点x0或多个初始总体开始 |
| 单一全局解决方案               | 全局搜索、多部分、模式搜索、particleswarm、ga、simulannealbnd、代理选项 | 模式搜索、ga、particleswarm、simulannealbnd、代理选项        |
| 采用并行处理的单一局部解       | 多部分优化工具箱函数                                         | 模式搜索，ga，particleswarm，代理选项                        |
| 使用并行处理的多个局部解       | MultiStart                                                   | patternsearch、ga或particleswarm从多个初始点x0或多个初始总体开始 |
| 使用并行处理的单一全局解决方案 | MultiStart                                                   | 模式搜索，ga，particleswarm，代理选项                        |

**“期望解决方案”的解释**

要理解“希望解决方案”中术语的含义，请考虑这个例子。
$$
f(x)=100x^2(1-x)^2-x，
$$
其局部极小值 x1 接近 0，x2 接近 1：

![image-20210823183636279](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210823183636279.png)

最小值位于：

```matlab
fun = @(x)(100*x^2*(x - 1)^2 - x);
x1 = fminbnd(fun,-0.1,0.1)
x1 =
  0.0051
x2 = fminbnd(fun,0.9,1.1)
x2 =
  1.0049
```

**Description of the Terms**

| Term             | Meaning                                                      |
| ---------------- | ------------------------------------------------------------ |
| 单一局部解       | 求一个局部解，即目标函数 $ f(x)$ 为局部极小值的点 $x$。有关更多详细信息，请参见第1-18页的“局部最优与全局最优”。在该示例中，x1和x2都是局部解 |
| 多个局部解决方案 | 找到一组局部解决方案。在本例中，局部解的完整集合是{x1，x2}。 |
| 单一全局解决方案 | 找到目标函数 $f(x)$ 为全局最小值的点 $x$。在本例中，全局解为x2。 |

##### 光滑问题解算器的选择

**单一全局解决方案**

1.尝试 Global search。它最专注于寻找全局解决方案，并有一个高效的局部解算器fmincon。

2.下一步尝试 MultiStart。它具有高效的局部解算器，可以搜索各种各样的起点。

3.下一步尝试模式搜索。它的效率较低，因为它不使用渐变。但是，patternsearch是健壮的，并且比其他局部解算器更有效地搜索全局解决方案，从各种起点开始patternsearch。

4.下一步尝试代理选项。代理选择尝试使用最少的目标函数评估来找到全局解决方案。与大多数其他解算器相比，surrogateopt每个函数的计算开销更大。代理选项需要有限的边界，并接受整数约束、线性约束和非线性不等式约束。

5.如果你的问题是无约束的或只有约束的，那么试试particleswarm next。通常，particleswarm比其他解算器更有效，也可能比patternsearch更有效。

6.下一步试试ga。它可以处理所有类型的约束，通常比simulannealbnd更有效。

7.同时进行最后一次尝试。它可以处理没有约束或有界约束的问题。
simulannealbnd通常是效率最低的解算器。然而，如果冷却进度足够慢，它可以找到一个全局解决方案。

**多个局部解决方案**

GlobalSearch和MultiStart都提供多个本地解决方案。有关获得多个解决方案的语法，请参见第3-17页的“多个解决方案”。GlobalSearch和MultiStart在以下特征上有所不同：

•MultiStart可以找到更多的局部极小值。这是因为GlobalSearch拒绝许多生成的起点（局部解决方案的初始点）。本质上，GlobalSearch仅在确定起点有很好的机会获得全局最小值时才接受该起点。相反，MultiStart将所有生成的起点传递给局部解算器。有关更多信息，请参阅第3-35页的“全球搜索算法”。

•MultiStart提供了本地解算器的选择：fmincon、fminunc、lsqcurvefit或lsqnonlin。GlobalSearch解算器仅使用fmincon作为其局部解算器。

•GlobalSearch使用分散搜索算法生成起点。相比之下，MultiStart在边界内随机均匀地生成点，或者允许您提供自己的点。

•MultiStart可以并行运行。请参阅第10-11页的“如何在全局优化工具箱中使用并行处理”。

##### 非光滑问题解算器的选择

选择数量最少的适用解算器。对于具有整数约束的问题，请使用ga。

1.仅在一维有界问题上首先使用fminbnd。fminbnd可证明在一维内快速收敛。

2.对任何其他类型的问题使用patternsearch。patternsearch可证明收敛，并处理所有类型的约束。

3.尝试替代选择具有耗时目标函数的问题。代理选项搜索全局解决方案。代理选项需要有限的边界，并接受整数约束、线性约束和非线性不等式约束。

4.对于低维无界问题，请尝试fminsearch。fminsearch不像patternsearch那样通用，可能无法收敛。对于低维问题，fminsearch使用起来很简单，因为它几乎没有调优选项。

5.在无界或有界约束问题上尝试particleswarm next。particleswarm几乎没有支持理论，但通常是一种有效的算法。

6.下一步试试ga。遗传算法几乎没有支持理论，而且通常不如模式搜索或particleswarm有效。ga处理所有类型的约束。ga和代理选项是唯一接受整数约束的全局优化工具箱解算器。

7.对于无界问题或有界问题，请尝试Simulannelbnd last。simulannealbnd可证明仅在对数冷却计划下收敛，该计划非常缓慢。simulannealbnd只接受有界约束，通常比ga效率低。

##### 解算器特性

| 解算器         | 收敛性                                                       | 特点                                                         |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| GlobalSearch   | 光滑问题的快速收敛到局部最优解                               | 确定性迭代， 基于梯度的 自动随机起点 以启发式方式删除许多起始点 |
| MultiStart     | 光滑问题的快速收敛到局部最优解                               | 确定性迭代 ，可以并行运行；请参阅第10-11页的“如何在全局优化工具箱中使用并行处理” 基于梯度的 随机或确定性起点，或两者的组合 自动随机起点 运行所有起点 |
| patternsearch  | 证明了收敛到局部最优；比基于梯度的解算器慢                   | 确定性迭代 ，可以并行运行；请参阅第10-11页的“如何在全局优化工具箱中使用并行处理” ，没有梯度， 用户提供的起点 |
| surrogateopt   | 有界问题的证明收敛到全局最优；比基于梯度的解算器慢；通常在达到功能评估极限或其他极限时停止 | 随机迭代，可以并行运行；请参阅第10-11页的“如何在全局优化工具箱中使用并行处理”，最好用于耗时的目标函数，需要有界约束，接受线性约束和非线性不等式约束，允许整数约束；参见第7-62页的“混合整数代理优化”，没有梯度，自动起点或用户提供的点，或两者的组合 |
| particleswarm  | 无收敛证明                                                   |                                                              |
| ga             | 无收敛证明                                                   | 随机迭代，可以并行运行；请参阅第10-11页的“如何在全局优化工具箱中使用并行处理”， |
| simulannealbnd | 证明了对于冷却速度非常慢的有界问题收敛到全局最优解           | 随机迭代，没有梯度，用户提供的起点，仅限边界约束             |

对某些特征的解释：

•收敛-当开始时远离局部最小值时，解算器可能无法收敛到任何解。当在局部极小值附近开始时，基于梯度的解算器对于平滑问题会快速收敛到局部极小值。patternsearch可证明收敛于广泛的问题，但收敛速度比基于梯度的解算器慢。对于某些问题，ga和simulannealbnd都无法在合理的时间内收敛，尽管它们通常是有效的。

•迭代-解算器迭代以找到解决方案。迭代中的步骤是迭代。某些解算器具有确定性迭代。其他人使用随机数并进行随机迭代。

•梯度-一些解算器在计算迭代时使用估计的或用户提供的导数。其他解算器不使用或估计导数，只使用目标和约束函数值。

•起点-大多数解算器要求您提供优化的起点，以获得决策变量的维度。ga和代理选择不需要任何起点，因为它们将决策变量的维数作为输入或从边界推断维数。这些解算器自动生成起点或填充，或接受您提供的一个或多个点。

所有这些优化工具箱解算器：

•具有确定性迭代

•需要起点或间隔

•只搜索一个吸引区

## Write Files for Optimization Functions

#### Compute Objective Functions

**Objective (Fitness) Functions**

要使用全局优化工具箱函数，请首先编写一个文件（或匿名函数），用于计算要优化的函数。对于大多数解算器，这称为目标函数，对于遗传算法，这称为适应度函数。该函数应接受一个向量，其长度为自变量的数量，并返回一个标量。对于gamultiobj，函数应返回目标函数值的行向量。对于矢量化解算器，函数应接受矩阵，其中每行表示一个输入向量，并返回目标函数值的向量。本节介绍如何编写该文件。

**Write a Function File**

此示例显示如何为要优化的函数编写文件。假设您希望最小化函数
$$
f(x)=e^{-(x_1^2+x_2^2)}(x_1^2-2x_1x_2+6x_1+4x_2^2-3x_2)
$$
计算此函数的文件必须接受长度为 2 的向量 $x$，对应于变量 $x_1$ 和 $x_2$，并返回等于函数在 $x$ 处的值的标量

1.从MATLAB®文件菜单中选择 新建>脚本（Ctrl+N）。将在编辑器中打开一个新文件

2.输入以下两行代码：

```matlab
function z = my_fun(x)
z = x(1)^2 - 2*x(1)*x(2) + 6*x(1) + 4*x(2)^2 - 3*x(2);
```

3.将文件保存在MATLAB路径上的文件夹中。

检查文件是否返回正确的值。

对于gamultiobj，假设您有三个目标。目标函数返回由三个目标函数值组成的三元素向量

```matlab
function z = my_fun(x)
z = zeros(1,3); % 分配产出
z(1) = x(1)^2 - 2*x(1)*x(2) + 6*x(1) + 4*x(2)^2 - 3*x(2);
z(2) = x(1)*x(2) + cos(3*x(2)/(2+x(1)));
z(3) = tanh(x(1) + x(2));
```

**Write a Vectorized Function**

ga、gamultiobj、paretosearch、particleswarm和patternsearch解算器在一次函数调用中可选地计算向量集合的目标函数。这种方法比串行计算向量的目标函数所需的时间要少。此方法称为矢量化函数调用。

要以矢量化方式计算：

•将目标函数写入：

​       •接受具有任意行数的矩阵。

​       •返回每行的函数值向量。

​       •对于gamultiobj或paretosearch，返回一个矩阵，其中每一行包含相应输入矩阵行的目标函数值。

•如果您有非线性约束，请确保以矢量化方式写入约束。有关详细信息，请参见第2-7页的“矢量化约束”。

•使用optimoptions将UseVectorized选项设置为true。对于patternsearch或paretosearch，还将UseCompletePoll设置为true。确保将选项传递给解算器。

例如，以矢量化方式在第2-2页上编写“编写函数文件”的目标函数，

```matlab
function z = my_fun(x)
z = x(:,1).^2 - 2*x(:,1).*x(:,2) + 6*x(:,1) + ...
4*x(:,2).^2 - 3*x(:,2);
```

要将my_fun用作模式搜索的矢量化目标函数，请执行以下操作：

```matlab
options = optimoptions('patternsearch','UseCompletePoll',true,'UseVectorized',true);
[x fval] = patternsearch(@my_fun,[1 1],[],[],[],[],[],[],...
[],options);
```

要将 my_fun 用作 ga 的矢量化目标函数：

```matlab
options = optimoptions('ga','UseVectorized',true);
[x fval] = ga(@my_fun,2,[],[],[],[],[],[],[],options);
```

对于 gamultiob j或 paretosearch

```matlab
function z = my_fun(x)
z = zeros(size(x,1),3); % allocate output
z(:,1) = x(:,1).^2 - 2*x(:,1).*x(:,2) + 6*x(:,1) + ...
4*x(:,2).^2 - 3*x(:,2);
z(:,2) = x(:,1).*x(:,2) + cos(3*x(:,2)./(2+x(:,1)));
z(:,3) = tanh(x(:,1) + x(:,2));
```

要将my_fun用作gamultiobj的矢量化目标函数，请执行以下操作：

```matlab
options = optimoptions('ga','UseVectorized',true);
[x fval] = gamultiobj(@my_fun,2,[],[],[],[],[],[],options);
```

有关为模式搜索编写矢量化函数的更多信息，请参阅第4-77页的“目标和约束函数矢量化”。有关为ga编写矢量化函数的更多信息，请参阅第5-98页的“适应度函数矢量化”。

**Gradients and Hessians**

如果使用GlobalSearch或MultiStart，则目标函数可以返回导数（梯度、雅可比或Hessian）。有关如何在目标函数中包含此语法的详细信息，请参阅“包含渐变和黑森”。使用optimoptions设置选项，以便解算器使用衍生信息

**局部解算器=fmincon，fminunc**

| 条件                | 选项设置                                                     |
| ------------------- | ------------------------------------------------------------ |
| 目标函数包含梯度    | 'SpecifyObjectiveGradient' = true 请参见“如何包含梯度”       |
| 目标函数包含Hessian | 'HessianFcn' = 'objective 见“包括Hessians”                   |
| 约束函数包含梯度    | 'SpecifyConstraintGradient' = true 请参见“在约束函数中包含梯度” |

局部解算器=lsqcurvefit，lsqnonlin

| 条件                   | 选项设置                          |
| ---------------------- | --------------------------------- |
| 目标函数包含雅可比矩阵 | 'SpecifyObjectiveGradient' = true |

**Maximizing vs. Minimizing**

全局优化工具箱优化函数最小化目标（或适应度）函数。也就是说，他们解决形式上如下的问题
$$
\min f(x)
$$
如果要最大化 $f((x)$​​​，请最小化 $–f(x)$​​，因为 $–f(x)$​最小值出现的点与 $f(x)$ 最大值出现的点相同。

例如，假设您希望最大化函数
$$
f(x)=\exp(− (x_1^ 2+x_2^ 2)) (x_1^ 2− 2x_1x_2+6x_1+4x_2^ 2− 3x_2)
$$
编写一个要计算的函数
$$
g(x) = -f (x) = - \exp(− (x_1^ 2+x_2^ 2)) (x_1^ 2− 2x_1x_2+6x_1+4x_2^ 2− 3x_2)
$$
然后最小化 $g(x)$。从点 $x0=[0,0]$ 开始。

```matlab
f = @(x)exp(-(x(1)^2 + x(2)^2))*(x(1)^2 - 2*x(1)*x(2) + 6*x(1) + 4*x(2)^2 - 3*x(2));
g = @(x)-f(x);
x0 = [0 0];
[xmin,gmin] = fminsearch(g,x0)
```

$f$ 的最大值是f（xmin）的值，即–gmin。

#### Write Constraints

**Set Bounds**

为全局解算器设置边界比为局部解算器设置边界更重要。全局解算器以多种方式使用边界：

•GlobalSearch要求生成分散搜索点的边界。如果不提供边界，GlobalSearch会将低于-9999的每个组件和高于-10001的每个组件进行边界搜索。但是，这些边界很容易不合适。

•如果您不提供边界，也不提供自定义起点，则多部分将每个组件的边界限制在-1000以下，1000以上。但是，这些边界很容易不合适。

•遗传算法使用边界和线性约束进行初始种群生成。对于无界问题，ga使用默认值0作为初始点生成的每个维度的下限，1作为上限。对于有界问题和具有线性约束的问题，遗传算法使用边界和约束来生成初始总体。

•simulannealbnd和patternsearch不需要边界，尽管它们可以使用边界。

**Ensure ga Options Maintain Feasibility**

ga解算器通常在边界和线性约束方面保持严格的可行性。这意味着，在每次迭代中，总体的所有成员都满足边界和线性约束。

但是，您可以设置导致此可行性失败的选项。例如，如果将MutationFcn设置为@mutationgaussian或@mutationuniform，则变异函数不考虑约束，你的种群可能变得不可行。类似地，一些交叉函数可能会导致不可行总体，尽管默认的GACreationLinearAppability并不考虑边界和线性约束。此外，当使用自定义变异或交叉函数时，ga可能存在不可行点。

为确保可行性，使用ga的默认交叉和变异函数。请特别注意，任何自定义函数在边界和线性约束方面都保持可行性。

当存在整数约束时，遗传算法不强制执行线性约束。相反，ga将线性约束冲突合并到惩罚函数中。参见第5-42页的“整数遗传算法”。

**Gradients and Hessians**

如果将GlobalSearch或MultiStart与fmincon一起使用，则非线性约束函数可以返回导数（梯度或Hessian）。有关详细信息，请参见第2-4页的“梯度和Hessian”。

**Vectorized Constraints**

ga和patternsearch解算器在一次函数调用中可选地计算向量集合的非线性约束函数。这种方法比串行计算向量的目标函数所需的时间要少。此方法称为矢量化函数调用。

要使解算器以矢量化方式进行计算，必须同时对目标（适应度）函数和非线性约束函数进行矢量化。有关详细信息，请参见第4-77页的“目标和约束函数矢量化”。
例如，假设三维问题的非线性约束为

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210823200219154.png" alt="image-20210823200219154" style="zoom:67%;" />

下面的代码以矢量化的方式给出这些非线性约束，假设输入矩阵 $x$ 的行是种群或输入向量

```matlab
function [c ceq] = nlinconst(x)
c(:,1) = x(:,1).^2/4 + x(:,2).^2/9 + x(:,3).^2/25 - 6;
c(:,2) = cosh(x(:,1) + x(:,2)) - x(:,3);
ceq = x(:,1).*x(:,2).*x(:,3) - 2;
```

例如，最小化向量化二次函数

```matlab
function y = vfun(x)
y = -x(:,1).^2 - x(:,2).^2 - x(:,3).^2;
```

使用patternsearch在具有约束的区域上

```matlab
options = optimoptions('patternsearch','UseCompletePoll',true,'UseVectorized',true);
[x fval] = patternsearch(@vfun,[1,1,2],[],[],[],[],[],[],...
@nlinconst,options)
Optimization terminated: mesh size less than options.MeshTolerance
and constraint violation is less than options.ConstraintTolerance.
```

结果为

```matlab
x=0.2191 0.7500 12.1712 
fval=-148.7480
```

使用遗传算法：

```matlab
options = optimoptions('ga','UseVectorized',true);
[x fval] = ga(@vfun,3,[],[],[],[],[],[],@nlinconst,options)
Optimization terminated: maximum number of generations exceeded
```

结果为

```matlab
x=-1.4098-0.121611.6664 
fval=-138.1066
```

对于这个问题，patternsearch可以更快更准确地计算解决方案。

#### Set and Change Options

对于除GlobalSearch和MultiStart之外的所有全局优化工具箱解算器，建议使用optimoptions函数设置选项。使用**名称-值**对设置GlobalSearch和MultiStart选项；参见第3-52页的“更改全局选项”。

例如，将ga最大时间设置为300秒，并设置迭代显示

```matlab
options = optimoptions('ga','MaxTime',300,'Display','iter');
```

更改选项如下：

•点符号。例如

```matlab
options.MaxTime = 5e3;
```

•optimoptions，例如

```matlab
options = optimoptions(options,'MaxTime',5e3);
```

确保在解算器调用中传递选项。例如

```matlab
[x,fval] = ga(@objfun,2,[],[],[],[],lb,ub,@nonlcon,options);
```

要查看可以更改的选项，请参阅“解算器函数参考”页面。有关选项详细信息，请参见选项参考部分

#### View Options

optimoptions“隐藏”了一些选项，这意味着它不会显示它们的值。例如，它隐藏了patternsearch MaxMeshSize选项。

```matlab
options=options（'patternsearch'，'MaxMeshSize'，1e2）
```

```matlab
options =
patternsearch options:
Set properties:
No options set.
Default properties:
AccelerateMesh: 0
ConstraintTolerance: 1.0000e-06
Display: 'final'
FunctionTolerance: 1.0000e-06
InitialMeshSize: 1
MaxFunctionEvaluations: '2000*numberOfVariables'
MaxIterations: '100*numberOfVariables'
MaxTime: Inf
MeshContractionFactor: 0.5000
MeshExpansionFactor: 2
MeshTolerance: 1.0000e-06
OutputFcn: []
PlotFcn: []
PollMethod: 'GPSPositiveBasis2N'
PollOrderAlgorithm: 'consecutive'
ScaleMesh: 1
SearchFcn: []
StepTolerance: 1.0000e-06
UseCompletePoll: 0
UseCompleteSearch: 0
UseParallel: 0
UseVectorized:
```

您可以使用点符号查看任何选项的值，包括“隐藏”选项。例如

```matlab
options.MaxMeshSize
```

解算器参考页以斜体列出“隐藏”选项。

有些选项“隐藏”有两个原因：

·它们没有用处。例如，ga StallTest选项允许您选择工作不正常的失速测试。因此，此选项是“隐藏的”。

•它们很少使用，或者很难知道何时使用。例如，patternsearch MaxMeshSize选项很难选择，因此“隐藏”选项也很难选择。
有关详细信息，请参阅第11-63页的“optimoptions隐藏的选项”。

## Using GlobalSearch and MultiStart

**GlobalSearch和MultiStart可以解决的问题**

全局搜索和多部分求解器适用于具有光滑目标和约束函数的问题。解算器搜索全局最小值或一组局部最小值。有关使用哪种解算器的详细信息，请参见第1-23页的“选择解算器的表格”。

GlobalSearch和MultiStart通过从各种起点启动局部解算器（如fmincon）来工作。一般来说，起点是随机的。但是，对于MultiStart，您可以提供一组起点。有关更多信息，请参阅第3-34页的“GlobalSearch和MultiStart如何工作”。

要了解如何使用这些解算器，请参阅第3-3页的“GlobalSearch和MultiStart工作流”。

#### Workflow for GlobalSearch and MultiStart

要为平滑问题找到全局或多个局部解决方案：1第3-4页“创建问题结构”2第3-7页“创建解算器对象”3（可选，仅适用于多部分）“为多部分设置起点”3-10页“运行解算器”3-13页下图说明了这些步骤

![image-20210823201343327](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210823201343327.png)

#### Create Problem Structure

**About Problem Structures**

要使用GlobalSearch或MultiStart解算器，必须首先创建问题结构。创建问题结构的推荐方法是使用第3-4页上的createOptimProblem函数。您可以手动创建结构，但这样做容易出错。

**Use the createOptimProblem Function**

按照以下步骤使用createOptimProblem函数创建问题结构。

1.将目标函数定义为文件函数或匿名函数。有关详细信息，请参阅第2-2页的“计算目标函数”。如果您的解算器是lsqcurvefit或lsqnonlin，请确保目标函数返回向量，而不是标量。

2.如果相关，创建约束，例如边界和非线性约束函数。有关详细信息，请参见第2-6页的“写入约束”。

3.创建一个起点。例如，创建三维随机起点xstart:xstart=randn（3,1）；

4.（可选）使用optimoptions创建选项。例如，options=optimoptions（@fmincon，'Algorithm'，'interior-point'）；

5.输入

```matlab
problem = createOptimProblem(solver,
```

其中solver是本地解算器的名称

•对于GlobalSearch：“fmincon”

•对于MultiStart，选择如下：

•“fmincon”

•“fmincon”

•“lsqcurvefit”

•“lsqnonlin”

有关选择的帮助，请参阅“优化决策表”。

6.使用“x0”参数设置初始点。如果初始点为xstart，而解算器为fmincon，则现在输入

```matlab
problem = createOptimProblem('fmincon','x0',xstart,
```

7.在目标中包含目标函数的函数句柄：

```matlab
problem = createOptimProblem('fmincon','x0',xstart, ...'objective',@objfun,
```

8.设置边界和其他适用的约束条件

| 约束                                 | 名称      |
| ------------------------------------ | --------- |
| 下限                                 | 'lb'      |
| 上界                                 | “ub”      |
| 线性不等式的矩阵Aineq Aineq x≤ bineq | 'Aineq'   |
| 线性不等式的向量bineq≤ bineq         | 'bineq'   |
| 线性等式的矩阵Aeq Aeq x=beq          | 'Aeq'     |
| 线性等式的向量beq Aeq x=beq          | “beq”     |
| 非线性约束函数                       | 'nonlcon' |

9.如果使用lsqcurvefit本地解算器，则包括输入数据和响应数据的向量，分别命名为“xdata”和“ydata”。

10.最佳实践：通过在结构上运行解算器来验证问题结构。例如，如果本地解算器为fmincon：

```matlab
[x,fval,eflag,output] = fmincon(problem);
```

**示例：使用createOptimProblem创建问题结构**

本例将第3-13页“运行解算器”中的函数最小化，受约束 $x_1+2x_2\ge4$，目标是

sixmin=$4x^2–2.1x^4+x^6/3+xy–4y^2+4y^4$。

使用fmincon的内点算法，将起点设置为$[2,3].$

1.为目标函数编写函数句柄。

```matlab
sixmin = @(x)(4*x(1)^2 - 2.1*x(1)^4 + x(1)^6/3 ...+ x(1)*x(2) - 4*x(2)^2 + 4*x(2)^4)
```

2 写出线性约束矩阵。 将约束更改为“小于”形式：

```matlab
A = [-1,-2];
b = -4;
```

3 创建局部选项以使用内点算法：

```matlab
opts = optimoptions(@fmincon,'Algorithm','interior-point');
```

4 使用 createOptimProblem 创建问题结构

```matlab
problem = createOptimProblem('fmincon', ...'x0',[2;3],'objective',sixmin, ...'Aineq',A,'bineq',b,'options',opts)
```

5 结果结构：

```matlab
problem =
     struct with fields:
         objective: @(x)(4*x(1)^2-2.1*x(1)^4+x(1)^6/3+x(1)*x(2)-4*x(2)^2+4*x(2)^4)
             x0: [2x1 double]
             Aineq: [-1 -2]
             bineq: -4
               Aeq: []
               beq: []
                lb: []
                ub: []
           nonlcon: []
            solver: 'fmincon'
           options: [1x1 optim.options.Fmincon]
```

6 最佳实践：通过在结构上运行求解器来验证问题结构

```matlab
[x,fval,eflag,output] = fmincon(problem);
```

#### Create Solver Object

**What Is a Solver Object?**

求解器对象包含您对优化全局部分的首选项。您无需设置任何首选项。 使用默认设置创建一个名为 gs 的 GlobalSearch 对象，如下所示： 

```matlab
gs = GlobalSearch
```

同样，创建一个名为 ms 的 MultiStart 对象，默认设置如下：

```matlab
ms = MultiStart;
```

**Properties (Global Options) of Solver Objects**

全局选项是 GlobalSearch 或 MultiStart 对象的属性。

| 属性名称          | 意义                                                         |
| ----------------- | ------------------------------------------------------------ |
| Display           | 迭代显示的详细程度。 设置为 'off' 表示不显示，设置为 'final'（默认）表示运行结束时的报告（指运行结束时显示迭代次数），或设置为 'iter' 表示求解器进行时的报告（指在运行中显示当前迭代次数）。 有关更多信息和示例，请参阅第 3-21 页上的“迭代显示”。 |
| FunctionTolerance | 求解器认为数值差异在 FunctionTolerance 内的目标函数值彼此相同（不不同）。 默认值：1e-6。 当解决方案同时满足 FunctionTolerance 和 XTolerance 容差时，求解器将解决方案分组。 |
| XTolerance        | 求解器将彼此在 XTolerance 距离内的解视为相同（不不同）。 默认值：1e-6。 当解决方案同时满足 FunctionTolerance 和 XTolerance 容差时，求解器将解决方案分组。 |
| MaxTime           | 如果运行超过 MaxTime 秒（由时钟测量）（而不是处理器秒），则求解器将停止。 默认值：Inf |
| StartPointsToRun  | 选择是运行“所有”（默认）起点，仅运行满足“边界”的那些点，还是仅运行对于边界和不等式约束与“边界 ineqs”相关的那些点。 有关示例，请参阅第 3-75 页上的“仅使用可行的起点进行优化”。 |
| OutputFcn         | 在每个本地求解器运行后运行的函数。 请参阅第 3-27 页的“GlobalSearch 和 MultiStart 的输出函数”。 默认值：[] |
| PlotFcn           | 绘制在每次本地求解器运行后运行的函数。 请参见第 3-30 页的“全局搜索和多起始点的绘图函数”。 默认值：[] |

**Properties for GlobalSearch**

| Property Name           | Meaning                         |
| ----------------------- | ------------------------------- |
| NumTrialPoints          | 要检查的试验点数。 默认值：1000 |
| BasinRadiusFactor       |                                 |
| DistanceThresholdFactor |                                 |
| MaxWaitCycle            |                                 |
| NumStageOnePoints       |                                 |
| PenaltyThresholdFactor  |                                 |

**Properties for MultiStart**

| Property Name | Mean                                                         |
| ------------- | ------------------------------------------------------------ |
| UseParallel   | 为 true 时，MultiStart 尝试将起点分配给本地求解器的多个处理器。 通过设置为 false（默认）来禁用。 有关详细信息，请参阅“如何在 Global Optimization Toolbox 中使用并行处理”（第 10-11 页）。 有关示例，请参阅第 3-82 页上的“并行多启动”。 |

**Creating a Nondefault GlobalSearch Object**

假设您想解决一个问题并且：

• 如果局部解彼此相差在 0.01 以内并且函数值在默认的 FunctionTolerance 容差范围内，则认为它们是相同的。

• 在计算上花费的时间不超过 2000 秒。

为了解决这个问题，创建一个 GlobalSearch 对象 gs 如下：

```matlab
gs = GlobalSearch('XTolerance',0.01,'MaxTime',2000);
```

**Creating a Nondefault MultiStart Object**

假设您要解决以下问题： 

• 如果局部解彼此相差在 0.01 以内且函数值在默认的 FunctionTolerance 容差范围内，则您认为它们是相同的。

• 您在计算上花费的时间不超过 2000 秒。

解决问题，创建一个MultiStart对象ms如下

```matlab
ms = MultiStart('XTolerance',0.01,'MaxTime',2000);
```

#### Set Start Points for MultiStart

**Four Ways to Set Start Points**

有四种方法可以告诉 MultiStart 本地求解器使用哪个起点： 

• 第 3-10 上的传递一个正整数 k。MultiStart 生成 k - 1 个起点，就像使用RandomStartPointSet 对象和问题结构一样。  MultiStart 还使用问题结构中的 x0 起点，总共 k 个起点。

• 传递 RandomStartPointSet 对象（第 3-10 页）。

• 传递 CustomStartPointSet 对象（第 3-11 页）。

• 传递第 3-12 页的 RandomStartPointSet 和 CustomStartPointSet 对象的元胞数组。

如果您有一些要运行的特定点，但又希望 MultiStart 使用其他随机起点，则传递一个元胞数组。

注意 您可以控制 MultiStart 是使用所有起点，还是仅使用满足边界或其他不等式约束的那些点。 有关详细信息，请参阅第 3-39 页上的“过滤器起点（可选）”。

**Positive Integer for Start Points**

为 k 个起点运行 MultiStart 的语法是

```matlab
[xmin,fmin,flag,outpt,allmins] = run(ms,problem,k);
```

正整数 k 指定 MultiStart 使用的起始点数。  MultiStart 使用问题的维度和问题结构的边界生成随机起点。MultiStart 生成 k - 1 个随机起点，并且还使用问题结构中的 x0 起点。

**RandomStartPointSet Object for Start Points**

创建一个 RandomStartPointSet 对象，如下所示：

```matlab
stpoints = RandomStartPointSet;
```

从 RandomStartPointSet 开始运行 MultiStart，如下所示：

```matlab
[xmin,fmin,flag,outpt,allmins] = run(ms,problem,stpoints);
```

默认情况下，RandomStartPointSet 对象生成 10 个起点。 使用 NumStartPoints 属性控制起点的数量。 例如，要生成 40 个起点：

```matlab
stpoints = RandomStartPointSet('NumStartPoints',40);
```

您可以为 RandomStartPointSet 设置人工边界。 该人工边界与问题结构的边界结合使用：

如果组件没有边界，RandomStartPointSet 使用人工边界的下限和人工边界的上限。

• 如果组件有下限 lb 但没有上限，则RandomStartPointSet 使用lb + 2* ArtificialBound 的上限。

• 类似地，如果一个组件有上界 ub 但没有下界，RandomStartPointSet 使用下界 ub - 2*ArtificialBound

例如，要生成 100 个人工边界为 50 的起点：

```matlab
stpoints = RandomStartPointSet('NumStartPoints',100, ...，'ArtificialBound',50);
```

RandomStartPointSet 对象生成与问题结构中 x0 点维度相同的起点； 见清单。

**CustomStartPointSet Object for Start Points**

要使用一组特定的起点，请将它们打包在 CustomStartPointSet 中，如下所示：

1 将起点放在矩阵中。 矩阵的每一行代表一个起点。MultiStart 运行矩阵的所有行，受 StartPointsToRun 属性过滤的影响。 有关详细信息，请参阅第 3-38 页上的“MultiStart 算法”。

2 从矩阵创建一个 CustomStartPointSet 对象 

```matlab
tpoints = CustomStartPointSet(ptmatrix);
```

例如，创建一组 40 个五维点，其中一个点的每个分量等于 10 加上一个均值为 25 的指数分布变量：

```matlab
pts = -25*log(rand(40,5)) + 10;
tpoints = CustomStartPointSet(pts);
```

从 CustomStartPointSet 开始运行 MultiStart，如下所示：

```matlab
[xmin,fmin,flag,outpt,allmins] = run(ms,problem,tpoints);
```

要从 CustomStartPointSet 对象获取原始点矩阵，请使用列表：

```matlab
pts = list(tpoints); % 假设 tpoints 是一个 CustomStartPointSet
```

CustomStartPointSet 有两个属性：StartPointsDimension 和 NumStartPoints。 您可以使用这些属性来查询 CustomStartPointSet 对象。 例如，示例中的 tpoints 对象具有以下属性：

```matlab
tpoints.StartPointsDimension
ans =5
tpoints.NumStartPoints 
ans = 40
```

**Cell Array of Objects for Start Points**

要使用一组特定的起点以及一些随机生成的点，请传递 RandomStartPointSet 或 CustomStartPointSet 对象的元胞数组。例如，要同时使用第 3-11 页上的“用于起点的 CustomStartPointSet 对象”的 40 个特定五维点和来自 RandomStartPointSet 的 40 个附加五维点：

```matlab
pts = -25*log(rand(40,5)) + 10;
tpoints = CustomStartPointSet(pts);
rpts = RandomStartPointSet('NumStartPoints',40);
allpts = {tpoints,rpts};
Run MultiStart starting from the allpts cell array:
% Assume ms and problem exist
[xmin,fmin,flag,outpt,allmins] = run(ms,problem,allpts);
```

#### Run the Solver

**Optimize by Calling run（通过调用运行优化）**

对于 GlobalSearch 和 MultiStart，运行求解器几乎相同。 语法上的唯一区别是 MultiStart 需要一个额外的输入来描述起点。

例如，假设您要查找 Sixmin 函数的几个局部最小值 

sixmin=$4x^2–2.1x^4+x^6/3+xy–4y^2+4y^4$

![image-20210824005306138](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210824005306138.png)

该函数也称为六峰驼背函数[3]。 所有局部最小值都位于 $–3 ≤ x,y ≤ 3$ 区域内。

**Example of Run with GlobalSearch**

要使用 GlobalSearch 查找 Sixmin 函数的多个局部最小值，请输入：

```matlab
% % Set the random stream to get exactly the same output
% rng(14,'twister')
gs = GlobalSearch;
opts = optimoptions(@fmincon,'Algorithm','interior-point');
sixmin = @(x)(4*x(1)^2 - 2.1*x(1)^4 + x(1)^6/3 ...+ x(1)*x(2) - 4*x(2)^2 + 4*x(2)^4);
problem = createOptimProblem('fmincon','x0',[-1,2],...'objective',sixmin,'lb',[-3,-3],'ub',[3,3],...'options',opts);
[xming,fming,flagg,outptg,manyminsg] = run(gs,problem);

The output of the run (which varies, based on the random seed):
xming,fming,flagg,outptg,manyminsg
xming =
0.0898 -0.7127
fming =
-1.0316
flagg =
1
outptg =
struct with fields:
funcCount: 2115
localSolverTotal: 3
localSolverSuccess: 3
localSolverIncomplete: 0
localSolverNoSolution: 0
message: 'GlobalSearch stopped because it analyzed all the trial po...'
manyminsg =
1x2 GlobalOptimSolution array with properties:
X
Fval
Exitflag
Output
X0
```

**Example of Run with MultiStart**

要使用带有 MultiStart 的 fmincon 运行 50 次来查找 Sixmin 函数的几个局部最小值，请输入：

```matlab
% % Set the random stream to get exactly the same output
% rng(14,'twister')
ms = MultiStart;
opts = optimoptions(@fmincon,'Algorithm','interior-point');
sixmin = @(x)(4*x(1)^2 - 2.1*x(1)^4 + x(1)^6/3 ...+ x(1)*x(2) - 4*x(2)^2 + 4*x(2)^4);
problem = createOptimProblem('fmincon','x0',[-1,2],...'objective',sixmin,'lb',[-3,-3],'ub',[3,3],...'options',opts);
[xminm,fminm,flagm,outptm,manyminsm] = run(ms,problem,50);

The output of the run (which varies based on the random seed):
xminm,fminm,flagm,outptm,manyminsm
xminm =
0.0898 -0.7127
fminm =
-1.0316
flagm =
1
outptm =
struct with fields:
funcCount: 2034
localSolverTotal: 50
localSolverSuccess: 50
localSolverIncomplete: 0
localSolverNoSolution: 0
message: 'MultiStart completed the runs from all start points.…'
manyminsm =
1x6 GlobalOptimSolution array with properties:
X
Fval
Exitflag
Output
X0

```

在这种情况下，MultiStart 定位了所有六个局部最小值，而 GlobalSearch 定位了两个。 有关 MultiStart 解决方案的图片，请参阅第 3-24 页上的“可视化吸引力盆地”。

#### Single Solution

您可以通过使用语法调用 **run** 来获得在运行期间找到的单个最佳解决方案

```matlab
[x,fval,eflag,output] = run(...);
```

• x 是具有最小目标函数值的局部最小值的位置。

• fval 是在 x 处评估的目标函数值。

• eflag 是全局求解器的退出标志。 其值为

全局求解器退出标志

| 2    | 至少找到一个局部最小值。 本地求解器的一些运行收敛（具有正退出标志）。 |
| ---- | ------------------------------------------------------------ |
| 1    | 至少找到一个局部最小值。 本地求解器的所有运行都收敛（具有正退出标志）。 |
| 0    | 未找到局部最小值。 本地求解器至少调用一次，并且至少有一个本地求解器超出 MaxIterations 或 MaxFunctionEvaluations 容差。 |
| -1   | 求解器被输出函数或绘图函数停止。                             |
| -2   | 找不到可行的局部最小值                                       |
| -5   | 超出最大时间限制                                             |
| -8   | 没有找到解决办法。 所有运行都有本地求解器退出标志 -1 或更小。 |
| -10  | 在用户提供的功能中遇到的故障。                               |

• 输出是一个结构，其中包含有关本地求解器多次运行的详细信息。 有关详细信息，请参阅第 3-23 页的“全局输出结构”。

输出列表适用于 $eflag > 0$​ 的情况。如果 $eflag <= 0$，则 x 如下：

• 如果某些局部解决方案可行，x 代表最低目标函数值的位置。  “可行”意味着约束违规小于问题.options.ConstraintTolerance。

• 如果没有可行的解决方案，x 是不可行性最低的解决方案。

• 如果不存在解，x、fval 和输出为空条目 ([])。

#### Multiple Solutions

**About Multiple Solutions**

您可以通过使用语法调用 run 来获取对象中的多个解决方案

```matlab
[x,fval,eflag,output,manymins] = run(...);
```

manymins 是解决方案对象的向量； 请参阅 GlobalOptimSolution。  manymins 向量按照目标函数值的顺序，从最低（最好）到最高（最差）。 每个解决方案对象都包含以下属性（字段）：

• X — 局部最小值 

• Fval — X 处目标函数的值 

• Exitflag — 局部求解器的退出标志（在局部求解器函数参考页中描述：fmincon exitflag、fminunc exitflag、lsqcurvefit exitflag 或 lsqnonlin exitflag 

• 输出 — 局部求解器的输出结构（在局部求解器函数参考页面中进行了描述：fmincon 输出、fminunc 输出、lsqcurvefit 输出或 lsqnonlin 输出 

• X0 — 指向解点 X 的起点元胞数组

有几种方法可以检查解对象的向量：

• 在MATLAB 工作区浏览器中。 双击解决方案对象，然后在变量编辑器中双击结果显示

![image-20210824010943413](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210824010943413.png)

![image-20210824010949461](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210824010949461.png)

![image-20210824010955208](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210824010955208.png)

• 使用点表示法。  GlobalOptimSolution 属性大写。 使用正确的大小写来访问属性

例如，要查找函数值的向量，请输入：

```matlab
fcnvals = [manymins.Fval]
fcnvals =
-1.0316 -0.2155
```

要获取导致最低函数值（ manymins 的第一个元素）的所有起点的元胞数组，请输入：

```matlab
smallX0 = manymins(1).X0
```

• 绘制一些字段值。 例如，要查看结果 Fval 的范围，请输入：

```matlab
histogram([manymins.Fval],10)
```

这导致计算出的函数值的直方图。  （该图显示了来自与前几幅图不同的示例的直方图。）

![image-20210824011210934](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210824011210934.png)

**Change the Definition of Distinct Solutions**

在获得多个本地解决方案后，您可能会发现您的容差不合适。您可以拥有比您想要的多得多的本地解决方案，它们之间的间隔太近。 或者您的解决方案可能比您想要的要少，因为 GlobalSearch 或 MultiStart 将太多的解决方案聚集在一起。

要处理这种情况，请使用不同的容差再次运行求解器。  XTolerance 和 FunctionTolerance 容差确定求解器如何将其输出分组到 GlobalOptimSolution 向量中。 这些容差是 GlobalSearch 或 MultiStart 对象的属性。

例如，假设您想使用 fmincon 中的 active-set 算法来解决第 3-14 页的“使用 MultiStart 运行的示例”中的问题。 进一步假设您希望 XTolerance 和 FunctionTolerance 的容差均为 0.01。  run 方法对目标函数值在彼此的 FunctionTolerance 范围内的局部解进行分组，并且彼此之间的距离也小于 XTolerance。 要获得解决方案：

```matlab
% % Set the random stream to get exactly the same output
% rng(14,'twister')
ms = MultiStart('FunctionTolerance',0.01,'XTolerance',0.01);
opts = optimoptions(@fmincon,'Algorithm','active-set');
sixmin = @(x)(4*x(1)^2 - 2.1*x(1)^4 + x(1)^6/3 ...+ x(1)*x(2) - 4*x(2)^2 + 4*x(2)^4);
problem = createOptimProblem('fmincon','x0',[-1,2],...
'objective',sixmin,'lb',[-3,-3],'ub',[3,3],...'options',opts);
[xminm,fminm,flagm,outptm,someminsm] = run(ms,problem,50);

MultiStart completed the runs from all start points.
All 50 local solver runs converged with a positive local solver exit flag.
someminsm
someminsm =
1x5 GlobalOptimSolution
Properties:
X
Fval
Exitflag
Output
X0
```

在这种情况下，MultiStart 生成了五个不同的解决方案。 这里的“不同”意味着解决方案在目标函数值或位置上相距超过 0.01。

#### Iterative Display

**Types of Iterative Display**

迭代显示为您提供有关求解器运行过程中进度的信息。

有两种类型的迭代显示：

• 全局求解器显示 

• 局部求解器显示

这两种类型都出现在命令行中，具体取决于全局和局部选项。

通过使用 optimoptions 将 problem.options 字段中的 Display 选项设置为 'iter' 或 'iter-detailed' 来获得局部求解器迭代显示。 有关详细信息，请参阅“迭代显示”。

通过将 GlobalSearch 或 MultiStart 对象中的 Display 属性设置为“iter”，获得全局求解器迭代显示。

全局求解器将本地求解器的默认显示选项设置为“关闭”，除非问题结构具有此选项的值。 全局求解器不会覆盖您为本地选项所做的任何设置

注意 将本地求解器显示选项设置为“关闭”以外的任何选项都可以产生大量输出。  optimoptions(@solver) 创建的默认显示选项是“final”。

**Examine Types of Iterative Display**

使用 GlobalSearch 和 GlobalSearch 迭代显示运行第 3-13 页的“运行求解器”中描述的示例

```matlab
GlobalSearch iterative display:
% % Set the random stream to get exactly the same output
% rng(14,'twister')
gs = GlobalSearch('Display','iter');
opts = optimoptions(@fmincon,'Algorithm','interior-point');
sixmin = @(x)(4*x(1)^2 - 2.1*x(1)^4 + x(1)^6/3 ...
+ x(1)*x(2) - 4*x(2)^2 + 4*x(2)^4);
problem = createOptimProblem('fmincon','x0',[-1,2],...
'objective',sixmin,'lb',[-3,-3],'ub',[3,3],...
'options',opts);
[xming,fming,flagg,outptg,manyminsg] = run(gs,problem);
Num Pts Best Current Threshold Local Local
Analyzed F-count f(x) Penalty Penalty f(x) exitflag Procedure
0 34 -1.032 -1.032 1 Initial Point
200 1275 -1.032 -0.2155 1 Stage 1 Local
300 1377 -1.032 248.7 -0.2137 Stage 2 Search
400 1477 -1.032 278 1.134 Stage 2 Search
446 1561 -1.032 1.6 2.073 -0.2155 1 Stage 2 Local
500 1615 -1.032 9.055 0.3214 Stage 2 Search
600 1715 -1.032 -0.7299 -0.7686 Stage 2 Search
700 1815 -1.032 0.3191 -0.7431 Stage 2 Search
800 1915 -1.032 296.4 0.4577 Stage 2 Search
900 2015 -1.032 10.68 0.5116 Stage 2 Search
1000 2115 -1.032 -0.9207 -0.9254 Stage 2 Search
GlobalSearch stopped because it analyzed all the trial points.
All 3 local solver runs converged with a positive local solver exit flag
```

#### Global Output Structures

全局输出结构运行可以产生两种类型的输出结构：

全局输出结构。 此结构包含有关从多个起点进行的整体运行的信息。 详情如下。

本地求解器输出结构。  GlobalOptimSolution 对象的向量在向量的每个元素中包含一个这样的结构。 有关此结构的说明，请参阅揙utput 结构？ 或本地求解器的函数参考页面： fmincon 输出、fminunc 输出、lsqcurvefit 输出或 lsqnonlin 输出

![image-20210824183513700](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210824183513700.png)

本地求解器的正退出标志通常表示运行成功。 负退出标志表示失败。  0 退出标志表示求解器因超出迭代或函数评估限制而停止。 有关更多信息，请参阅“退出标志和退出消息”或“容差和停止标准”。

#### Visualize the Basins of Attraction

哪个起点通向哪个盆地？ 对于最速下降求解器，附近的点通常通向同一个盆地； 请参阅第 1-19 页的“景点盆地”。 但是，对于 Optimization Toolbox 求解器，盆地更为复杂。

绘制示例中的 MultiStart 起点，“使用 MultiStart 运行的示例”（第 3-14 页），并用它们结束的盆地进行颜色编码。

```matlab
% rng(14,'twister')
% Uncomment the previous line to get the same output
ms = MultiStart;
opts = optimoptions(@fmincon,'Algorithm','interior-point');
sixmin = @(x)(4*x(1)^2 - 2.1*x(1)^4 + x(1)^6/3 ...
+ x(1)*x(2) - 4*x(2)^2 + 4*x(2)^4);
problem = createOptimProblem('fmincon','x0',[-1,2],...
'objective',sixmin,'lb',[-3,-3],'ub',[3,3],...
'options',opts);
[xminm,fminm,flagm,outptm,manyminsm] = run(ms,problem,50);
possColors = 'kbgcrm';
hold on
for i = 1:size(manyminsm,2)
% Color of this line
cIdx = rem(i-1, length(possColors)) + 1;
color = possColors(cIdx);
% Plot start points
u = manyminsm(i).X0;
x0ThisMin = reshape([u{:}], 2, length(u));
plot(x0ThisMin(1, :), x0ThisMin(2, :), '.', ...
'Color',color,'MarkerSize',25);
% Plot the basin with color i
plot(manyminsm(i).X(1), manyminsm(i).X(2), '*', ...
'Color', color, 'MarkerSize',25);
end % basin center marked with a *, start points with dots
hold off
```

![image-20210824183613003](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210824183613003.png)

图中用彩色 * 符号显示了盆地的中心。 与 * 符号颜色相同的起点会聚到 * 符号的中心。

起点并不总是会聚到最近的盆地。 例如，红色点比红色盆地中心更靠近青色盆地中心。 此外，许多黑色和蓝色起点更靠近相对的盆地中心。洋红色和红色盆地较浅，如下面的等高线图所示。

![image-20210824183643095](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210824183643095.png)

#### Output Functions for GlobalSearch and MultiStart

**What Are Output Functions?**

输出函数允许您检查优化中的中间结果。 此外，它们允许您以编程方式停止求解器。

有两种类型的输出函数，如第 3-23 页上的两种类型的输出结构： 

• 全局输出函数在每次本地求解器运行后运行。 它们也会在全局求解器开始和结束时运行。

• 局部输出函数在局部求解器的每次迭代后运行。 请参阅“Optimization Toolbox™ 的输出函数”。

要使用全局输出函数： 

• 使用第 11-3 页上的“OutputFcn”中描述的语法编写输出函数。

• 将 GlobalSearch 或 MultiStart 求解器的 OutputFcn 属性设置为输出函数的函数句柄。 您可以通过将 OutputFcn 属性设置为函数句柄元胞数组来使用多个输出函数。

**GlobalSearch Output Function**

此输出函数在找到五个具有正退出标志的不同局部最小值后，或在找到小于 0.5 的局部最小值后停止 GlobalSearch。 输出函数使用持久局部变量 foundLocal 来存储局部结果。  foundLocal 使输出函数能够确定本地解决方案是否与其他解决方案不同，在 1e-4 的容差范围内。

要使用嵌套函数而不是持久变量存储本地结果，请参阅“嵌套输出函数示例

1 使用第 11-3 页上的“OutputFcn”中描述的语法编写输出函数。

```matlab
function stop = StopAfterFive(optimValues, state)
persistent foundLocal
stop = false;
switch state
   case 'init'
     foundLocal = []; % initialized as empty
   case 'iter'
     newf = optimValues.localsolution.Fval;
     eflag = optimValues.localsolution.Exitflag;
% Now check if the exit flag is positive and
% the new value differs from all others by at least 1e-4
% If so, add the new value to the newf list
     if eflag > 0 && all(abs(newf - foundLocal) > 1e-4)
        foundLocal = [foundLocal;newf];
        % Now check if the latest value added to foundLocal
% is less than 1/2
% Also check if there are 5 local minima in foundLocal
% If so, then stop
        if foundLocal(end) < 0.5 || length(foundLocal) >= 5
           stop = true;
     end
  end
end
```

2 将 StopAfterFive.m 作为文件保存在 MATLAB 路径上的文件夹中。

3 编写目标函数并创建优化问题结构，如第 3-57 页上的“查找全局或多个局部最小值”中所述。

```matlab
function f = sawtoothxy(x,y)
[t r] = cart2pol(x,y); % change to polar coordinates
h = cos(2*t - 1/2)/2 + cos(t) + 2;
g = (sin(r) - sin(2*r)/2 + sin(3*r)/3 - sin(4*r)/4 + 4) ...
.*r.^2./(r+1);
f = g.*h;
end
```

4 将sawtoothxy.m 作为文件保存在MATLAB 路径上的文件夹中。

5 在命令行中，创建问题结构：

```matlab
problem = createOptimProblem('fmincon',...
'objective',@(x)sawtoothxy(x(1),x(2)),...
'x0',[100,-50],'options',...
optimoptions(@fmincon,'Algorithm','sqp'));
```

6 创建一个以@StopAfterFive 作为输出函数的GlobalSearch 对象，并将迭代显示属性设置为'iter'。

```matlab
gs = GlobalSearch('OutputFcn',@StopAfterFive,'Display','iter');
```

7 （可选）要获得与本示例相同的答案，请设置默认随机数流。

8 运行问题。

```matlab
[x,fval] = run(gs,problem)
Num Pts Best Current Threshold Local Local
Analyzed F-count f(x) Penalty Penalty f(x) exitflag Procedure
0 200 555.5 555.5 0 Initial Point
200 1463 1.547e-15 1.547e-15 1 Stage 1 Local
GlobalSearch stopped by the output or plot function.
1 out of 2 local solver runs converged with a positive local solver exit flag.
x =
1.0e-07 *
0.0414 0.1298
fval =
1.5467e-15
```

运行提前停止，因为 GlobalSearch 找到了一个函数值小于 0.5 的点。

#### Plot Functions for GlobalSearch and MultiStart

**What Are Plot Functions?**

选项的PlotFcn字段指定优化函数在每次迭代时调用的一个或多个函数。Plot函数在算法执行时绘制各种进度度量。传递函数句柄或函数句柄的单元格数组。绘图函数的结构与输出函数的结构相同。有关此结构的更多信息，请参阅第113的“OutputFcn”。

绘图函数是专门的输出函数（参见第3-27页的“GlobalSearch和MultiStart的输出函数”）。有两个预定义的打印函数：

- @gsplotbestf 绘制最佳目标函数值。
- @gsplotfunccount 绘制函数求值的数量。

打印功能窗口具有暂停和停止按钮。默认情况下，所有打印显示在一个窗口中。
要使用全局绘图函数：

•使用第11-3页“OutputFcn”中描述的语法编写绘图函数。

•将GlobalSearch或MultiStart对象的PlotFcn属性设置为plot函数的函数句柄。通过将PlotFcn特性设置为函数句柄的单元格数组，可以使用多个打印函数。

**Details of Built-In Plot Functions**

内置的绘图函数具有让您惊讶的特性。

•@gsplotbestf可以具有不严格递减的绘图。这是因为早期值可能来自带有负退出标志（例如不可行解决方案）的本地解算器运行。具有正退出标志的后续局部解更好，即使其函数值更高。一旦局部解算器返回带有正退出标志的值，绘图将单调递减。

•@gsplotfunccount可能无法绘制函数求值的总数。这是因为GlobalSearch在最后一次调用plot函数后可以继续执行函数求值。有关更多信息，请参阅第3-35页的“全球搜索算法”。

























































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































