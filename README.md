# Setup raspi3 with ansible

## Write os image to SD card using mac

1. Identify the disk of SD card, e.g., `disk4` (not `disk4s1`) with `diskutil list`
2. Format the SD card

    ``` bash
    diskutil eraseDisk FAT32 <RASPBIAN> </dev/diskX>
    ```

    Here, `<RASPBIAN>` is any name of the volume (must be capital characters),
    and `diskX` is a disk identifier such as `disk4`.

3. Unmount disk

    ``` bash
    diskutil unmountDisk </dev/diskX>
    ```

4. Write os image

    ``` bash
    sudo dd bs=1m if=<path/to/raspbian.img> of=</dev/rdiskX> status=progress conv=sync
    ```

    Note that 'r' is prefixed to the disk identifier.
    If this command results in `dd: invalid number '1m'`,
    replace `bs=1m` with `bs=1M` (and `sync` must be replaced with `fsync`
    when you install Raspbian using Linux).
    If this command still fails, see [here][1].

5. Enable ssh

    Create file `ssh` under `boot` directory in the SD card.

    ``` bash
    touch <path to sd card disk>/boot/ssh
    ```

6. Configure wifi setting

    Create file `wpa_supplicant.conf` under `boot` directory in SD card.

    ```
    country=GB
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    network={
	ssid="<your_WiFi_SSID>"
	psk=<passphrase>
    }
    ```

    If you have `wpa_passphrase` command available, the network block with encrypted passphrase
    can be generated by issuing

    ```bash
    wpa_passphrase <your_WiFi_SSID> <passphrase>
    ```

    > **NOTE** Raspberry pi supports 2.4 GHz band only.

7. Eject SD card

    ``` bash
    diskutil eject </dev/diskX>
    ```

See [here][1] for detailed information.

## Initial setup

1. Insert the SD card to raspi
2. Connect raspi and rooter with LAN cable as needed
3. Turn on raspi
4. Login to raspi via SSH

    ``` bash
    ssh pi@raspberrypi.local
    ```

    The default password is 'raspberry'.

5. Create users

6. Configure the following settings with `sudo raspi-config`

    - Expand FileSystem: To make entire space of SD card available
    - Change User Password: Change default password of the user 'pi'

7. Update system

    ``` bash
    sudo apt-get update
    sudo apt-get upgrade
    sudo rpi-update
    sudo reboot
    ```


## Setup with ansible

Raspi setup procedure, e.g., development environment setup, is described in the ansible playbook, site.yml.
Check roles described in site.yml and README.md of each role
to see what type of tasks will be performed.
Take the following procedure to configure your raspi.

1. Clone raspi3_setup repository on control (host) computer.

    ``` bash
    git clone https://github.com/yusekiya/raspi3_setup.git
    ```

2. Copy template inventory file, template_hosts, to inventory file, hosts, and write the ip address of raspi into the inventory file, e.g.,

    ``` ini:hosts
    [raspi]
    192.168.xxx.xxx
    # or with port number
    192.168.xxx.xxx:yyyy
    ```

3. Copy vars/template.yml to vars/main.yml, and modify variables in the file.
See README.md of each role for detailed information about the variables.
4. Modify the value of `remote_user` in ansible.cfg to a user name you want to login as. Default is 'pi'.
5. Fetch required roles

    ```bash
    ansible-galaxy install -r requirements.yml -p ./roles
    ```

6. Run playbook

    ``` bash
    ansible-playbook site.yml
    ```

7. Rerun playbook after reboot just in case, and confirm that each task is ok (not changed).


## Launch application automatically after login for kiosk user
Edit /home/`<user>`/.config/autostart/`<name>`.desktop.

``` ini:/home/<user>/.config/autostart/<name>.desktop
[Desktop Entry]
Type=Application
Name=<name>
Exec=<absolute/path/to/executable>
```


## Optional setup

### Disable swap

```bash
sudo dphys-swapfile swapoff
sudo dphys-swapfile uninstall
sudo insserv -r dphys-swapfile
sudo systemctl disable dphys-swapfile
# Check if it went well after reboot
free
```

### Assign static IP (>= Raspbian Jessie)

Edit `/etc/dhcpcd.conf`, for instance,

```
interface wlan0
static ip_address=192.168.0.10/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1
```

then reboot.


### Make storage read only

~~See [here](https://github.com/josepsanzcamp/root-ro)~~.
(Doesn't work on raspbian jessie on raspberry pi zero w)

~~See [here](https://hallard.me/raspberry-pi-read-only/) for detail~~
(It works except docker.service)

cf. [here](https://www.indetail.co.jp/blog/11421/)
(docker.service fails to start)

#### Disable swap
Follow the [above instruction](#disable-swap).

#### Load overlay module at boot
Add `overlay` to `/etc/modules`.

#### Create RAM disk
Edit `/etc/fstab`

```
proc            /proc           proc    defaults          0       0
/dev/mmcblk0p5  /boot           vfat    defaults          0       2
/dev/mmcblk0p6  /               ext4    defaults,noatime  0       1
# Add the followings
tmpfs           /tmp            tmpfs   defaults,size=32m 0       0
tmpfs           /var/tmp        tmpfs   defaults,size=16m 0       0
tmpfs           /var/log        tmpfs   defaults,size=32m 0       0
```

#### overlayfs
Create `/fsprotect` with `mkdir /fsprotect`.

Edit `/etc/fstab` to mount SD card as read only

```
/dev/mmcblk0p5  /boot           vfat    defaults,ro          0       2
/dev/mmcblk0p6  /               ext4    defaults,noatime,ro  0       1
```

Edit `/etc/init.d/mount-overlay`

```
#!/bin/sh

### BEGIN INIT INFO
# Provides: mount-overlay
# Required-Start: mountall-bootclean
# Required-Stop:
# Default-Start: S
# Default-Stop:
# X-Start-Before: procps udev-mtab urandom
# Short-Description: overlay mode
# Descrition: Shutdown process will not be required
### END INIT INFO

/bin/mount /boot

cd /boot
file=noprotect
if [ -e ${file} ]; then
exit 0
fi

/bin/mount -t tmpfs tmpfs /fsprotect

for d in etc home root var usr
do
mkdir /fsprotect/${d}
mkdir /fsprotect/${d}_rw

OPTS="-o lowerdir=/${d},upperdir=/fsprotect/${d},workdir=/fsprotect/${d}_rw"
/bin/mount -t overlay ${OPTS} overlay /${d}
done

exit 0
```

then change mode `sudo chmod 755 /etc/init.d/mount-overlay`.

Add the following lines to `/etc/rc.local`.

```
file=noprotect
if [ -f /boot/${file} ]; then
  mount -o rw,remount /
  mount -o rw,remount /boot
fi
```

Run `mount-overlay` at startup.

``` shell
sudo update-rc.d mount-overlay defaults 01 10
```

#### Add script to enable/disable overlayfs
Edit `/usr/local/bin/noprotect`

```
#!/bin/sh

mount -o rw,remount /boot
cd /boot
if [ -e "protect" ]; then
    rm /boot/protect
fi

if [ -e "noprotect" ]; then
    echo "noprotect mode"
else
    touch /boot/noprotect
    echo "noprotect mode"
fi

mount -o ro,remount /boot
```

Edit `/usr/local/bin/protect`

```
#!/bin/sh

mount -o rw,remount /boot
cd /boot
if [ -e "noprotect" ]; then
    rm /boot/noprotect
fi

if [ -e "protect" ]; then
    echo "protect mode"
else
    touch /boot/protect
    echo "protect mode"
fi

mount -o ro,remount /boot
```

Make the executable

``` shell
sudo chmod a+x /usr/local/bin/noprotect
sudo chmod a+x /usr/local/bin/protect
```



<!-- Reference -->
[1]: https://www.raspberrypi.org/documentation/installation/installing-images/mac.md
