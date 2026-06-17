# Como establecer y hacer funcionar plymouth en Alpine Linux y derivados

Se que no soy muy famoso pero mi guia es sencilla, como dice el titulo, es sobre hacer funcionar plymouth en Alpine Linux y derivados.

**Disclaimer:** Recuerda que eres el dueño de tu sistema, por lo tanto eres responsable bajo cualquier cambio que hagas, se que lo harás bien xd.

Si no tienes `sudo` puedes usar `doas`

Antes que nada, instala los paquetes necesarios: `sudo apk add plymouth plymouth-themes pax-utils dracut mkinitfs mesa-dri-gallium mesa-va-gallium xf86-video-intel xf86-video-amdgpu`  

Solo instala `xf86-video-intel` para tarjetas Intel, `xf86-video-amdgpu` para tarjetas AMD y `xf86-video-nouveau` para tarjetas NVIDIA (Recomiendo que instales tambien `libdrm` y `mesa-vulkan-nouveau`) Pero solo instala el driver necesario para tu GPU sino pueden haber conflictos de drivers

Asegurate que en /etc/mkinitfs/mkinitfs.conf, la linea `modules` esté antes que la de `features`, y esto es lo que deberia contener (Si no está, la puedes añadir) :

En mi caso el driver es `i915` ya que mi GPU es Intel pero si es NVIDIA sería `nouveau`, y con AMD sería `amdgpu` : `modules="i915"`

Ejemplo: modules="i915"

Luego en `features` tiene que decir esto, sino agregale mas opciones: `features="base kms plymouth ata ide scsi usb virtio ext4"`

Recuerda instalar mkinitfs y dracut si no los tienes, despues de haber instalado ambos haces `sudo mkinitfs $(uname -r)`

Sigue el grub, si no tienes grub, puedes saltarte los pasos

En la linea del grub que es `GRUB_CMDLINE_LINUX_DEFAULT` (Como digo, si no está la puedes añadir), asegurate que esté `quiet` y `splash`, pero yo recomiendo que esté así: `GRUB_CMDLINE_LINUX_DEFAULT="quiet splash rw fbcon=nodefer loglevel=1 vt.global_cursor_default=0 plymouth.ignore-serial-consoles console=tty2 "`

Esto hace que: EL booteo sea silencioso y aparezca plymouth, no aparezca informacion innecesaria del booteo ni tampoco el _ que parpadea, y al final el inicio se mostrara en la tty2 para dejar la tty1 limpia

Si es posible, despues del cambio haz `sudo update-grub ` de lo contrario, haz sudo `sudo grub-mkconfig -o /boot/grub/grub.cfg`

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
