# ROS param 的使用

## 一、rosparam 命令行操作

1. 列出当前的所有参数：

   ```
   rosparam list
   ```

2. 显示某个参数的值

   ```
   rosparam get param_key
   ```

3. 设置某个参数的值

   ```
   rosparam set param_key param_value
   ```

4. 保存参数到文件

   ```
   rosparam dump file_name
   ```

5. 从文件读取参数

   ```
   rosparam load file_name
   ```

   ```
   注意这里的文件必须是YAML格式：
   
   YAML格式举例：
   name：‘ZhangSan’
   age:20
   gender:‘M’
   score:{Chinese:80, Math:90}
   score_history:[85,82,88,90]
   ```

6. 删除某个参数

   ```
   rosparam delete param_key
   ```

## 二、launch 文件载入param

1. 通过param 标签设置参数的值

   ```launch
   <launch>
   	<param name="publish_frequency" value="20.0"/>
   </launch>
   ```

2. 通过加载yaml文件来设置param：
   下面这句指令也就是执行了 load 命令来实现装载文件的命令。

   ```launch
   <launch>
   	<!-- 把关节控制的配置信息读取到参数服务器 -->
   	<rosparam file="$(find robot_sim_demo)/param/xbot-u_control.yaml" command="load"/>
   </launch>
   ```

3. 加载URDF 文件
   下面这句指令是 xacro.py 执行了 robot.xacro 之后输出的结果作为值赋给 robot_description。

   ```launch
   <launch>
   	<!-- URDF and TF support -->
     <param name="robot_description" command="$(find xacro)/xacro.py $(find robot_sim_demo)/urdf/robot.xacro" />
   </launch>
   ```

**launch 文件操作param 示例：**

```launch
<launch>
	<!--param参数配置-->
	<param name="param1" value="1" />
	<param name="param2" value="2" />
	<!--param name="table_description" command="$(find xacro)/xacro.py $(find gazebo_worlds)/objects/table.urdf.xacro" /-->
	<!--rosparam参数配置-->
	<rosparam>   
        param3: 3
        param4: 4
        param5: 5
    </rosparam>
	<!--以上写法将参数转成YAML文件加载，注意param前面必须为空格，不能用Tab，否则YAML解析错误-->
	<!--rosparam file="$(find robot_sim_demo)/config/xbot-u_control.yaml" command="load" /-->
	<node pkg="param_demo" type="param_demo" name="param_demo" output="screen" >
	</node>
</launch>
```

## 三、c++ 中操作 param

```c++
#include<ros/ros.h>

int main(int argc, char **argv){
	ros::init(argc, argv, "param_demo");
	ros::NodeHandle nh;
	int parameter1, parameter2, parameter3, parameter4, parameter5;
	
	//Get Param的三种方法
	//① ros::param::get()获取参数“param1”的value，写入到parameter1上
	bool ifget1 = ros::param::get("param1", parameter1);
	
	//② ros::NodeHandle::getParam()获取参数，与①作用相同
	bool ifget2 = nh.getParam("param2",parameter2);
	
	//③ ros::NodeHandle::param()类似于①和②
	//但如果get不到指定的param，它可以给param指定一个默认值(如33333)
        nh.param("param3", parameter3, 33333);//parameter3 = nh.param("param3", 33333); //这两种方式都可以获取参数
		
	if(ifget1)
		ROS_INFO("Get param1 = %d", parameter1);
	else
		ROS_WARN("Didn't retrieve param1");
	if(ifget2)
		ROS_INFO("Get param2 = %d", parameter2);
	else
		ROS_WARN("Didn't retrieve param2");
	if(nh.hasParam("param3"))
		ROS_INFO("Get param3 = %d", parameter3);
	else
		ROS_WARN("Didn't retrieve param3");


    //Set Param的两种方法
	//① ros::param::set()设置参数
	parameter4 = 4;
	ros::param::set("param4", parameter4);

	//② ros::NodeHandle::setParam()设置参数
	parameter5 = 5;
	nh.setParam("param5",parameter5);
	
	ROS_INFO("Param4 is set to be %d", parameter4);
	ROS_INFO("Param5 is set to be %d", parameter5);


	//Check Param的两种方法
	//① ros::NodeHandle::hasParam()
	bool ifparam5 = nh.hasParam("param5");

	//② ros::param::has()
	bool ifparam6 = ros::param::has("param6");

	if(ifparam5) 
		ROS_INFO("Param5 exists");
	else
		ROS_INFO("Param5 doesn't exist");
	if(ifparam6) 
		ROS_INFO("Param6 exists");
	else
		ROS_INFO("Param6 doesn't exist");


	//Delete Param的两种方法
	//① ros::NodeHandle::deleteParam()
	bool ifdeleted5 = nh.deleteParam("param5");

	//② ros::param::del()
	bool ifdeleted6 = ros::param::del("param6");
	

	if(ifdeleted5)
		ROS_INFO("Param5 deleted");
	else
		ROS_INFO("Param5 not deleted");
	if(ifdeleted6)
		ROS_INFO("Param6 deleted");
	else
		ROS_INFO("Param6 not deleted");


	ros::Rate rate(0.3);
	while(ros::ok()){
		int parameter = 0;
		
		ROS_INFO("=============Loop==============");
		//roscpp中尚未有ros::param::getallparams()之类的方法
		if(ros::param::get("param1", parameter))
			ROS_INFO("parameter param1 = %d", parameter);
		if(ros::param::get("param2", parameter))
			ROS_INFO("parameter param2 = %d", parameter);
		if(ros::param::get("param3", parameter))
			ROS_INFO("parameter param3 = %d", parameter);
		if(ros::param::get("param4", parameter))
			ROS_INFO("parameter param4 = %d", parameter);
		if(ros::param::get("param5", parameter))
			ROS_INFO("parameter param5 = %d", parameter);
		if(ros::param::get("param6", parameter))
			ROS_INFO("parameter param6 = %d", parameter);
		rate.sleep();
	}
}
```

按照这样的配置运行launch文件，输出结果为：

```shell
[ INFO] [1599227480.007106576]: Get param1 = 1
[ INFO] [1599227480.007183637]: Get param2 = 2
[ INFO] [1599227480.008421688]: Get param3 = 3
[ INFO] [1599227480.013664463]: Param4 is set to be 4
[ INFO] [1599227480.013728307]: Param5 is set to be 5
[ INFO] [1599227480.016448700]: Param5 exists
[ INFO] [1599227480.016485628]: Param6 doesn't exist
[ INFO] [1599227480.018160277]: Param5 deleted
[ INFO] [1599227480.018213034]: Param6 not deleted
[ INFO] [1599227480.018256838]: =============Loop==============
[ INFO] [1599227480.019019334]: parameter param1 = 1
[ INFO] [1599227480.019704553]: parameter param2 = 2
[ INFO] [1599227480.020431854]: parameter param3 = 3
[ INFO] [1599227480.021171333]: parameter param4 = 4
[ INFO] [1599227483.351733159]: =============Loop==============
[ INFO] [1599227483.354448047]: parameter param1 = 1
[ INFO] [1599227483.355546242]: parameter param2 = 2
[ INFO] [1599227483.356730625]: parameter param3 = 3
[ INFO] [1599227483.357654706]: parameter param4 = 4
.......
```

## 四、操作 param 时注意命名空间

在 c++ 操作参数的时候，我们经常会看到
`ros::NodeHandle nh`; 和 `ros::NodeHandle nh("~")`; 两种用法。
主要是全局命名空间和局部命名空间的不同。
`ros::NodeHandle nh` 是 全局命名空间，
`ros::NodeHandle nh("~")` 是 局部命名空间。

```launch
<launch>
	<!-- 全局的 test_param -->
	<param name="param1" value="1" />
	<param name="param2" value="2" />
	
	<node pkg="param_demo" type="param_demo" name="param_demo" output="screen" >
		<!-- 局部的 test_param -->
		<param name="param3" value="3" />
		<param name="param4" value="4" />
	</node>
</launch>
```


那么对应到cpp中，对于这两种param的获取方式有所不同：

```c++
#include<ros/ros.h>

int main(int argc, char **argv){
	ros::init(argc, argv, "param_demo");
	ros::NodeHandle nh_global;
	ros::NodeHandle nh_local("~");
	int parameter1, parameter2, parameter3, parameter4;
	
	parameter1 = nh_global.param("param1", 1111);
	parameter3 = nh_global.param("param_demo/param3", 1111);	//获取局部参数，需要增加节点名

	parameter2 = nh_local.param("/param2", 2222);	//获取全局参数，需要增加 "/""
	parameter4 = nh_local.param("param4", 2222);
	
	ROS_INFO("parameter param1 = %d", parameter1);
	ROS_INFO("parameter param2 = %d", parameter2);
	ROS_INFO("parameter param3 = %d", parameter3);
	ROS_INFO("parameter param4 = %d", parameter4);

	
	ros::spin();
    return 0;
}
```

按照这样的配置运行launch文件，输出结果为：

```shell
[ INFO] [1599231214.229291485]: parameter param1 = 1
[ INFO] [1599231214.229367939]: parameter param2 = 2
[ INFO] [1599231214.229398017]: parameter param3 = 3
[ INFO] [1599231214.229421101]: parameter param4 = 4
```

