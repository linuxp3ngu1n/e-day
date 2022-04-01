### Crear un cluster bare-metal con 3 Raspberry Pi:

#### Configurar el kernel en las 3 máquinas

Necesitamos habilitar los grupos c para que el kubelet funcione fuera de la caja. Para ello es necesario modificar el archivo de configuración cmdline.txt:

`sudo nano /boot/firmware/cmdline.txt`

Y agregue las siguientes opciones:

`cgroup_enable=memory cgroup_memory=1`

El contenido completo de cmdline.txt es:

`cgroup_memory=1 cgroup_enable=memory ip=<DIRECCION-IP>::<GATEWAY>:<MASCARA-DE-RED>:<HOSTNAME>:eth0:off`

Ejemplo:

`cgroup_memory=1 cgroup_enable=memory ip=192.168.105.251::192.168.105.1:255.255.255.0:k8s-01:eth0:off`

El hosname lo nombramos como k8s-0x, donde x va del 1 al 3, ya que tenemos 3 máquinas; `eth0` es el nombre de la interfaz a la cual queremos asignar la configuración de red y `off` sirve para apagar la autoconfiguración.

‎Ahora guarde el archivo en su editor y reinicie:‎

`sudo reboot`

