# EigenGeometry 

```c++
#include<iostream> 
#include<cmath> 

using namespace std; 

#include<Eigen/Core> 
#include<Eigen/Geometry> 

using namespace Eigen; 

//本程序演示了Eigen几何模块的使用方法 
int main(intargc,char**argv){ 
    //Eigen/Geometry模块提供了各种旋转和平移的表示 
    //3D旋转矩阵直接使用Matrix3d或Matrix3f 
    Matrix3drotation_matrix=Matrix3d::Identity();//单位矩阵 

    //旋转向量使用AngleAxis,它底层不直接是Matrix，但运算可以当作矩阵（因为重载了运算符） 
    AngleAxisdrotation_vector(M_PI/4,Vector3d(0,0,1));//沿Z轴旋转45度 

    cout.precision(3); 
    cout<<"rotationmatrix=\n"<<rotation_vector.matrix()<<endl;//用matrix()转换成矩阵 

    //也可以直接赋值 
    rotation_matrix=rotation_vector.toRotationMatrix(); 

    //用AngleAxis可以进行坐标变换 
    Vector3dv(1,0,0); 
    Vector3dv_rotated=rotation_vector*v; 
    cout<<"(1,0,0)afterrotation(byangleaxis)="<<v_rotated.transpose()<<endl; 

    //或者用旋转矩阵 
    v_rotated=rotation_matrix*v; 
    cout<<"(1,0,0)afterrotation(bymatrix)="<<v_rotated.transpose()<<endl; 

    //欧拉角:可以将旋转矩阵直接转换成欧拉角 
    Vector3deuler_angles=rotation_matrix.eulerAngles(2,1,0);//ZYX顺序，即yaw-pitch-roll顺序 
    cout<<"yawpitchroll="<<euler_angles.transpose()<<endl; 
    
    //欧氏变换矩阵使用Eigen::Isometry 
    Isometry3dT=Isometry3d::Identity();//虽然称为3d，实质上是4＊4的矩阵 
    T.rotate(rotation_vector);//按照rotation_vector进行旋转 
    T.pretranslate(Vector3d(1,3,4));//把平移向量设成(1,3,4) 
    cout<<"Transformmatrix=\n"<<T.matrix()<<endl; 

    //用变换矩阵进行坐标变换 
    Vector3dv_transformed=T*v;//相当于R*v+t 
    cout<<"vtranformed="<<v_transformed.transpose()<<endl; 

    //对于仿射和射影变换，使用Eigen::Affine3d和Eigen::Projective3d即可，略 
    ##都是调用了有参构造函数 
    //四元数 
    //可以直接把AngleAxis赋值给四元数，反之亦然 
    Quaterniondq=Quaterniond(rotation_vector); 
    cout<<"quaternionfromrotationvector="<<q.coeffs().transpose()<<endl;//请注意coeffs的顺序是(x,y,z,w),w为实部，前三者为虚部 

    //也可以把旋转矩阵赋给它 
    q=Quaterniond(rotation_matrix); 
    cout<<"quaternionfromrotationmatrix="<<q.coeffs().transpose()<<endl; 

    //使用四元数旋转一个向量，使用重载的乘法即可 
    v_rotated=q*v;//注意数学上是qvq^{-1} 
    cout<<"(1,0,0)afterrotation="<<v_rotated.transpose()<<endl; 

    //用常规向量乘法表示，则应该如下计算 
    cout<<"shouldbeequalto"<<(q*Quaterniond(0,1,0,0)*q.inverse()).coeffs().transpose()<<endl; 

    return0; 

} 
```

