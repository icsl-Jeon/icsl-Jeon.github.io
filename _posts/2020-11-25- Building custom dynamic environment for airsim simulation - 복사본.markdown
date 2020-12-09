---
title:  "Ch1: Building custom dynamic environments"
date:   2016-01-08 15:04:23
categories: [Airsim]
tags: [Airsim]
---

In this tutorial, I write the basic process to perform the simulation such as the below: 

{% include youtubePlayer.html id="5YWw47K3w2Y" %}

As you might guess, the poses of the female actor, the elephant and the horse are being fed into the 
ROS node which plans the movement of the drone. From this posting, we will walk through to learn how to conduct such simulation using Unreal, Airsim and ROS. The tested computer is based on Ubuntu 18.04. 

The supplementary video for this posting can be found below:  

{% include youtubePlayer.html id="izSMF3auLsY" %}





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

**Note**: If you are interested in the asset of the robot, go to `Content/ThirdPersonBP/Blueprints`, and navigate the file. 


![image tooltip here](/images/ch1/s4.png)

Click play button and move around the environment  like you do in Counter strike. Congraturation! your first custom unreal project. 

![image tooltip here](/images/ch1/s5.gif)


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

Were you able to find any difference? Might not. To find out how the plugin is applied, open `World Settings` window as below. Then you end up finding the new game mode `AirSimGameMode` has been already created. 

![image tooltip here](/images/ch1/s6.png)

If  you hit play button again, now the project will give you a pop up asking whether you will do car simulation like this: 

![image tooltip here](/images/ch1/s7.png)

Click. Then a disaster happens, where the car (SUV) is spawned on top of the robot. For a normal operation, we can remove the robot and will create a spawn location by adding `Player Start` actor.  

![image tooltip here](/images/ch1/s8.png)

If you play again, now car is spawned and you can drive it with keyboard. Have fun before we move onto the next section. 

![image tooltip here](/images/ch1/s9.png)





## 2. Creating a dynamic object (or multiple) moving along spline

### Importing assets  

![image tooltip here](/images/ch1/s10.gif)

We will be filling the world with a character which moves along a spline curve you create. For that, let us prepare two assets: `SplineJBS.uasset` [Download](https://drive.google.com/file/d/15kE-tTFQWk5zLIM3MB1FAX5Z2oT-9FkZ/view?usp=sharing) and `TargetObject.uasset` [Download](https://drive.google.com/file/d/1dSYwccKR_sq5WnCJZMUTsUkeXt6_ZI2_/view?usp=sharing). Put them in `~/Documents/Unreal\ Projects/AirsimBasic/Content`. You should see them in the content browser:

![image tooltip here](/images/ch1/s11.png) 

Now, summon them into unreal with <u>defined order</u>: `SplineJBS` first, and then `TargetBoject`.  

After spawning `SplineJBS` asset, you should be able to draw a spline by clicking the a know, while holding `Ctrl` + `Alt`. 

![image tooltip here](/images/ch1/s13.gif) 

Next, time to call the character which moves along the spline by drag and drop `TargetObject`in to the world. What is to be done first is to make human appearance from skeletal mesh and  behavior from animation blueprint. 

**Note**:  In this action, you may customize the character from downloading different assets from [unreal market place](https://www.unrealengine.com/marketplace/ko/store). (But you know what? You have to go to WIndow and return to linux to import it.. For the moment, we just use basic `Mannequin` avatar included in this template. 

Refer the figures to adjust the properties in Details window. 

![image tooltip here](/images/ch1/s14.png) 

Now, let us input the spline object into our character.  In the below, we can set the horizon [s] for the object to traverse the entire spline. 

![image tooltip here](/images/ch1/s15.png){: width="350" height="300"}

Want to adjust the walking speed of the character? Simple. See `Max Walk Speed` in the below. 




 ![image tooltip here](/images/ch1/s16.png){: width="400" height="600"}{: .center} 

An explanation of the working principle of this module: it is like a controlling a robot where the goal position is being fed into the robot along the spline, and the robot tracks the position by its maximum walking speed. Additional stuff we need to prepare is a navigation volume inside of which the character is controlled to track an instant goal location (maybe something like A* is working behind the scene). Spawn the `NavMeshBoundVolume` and <u>make sure that the volume encloses the character and all the spline</u>.

![image tooltip here](/images/ch1/s17.png)

All setting is done. We now have to decide how to activate the spline-moving for the character. A simple way is to activate it once the game play starts.  The governing principle for the sequences to be done after game starts  is  *level blueprint* which can be accessed with the below menu. 

  ![image tooltip here](/images/ch1/s18.png)



### Triggering the spline-moving of the robot?



What we should do is playing with block diagram consisting of the nodes and links. This is called *editing BLUEPRINT.*  If you want to be an advanced user of Airsim who can even handle what's going on in unreal world, highly recommend you to be familiar with this guy. 

  ![image tooltip here](/images/ch1/s19.gif)

However, in many cases, it is better to having a control over when to trigger the character's movement and bring the robot to its original location by keyboard. The youtube link above explains this in the later part. 

In either mode, hit the play button, and then you can watch the character moves along the spline!  

From the next chapter, we will get the ENU pose of the moving robot and publish it in ROS topic.   

If you have any question, contact me with the e-mail. 

   





