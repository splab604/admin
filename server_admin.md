# User Admin
## create user
```
sudo adduser --home /data/zhangsan zhangsan
cd /data & sudo chmod -R 700 zhangsan
```

## create shareable folder
```
sudo system-config-samba 
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
