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
