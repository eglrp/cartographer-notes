## cartographer-ros node
extrapolators_  外推器 
TrajectoryId  轨迹id
TrajectoryData 轨迹数据

node 发布
## 参数
- node_options_
- map_builder_bridge_
- 
## 发布
- SubmapList
- LocalTrajectoryData
- TrajectoryNodeList
- LandmarkPosesList
- ConstraintList

## 
### LocalTrajectoryData
TrajectoryId TrajectoryData

- scan_matched_point_cloud_publisher_.publish trajectory_data.local_slam_data trajectory的点云数据可能已经做过优化处理
- extrapolator.AddPose
- tf_broadcaster_.sendTransform map_to_odom