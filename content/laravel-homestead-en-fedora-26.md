Title: Laravel Homestead en Fedora 26
Slug: laravel-homestead-en-fedora-26
Date: 2017-06-29 17:15:59
Tags: laravel, vagrant


## Instalación ##
```text
$ vagrant box add laravel/homestead
$ git clone https://github.com/laravel/homestead.git Homestead
$ cd Homestead
$ git checkout v5.4.0
$ bash init.sh
```
## Homestead.yml ##
```
folders:
    - map: ~/sitios
      to: /home/vagrant/sitios
      type: "nfs"

sites:
    - map: interpos.app
      to: /home/vagrant/sitios/interpos/public
```
## /etc/hosts ##
```text
192.168.10.10  mysite.app
```
## Soporte NFS ##
```text
$ vagrant plugin install vagrant-bindfs
$ sudo dnf install nfs-utils nfs-kernel-server firewall-config
$ sudo systemctl enable nfs-server
$ sudo systemctl start nfs-server
$ sudo firewall-cmd --permanent --add-service=nfs
$ sudo firewall-cmd --permanent --add-service=rpc-bind
$ sudo firewall-cmd --permanent --add-service=mountd
$ sudo firewall-cmd --reload
```
Desde la versión 2.1.1 de `nfs-utils` está deshabilitado el soporte a UDP por defecto, por lo que `vagrant up`, se queda bloqueado en `==> homestead-7: Mounting NFS shared folders...`, para resolver ésto, hay que editar `/etc/sysconfig/nfs` y añadir `--udp` a `RPCNFSDARGS`.

La anterior solución la encontré en este [enlace](https://robertbasic.com/blog/enable-udp-for-nfs-on-fedora/).

```text
RPCNFSDARGS="--udp"
```
## Virtualbox 5.1.24 [bug](https://www.virtualbox.org/ticket/16911) workaround ##
```text
$ sudo ip link set vboxnet0 up
```
## Firewall con GUI ##
Tomado de [aquí](https://meta.discourse.org/t/solved-nfs-mount-hangs-need-vagrant-file-for-fedora-23/41314/2).
```text
Example for easily configuring firewalld using the "firewall-config" GUI:
- Choose "Configuration: Permanent" from the drop down menu
- Select the zone you want to modify (i chose "internal").
- Go to the "Interfaces" tab, assign vboxnet0 (or other host-only adapter) to the zone.
- Go to the "Services" tab, check "mountd", "nfs" and "rpc-bind"
- Go to the global "Services" tab (next to "Zones"), select NFS and add Port 2049 UDP (only TCP is predefined).
- Finally choose "Options/Reload Firewalld".
```
















