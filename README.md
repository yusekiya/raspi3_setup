# Setup raspi3 with ansible

## Write os image to SD card using mac

1. Identify the disk of SD card, e.g., `disk4` (not `disk4s1`) with `diskutil list`
2. Unmount disk

  ``` bash
  $ diskutil unmountDisk </dev/diskX>
  ```

  Here, `diskX` is a disk identifier such as `disk4`
3. Write os image

  ``` bash
  $ sudo dd bs=1m if=<path/to/raspbian.img> of=</dev/rdiskX>
  ```

  Note that 'r' is prefixed to the disk identifier.
  If this command results in `dd: invalid number '1m'`,
  replace `bs=1m` with `bs=1M`.
  If this command still fails, see [here][1].

See [here][1] for detailed information.


## Initial setup

1. Insert the SD card to raspi
2. Connect raspi and rooter with LAN cable
3. Turn on raspi
4. Login to raspi via SSH

  ``` bash
  $ ssh pi@raspberrypi.local
  ```

  The default password is 'raspberry'.
5. Configure the following settings with `sudo raspi-config`
  - Expand FileSystem: To make entire space of SD card available
  - Change User Password: Change default password of the user 'pi'
6. Update system

  ``` bash
  $ sudo apt-get update
  $ sudo apt-get upgrade
  $ sudo rpi-update
  $ sudo reboot
  ```


## Setup with ansible





<!-- Reference -->
[1]: https://www.raspberrypi.org/documentation/installation/installing-images/mac.md
