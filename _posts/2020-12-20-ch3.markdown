---
title:  "Ch3: Processing unreal map (.umap) to pointcloud (.ply or .pcd)"
date:   2020-12-09 15:04:23
categories: [Unreal]
tags: [Unreal]
---

In this post, we endeavor to extract pointcloud (*.ply or .pcd) from  an unreal map (.umap). 


You might be familiar with the ply and pcd format for encoding pointcloud (a.k.a pcl). These formats are commonly used for robotics application.

The .umap extension  is what we get directly from Unreal Editor, once a level is created and saved  in a project.   The file consists of a lot of stuffs from `UnrealProject/Content` folder, which constructs the environment  what you are seeing from editor.  For example, the below is a `*.umap` that I am working on these days. 

![image tooltip here](/images/ch3/factory1.png){: width="80%" height="80%"}{: .center} 

![image tooltip here](/images/ch3/factory2.png){: width="80%" height="80%"}{: .center} 

The ultimate purpose of Unreal to me is a simulation environment for my motion planning research. It is very common for a  motion planning  to utilize a mapping algorithm such as [octomap](https://octomap.github.io/) and [voxblox](https://github.com/ethz-asl/voxblox).  Both of them receives the pointcloud as raw inputs. 

This is why I am writing this post tagging Unreal. We, the motion planning engineer, often needs the global or local  pointclouds of a task space. In this post, I introduce two methods for doing that by 1) constructing `*.pcd` file of `*.umap` or 2) SLAM with a drone spawned in Unreal with AirSim Plugin. 

## Method 1: Extracting mesh of umap and converting it to pcl.       

![image tooltip here](/images/ch3/process.png){: width="100%" height="100%"  description="summary of making pcd from mesh of umap"}{: .center}  
*summary of making pcd from mesh of umap*

The video version of explanation is available at my [youtube channel](https://youtu.be/cwrNCQNEEvc)

{% include youtubePlayer.html id="cwrNCQNEEvc" %}



#### Step 1 : export mesh (.obj) file of considered assets from unreal editor



 The first method is quite straight forward and accurate. We first export **mesh** of the subset of assets existing in unreal map and  convert it to pointcloud format. Here, I recommend you to select only necessary assets and group them into a subfolder at `World Outliner`  



![image tooltip here](/images/ch3/select_assets.png){: width="100%" height="100%" }{: .center}  
*Select all the descendants of relavant groups*

While selecting only the assest of interest (yellow color in the above), let us export them into `.obj` file.  

![image tooltip here](/images/ch3/export_mesh.gif){: width="100%" height="100%" }{: .center}  
*Export them into obj*

`File` >`Export Selected` and select `.obj` format not `fbx`. (In my experience, `.fbx` is not working correctly in many utilities).



> Wait, the coordinate system of the obj file ? Would it be different with Unreal ?  
>
> Answer: same location of origin, complete different xyz axis. They can not be transformed with standard SO3 rotation. 



Please refer this figure for understanding where the origin of each file is noted:

![image tooltip here](/images/ch3/coordinate.png){: width="100%" height="100%" }{: .center}  
*Coordinate system of obj and unreal*

As can be seen, you will have to do some manipulation to suit your coordinate system. 



#### Step 2 : Convert mesh file (.obj) to pcl (.ply) using *MeshLab*

We are not on the last step.  For starter, download [MeshLab](https://www.meshlab.net/). Although I ran it on Window, it might support other OS.  The application has a variety of features related to mesh-operation. But for the moment, we will focus on how to generate the pointcloud of the mesh of unreal map. 

First, let's import the the mesh file by `File>Import Mesh`. There, select the obj you created. After waiting for a few second,  you will see the following view: 

![image tooltip here](/images/ch3/meshlab1.png){: width="100%" height="100%" }{: .center}  

We now sample (or generate) on the meshes of the obj file, from *Possion-Disk* method. 

![image tooltip here](/images/ch3/meshlab2.png){: width="100%" height="100%" }{: .center}  

As I am not an expert in the PCL processing, I just tried 1E+6 points when setting the `Number of samples` .  Except that, I did not change any items. There you go, now a points layer (*Poisson-disk Samples*) appeared on the upper-right panel. You might want look around the points. You can customize the visualization of them. 

![image tooltip here](/images/ch3/meshlab3.png){: width="100%" height="100%" }{: .center}  

Finished! Just click the disk icon on the top menubar and set the output format as `.ply`.  The below is the importing result of the ply file form Matlab. I used the this [code](https://kr.mathworks.com/help/vision/ref/pcread.html) in matlab.  What you have to note here is that **scale** of the ply. The output ply from unreal map uses their own unit which is `cm` !  So you should multiply `1e-2` to all the point to be used as `m`.  

![image tooltip here](/images/ch3/matlab.png){: width="100%" height="100%" }{: .center}  



#### Step 3 : Import the pointcloud into ROS 

You might want to use the `ply` file to your ROS project. For that, I will publish the `sensor_msgs/PointCloud2` typed topic from the file. Before that, I will reduce the field of pointcloud for Rviz compatibility. The field of pointcloud which are obtained from the procedure I explained is that `x y z normal_x normal_y normal_z` where the last three are not necessary. I want a new pcd file whose field is `x y z`. Also, the transformation is applied to align x+ axis to z+ axis.   

In matalb, I did : 

```matlab
trans = affine3d([0 -1 0 0 ; 0 0 -1 0 ; 1 0 0 0 ; 0 0 0 1]);
ptCloudTransformed = pctransform(ptCloudOrig,(trans));
location = [-ptCloudTransformed.Location(:,1) ptCloudTransformed.Location(:,2) -ptCloudTransformed.Location(:,3)] *1e-2;
ptCloud = pointCloud(location); % final pcd 
pcshow(ptCloud)
```

Please note that the transformation might be adjusted for your purpose. 

Let's bring it into ROS: 

```
rosrun pcl_ros pcd_to_pointcloud your_pointcloud.pcd 0.1 _frame_id:=/map
```

Subscribing the topic gave me the following: 

![image tooltip here](/images/ch3/pcl_ros.png){: width="100%" height="100%" }

#### Step 4 (optional) : building octomap from pcd file 

```
git clone https://github.com/gaoxiang12/octomap_tutor.git
cd octomap_tutor
./build.sh
./bin/pcd2octomap your_pointcloud.pcd your_octomap.bt
```



The octomap output example is shown below: 

![image tooltip here](/images/ch3/pcl_octomap.png){: width="100%" height="100%" }

#### 

####  







## Method 2: Spawning a drone and performing SLAM.       

TODO





 

 











