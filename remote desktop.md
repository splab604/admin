# ubuntu 16.04 安装远程桌面

* 安装 `xrdp`
  ```bash
  sudo apt-get install xrdp  
  ```
  
* 安装 `vnc4server`
  ```bash
  sudo apt-get install vnc4server
  ```
  
* 安装 `xubuntu-desktop`
  ```bash
  sudo apt-get install xubuntu-desktop
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
