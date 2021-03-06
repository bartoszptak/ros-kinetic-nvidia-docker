# ros-kinetic-nvidia-docker
Extends the `osrf/ros:kinetic-desktop-full` image by adding opengl, glvnd, cuda 8.0 and kalibr. This makes opengl work from any docker environment when used with
[nvidia-docker2](https://github.com/NVIDIA/nvidia-docker). Thanks to
[phromo](https://github.com/phromo/ros-indigo-desktop-full-nvidia) for the
baseline. Note that this is currently supported for Linux systems only.

To extend the Dockerfile (e.g. to add more ROS packages or users), take a
look at the commented out lines at the end of file.

# Installation
1. Install docker
2. Install nvidia-docker2 using instructions
[here](https://github.com/NVIDIA/nvidia-docker).
3. Clone and build
```bash
git clone git@github.com:bartoszptak/ros-kinetic-nvidia-docker.git
cd ros-kinetic-nvidia-docker
docker build -t nsfw .
```
4. Run the following commands to give docker permission to run on X:
```bash
xhost +local:docker
XSOCK=/tmp/.X11-unix
XAUTH=/tmp/.docker.xauth
sudo touch $XAUTH
xauth nlist $DISPLAY | sed -e 's/^..../ffff/' | sudo xauth -f $XAUTH nmerge -
mkdir $HOME/shared
```
5. Start the container:
```bash
nvidia-docker run -it \
  --privileged \
  --volume=$XSOCK:$XSOCK:rw \
  --volume=$XAUTH:$XAUTH:rw \
  --volume="$HOME/shared:/root/share:rw" \
  --env="QT_X11_NO_MITSHM=1" \
  --env="XAUTHORITY=${XAUTH}" \
  --env="DISPLAY" \
  --network host \
  --name="SuperContainer" \
  nsfw:latest \
  bash
```


## docker tips
* docker exec -it SuperContainer bash
