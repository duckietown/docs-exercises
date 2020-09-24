# Exercise: Control your Duckiebot {#exercise-control type=slides status=ready }


Goal: Apply a control strategy to make a Duckiebot drive in a lane.

Requires: A calibrated (camera and odometry) and charged Duckiebot in configuration `DB18`, a laptop and an internet connection.

Recommended: Basic knowledge of Algebra and Control Theory.

Results: Your own implementation of a controller.



## Preliminaries: Getting the math right

A simple model for a Duckiebot could be:

<!-- <figure class="stretch">
  <img style='float: right;width:6em'  src="model.png"/>
</figure> -->

<img src="model.png" style='float: center' width="200" height="200" />



State:

$$
x = [ d,\phi ]^T
$$

Input:

$$
u=\omega
$$

Dynamics:

$$
\begin{bmatrix} \dot{d}=v_0\cdot \sin(\phi)\\ \dot{\phi}=\omega=u \end{bmatrix}
$$

The LTI system:
$$
\dot{x}=\begin{bmatrix}
 0& v_0 \\
0 & 0
\end{bmatrix}x+ \begin{bmatrix}
  0\\
  1
\end{bmatrix} u,\quad y=\begin{bmatrix}
1 & 0 \\
0 & 1
\end{bmatrix}x
$$

## Derivation of a PI controller

Augment states with integrals:

$$
z=[d,\phi,d_I,\phi_I]^T
$$

Augmented system:

$$
\dot{z}=\begin{bmatrix}
 A & 0_{2\times2} \\
I_{2\times2} & 0_{2\times2}
\end{bmatrix}z+ \begin{bmatrix}
  B\\
  0_{2\times1}
\end{bmatrix} u,\quad y_n=I_{4\times4}\cdot z
$$

We close the loop with:

$$
u = -K\cdot y_n \quad where \quad K= [k_{P_d},k_{P_\phi},k_{I_d},k_{I_\phi}]
$$


## Our beloved pipeline


<!-- <figure class="stretch">
  <img style='width:30em'  src="loop.png"/>
</figure> -->

<img src="loop.png" class="center" height="250" />


For this exercise, we will use a different controller rather than the standard one used in the `lane_controller_node`.


## Controller template

<figure class="stretch">
  <img style='width:30em'  src="controller.png"/>
</figure>



## Get the Code

Pull the code from
[https://github.com/![your docker username]/Software/tree/sysid-into-master-merge](https://github.com/raabuchanan/Software/tree/sysid-into-master-merge)

Note: Make sure you are on the `sysid-into-master-merge` branch.

Make sure your DOCKER_HOST is set to nothing:
```markduck
laptop $ export DOCKER_HOST=
```

Then login in to your Dockerhub account with
```markduck
laptop $ docker login
```


## Make your own Docker Image

Edit your controller in `~/Software/exercises/controls_exercise/controller.py`

Navigate to duckietown root (~/Software) on your computer, then

```markduck
laptop $ docker build -t ![your docker username]/rpi-duckiebot-![your image name]:![your tag] .
```

Don't forget the period at the end. Push the image to your Docker Hub account:
```markduck
laptop $ docker push ![your docker username]/rpi-duckiebot-![your image name]:![your tag]
```


## How to launch your own controller

First you will need to run the docker image created for this exercise.

Note: Make sure your ros-picam and joystick containers are off.

```markduck
laptop $ docker -H ![hostname].local run -it --net host --privileged -v /data:/data --name lane_follower_exercise ![your docker username]/rpi-duckiebot-![your image name]:![your tag] /bin/bash
```
Once this is running, launch the controller inside the container with:

```markduck
laptop-container $ roslaunch duckietown_demos lane_following_exercise.launch /exercise/sampling_factor:=1.0 /exercise/time_delay:=0.0 /exercise/omega_sat:=100
```

***On your laptop***
Run `dts keyboard_control`, enter autonomous mode pressing <kbd>a</kbd>, stop it with <kbd>s</kbd>.

## How to do small modifications

First, ssh into the container with:
```markduck
laptop $ docker exec -it lane_follower_exercise /bin/bash
```

Then use a text editor to edit your file, i.e.

```markduck
container $ vim ![your/path]/[your file]
```


## References


This exercise is adapted from the [Lane Following Demo](https://docs.duckietown.org/DT18/opmanual_duckiebot/out/demo_lane_following.html) so look there if something in this exercise is unclear. Deliverables are TBD but most likely your controller.py
