# 1. **Nvidia driver 설치**

```jsx
ubuntu-drivers devices
```



위와 같은 사진이 뜨지 않을경우 참고해서 따라가기

[[Ubuntu 20.04 LTS]Nvidia드라이버 설치하기](https://pstudio411.tistory.com/entry/Ubuntu-2004-Nvidia드라이버-설치하기)

# 2. **Nvidia-docker 설치**

정확한 설치는 NVIDIA공식홈페이지를 따라가는것이 좋다. 설명이 더 자세하다.

[Installation Guide — NVIDIA Cloud Native Technologies  documentation](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker)

```jsx
curl https://get.docker.com | sh \
  && sudo systemctl --now enable docker
```

package repsitory를 위한 GPG key

```jsx
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
            sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
            sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

```jsx
sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
```

docker daemon이 NVIDA container Runtime을 제대로인식했는지 확인

```jsx
sudo nvidia-ctk runtime configure --runtime=docker
```

Docker daemon을 재시작해서 default runtime을 바꿔주자.

```jsx
sudo systemctl restart docker
```

정상적으로 깔렸는지 체크

```jsx
sudo docker run --rm --runtime=nvidia --gpus all nvidia/cuda:11.6.2-base-ubuntu20.04 nvidia-smi
```

아래와 같이 나오면 잘 깔린것이다.

```jsx
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 450.51.06    Driver Version: 450.51.06    CUDA Version: 11.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Tesla T4            On   | 00000000:00:1E.0 Off |                    0 |
| N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```