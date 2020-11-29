---
title:  "Ch1: Building custom dynamic environments"
date:   2016-01-08 15:04:23
categories: [Airsim]
tags: [Airsim]
---

In this tutorial, I write the basic process to perform the simulation such as the below simualtion: 

{% include youtubePlayer.html id="5YWw47K3w2Y" %}


As you might guess, the poses of the female actor, the elephant and the horse are being fed into the 
ROS node which plans the movement of the drone. From this posting, we will walk through to learn how to conduct such simulation using Unreal, Airsim and ROS. The tested computer is based on Ubuntu 18.04. 

## 1. Making a unreal project with airsim plugin 

### Make new custom project

Ok, we need to build an unreal environmet for your own purpose. Frist, we run Unreal Editor (UE) in `path/to/unreal/Engine/Binaries/Linux/UE4Editor`. This will exist once you have built the unreal engine in your linux. For efficient running, I added the following line in `~/.bashrc`.

```bash
alias unreal='~/UnrealEngine/Engine/Binaries/Linux/UE4Editor'
```

Now. turn it on by `$ unreal` in your terminal. 


![image tooltip here](/_posts/ch1/s1.png)


### Try Airsim game mode and check things are OK  

Now,  

## 2. Creating a dynamic object (or multiple) moving along spline

### Importing assets 

