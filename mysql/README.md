# MySQL en Kubernetes EKS

Este documento detalle y expone el proceso y los componentes utilizados para desplegar MySQL en un clúster de Kubernetes alojado en Amazon EKS. En lugar de optar por soluciones gestionadas como Amazon RDS, este enfoque permite un control completo sobre la configuración de la base de datos, proporcionando flexibilidad para adaptar la instancia a las necesidades específicas del entorno de Kubernetes y capacidad de ajustar la configuración del despliegue.

El despliegue se realiza a través de contenedores directamente manejados dentro del clúster, lo que facilita la integración con otros servicios y aplicaciones en Kubernetes.
Se emplea manifiestos de Kubernetes para configurar y gestionar todos los aspectos del ciclo de vida del servidor MySQL, desde su implementación hasta el manejo del almacenamiento persistente y la seguridad de las credenciales.

## Componentes

- **Deployment:** Controla la creación y actualización del pod de MySQL. Establece la configuración del contenedor, maneja las conexiones de red y especifica los volúmenes para el almacenamiento persistente.
- **Secret:** Gestiona las credenciales de acceso a la base de datos de forma segura usando un Secret de Kubernetes.
- **PersistentVolumeClaim:** Garantiza que los datos almacenados en la base de datos persistan entre reinicios y actualizaciones del pod, facilitando la gestión del estado en entornos de Kubernetes.

## Detalles de la Implementación

Se ha configurado un pod de MySQL utilizando un contenedor de la imagen oficial de MySQL 8.0, configurado a través de un `Deployment`. La configuración detalla específicamente:

### Servicio MySQL (Service)

- **ClusterIP(Headless Service)** : Se define un Service tipo ClusterIP con `clusterIP: None`, lo que crea un servicio sin una dirección IP asignada. Esto permite que las aplicaciones descubran y se conecten directamente a las instancias de pods MySQL. Esto es útil para configuraciones de bases de datos que requieren conexión directa sin un balanceador de carga intermedio.

### Despliegue (Deployment)

- **MySQL Pod:** El Deployment despliega y gestiona un pod que ejecuta MySQL 8.0. Utiliza variables de entorno para configurar el usuario y la contraseña (referenciadas desde el Secret), que se almacenan de forma segura en un Secret. El pod monta el PersistentVolumeClaim para garantizar que los datos de `/var/lib/mysql`. se conserven entre diferentes ciclos del pod.

### Almacenamiento Persistente (PersistentVolumeClaim) (PVC)

- **Storage Request:** Se configura un PersistentVolumeClaim con una solicitud de 20 GiB bajo el modo de acceso ReadWriteOnce, permitiendo que el volumen sea montado en lectura y escritura por un solo nodo. Este almacenamiento persistente es esencial para mantener los datos de MySQL seguros y disponibles, incluso a través de reinicios del pod.

### Secretos (Secret):

- **Mysql secret**: Se emplea un objeto Secret de Kubernetes para almacenar y gestionar de forma segura las credenciales de acceso a MySQL, evitando la exposición directa de información sensible. Las credenciales de MySQL se almacenan en un `Secret` para evitar la exposición de información sensible. Se utiliza la referencia `secretKeyRef` en el Deployment para pasar estas credenciales al contenedor de manera segura.

#### Comandos

Para desplegar y gestionar MySQL en Kubernetes, se utilizan los siguientes comandos:

```bash
kubectl apply -f mysql/manifiest/mysql-secret.yml
kubectl apply -f mysql/manifiest/mysql-deployment.yaml
```

o para aplicar todos los manifiesto estando en el directorio donde se encuentre:

```bash
kubectl apply -k .
```

Para verificar el estado del `Service`, `Deployment`, `Pod`, y `PVC`, respectivamente se pueden utilizar:

```bash
kubectl get svc
kubectl get deployments
kubectl get pods
kubectl get pvc
kubectl describe pvc
```

Para acceder a los logs del pod de MySQL y realizar un seguimiento o diagnóstico:

```bash
kubectl logs <nombre-del-pod-mysql>
```

### Seguridad y Gestión

- Persistent Volumes: Utiliza PersistentVolumeClaim para garantizar la integridad y la seguridad de los datos de MySQL, permitiendo su recuperación en caso de interrupciones del servicio.
- Secrets: Las credenciales de acceso a la base de datos se manejan a través de un objeto Secret, proporcionando una capa adicional de seguridad al evitar la exposición directa de información sensible.
