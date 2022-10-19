# Ros中Remap(话题重映射)的两种使用方法

- remap在node之外的作用域是他之后的所有节点，在node中的作用域是当前节点，此外要注意想要remap的话题是这个节点要接收的还是要发布的。
- 如果是要remap一个该节点发布的**source_topic**到**target_topic**,应该是`<remap from="/source_topic" to="/target_topic" />`
- 如果是要remap一个该节点想要接收的的**target_topic**，而实际被另外一个节点发布的话题是**source_topic**,应该是`<remap from="/target_topic" to="/source_topic" />`
  **举两个例子！要注意区分两种使用情况！**

### 1、remap要发布的话题

节点中通过ros::Publisher发布了base/joint_states，head/joint_states，torso/joint_states，想要把发布出来的话题重映射到joint_states上，可以这么写：

```
<?xml version="1.0"?>
<launch>
 
    <group ns="dhrobot">
        <remap from="base/joint_states" to="joint_states" />
        <remap from="head/joint_states" to="joint_states" />
        <remap from="torso/joint_states" to="joint_states" /> 
        <node name="robot_driver" pkg="dhrobot_driver" type="robot_driver" />
    </group>
 
</launch>
```

rostopic list一下可以看到话题：

`/dhrobot/joint_states`

### ２、remap要接收的话题

节点中通过Ros::Subscriber**想要**接收/image话题，但是实际摄像头发布的话题是/kinect2/hd/image_color，所以需要这样处理：

```
<?xml version="1.0"?>
<launch>
   <node name="robot_visual" pkg="dhrobot_demo" type="robot_visual" >
    <remap from="/image" to="/kinect2/hd/image_color" />
  </node>
</launch>
```



- 最后附一下在终端中节点启动时的remap方法,还是举两个例子，一个发布一个接收：

```
rosrun joint_state_pub base/joint_states:=joint_states
rosrun robot_state_publisher /joint_states:=/base/joint_states
```

