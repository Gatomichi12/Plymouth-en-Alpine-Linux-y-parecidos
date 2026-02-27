# Como establecer y hacer funcionar plymouth en Alpine Linux y derivados

Se que no soy muy famoso pero mi guia es sencilla, como dice el titulo, es sobre hacer funcionar plymouth en Alpine Linux y derivados.

**Disclaimer:** Recuerda que eres el dueño de tu sistema, por lo tanto eres responsable bajo cualquier cambio que hagas, se que lo harás bien xd.

Antes que nada, instala los paquetes necesarios: `sudo apk add plymouth plymouth-themes pax-utils dracut mkinitfs mesa-dri-gallium mesa-va-gallium xf86-video-intel xf86-video-amdgpu`  

Asegurate que en /etc/mkinitfs/mkinitfs.conf, la linea `modules` esté antes que la de `features`, y esto es lo que deberia contener:

En mi caso el driver es `i915` ya que mi GPU es Intel pero si es NVIDIA sería `nouveau`, y con AMD sería `amdgpu` : `modules="i915"`

Luego en `features` tiene que decir esto, sino agregale mas opciones: `features="base kms plymouth ata ide scsi usb virtio ext4"`

Recuerda instalar mkinitfs y dracut si no los tienes, despues de haber instalado ambos haces `sudo mkinitfs $(uname -r)`

Sigue el grub, si no tienes grub, puedes saltarte los pasos

En la linea del grub que es `GRUB_CMDLINE_LINUX_DEFAULT`, asegurate que esté `quiet` y `splash`, pero yo recomiendo que esté así: `GRUB_CMDLINE_LINUX_DEFAULT="quiet splash rw fbcon=nodefer video=1280x1024@60 loglevel=1 vt.global_cursor_default=0 plymouth.ignore-serial-consoles"`

Puedes cambiar la resolucion, que en mi caso es `video=1280x1024@60` pero recomiendo que pongas la adecuada a tu pantalla.

Si es posible, despues del cambio haz `sudo update-grub ` de lo contrario, haz sudo `sudo grub-mkconfig -o /boot/grub/grub.cfg`

Estoy seguro que a partir de ahora va a funcionar plymouth, necesitarás instalarlo si no lo tienes obviamente.

Ya puedes descargar tu tema de por ejemplo desde pling o el que quieras, sigue estos pasos despues de descomprimirlo:

Primero mueve tu carpeta con la bootanimation a los temas de plymouth: `sudo mv '/home/$USER/tucarpetaconeltema' /usr/share/plymouth/themes/`

Segundo, verifica que si esté ahi: `plymouth-set-default-theme -l`

Tercero, haz que tu tema sea el por defecto: `sudo plymouth-set-default-theme tu-tema`

Y por ultimo, tienes que tener dracut para esto: `sudo dracut --force --add plymouth /boot/initramfs-lts $(uname -r)`

Deberia funcionar si ya configuraste tanto como el initrd y el default/grub.

Espero haber sido de ayuda.

Que lio me dio aprender a hacerlo pero quisiera compartir mis conocimientos con el mundo xd.
