
ADD-ONS
api-server
controller-manager
scheduler
kubelet
cni
kube-proxy
https://microk8s.io/docs/addons?_ga=2.216470485.271810771.1647107577-1541728565.1647107577


Habilitamos varios add-ons:

habilite el complemento CoreDNS:‎

microk8s enable dns


‎Alta disponibilidad (HA)‎
‎La alta disponibilidad se habilita automáticamente en MicroK8s para clústeres con tres o más nodos.‎


Hay tres componentes necesarios para un clúster de Kubernetes de alta disponibilidad:‎

1. ‎Debe haber más de un nodo disponible en cualquier momento.‎
2. ‎El plano de control debe ejecutarse en más de un nodo para que la pérdida de un solo nodo no deje inoperable el clúster.‎
3. ‎El estado del clúster debe estar en un almacén de datos que sea en sí mismo de alta disponibilidad.‎


‎Todos los nodos del clúster de alta disponibilidad ejecutan el plano de control maestro. Los tres roles de nodo son:

voters: replicar la base de datos, participar en la elección del‎ líder
‎standby‎‎: replicar la base de datos, no participar en la elección del‎ líder
spare‎‎: no replicar la base de datos, no participar en la elección del líder‎

Chequear el estado:
microk8s status
