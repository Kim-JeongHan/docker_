1. dockerfile build하기
    
    ```jsx
    sudo docker build --no-cache --force-rm -f Dockerfile --build-arg HOST_USER=$USER -t ros:humble-set .
    ```
    
    - dockerfile을 이용하여 빌드 수행
    - --no-cache: 이전 빌드에서 생성된 캐시 사용하지 않음
    - --force-rm: 빌드하다가 fail시 자동 삭제
    - -f: 사용할 Dockerfile의 이름을 명시
    - -t: 생성할 이미지의 ID와 TAG 명시
    - . 는 Dockerfile의 경로를 명시

2. docker image 생성되었는지 확인
    
    ```jsx
    docker ps -a
    ```
    
3. docker 실행하기
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
            -v /dev:/dev \
            -v $HOME/.Xauthority:/root/.Xauthority:rw \
            -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
            ros:humble-set \
            ${OTHER_PARAMS[@]}  \
            /bin/bash  

        ```
를 linux기반에서는 넣어주어야한다.


---
만약 자신이 humble이 아닌 다른 foxy나 rolling을 사용하고 싶다면 그에 맞게 수정해서 사용하면된다.
