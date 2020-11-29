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

## 1. Making an unreal project with airsim plugin 

### Make new custom project

Ok, we need to build an unreal environmet for your own purpose. Frist, we run Unreal Editor (UE) in `path/to/unreal/Engine/Binaries/Linux/UE4Editor`. This should  exist once you have built the unreal engine in your linux. For efficient running, I added the following line in `~/.bashrc`.

```bash
alias unreal='~/UnrealEngine/Engine/Binaries/Linux/UE4Editor'
```

Now. turn it on by `$ unreal` in your terminal. We will create a environment based on Third person template as it enables us to easily hang around the environment as any FPS games do.  

So, select ThirdPerson template when new project pops up. 

![image tooltip here](/images/ch1/s2.png)

We  name the project as AirsimBasic.

![image tooltip here](/images/ch1/s1.png)

Then, you wil ses the following screen in your viewport where a robot (or mock human?) is breathing. That's an character blueprint you operate with keyboard and mouse. 

If you didn't speicify anything, the project folder should be created in `Documents/Unreal Projects/ArisimBasic`. 

If you are interested in the asset of the robot, go to `Content/ThirdPersonBP/Blueprints`, and navigate the file. 


![image tooltip here](/images/ch1/s4.png)

Click play button and move around the environment with like you do in Counter strike. Congraturation! your first custom unreal project. 

![image tooltip here](/images/ch1/s5.png)


### Try Airsim game mode and check things are OK  

We are not here to make and play the game. We now focus on airsim stuff. To make your project an airsim-aware project,  you might want to do the two things: 

1. Include `AirSim` plugin in your project folder (`Documents/Unreal Projects/ArisimBasic`).

   ```
   cp -r path/to/Arisim/Unreal/Plugins ~/Documents/Unreal\ Projects/AirsimBasic 
   ```

2. Modify  `AirsimBasic.uproject`  to include Arisim plugin  

   ```
   vim ~/Documents/Unreal\ Projects/AirsimBasic/AirsimBasic.uproject 
   ```

   Rewrite it as  below 

   ```
   {
           "FileVersion": 3,
           "EngineAssociation": "{0004502F-08D8-842D-F179-79C4CF58075C}",
           "Category": "",
           "Description": "",
           "Plugins": [
                   {
                           "Name": "AirSim",
                           "Enabled": true
                   }
           ]
   }
   ```

Are you ready? Let's re run the AirsimBasic project. 

Were you able to find any difference? Might not. To find out how the plugin is applied, open `World Outliner` window as below. Then you end up finding the new game mode `AirSimGameMode` has been already created. 

![image tooltip here](/images/ch1/s6.png)

If  you hit play button again, now the project will give you a pop up asking whether you will do car simulation like this: 

![image tooltip here](/images/ch1/s7.png)

Click. Then a disaster happens, where the car (SUV) is spawned on top of the robot. For a normal operation, we can remove the robot and will create a spawn location by adding `Player Start` actor.  

![image tooltip here](/images/ch1/s8.png)

If you play again, now car is spawned and you can drive it with keyboard. Have fun before we move onto the next section. 

![image tooltip here](/images/ch1/s9.png)





## 2. Creating a dynamic object (or multiple) moving along spline

### Importing assets 

