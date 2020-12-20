# User Admin
## create user
```
sudo adduser --home /data/zhangsan zhangsan
# copy .bashrc from system
cp /etc/skel/.bashrc /data/zhangsan/

cd /data
sudo chmod -R 700 zhangsan

# list directory
ls -al
```

## change passwd
```
sudo passwd zhangsan
```

## create shareable folder
```
# open in remote desktop terminal, require UI
sudo system-config-samba 
# add samba user
preferences -> samba users ->  add user -> unix username select as zhangsan -> enter new passwd
# add shareable folder
click + icon -> in 'basic' panel, browser /data/zhangsan -> in 'access' panel, select zhangsan 
```

## delete user and data
```
# 1. delete samba user first if it exists
sudo system-config-samba
# 2. delete user
sudo deluser zhangsan
# 3. delete user data
sudo rm –rf /data/zhangsan
```
# User Acess
## temporary block user access
```
chage -E0 testuser
```

## allow user access
```
chage –E -1 testuser
```

## 修改文件夹所属用户名和用户组

```
sudo chown -R <owner>:<group> <folder name> 
```

## check net traffic usage
```
sudo nethogs
```

## 远程挂载

* 例如在 216，安装 `sshfs`
  ```bash
  sudo apt-get install sshfs
  ```
  
* 创建文件夹
  ```bash
  sudo mkdir /data/corpus_211
  ```
  
* 查看 `uid`
  ```bash
  cat /etc/passwd | grep lijianchen
  ```

* 远程挂载
  ```bash
  # 216
  sudo sshfs -o allow_other,uid=1000,gid=1000 lijianchen@192.168.1.211:/data/corpus /data/corpus_211
  # 215
  sudo sshfs -o allow_other,uid=1007,gid=1007 lijianchen@192.168.1.211:/data/corpus /data/corpus_211
  # 使用 192.168.1.211 而不是 192.168.11.211 因为前一个走的是 Infiniband，后一个走的是较慢的路由器
  
  # 查看参数帮助
  sshfs -h
  ```
  
* 取消挂载
  ```bash
  sudo umount /data/corpus_211
  ```
  
* 开机自动挂载
  ```bash
  cd ~
  touch auto_mount.sh && chmod +x auto_mount.sh
  
  vim auto_mount.sh
  # 写入
  echo "<ssh_passwd>" | sshfs -o allow_other,uid=1007,gid=1007,password_stdin lijianchen@192.168.1.211:/data/corpus /data/corpus_211
  
  sudo vim /etc/rc.local
  # 写入
  sudo bash /data/lijianchen/auto_mount.sh
  ```
  
## linux 下安全删除，每次用 `rm -rf` 都胆战心惊
  * 创建脚本 `safe_rm.sh`，`trash` 文件夹位于 `/data/$USER` 下
    ```bash
    #!/bin/bash
    # 安全的rm脚本
    # reference: https://blog.csdn.net/newbietao/article/details/79833655


    dir=$(date "+%y_%m_%d")
    # dir="/trash/$dir"
    dir="/data/$USER/trash/$dir"
    #echo $dir
    if [ ! -d $dir ]; then
      mkdir -p $dir
    fi

    is_f=false
    args=""

    function f_remove() {
      for i in ${args}; do
        if [ -d "$i" -o -f "$i" ]; then
          name=`basename $i`
          if [ -d "$dir/$name" -o -f "$dir/$name" ]; then
            new_name="$dir/${name}_$(date '+%T')"
            mv $i $new_name && echo "$i has been moved to $new_name. You can use \`\\rm -r $new_name\` to permanently delete $i."
          else
            mv $i $dir && echo "$i has been moved to $dir/$i. You can use \`\\rm -r $dir/$i\` to permanently delete $i."
          fi
        else
          echo "参数错误"
        fi
      done
    }


    function remove() {
      for j in ${args}; do
        if [ -d "$j" -o -f "$j" ]; then
          name=`basename $j`
          read -p "Remove $name?[y/n]" bool
          if [ $bool == "n" ]; then
            exit
          elif [ $bool == "y" ]; then
            if [ -d "$dir/$name" -o -f "$dir/$name" ]; then
              new_name="$dir/${name}_$(date '+%T')"
              mv $j $new_name && echo "$j has been moved to $new_name. You can use \`\\rm -r $new_name\` to permanently delete $j."
            else
              mv $j $dir && echo "$j has been moved to $dir/$j. You can use \`\\rm -r $dir/$j\` to permanently delete $j."
            fi
          fi
        else
          echo "参数错误"
        fi
      done
    }


    while [ "$1" ]; do
      case "$1" in
        -fr|-rf)
          is_f=true
          shift
          ;;
        -i)
          is_f=false
          shift
          ;;
        *)
          args="$1 $args"
          shift
          ;;  
      esac
    done


    if [[ $is_f = true ]]; then
      f_remove
    else
      remove
    fi
    ```
  * 设置别名
  
    将上边的脚本保存到 `/usr/bin/` 下面，赋予执行权限，然后设置别名。
    
    ```bash
    sudo cp safe_rm.sh /usr/bin/safe_rm.sh
    sudo chmod 755 /usr/bin/safe_rm.sh
    sudo vim /etc/bash.bashrc
    # 在bash.bashrc中添加下面的一行
    alias rm=/usr/bin/safe_rm.sh
    ```
    
    现在使用 `rm` 删除的文件都会在 `/trash` 中找到。

    当你想使用真正的 `rm` 命令时你只需要在 `rm` 前面加一个 `\` 就行
    
    ```bash
    \rm -rf test.txt
    ```
  * `/trash` 目录自动清理
    ```bash
    crontab -e
    # 输入
    0 0 * * 0 cd /data/$USER/trash && \rm -r *
    ```
