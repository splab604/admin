# User Admin
## create user
```
sudo adduser --home /data/zhangsan zhangsan
cd /data & sudo chmod -R 700 zhangsan

# list directory
ls -al
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
