# WordPress en Kubernetes EKS

Este documento detalle y expone los pasos y componentes seguidos para desplegar la aplicación WordPress en un entorno de Kubernetes administrado por Amazon EKS. Este enfoque manual implica configurar cada aspecto del servicio, desde el servidor web hasta la conexión con la base de datos MySQL y el almacenamiento persistente, solo se recurre a la solución gestionada externa de EFS para la gestión del NFS de archivos distribuidos. Se pone especial atención en asegurar que el almacenamiento de datos y la configuración sean gestionados eficientemente a través de manifiestos de Kubernetes.

## Componentes

- **Service**: Se configura un servicio de tipo ClusterIP que facilita la comunicación interna dentro del clúster sin exponer el servicio de WordPress directamente al tráfico externo. Este cambio es esencial dado que se utiliza un Ingress Controller para manejar el acceso externo.
- **Deployment:** Administra la creación de pods que ejecutan la imagen de WordPress, asegurando la disponibilidad y escalabilidad del servicio.
- **Secret:** Gestiona las credenciales de acceso a la base de datos de forma segura usando un Secret de Kubernetes.
- **PersistentVolumeClaim:** Utilizado para solicitar almacenamiento persistente en Amazon EFS, garantizando que los datos y archivos de WordPress permanezcan seguros entre reinicios de pods o escalados del servicio.
- **StorageClass:** Define una clase de almacenamiento que emplea el CSI driver de EFS para proporcionar el sistema de archivos necesario para WordPress.

## Detalles de la Implementación

Se ha configurado un pod de WordPress utilizando un contenedor de la imagen oficial de WordPress, configurado a través de un `Deployment`. La configuración detalla específicamente:

### Servicio WordPress (Service)

- **ClusterIP**: Este tipo de servicio no asigna una IP pública al servicio de WordPress, sino que permite la comunicación dentro del clúster. El tráfico externo es manejado por el Ingress Controller que redirige las solicitudes a los pods adecuados.

### Despliegue (Deployment)

- **WordPress Pods:** Los pods se configuran para ejecutar WordPress, conectándose a la base de datos MySQL utilizando variables de entorno que referencian Kubernetes Secrets de manera segura.
- **Environment Variables**: Configura la conexión a la base de datos por secrets de Kubernetes y otros parámetros esenciales de WordPress.

### Almacenamiento Persistente (PersistentVolumeClaim)

- **PersistentVolumeClaim (PVC):** Configurados para uso con Amazon EFS, proporcionan un almacenamiento fiable y de alta disponibilidad para los datos de WordPress.

#### Comandos

Para desplegar y gestionar WordPress en Kubernetes, se utilizan los siguientes comandos:

```bash
kubectl apply -f wp/manifest/wordpress-deployment.yaml
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
- Seguridad de Datos: Los datos de WordPress se almacenan en EFS, asegurando durabilidad y disponibilidad. Las credenciales están protegidas utilizando Secrets de Kubernetes.
- Escalabilidad y Alta Disponibilidad: La configuración de Ingress Controller y el uso de PVC garantizan que el sitio pueda escalar eficientemente y manejar aumentos de tráfico mientras permanece disponible.
- Secrets: Las credenciales de acceso a la base de datos se manejan a través de un objeto Secret, proporcionando una capa adicional de seguridad al evitar la exposición directa de información sensible.

