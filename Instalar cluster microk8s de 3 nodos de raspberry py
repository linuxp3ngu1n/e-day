cluster GKE e-day black friday in 3 Raspberry Pi

https://microk8s.io/docs

Instalar MicroK8s en las 3 Raspberry Pi:

https://ubuntu.com/tutorials/how-to-kubernetes-cluster-on-raspberry-pi#4-installing-microk8s
Installing MicroK8s

Necesitamos habilitar los grupos c para que el kubelet funcione fuera de la caja. Para ello es necesario modificar el archivo de configuración /boot/firmware/cmdline.txt:‎

sudo nano /boot/firmware/cmdline.txt
‎Y agregue las siguientes opciones:‎

cgroup_enable=memory cgroup_memory=1

‎Ejemplo de la línea completa para esta raspberry pi en particular se ve así:‎

cgroup_enable=memory cgroup_memory=1 net.ifnames=0 dwc_otg.lpm_enable=0 console=ttyAMA0,115200 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline rootwait


‎Ahora guarde el archivo en su editor y reinicie:‎

sudo reboot
‎Una vez hecho esto, ahora podemos instalar el complemento MicroK8s:‎

sudo snap install microk8s --classic


‎Ahora que tiene MicroK8s instalados en todas las placas, elija una para ser el nodo maestro de su clúster.‎


‎En el elegido, ejecute el siguiente comando:‎

sudo microk8s.add-node
‎Este comando generará una cadena de conexión en forma de .‎<master_ip>:<port>/<token>

‎Agregar un nodo‎
‎Ahora, debe ejecutar el comando desde otro Pi que desee agregar al clúster:‎join

microk8s.join <master_ip>:<port>/<token>

‎Por ejemplo:‎

microk8s.join 10.55.60.14:25000/JHpbBYMIevZSAMnmjMHmFwanrOYCWZLu

‎Debería poder ver el nuevo nodo en unos segundos en el maestro con el siguiente comando:‎

microk8s.kubectl get node

Para cada nuevo nodo, debe ejecutar el comando en el maestro, copiar la salida y luego ejecutarlo en la hoja.

‎microk8s.add-nodemicrok8s.join <master node output>

Comandos básicos de microk8s:

‎El comando iniciará todos los servicios de Kubernetes habilitados: ‎startmicrok8s.start
‎El comando le dará el estado de los servicios: ‎inspectmicrok8s.inspect
‎El comando detendrá todos los servicios de Kubernetes: ‎stopmicrok8s.stop
‎Puede habilitar fácilmente los complementos de Kubernetes, por ejemplo. para habilitar "kubedns": ‎microk8s.enable dns
‎Para obtener el estado del clúster: ‎microk8s.kubectl cluster-info
