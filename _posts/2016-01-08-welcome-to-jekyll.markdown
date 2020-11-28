---
title:  "ROS subscribing poses of objects in Unreal world using Airsim"
date:   2020-11-27 15:04:23
categories: [Airsim]
tags: [Airsim]
---
In the previous tutorial, we looked into how to generate a custom unreal environment and spawn an character which moves along a spline curve. 

Now we move on to associate the ROS with Unreal environment we created. For that, we use [airsim_ros_pkgs](https://github.com/icsl-Jeon/airsim_ros_pkgs) which is custom forked version from original package to include object pose extraction. 

### Basic dependenices 

* mavros_msgs in ROS 

```
sudo apt-get install ros-melodic-mavros-msgs  
```

* gcc 8.0 

```
 sudo apt install gcc-8 g++-8
```

### Building  `airsim_ros_pkgs`

```
cd ~/catkin_ws/src/AirSim/ros/src
rm -r airsim_ros_pkgs
git clone https://github.com/icsl-Jeon/airsim_ros_pkgs.git
cd ~/catkin_ws
catkin build airsim_tutorial_pkgs -DCMAKE_C_COMPILER=gcc-8 -DCMAKE_CXX_COMPILER=g++-8
```







We now proceed to more airsim 


{% include youtubePlayer.html id="5YWw47K3w2Y" %}

As you might guess, the pose of the female actor, the elephant and the horse is being fed into the 
ROS node which plans the movement of the drone.   



You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve --watch`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

``` ruby
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
```

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll’s dedicated Help repository][jekyll-help].

[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
