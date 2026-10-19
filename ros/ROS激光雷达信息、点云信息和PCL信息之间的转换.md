# ROS激光雷达信息、点云信息和PCL信息之间的转换

## 一、消息类型

```
sensor_msgs::LaserScan                      // ROS激光雷达信息
sensor_msgs::PointCloud                  // ROS点云信息
sensor_msgs::PointCloud2                  // ROS点云信息
pcl::PointCloud<pcl::PointXYZ>         // PCL点云信息
```

## 二、sensor_msgs::LaserScan 与sensor_msgs::PointCloud之间的转换

```
 sensor_msgs::LaserScan::ConstPtr& scan_msg;
 laser_geometry::LaserProjection projector_;
 sensor_msgs::PointCloud laser_point_cloud;
 // LaserScan转换到pointcloud2 类型
 projector_.projectLaser(*scan_msg,laser_point_cloud);
```

##  三、 sensor_msgs::PointCloud2与 pcl::PointCloud<pcl::PointXYZ>之间的转换

```
 sensor_msgs::PointCloud2 laser_point_cloud;
 pcl::PointCloud<pcl::PointXYZ> pclCloud;
 pcl::fromROSMsg(laser_point_cloud,pclCloud); // ROS转pcl
 pcl::toROSMsg(pclCloud,laser_point_cloud);   // pcl转ROS
```

