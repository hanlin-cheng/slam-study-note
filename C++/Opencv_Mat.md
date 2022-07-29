# Opencv Mat



## **Mat_\<\>()**

```
Mat k = (Mat_<double>(3,3) << 1, 2, 3, 4, 5, 6, 7, 8, 9);
```



## **Mat.at\<\>()**

```
k.at<double>(0,0) = 1;
```



## **Mat::eye**

返回一个恒等指定大小和类型矩阵。

```
C++: static MatExpr Mat::eye(int rows, int cols, inttype)

C++: static MatExpr Mat::eye(Size size, int type)
```

**参数**

**rows** –的行数。

**cols**– 的列数。

**size** –替代矩阵大小规格Size(cols, rows)的方法。

**type** – 创建的矩阵的类型。

该方法返回 Matlab 式恒等矩阵初始值设定项，类似 Mat::zeros()和 Mat::ones()，你可以用缩放操作高效地创建缩放的恒等矩阵：

/ / 创建4 x 4 的对角矩阵并在对角线上以0.1的比率缩小。

```
Mat A = Mat::eye(4, 4, CV_32F) * 0.1;
```



## **Mat::create**

分配新的阵列数据 （如果需要）。

```
C++: void Mat::create(int rows, int cols, int type)

C++: void Mat::create(Size size, int type)

C++: void Mat::create(int ndims, const int\* sizes, inttype)
```

**参数**

**ndims** – 新数组的维数。

**rows** –新的行数。

**cols** – 新的列数。

**size** – 替代新矩阵大小规格：Size(cols, rows)。

**sizes** – 指定一个新的阵列形状的整数数组。

**type** – 新矩阵的类型。

这是关键的Mat方法之一。大多数新样式 OpenCV 函数和产生阵列的方法每个输出数组都调用这个方法。此方法使用如下算法：

1.如果当前数组形状和类型匹配新的请立即返回。否则，通过调用 Mat::release()取消引用以前的数据。

2.初始化新矩阵头。

3.分配新的 total()\*elemSize() 个字节的数据空间。

4.分配新的关联数据的引用计数并将其设置为 1。
