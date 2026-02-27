Si algún dia quieres cambiar la bootanimation de nuevo solo haz esto:

Antes que todo, asegurate que en /etc/mkinitfs/mkinitfs.conf, la linea modules esté antes que la de features, y esto es lo que deberia contener:

Antes tienes que instalar los drivers especificos para tu GPU, en mi caso el driver es `i915` ya que mi GPU es Intel pero si es NVIDIA sería `noveau`, y con AMD sería `amdgpu` : `modules="i915"`

Luego en features tiene que decir esto, sino agregale mas opciones: `features="base kms plymouth ata ide scsi usb virtio ext4"`

Recuerda instalar mkinitfs y dracut de una vez, haces `mkinitfs $(uname -r)`

Sigue el grub, si no tienes grub, puedes saltarte los pasos

En la linea del grub que es GRUB_CMDLINE_LINUX_DEFAULT, asegurate que esté quiet y splash, pero yo recomiendo que esté así: `GRUB_CMDLINE_LINUX_DEFAULT="quiet splash rw fbcon=nodefer video=1280x1024@60 loglevel=1 vt.global_cursor_default=0 plymouth.ignore-serial-consoles"`

Si es posible, despues del cambio haz `sudo update-grub ` de lo contrario, haz sudo `sudo grub-mkconfig -o /boot/grub/grub.cfg`

Estoy seguro que a partir de ahora va a funcionar plymouth, necesitarás instalarlo obviamente.

Ya puedes descargar tu tema de por ejemplo pling o el que quieras, sigue estos pasos despues de descomprimirlo:

Primero mueve tu carpeta con la bootanimation a los temas de plymouth: `sudo mv '/home/$USER/tucarpetaconeltema' /usr/share/plymouth/themes/`

Segundo, verifica que si esté ahi: `plymouth-set-default-theme -l`

Tercero, haz que tu tema sea el por defecto: `sudo plymouth-set-default-theme tutema`

Y por ultimo, tienes que tener dracut para esto:`sudo dracut --force --add plymouth /boot/initramfs-lts $(uname -r)`

Deberia funcionar si ya configuraste tanto como el initrd y el default/grub.

Espero haber sido de ayuda.
