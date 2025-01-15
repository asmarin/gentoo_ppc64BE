Documentación del proceso de instalación de gentoo ppc64 Big Endian en mi powermac g5 dual

Añado los archivos de configuración, esquemas de particionado y demás información util para llegar a hacer funcional el sistema.

Casi toda la documentación la he sacado de la página oficial de Gentoo, https://wiki.gentoo.org/wiki/Handbook:PPC64

1 boot cd - press c

2 loadkeys es

3 net-setup

4 /etc/resolv.conf

5 particionar con mac-fdisk (ver archivo del lsblk)

6 mkswap /dev/sda4 (swap)
7 swapon /dev/sda4
8 mkfs.ext4 /dev/sda5 (/ root)
9 mount /dev/sda5 /mnt/gentoo
10 cd /mnt/gentoo
11 links distfiles.gentoo.org (descargar stage3 en ppc/current-iso)
12 tar xvpf stage3-xxxx --xattrs-include='*.*' --numeric-owner
13 nano -w /mnt/gentoo/etc/portage/make.conf
      *añadir al archivo
      cflags -mcpu=970
      VIDEO_CARDS="radeon" (en mi caso, sustituir por la que corresponda)
      MAKEOPTS="-j3" (si no tienes mucha memoria ram tienes que bajarla a 1 para que puedas compilar)
14 construir chroot
15 mount /dev/sda3 /boot
16 emerge-websync
17 eselect profile list
   eselect profile set XXXX (elegir el correspondiente tras consultar con eselect profile list)
18 emerge --ask --verbose --update --deep --newuse @world
19 echo "Europe/Madrid"  /etc/timezone
   emerge --config sys-libs/timezone-data
20 nano -w /etc/locale.gen  (añadir es.utf8 o el que corresponda)
   locale-gen
   eselect locale list
   eselect locale set XXX (elegir el correspondiente tras consultar con eselect locale list)
   env-update && source /etc/profiel && export PS1="(chroot) 
