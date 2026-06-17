# Como establecer y hacer funcionar plymouth en Alpine Linux y derivados

Se que no soy muy famoso pero mi guia es sencilla, como dice el titulo, es sobre hacer funcionar plymouth en Alpine Linux y derivados.

**Disclaimer:** Recuerda que eres el dueño de tu sistema, por lo tanto eres responsable bajo cualquier cambio que hagas, se que lo harás bien xd.


**1)**

Si no tienes `sudo` puedes usar `doas`

Antes que nada, instala los paquetes necesarios: `sudo apk add plymouth plymouth-themes pax-utils dracut mkinitfs mesa-dri-gallium mesa-va-gallium mesa-gl`  

Ademas de los drivers de video:

Para **Intel** solo instala: `xf86-video-intel linux-firmware-intel`

Para **AMD**: `xf86-video-amdgpu` 
Y dependiendo de tu setup instalas: `linux-firmware-amd` `linux-firmware-amdgpu` o `linux-firmware-amdnpu`

Para **NVIDIA**: `xf86-video-nouveau libdrm mesa-vulkan-nouveau linux-firmware-nvidia`

Pero solo instala el driver necesario para tu GPU sino pueden haber conflictos de drivers


**2)**

Edita `/etc/mkinitfs/mkinitfs.conf`

Asegurate que la linea `modules` **(Puedes añadirla si no está)** esté antes que la de `features`, y esto es lo que deberia contener:

En mi caso el driver es `i915` ya que mi GPU es Intel, pero si es NVIDIA sería `nouveau`, y con AMD sería `amdgpu`:

Luego en `features` tiene que decir esto, sino agregale mas opciones: `features="base kms plymouth ata ide scsi usb virtio ext4"`

Ejemplo de mi pc:

**modules="i915"**

**features="base kms plymouth ata ahci ide scsi usb virtio ext4"**


Recuerda instalar mkinitfs y dracut si no los tienes, despues de haber instalado ambos haces `sudo mkinitfs $(uname -r)`


**2)**

Sigue el grub, si no tienes grub, puedes saltarte los pasos

Edita `/etc/deafult/grub`

En la linea del grub que es `GRUB_CMDLINE_LINUX_DEFAULT` **(Como digo, si no está la puedes añadir)**, asegurate que esté `quiet` y `splash`, pero yo recomiendo que esté así: `GRUB_CMDLINE_LINUX_DEFAULT="quiet splash rw fbcon=nodefer loglevel=1 vt.global_cursor_default=0 plymouth.ignore-serial-consoles console=tty2 "`

Esto hace que: El booteo sea silencioso y aparezca plymouth, no aparezca informacion innecesaria del booteo ni tampoco el _ que parpadea, y al final los logs de tu init se mostraran en la tty2 para dejar la tty1 limpia

Si es posible, despues del cambio haz `sudo update-grub ` de lo contrario, haz sudo `sudo grub-mkconfig -o /boot/grub/grub.cfg`


**2)**

Estoy seguro que a partir de ahora va a funcionar plymouth, necesitarás instalarlo si no lo tienes obviamente.

Ya puedes descargar tu tema de por ejemplo desde pling o el que quieras, sigue estos pasos despues de descomprimirlo:

Primero mueve tu carpeta con la bootanimation a los temas de plymouth: `sudo mv '~/TU-CARPETA-CON-EL-TEMA' /usr/share/plymouth/themes/`

Segundo, verifica que si esté ahi: `plymouth-set-default-theme -l`

Tercero, haz que tu tema sea el por defecto: `sudo plymouth-set-default-theme TU-TEMA`

Puede variar si tu kernel es lts o no, compruebalo con `ls /boot/initramfs*` (No uses el virtual si no lo estas corriendo en una VM) y si es necesario cambia la linea en `/boot/initramfs-lts` por el nombre de tu kernel como por ejemplo `/boot/initramfs-generic` y así

Y por ultimo, tienes que tener dracut para esto: `sudo dracut --force --add plymouth /boot/initramfs-lts $(uname -r)`

Por ultimo regenera la configuracion del grub: `sudo update-grub` o `sudo grub-mkconfig -o /boot/grub/grub.cfg`

Deberia funcionar si seguiste los pasos correctamente jajaja.

Espero haber sido de ayuda.
