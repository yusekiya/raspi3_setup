# Setup raspi3 with ansible

## Write os image to SD card using mac

1. Identify the disk of SD card, e.g., `disk4` (not `disk4s1`) with `diskutil list`
2. Format the SD card

  ``` bash
  $ diskutil eraseDisk FAT32 <RASPBIAN> </dev/diskX>
  ```

  Here, `<RASPBIAN>` is any name of the volume, and `diskX` is a disk identifier such as `disk4`.

3. Unmount disk

  ``` bash
  $ diskutil unmountDisk </dev/diskX>
  ```

4. Write os image

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

1. Copy template inventory file, template_hosts, to inventory file, hosts, and write the ip address of raspi into the inventory file.
2. Copy var/template.yml to var/main.yml, and modify variables in the file.
3. To setup wifi, the vault password is required. If you don't need wifi setup, comment out `vars/vault_wifi_key.yml` in site.yml, and `ask_vault_pass = True` in ansible.cfg. Otherwise, modify variable `raspi_network_wifi` according to your environment.
4. Run playbook

  ``` bash
  $ ansible-playbook site.yml
  ```


## TODO

- [x] common task
  - [x] change timezone
  - [x] change hostname
  - [x] remove root privilege from user 'pi'
  - [x] apt-get install
  - [x] install virtual keyboard
- [x] setup firewall
- [x] wifi setup
- [ ] dev environment task
  - [x] add user
  - [x] make basic directories
  - [x] install my dotfiles
  - [x] setup of python environment
  - [ ] apt-get install
    - [x] git
    - [x] build-essential
    - [x] htop
    - [x] pkg-config
    - [x] tree
    - [x] autoconf
    - [x] automake
    - [x] bash-completion
    - [x] cmake
    - [x] colordiff
    - [x] exuberant-ctags
    - [x] direnv
    - [x] nkf
    - [ ] lua
    - [x] pandoc
    - [x] clang
  - [x] install fzf
  - [x] install latest tmux
  - [x] install latest tig
  - [x] install nodebrew
  - [ ] clone git repositories
    - [x] tpm
    - [ ] solarized
    - [x] dircolors-solarized
    - [x] enhancd
- [ ] kiosk environment task
  - [x] add user
  - [ ] auto login
  - [ ] setup screen saver
  - [ ] autohide LXDE panel
  - [ ] rotate screen upside down
  - [ ] modify backlight brightness (only when sys/class/backlight/rpi_backlight/brightness exists)



<!-- Reference -->
[1]: https://www.raspberrypi.org/documentation/installation/installing-images/mac.md
