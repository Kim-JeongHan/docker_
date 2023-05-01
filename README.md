1. git clone 하기
2. dockerfile build하기
    
    ```jsx
    sudo docker build --no-cache --force-rm -f Dockerfile --build-arg HOST_USER=$USER -t osrf/ros:melodic-desktop-full-bionic .
    ```
    
    - dockerfile을 이용하여 빌드 수행
    - --no-cache: 이전 빌드에서 생성된 캐시 사용하지 않음
    - --force-rm: 빌드하다가 fail시 자동 삭제
    - -f: 사용할 Dockerfile의 이름을 명시
    - -t: 생성할 이미지의 ID와 TAG 명시
    - . 는 Dockerfile의 경로를 명시
3. docker image 생성되었는지 확인
    
    ```jsx
    docker ps -a
    ```
    
4. docker 실행하기
    1. gui연동을 위한 명령어(다른 터미널에서 시작)
        
        ```jsx
        xhost +local:docker
        ```
        
    2. docker container 실행
        
        ```jsx
        docker run -it \
            --init \
            --ipc=host \
            --shm-size=8G \
            --privileged \
            --net=host \
            -e DISPLAY=$DISPLAY \
            -e XDG_RUNTIME_DIR=/run/user/1000 \
            -e QT_GRAPHICSSYSTEM=native \
            -e USER=$USER \
            --env=UDEV=1 \
            --env=LIBUSB_DEBUG=1 \
            --env="DISPLAY" \
            --env="QT_X11_NO_MITSHM=1" \
            ${ENV_PARAMS[@]} \
            -v /home/$USER/workspace:/home/$USER/workspace \
            -v /dev:/dev \
            -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
            osrf/ros:melodic-desktop-full-bionic \
            ${OTHER_PARAMS[@]}  \
            /bin/bash  \

        ```
        
5. docker container안에서 실행
    
    ```jsx
    mkdir -p $HOME/catkin_ws/src && cd $HOME/catkin_ws/src && catkin_init_workspace
    
    # 소스코드 다운로드
    
    cd $HOME/catkin_ws && catkin_make
    
    echo "source /opt/ros/melodic/setup.bash" >> $HOME/.bashrc
    echo "source /$HOME/catkin_ws/devel/setup.bash" >> $HOME/.bashrc
    echo "export ROS_HOSTNAME=localhost" >> $HOME/.bashrc
    echo "export ROS_MASTER_URI=http://localhost:11311" >> $HOME/.bashrc
    echo "alias cw='cd ~/catkin_ws'" >> $HOME/.bashrc
    echo "alias cs='cd ~/catkin_ws/src'" >> $HOME/.bashrc
    echo "alias cm='cd ~/catkin_ws && catkin_make'" >> $HOME/.bashrc
    
    source /opt/ros/$ROS_DISTRO/setup.bash
    source /$HOME/catkin_ws/devel/setup.bash
    
    echo "USER=$USER"
    echo "HOME=$HOME"
    echo "ROS_ROOT=$ROS_ROOT"
    echo "ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH"
    echo "ROS_HOSTNAME=$ROS_HOSTNAME"
    echo "ROS_MASTER_URI=$ROS_MASTER_URI"
    ```
