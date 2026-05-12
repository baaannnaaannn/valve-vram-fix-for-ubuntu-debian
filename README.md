# valve-vram-fix-for-ubuntu-debian
##kernel
mainstream - still has no patches for fix
liquorix - has patches but it wouldnt load for me
xanmod - still has not patches
pika os - has patches and works

so ive compiled dmemcg booster and it seems compiled right
<img width="640" height="334" alt="Снимок экрана от 2026-05-12 15-52-04" src="https://github.com/user-attachments/assets/f5fe60af-4b1e-4dd1-8e1d-72bb0dbde305" />

then ai gave me two options for installing and i couldnt find any instructions in github repo
```
sudo install -Dm755 target/release/dmemcg-booster /usr/local/bin/dmemcg-booster
sudo install -Dm644 dmemcg-booster-system.service /etc/systemd/system/dmemcg-booster-system.service
sudo install -Dm644 dmemcg-booster-user.service /etc/systemd/user/dmemcg-booster-user.service
sudo systemctl daemon-reload
sudo systemctl enable --now dmemcg-booster-system.service
systemctl --user enable --now dmemcg-booster-user.service
```
and
```
sudo cp target/release/dmemcg-booster /usr/local/bin/
sudo cp dmemcg-booster-system.service /etc/systemd/system/
sudo cp dmemcg-booster-user.service /etc/systemd/user/
sudo systemctl daemon-reload
sudo systemctl enable --now dmemcg-booster
systemctl --user enable --now dmemcg-booster

systemctl status dmemcg-booster
```
and it seems like first one is much more accurate but with
`systemctl status dmemcg-booster` it couldnt find any services
but it seems like `systemctl status dmemcg-booster-system.service` worked
so with all this checks i can confirm my result
```
lvvy@lvvy-desktop:~/Загрузки/dmemcg-booster$ systemctl status dmemcg-booster-system.service
systemctl --user status dmemcg-booster-user.service
systemctl list-unit-files | grep dmemcg
journalctl -u dmemcg-booster-system.service -b
journalctl --user -u dmemcg-booster-user.service -b
● dmemcg-booster-system.service - Service for enabling and controlling dmem cgr>
     Loaded: loaded (/etc/systemd/system/dmemcg-booster-system.service; enabled>
     Active: active (running) since Tue 2026-05-12 16:17:01 +04; 5min ago
   Main PID: 712 (dmemcg-booster)
      Tasks: 1 (limit: 9310)
     Memory: 1.1M (peak: 2.8M)
        CPU: 487ms
     CGroup: /system.slice/dmemcg-booster-system.service
             └─712 /usr/bin/dmemcg-booster --use-system-bus

мая 12 16:17:01 lvvy-desktop systemd[1]: Started dmemcg-booster-system.service >

● dmemcg-booster-user.service - Service for enabling and controlling dmem cgrou>
     Loaded: loaded (/etc/xdg/systemd/user/dmemcg-booster-user.service; enabled>
     Active: active (running) since Tue 2026-05-12 16:18:32 +04; 4min 1s ago
   Main PID: 2982 (dmemcg-booster)
      Tasks: 1 (limit: 9310)
     Memory: 428.0K (peak: 736.0K)
        CPU: 18ms
     CGroup: /user.slice/user-1000.slice/user@1000.service/app.slice/dmemcg-boo>
             └─2982 /usr/bin/dmemcg-booster

мая 12 16:18:32 lvvy-desktop systemd[1325]: Started dmemcg-booster-user.service>

dmemcg-booster-system.service                enabled         enabled
мая 12 16:17:01 lvvy-desktop systemd[1]: Started dmemcg-booster-system.service >

мая 12 16:18:32 lvvy-desktop systemd[1325]: Started dmemcg-booster-user.service>

lvvy@lvvy-desktop:~/Загрузки/dmemcg-booster$ 

```

```
lvvy@lvvy-desktop:~/Загрузки/dmemcg-booster$ systemctl status dmemcg-booster-system.service
systemctl --user status dmemcg-booster-user.service
systemctl list-unit-files | grep dmemcg
journalctl -u dmemcg-booster-system.service -b
journalctl --user -u dmemcg-booster-user.service -b
● dmemcg-booster-system.service - Service for enabling and controlling dmem cgr>
     Loaded: loaded (/etc/systemd/system/dmemcg-booster-system.service; enabled>
     Active: active (running) since Tue 2026-05-12 16:17:01 +04; 5min ago
   Main PID: 712 (dmemcg-booster)
      Tasks: 1 (limit: 9310)
     Memory: 1.1M (peak: 2.8M)
        CPU: 487ms
     CGroup: /system.slice/dmemcg-booster-system.service
             └─712 /usr/bin/dmemcg-booster --use-system-bus

мая 12 16:17:01 lvvy-desktop systemd[1]: Started dmemcg-booster-system.service >

● dmemcg-booster-user.service - Service for enabling and controlling dmem cgrou>
     Loaded: loaded (/etc/xdg/systemd/user/dmemcg-booster-user.service; enabled>
     Active: active (running) since Tue 2026-05-12 16:18:32 +04; 4min 1s ago
   Main PID: 2982 (dmemcg-booster)
      Tasks: 1 (limit: 9310)
     Memory: 428.0K (peak: 736.0K)
        CPU: 18ms
     CGroup: /user.slice/user-1000.slice/user@1000.service/app.slice/dmemcg-boo>
             └─2982 /usr/bin/dmemcg-booster

мая 12 16:18:32 lvvy-desktop systemd[1325]: Started dmemcg-booster-user.service>

dmemcg-booster-system.service                enabled         enabled
мая 12 16:17:01 lvvy-desktop systemd[1]: Started dmemcg-booster-system.service >

мая 12 16:18:32 lvvy-desktop systemd[1325]: Started dmemcg-booster-user.service>

lvvy@lvvy-desktop:~/Загрузки/dmemcg-booster$ 
```
```
lvvy@lvvy-desktop:~/Загрузки/dmemcg-booster$ cat /sys/fs/cgroup/cgroup.controllers
cpuset cpu io memory hugetlb pids rdma misc dmem
lvvy@lvvy-desktop:~/Загрузки/dmemcg-booster$ grep -i DMEM /boot/config-$(uname -r)
CONFIG_CGROUP_DMEM=y
CONFIG_UIO_DMEM_GENIRQ=m
lvvy@lvvy-desktop:~/Загрузки/dmemcg-booster$ systemctl status user@1000.service | grep Delegate
             │ │   └─5731 grep --color=auto Delegate
lvvy@lvvy-desktop:~/Загрузки/dmemcg-booster$ cat /sys/fs/cgroup/user.slice/user-1000.slice/cgroup.subtree_control
cpu memory pids dmem
lvvy@lvvy-desktop:~/Загрузки/dmemcg-booster$ 


lvvy@lvvy-desktop:~/Загрузки/dmemcg-booster$ cat /sys/fs/cgroup/cgroup.controllers | grep -w dmem
grep -i CGROUP_DMEM /boot/config-$(uname -r)
cat /sys/fs/cgroup/user.slice/user-1000.slice/cgroup.subtree_control
cpuset cpu io memory hugetlb pids rdma misc dmem
CONFIG_CGROUP_DMEM=y
cpu memory pids dmem
lvvy@lvvy-desktop:~/Загрузки/dmemcg-booster$ 


```
```
lvvy@lvvy-desktop:~/Загрузки/dmemcg-booster$ systemctl show user@1000.service -p Delegate
Delegate=yes
lvvy@lvvy-desktop:~/Загрузки/dmemcg-booster$ 
```
so i hope its working fine, isnt it?
