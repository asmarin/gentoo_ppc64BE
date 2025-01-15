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

     *cp --dereference /etc/resolv.conf /mnt/gentoo/etc/
     mount --types proc /proc /mnt/gentoo/proc
     mount --rbind /sys /mnt/gentoo/sys
     mount --make-rslave /mnt/gentoo/sys
     mount --rbind /dev /mnt/gentoo/dev
     mount --make-rslave /mnt/gentoo/dev
     mount --bind /run /mnt/gentoo/run
     mount --make-slave /mnt/gentoo/run 

     chroot /mnt/gentoo /bin/bash 
     source /etc/profile 
     export PS1="(chroot) ${PS1}"

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
   env-update && source /etc/profiel && export PS1="(chroot) ${PS1}"

21 emerge --ask sys-kernel/gentoo-sources
  
   eselect kernel list
   eselect kernet set 1
   emerge --ask sus-apps/pci-utils
   cd /usr/src/linux
   make g5_defconfig
   make menuconfig (para personalizar el kernel)
   make
   make modules_install
   cp vmlinux /boot/kernel-x.xx.x-gentoo
   nano /etc/portage/make.conf
      *ACCEPT_LICENSE="*"
   emerge --ask sys-kernel/linux-firmware
   ln -sf /usr/src/linux-x.xx.x /usr/src/linux
   
22 nano -w /etc/fstab (modificar con el esquema de particionado del archivo que contiene el lsblk)
   
   emerge --ask --noreplace net-misc/netifrc
   ifconfig (para ver como nombra la tarjeta de red, normalmente es eth0 pero puede que el kernel te saque un churro aleatorio de nombre)
   nano -w /etc/conf.d/net (poner los datos de ip fija en función del esquema de red)

    *config_eth0="192.168.0.2 netmask 255.255.255.0 brd 192.168.0.255"
     routes_eth0="default via 192.168.0.1"
   cd /etc/init.d 
   ln -s net.lo net.eth0 
   rc-update add net.eth0 default

23 emerge --ask app-admin/sysklogd

   rc_update add sysklogd default
   rc_update add sshd default

24 configurar grub2 https://wiki.gentoo.org/wiki/GRUB_on_Open_Firmware_(PowerPC)

   *este aparte del particionado es uno de los puntos chungos, me costó varios intentos 

25 useradd -m -G users,wheel,audio -s /bin/bash larry (larry o el nombre que quieras ponerle) 

   passwd larry
   
