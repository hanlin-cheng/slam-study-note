# Eigen几何模块的引入 

## 1、在CMakeLists文件中添加 

```
include_directories("/usr/include/eigen3") 
```

## 2、在main.cpp中添加 

```
#include <cmath>             //c++的标准数学函数库，不是Eigen中的，但一般要用到 
#include <Eigen/Core> 
#include <Eigen/Geometry> 
using namespace Eigen; 
```

## 二、Eigen几何模块的使用 

### 1、定义 

#### 1）三维空间坐标定义 

```
Vector3d v(1,0,0); 
//使用三维纵向量Vector3d定义 
```

#### 2）旋转矩阵定义 

```
Matrix3d rotation_matrix = Matrix3d::Identity(); 
//使用Matrix3d或者Matrix3f定义，Matrix3d::Identity()是三阶单位阵
```

#### 3）旋转向量定义 

```
AngleAxisd rotation_vector(M_PI / 4,Vector3d(0,0,1)); 
//沿Z轴旋转45度 
//使用AngleAxisd定义，AngleAxisd（旋转角度，旋转轴单位向量Vector3d） 
//AngleAxisd的底层不直接是Matrix，但由于重载了运算符，因此仍可以当做矩阵运算
```

注意：AngleAxisd不能用cout输出，以该定义为例，要用rotation_vector.matrix()转换成矩阵形式才能输出，但转换后不再是旋转向量形式，而是3x3的旋转矩阵形式。 

**注意：使用AngleAxisd定义旋转向量时候，C++中M_PI的精度是二十位，并不是真正数学意义上的π \piπ,因此在计算过程中会引入误差。例子如下：** 

```
AngleAxisd angle_vector = AngleAxisd(M_PI/2,Vector3d(0, 0, 1)); 
cout << angle_vector.matrix() << endl; 
```

数学理论上所出来的angle_vector对应的矩阵应该为： 

[0 -1 0] 

[1 0 0] 

[0 0 1] 

但程序输出为： 

6.12323e-17 -1 0 

1 6.12323e-17 0 

0 0 1 

将程序中精度由d改成f后，程序输出为： 

-4.37114e-08 -1 0 

1 -4.37114e-08 0 

0 0 1 

其中，本应该矩阵中应该为0的元素出现了一个极小值，而且该极小值会随着精度的变化而变化，不过目前发现的影响只有对0元素的影响，这个极小值可以看成0. 

#### 4）欧拉角定义 

```
Vector3d eulerangles(0,0,0); 
//使用三维纵向量Vector3d定义
```

#### 5）欧氏变换矩阵定义 

```
Isometry3d T = Isometry3d::Identity(); 
//使用Isometry3d定义，虽然叫3d，但实际上是4x4矩阵 
//Isometry3d::Identity()是将欧氏矩阵初始化为单位矩阵 
Isometry3d T(q); 
//使用四元数初始化欧氏变换矩阵 
```

注意：欧氏变换矩阵不能直接用cout输出，以该定义为例，要使用 

T.matrix()转换为矩阵形式输出，转换后仍为4x4的欧氏变换矩阵 

#### 6）四元数定义 

```
Quaterniond q(Vector4d(1,2,3,4)); 
//使用Vector4d定义，定义格式为（x,y,z,w) 
Quaterniond q((1,2,3,4)); 
//直接赋值，定义格式为(w,x,y,z) 
```

注意：该定义方法下，Vector中四个元素对应关系为（x,y,z,w)，即最后以为才是四元数的实部 

注意：四元数不能直接用cout输出，以该定义为例，输出格式为 

```
cout << q.coeffs() << endl;  //输出格式为（x,y,z,w)
```

注意：四元数在使用前要进行归一化，这样由四元数转化出来的旋转矩阵才满足正交性。归一化方法为： 

```
q.normalize(); 
```

### 2、各种变换表达形式之间的转换 

#### 1）旋转向量与旋转矩阵之间的变换 

显示转换 

```
rotation_matrix = rotation_vector.toRotationMatrix(); 
或 
rotation_matrix = rotation_vector.matrix(); 
```

注意：此处也存在M_PI引入的误差问题，详讲1-3）旋转向量定义。 

隐式转换（直接赋值） 

```
rotation_matrix = rotation_vector; 
rotation_vector = rotation_matrix; 
```

#### 2）旋转矩阵与欧拉角的变换 

```
eulerangles = rotation_matrix.eulerAngles(2,1,0); 
//参数（2.1.0）表示这是ZYX顺序，即roll pitch yaw 
```

#### 3）欧氏变换矩阵按照旋转向量或旋转矩阵旋转 

```
T.rotate(rotation_matrix);   //在原来T的基础上按照旋转矩阵进行旋转，然后返回T。就是讲旋转矩阵的变换整合到T中 
T.rotate(rotation_vector);   //按照旋转向量进行旋转
```

#### 4）欧氏变换矩阵平移 

```
T.pretranslate(Vector3d(1,3,4));   //按照向量Vector3d(1,3,4)平移
```

#### 5）四元数与旋转向量旋转矩阵之间的转换 

显式转换 

```
 q = Quaterniond(rotation_vector)
 q = Quaterniond(rotation_matrix)
```

隐式转换 

```
rotation_vector = q
rotation_matrix = q
q = rotation_matrix
q = rotation_vector
```

### 3、坐标变换 

```
Vector3d v(1,0,0); //定义初始坐标v 
Vector3d v_rotated； //定义坐标转换后坐标v_rotated
```

#### 1）使用旋转矩阵 

```
v_rotated = rotation_matrix * v 
```

#### 2）使用旋转向量 

```
v_rotated = rotation_vector * v 
```

#### 3）使用变换矩阵 

```
Vector3d v_tranformed = T * v
```

#### 4）使用四元数 

```
v_rotated = q*v
```

