# Dockerfile to build and run the utbm-dataset workspace in ROS melodic
This Dockerfile is based on the tutorial in [Amirdawesh](https://amirdarwesh.com/posts/2019/09/13/ROS-Docker-Vscode).
The source-folders are obtained and installed according to the tutorial [utbm-robocar-dataset](https://github.com/epan-utbm/utbm_robocar_dataset/tree/baselines/launch).
The package pyenv is installed with the tutorial [Sunder](https://gist.github.com/jprjr/7667947?permalink_comment_id=3684823#gistcomment-3684823).
The installation of the ROS-workspace folders is carried out according to [ruffsl](https://answers.ros.org/question/312577/catkin_make-command-not-found-executing-by-a-dockerfile/?answer=312728#post-id-312728).

1. Move the repository-folder (utbm_docker_ws) to your home-directory (/home/$USER/).
	*1.1 (optional) Modify the wget line in /utbm_docker_ws/Dockerfile to fetch your desired .bag-file from the utbm-dataset. 
2. Install vs-code with CMake, CMake Tools, Docker, Dev Containers extensions to build the Dockerimage.
3. Open the folder utbm_docker_ws from within vs-code and wait for a prompt to "reload" the container [Amirdawesh](https://amirdarwesh.com/posts/2019/09/13/ROS-Docker-Vscode/). Click on reload.
4. Build the container. Be aware, that when the setup is about to finish, the container "hangs" itself, because wget fetches the .bag-file.
5. Workspaces are already sourced. Launch the sensors and your .bag-file (modify the location for the .bag, if necessary) to publish the utbm-data. Open additonal Terminals to inspect the data.
```
roslaunch /catkin_ws/src/utbm_robocar_dataset/launch/utbm_dataset_play.launch bag:=/catkin_ws/src/utbm_robocar_dataset/launch/utbm_robocar_dataset_20180713_noimage.bag
```
