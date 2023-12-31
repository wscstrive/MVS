# Recurrent MVSNet for High-resolution Multi-view Stereo Depth Inference(CVPR2019)


## :sparkles: Motivation
- 使用GRU将3D-CNN的三次方计算量转换为深度方向的序列形式的2次方

## Contribution
- 减少内存消耗，可以适应更大尺度的重建任务
- :dizzy:	为后续循环神经网络处理mvs提供了新思路

## result
 
*DTU*  
| Model | view number | Acc.(mm)↓ | Comp.(mm)↓ | Overall(mm)↓ |
| :-: | :-: | :-: | :-: | :-: |
| R-MVSNet(D=256) | 3 | 0.385 | 0.459 | 0.422 |
| R-MVSNet(D=512) | 3 | 0.383 | 0.452 | 0.417 |

*Tanks and Temples*  
| Model | Train view | Mean↑ | Family↑ | Francis↑ | Horse↑ | LightHouse↑ | M60↑ | Panther↑ | Playground↑ | Train↑ |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| R-MVSNet | 5 | 48.40 | 69.96 | 46.65 | 32.59 | 42.95 | 51.88 | 48.80 | 52.00 | 42.38 |

## Network Architecture

<img width="703" alt="image" src="https://github.com/elleryw0518/MVS/assets/101634608/7a349e37-69ea-4e08-aa4e-f0541bf4b6d3">

### Feature extract

- 与MVSNet类似，八个卷积层，并分别在第三和第六层进行下采样来减少后续任务中的计算量

> 中间省略了对cost volume construction的描写，直接对cost volume进行了处理

### Recurrent regularization

- 如大部分的RNN结构一样，作者对代价体的每个深度平面层进行序列的展开迭代计算。
重置门和更新门，

$$
\mathrm{R}(t) = \sigma_g(\mathrm{W}_r * [\mathrm{C}(t),\mathrm{C}_r(t-1)]+\mathrm{b}_r)
$$

$$
\mathrm{U}(t) = \sigma_g(\mathrm{W}_u * [\mathrm{C}(t),\mathrm{C}_r(t-1)]+\mathrm{b}_u)
$$

> 重置门决定是否要忘记过去的信息；而更新门绝对是否需要对当前状态进行更新

候选状态

$$
\mathrm{C}_u(t) = \sigma_c(\mathrm{W}_c * [\mathrm{C}(t),\mathrm{R}(t)\odot \mathrm{C}_r(t-1)]+\mathrm{b}_c)
$$

> 包含了当前时间步和前一时间步的中间状态，包含了对该时间段的隐层信息以负责更新和重置

最后的更新状态（每一深度平面的代价体）

$$
\mathrm{C}_r(t)=(1-\mathrm{U}(t) )\odot\mathrm{C}_r(t-1)+\mathrm{U}(t)\odot\mathrm{C}_u(t)    
$$

作者将每个平面层的代价体进行堆叠，对于初始状态的并分别采用三个卷积层将特征通道降为32-16-4-1，最后把所有平面层推叠在一起并使用softmax得到概率体。
> 这地方有点疑惑他是怎么边进行GRU边降维的，感觉会影响信息的完整性

### Loss Function
作者采用了交叉熵而不是回归方法，因为他在对深度平面切分时采用的逆深度值，因此深度间距无法保证平等
> 这里本文都没有提及关于逆深度的一个方法和过程，有点懵

$$
Loss=\sum_{\mathbf{p} }(\sum_{i=1}^{D}-\mathrm{P}(i,\mathrm{p})\cdot log\mathrm{Q}(i,\mathrm{p})) 
$$

> 概率体进行了winner-take-all处理，Q是GT的1或0的mask值

### depth map refinement

为了缓解楼梯效应，作者增强了不同视图间的photo-consistency和平滑操作

<img width="1092" alt="image" src="https://github.com/elleryw0518/MVS/assets/101634608/09b004dd-2043-4ce4-b548-3db52f88027c">

其中I(i->1)是通过homography warp得到的计算的两视图之间的差异，S是双边平方深度差异

> 双边平方深度差异这步骤作者没有说怎么操作，目前了解到可能是像素点距离和像素值之间的差异这两个值的平方差异。这个优化操作无法感觉到她的有效性。


## Experiments

train input:n,h,w,d=3,640,512,192

## Ablation study

validation input:h,w,d=640,512,256

- 2D CNNs + 3D CNNs
- 2D CNNs + GRU
- 2D CNNs + Spatial
- 2D CNNs + WTA
- ZNCC + WTA


> 整体来说是个很不错的文章，通过对代价体的切割避免了高内存消耗的3D-CNN操作，通过切片方法为后续方法提供了很好的切入点。但以牺牲时间换内存的方法并没有实质去解决问题并没有后续拖带问题，3D变2D是否对于像素的准确度大打折扣，无法对空间信息进行感知？此外，作者里面有很多细节都是一带而过，并没有详细解释。
