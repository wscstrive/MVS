# :balloon: Rigid body transformation
<img src="https://github.com/elleryw0518/MVS/assets/101634608/6acf03ea-423c-4f51-acd6-a9601b1736a3" alt="rigib_body" width="400px">  

Assume that the coordinates of a point P in the Euclidean space in the two coordinate systems are P1 and P2 respectively:  

$$
p_2 = R p_1 + t,\ p_1=(x, y, z),\ p_2=(x', y', z') 
$$  

> where R is rotation matrix, t is translation matrix.

i.e.,:  

$$
\begin{bmatrix}
x' \\
y' \\
z'
\end{bmatrix} =
\begin{bmatrix}
r_{11} & r_{12} & r_{13} \\
r_{21} & r_{22} & r_{23} \\
r_{31} & r_{32} & r_{33}
\end{bmatrix}
\begin{bmatrix}
x \\
y \\
z
\end{bmatrix} +
\begin{bmatrix}
t_1 \\
t_2 \\
t_3
\end{bmatrix}
$$  
> Notice: The position and orientation of rigid objects can be transformed while their shape and size remain unchanged.  
Degrees of freedom: 6 (3 rotations and 3 translations)  
Rotation matrix: _3*3_? Rotation around an axis affects other axes, and the coordinates are related to each other  
Translation matrix: _3*1_? coordinates are independent of each other  
## Translation
 
![translate](https://github.com/elleryw0518/MVS/assets/101634608/44ac9cb5-1070-43f9-8dca-70c60ea652ef)

## Rotation

- 2D Rotation operation

![2drotate](https://github.com/elleryw0518/MVS/assets/101634608/019ff453-eb27-43b7-a2cf-aa6f14c3fb75)  

- 3D Rotation operation

![3drotate](https://github.com/elleryw0518/MVS/assets/101634608/74fd03ed-31b2-4fd2-b34e-6b531fe763d4)  



# :balloon: Coordinate systems
- World coordinate system, Camera coordinate system, Image coordinate system, and Pixel coordinate system.
## World coordinate system -> Camera coordinate system
- Rigid body: just change direction and position, remain size and shape

<img src="https://github.com/elleryw0518/MVS/assets/101634608/9737703f-520c-4dcd-9366-4e235df499ae" alt="world- camera" width="300px">  

The world coordinate system is a special coordinate system that was introduced because of the camera to determine the position of each thing. So, we only need to align the camera coordinate system with the world coordinate system.  

$$
\begin{bmatrix}
X_c \\
Y_c \\
Z_c
\end{bmatrix}=
R
\begin{bmatrix}
X_w \\
Y_w \\
Z_w
\end{bmatrix}+
T \Longrightarrow 
\begin{bmatrix}
X_c \\
Y_c \\
Z_c \\
1
\end{bmatrix}=
\begin{bmatrix}
R & T \\
0^T & 1 \\
\end{bmatrix}
\begin{bmatrix}
X_w \\
Y_w \\
Z_w \\
1
\end{bmatrix}
$$  

## Camera coordinate system -> Image coordinate system
- perspective projection: change object position and shape

![camera- image](https://github.com/elleryw0518/MVS/assets/101634608/aad12fc9-7ee8-49e1-8b15-03de9f41a192)

> where f is focal length.  

## Image coordinate system -> Pixel coordinate system

<img src="https://github.com/elleryw0518/MVS/assets/101634608/7c5817db-7ce3-46c9-a776-f03773c7fef9" alt="image- pixel" width="300px">  


- :warning: These two coordinate systems are easily confused  
- The unit of the Image coordinate system is mm, which is a physical unit, while the unit of the Pixel coordinate system is pixel. We usually describe a pixel in terms of rows and columns. So the conversion between the two is as follows: where dx and dy represent how many mm each column and each row represent, that is, 1 (pixel) = dx (mm).  

$$
\left\{
\begin{matrix}
u = \frac{x}{dx} + u_0 
\\
v = \frac{y}{dy} + v_0
\end{matrix}
\right.
$$

$$
\begin{bmatrix}
u \\
v \\
1
\end{bmatrix} = 
\begin{bmatrix}
\frac{1}{dx}&0&u_0 \\
0&\frac{1}{dy}&v_0 \\
0&0&1
\end{bmatrix} 
\begin{bmatrix}
x \\
y \\
1
\end{bmatrix}
$$
## World coordinate system -> Camera coordinate system

$$
Z_w
\begin{bmatrix}
u \\
v \\
1
\end{bmatrix} =
\begin{bmatrix}
\frac{1}{dx} & 0 & u_0 \\
0 & \frac{1}{dx} & v_0 \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
f & 0 & 0 & 0\\ 
0 & f & 0 & 0 \\
0 & 0 & 1 & 0
\end{bmatrix}
\begin{bmatrix}
R & T \\
0^T & 1
\end{bmatrix}
\begin{bmatrix}
X_w \\
Y_w \\
Z_w \\
1
\end{bmatrix} =
\begin{bmatrix}
f_x & 0 & c_x & 0 \\
0 & f_y & c_y & 0 \\
0 & 0 & 1 & 0 
\end{bmatrix} 
\begin{bmatrix}
R_{3\times 3} & T_{3\times 1} \\
0^T_{1\times 3} & 1
\end{bmatrix}
\begin{bmatrix}
X_w \\
Y_w \\
Z_w \\
1
\end{bmatrix}
=\mathbf{M_1}\cdot \mathbf{M_2}\cdot \mathbf{X}_w
$$

> where fx,fx is the normalized focal length, (f_x, f_y, c_x, c_y) is only related to the internal transformation of the camera and  M_1 is called the internal camera parameter. (R, T) are determined by the camera's orientation relative to the world coordinate system, M_2 is called the camera's external parameters.


# Reference
1. [深入理解旋转矩阵和平移向量的本质](https://zhuanlan.zhihu.com/p/141597984)
2. [【相机标定】四个坐标系之间的变换关系](https://cloud.tencent.com/developer/article/1820935)
