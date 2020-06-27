# User Admin
## create user
```
sudo adduser --home /data/zhangsan zhangsan
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
  sudo sshfs -o allow_other,uid=1000,gid=1000 lijianchen@192.168.11.211:/data/corpus /data/corpus_211
  
  # 查看参数帮助
  sshfs -h
  ```
  
* 取消挂载
  ```bash
  sudo umount /data/corpus_211
  ```
