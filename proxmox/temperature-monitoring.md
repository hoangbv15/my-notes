[Back to Proxmox](README.md)

# System & Temperature Monitoring in Proxmox

Since the motherboard is not directly exposed to the VMs, we can't monitor temperatures of the CPU and motherboard directly in the VM.
However, we can do this on the Proxmox host.

## s-tui (preferred way)
My preferred way is to use a tool called [s-tui](https://github.com/amanusk/s-tui), which gives a nice graphical representation of the CPU speeds, temps and even power consumption, without the process view. 

It can be installed via apt
```
apt-get install s-tui
```
And we can run it with
```
s-tui
```
I sometimes struggle to remember the command name, so I create an alias in ~/.bashrc file
```
alias cpu='s-tui'
```
So I can run the tool with just `cpu`

Here's a preview of what it looks like

![s-tui preview](https://github.com/amanusk/s-tui/blob/master/ScreenShots/s-tui-1.0.gif?raw=true)

## lm-sensors
First, install the lm-sensors package
```
apt install lm-sensors
```

Now we can see the current readings with
```
sensors
```

To continually monitor the sensors, do
```
watch -n 4 sensors
```
The above command will show the readings and refresh every 4 seconds.

Here's an example output:
```
Every 4.0s: sensors                                                pve: Mon Sep  9 20:04:56 2024

coretemp-isa-0000
Adapter: ISA adapter
Package id 0:  +39.0°C  (high = +75.0°C, crit = +93.0°C)
Core 0:        +33.0°C  (high = +75.0°C, crit = +93.0°C)
Core 1:        +34.0°C  (high = +75.0°C, crit = +93.0°C)
Core 2:        +32.0°C  (high = +75.0°C, crit = +93.0°C)
Core 3:        +32.0°C  (high = +75.0°C, crit = +93.0°C)
Core 4:        +33.0°C  (high = +75.0°C, crit = +93.0°C)
Core 5:        +29.0°C  (high = +75.0°C, crit = +93.0°C)
Core 6:        +28.0°C  (high = +75.0°C, crit = +93.0°C)
Core 8:        +32.0°C  (high = +75.0°C, crit = +93.0°C)
Core 9:        +32.0°C  (high = +75.0°C, crit = +93.0°C)
Core 10:       +29.0°C  (high = +75.0°C, crit = +93.0°C)
Core 11:       +27.0°C  (high = +75.0°C, crit = +93.0°C)
Core 12:       +32.0°C  (high = +75.0°C, crit = +93.0°C)
Core 13:       +30.0°C  (high = +75.0°C, crit = +93.0°C)
Core 14:       +30.0°C  (high = +75.0°C, crit = +93.0°C)

nvme-pci-0100
Adapter: PCI adapter
Composite:    +27.9°C  (low  = -40.1°C, high = +83.8°C)
                       (crit = +87.8°C)
Sensor 1:     +43.9°C  (low  = -273.1°C, high = +65261.8°C)
Sensor 2:     +27.9°C  (low  = -273.1°C, high = +65261.8°C)
```

There is a way to edit the Proxmox dashboard's template and manually add html and javascript to show the values. However, it's not a method I recommend, due to the below reasons
- If you make a mistake when editing the template, such as a typo, you won't be able to access the dashboard
- Any future update that changes the template will revert your changes

## htop
Another way is to use good old `htop`.
```
apt install htop
```
And then simply run `htop` from terminal. Press F2 to go to htop settings and enable temperature and clock speed.

I haven't found a way to disable the process list that takes up 2/3 of the screen, but hey, at least we can monitor the temps and clock speed this way.

Here's an example output:
```
    0[1.1% 2200MHz 39°C]   4[0.4% 2204MHz 31°C]   7[|1.8% 2200MHz 28°C] 11[|0.7% 2203MHz 29°C]
    1[1.8% 2203MHz 33°C]   5[1.8% 2203MHz 32°C]   8[| 0.2% 2203MHz N/A] 12[|2.3% 2203MHz 25°C]
    2[4.5% 2203MHz 34°C]   6[2.0% 2200MHz 31°C]   9[|0.4% 2200MHz 30°C] 13[|1.6% 2203MHz 30°C]
    3[0.4% 2200MHz 32°C]                         10[|2.2% 2200MHz 32°C]
  Mem[|||||||||||||||||||||||||    33.5G/62.7G] Tasks: 46, 45 thr, 234 kthr; 1 running
  Swp[                                0K/4.00G] Load average: 0.41 0.64 0.77 
                                                Uptime: 00:18:32

  [Main] [I/O]
    PID USER       PRI  NI  VIRT▽  RES   SHR S  CPU% MEM%   TIME+  Command
      1 root        20   0  164M 12532  9076 S   0.0  0.0  0:01.22 /sbin/init
   2355 root        20   0 67.7G 32.8G 12160 S  35.9 51.1 15:15.60 ├─ /usr/bin/kvm -id 105 -name
   2356 root        20   0 67.7G 32.8G 12160 S   0.0 51.1  0:00.16 │  ├─ /usr/bin/kvm -id 105 -n
   2357 root        20   0 67.7G 32.8G 12160 S   0.0 51.1  0:04.77 │  ├─ /usr/bin/kvm -id 105 -n
   2418 root        20   0 67.7G 32.8G 12160 S   4.9 51.1  2:10.81 │  ├─ /usr/bin/kvm -id 105 -n
   2419 root        20   0 67.7G 32.8G 12160 S   5.2 51.1  2:07.07 │  ├─ /usr/bin/kvm -id 105 -n
   2420 root        20   0 67.7G 32.8G 12160 S   3.1 51.1  1:44.18 │  ├─ /usr/bin/kvm -id 105 -n
   2421 root        20   0 67.7G 32.8G 12160 S   5.6 51.1  2:19.32 │  ├─ /usr/bin/kvm -id 105 -n
   2422 root        20   0 67.7G 32.8G 12160 S   4.5 51.1  1:43.96 │  ├─ /usr/bin/kvm -id 105 -n
   2423 root        20   0 67.7G 32.8G 12160 S   3.1 51.1  1:27.96 │  ├─ /usr/bin/kvm -id 105 -n
   2424 root        20   0 67.7G 32.8G 12160 S   3.1 51.1  1:33.35 │  ├─ /usr/bin/kvm -id 105 -n
   2425 root        20   0 67.7G 32.8G 12160 S   7.0 51.1  1:53.32 │  ├─ /usr/bin/kvm -id 105 -n
   2448 root        20   0 67.7G 32.8G 12160 S   0.0 51.1  0:00.00 │  ├─ /usr/bin/kvm -id 105 -n
   2546 root        20   0 67.7G 32.8G 12160 S   0.2 51.1  0:00.05 │  ├─ /usr/bin/kvm -id 105 -n
   2547 root        20   0 67.7G 32.8G 12160 S   0.0 51.1  0:00.00 │  ├─ /usr/bin/kvm -id 105 -n
   2548 root        20   0 67.7G 32.8G 12160 S   0.0 51.1  0:00.00 │  ├─ /usr/bin/kvm -id 105 -n
   2549 root        20   0 67.7G 32.8G 12160 S   0.0 51.1  0:00.00 │  ├─ /usr/bin/kvm -id 105 -n
   2553 root        20   0 67.7G 32.8G 12160 S   0.0 51.1  0:00.00 │  ├─ /usr/bin/kvm -id 105 -n
   2579 root        20   0 67.7G 32.8G 12160 S   0.0 51.1  0:00.00 │  ├─ /usr/bin/kvm -id 105 -n
   2580 root        20   0 67.7G 32.8G 12160 S   0.0 51.1  0:00.04 │  ├─ /usr/bin/kvm -id 105 -n

```


## Good resources:
- https://gist.github.com/tinwhisker/53d77c887535129021a1f58359930935
- https://itsfoss.com/monitor-cpu-gpu-temp-linux/
