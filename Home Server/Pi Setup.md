### Flash OS 

​	Current os of choice: Raspbian Buster

Use Etcher



### On Pi

Configure static ip:

```bash
sudo vi /etc/dhcpcd.conf
```

and add this:

```
interface eth0

static ip_address=192.168.1.13/24
static routers=192.168.1.1
static domain_name_servers=192.168.1.1
```



Change Passwords - `passwd`

Change hostname:

```bash
sudo vi /etc/hosts
# Change raspberrypi to desired name
sudo vi /etc/hostname
```





Enable cgroup:

```bash
sudo vi /boot/cmdline.txt
# Add the following
cgroup_enable=cpu cgroup_memory=1 cgroup_enable=memory-
```

