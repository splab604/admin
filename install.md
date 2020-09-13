## ubuntu 16.04 安装远程桌面

* 安装 `xrdp`
  ```bash
  sudo apt-get install xrdp  
  ```
  
* 安装 `vnc4server`
  ```bash
  sudo apt-get install vnc4server
  ```
  
* 安装 `xfce4`
  ```bash
  sudo apt-get install xfce4
  
  # 或者安装 mate-session
  sudo apt install mate-session-manager
  #后面的 xfce4-session 换成 mate-session
  ```
  
* 向 `xsession` 中写入 `xfce4-session`
  ```bash
  # 单用户
  echo "xfce4-session" >~/.xsession
  # 所有用户
  sudo sed -i.bak '/fi/a #xrdp multi-users \n xfce4-session \n' /etc/xrdp/startwm.sh
  ```
  
* 开启 `xrdp` 服务
  ```bash
  sudo systemctl start xrdp
  ```
  
* `xrdp` 开机自动启动
  ```bash
  /lib/systemd/systemd-sysv-install enable xrdp
  ```

* xfce4 tab 补全键冲突：
  https://blog.csdn.net/xuezhisdc/article/details/48662435
  
## 安装 `samba`，开启局域网共享服务
* 安装
  ```bash
  sudo apt-get install samba
  sudo apt-get install system-config-samba
  ```
  
* 问题
  * 运行 `system-config-samba` 出现 `SystemError: Could not open configuration file '/etc/libnbuser.conf'`
  
    解决：在 `/etc` 目录下创建 `libuser.conf` 文件
    ```bash
    sudo touch /etc/libuser.conf
    ```
    
## 安装 cuda & cudnn
* 第二次安装其他版本时需要 runfile 方式安装

* [NVIDIA CUDA Installation Guide for Linux](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)

* 已经安装好 NVIDIA 驱动

* 禁用 nouveau
  ```bash
  lsmod | grep nouveau
  # 如果没有输出就是已经禁用了
  ```
  
* 下载 cuda：https://developer.nvidia.com/cuda-toolkit-archive

* 安装 cuda
  ```bash
  # 例如：
  wget https://developer.nvidia.com/compute/cuda/9.0/Prod/local_installers/cuda_9.0.176_384.81_linux-run
  
  sudo bash cuda_9.0.176_384.81_linux-run --no-opengl-libs
  
  # 一直按回车
  #
  # Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 384.81? 
  # **选 no**
  # 
  # cuda samples 装不装应该都行
  
  # 添加系统环境变量
  sudo vim /etc/bash.bashrc
  # 写入：
  export PATH=/usr/local/cuda-9.0/bin:$PATH
  export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:$LD_LIBRARY_PATH
  # 或者，因为安装时已经建立了软链接
  export PATH=/usr/local/cuda/bin:$PATH
  export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
  
  # 如果其他人想要使用其他版本的 cuda，在自己的 bashrc 下添加：
  export PATH=$PATH:/usr/local/cuda-10.2/bin
  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-10.2/lib64
  ```
 
* 验证是否安装成功
  ```bash
  nvcc --version
  /usr/local/cuda-10.2/bin/nvcc --version
  ```
  
* cudnn
  * 第二次安装其他版本时选择 tar file
  * 参考[官方安装文档](https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html#installlinux) 安装，不要看乱七八槽的博客
  * 下载：[cuDNN Runtime Library for Ubuntu16.04 (Deb)& cuDNN Code Samples and User Guide for Ubuntu16.04 (Deb) & cuDNN Developer Library for Ubuntu16.04 (Deb)](https://developer.nvidia.com/rdp/cudnn-download)
  * 安装：
    ```bash
    sudo dpkg -i libcudnn7_7.6.5.32-1+cuda9.0_amd64.deb
    sudo dpkg -i libcudnn7-dev_7.6.5.32-1+cuda9.0_amd64.deb
    sudo dpkg -i libcudnn7-doc_7.6.5.32-1+cuda9.0_amd64.deb
    ```

* 验证 cudnn 是否安装成功

  * 参考[官方文档](https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html#verify)测试
  
* cudnn 卸载
  ```bash
  dpkg -l | grep cudnn
  
  sudo dpkg -r libcudnn7-doc
  sudo dpkg -r libcudnn7-dev
  sudo dpkg -r libcudnn7
  ```
  
* 安装多个版本的 cudnn
  ```bash
  tar -xzvf cudnn-10.2-linux-x64-v7.6.5.32.tgz
  
  sudo cp cuda/include/cudnn*.h /usr/local/cuda-10.2/include
  sudo cp cuda/lib64/libcudnn* /usr/local/cuda-10.2/lib64
  sudo chmod a+r /usr/local/cuda-10.2/include/cudnn*.h /usr/local/cuda-10.2/lib64/libcudnn*
  ```

