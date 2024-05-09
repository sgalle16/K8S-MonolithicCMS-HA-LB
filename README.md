# CMS Monolítico con Balanceo de Carga y Datos Distribuidos en EKS

## ST0263 Tópicos Especiales en Telemática

### Estudiante: Santiago Gallego Quintero, sgalle16@eafit.edu.co

### Estudiante: Jairo Esteban Santacruz Hernandez, jsantac6@eafit.edu.co

### Profesor: Juan Carlos Montoya, jcmontoy@eafit.edu.co

## Reto 4: Despliegue de WebApp Monolítica en Clúster Kubernetes con AWS EKS

Este reto es una extensión del [reto anterior](https://github.com/sgalle16/MonolithicCMS-LB-DistributedData) con adicional que se realiza en un cluster de kubernetes con el servicio gestionado de Amazon Elastic Kubernetes Service (EKS). Este consiste en el despliegue de un sistema CMS WordPress utilizando EKS e integrando con EFS para almacenamiento persistente y escalable, junto con MySQL como base de datos, garantizando la escalabilidad y alta disponibilidad. Adicional, mediante la implementación un balanceador de carga con ingress, nginx y cert-manager para los certificados SSL para HTTPS.

## 1.1. Que aspectos cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)

- [x] **Cluster EKS**: Creación de cluster de Kubernetes con EKS
- [x] **Balanceo de Carga:** Implementación con Ingress y Nginx como balanceador de carga para distribuir el tráfico entre los services de wordpress.
- [x] **Seguridad con SSL:** Uso de certificados SSL gestionados a través de Let's Encrypt para el dominio configurado.
- [x] **Base de Datos Centralizada:** Utilización de MySQL desplegado dentro del clúster o como servicio administrado.
- [x] **Almacenamiento Persistente:** Uso de NFS en este caso el servicio de EFS en AWS para almacenamiento persistente, permitiendo la consistencia entre las réplicas de WordPress.
- [x] **Despliegue Automatizado:** Implementación completa utilizando manifiestos de Kubernetes.
- [x] **Despliegue de CMS WordPress:** Empleando la tecnología de contenedores dentro del cluster.
- [x] **Dominio Personalizado y HTTPS:** Despliegue en AWS con propio dominio con certificado SSL.
- [x] **Amazon EFS**: Utilizado para proporcionar un sistema de archivos NFS que se escala automáticamente.

## 1.2. Que aspectos NO cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)

- Se cumplio con todo lo propuesto por el profesor.

## Requisitos Funcionales y No Funcionales

El sistema fue diseñado para cumplir con los siguientes requisitos funcionales y no funcionales:

- **Requisitos Funcionales:**

  - **Despliegue de WordPress en Contenedores:** Utilización de contenedores Docker para desplegar múltiples instancias de WordPress, permitiendo un escalado fácil y rápido.
  - **Gestión de Tráfico HTTPS con Balanceador de Carga:** Implementación de Ingress con Nginx que distribuye el tráfico de manera inteligente entre las instancias de aplicación basadas en la carga, la salud del pod y otros criterios.
  - **Base de Datos con Alta Disponibilidad:** Despliegue de MySQL dentro del clúster con configuración para alta disponibilidad y respaldos.
  - **Almacenamiento Persistente con EFS:** Configuración de Persistent Volume Claims (PVCs) que utilizan Amazon EFS para proporcionar un sistema de archivos compartido NFS que es resiliente y escalable.
  - **Automatización de Certificados SSL:** Configuración de Cert-Manager para automatizar la obtención y renovación de certificados SSL de Let's Encrypt.

- **Requisitos No Funcionales:**
  - **Alta Disponibilidad:** Implementación de múltiples instancias de WordPress para asegurar la disponibilidad del servicio, en este caso se desplejaron 2 replicas dentro del cluster.
    - **Aplicación WordPress:** Despliegue de múltiples réplicas de pods de WordPress distribuidos a través de diferentes nodos para asegurar disponibilidad y tolerancia a fallos.
    - **Base de Datos:** Despliegue de MySQL dentro del clúster gestionado por kubernetes con configuración para alta disponibilidad.
    - **Grupos de Nodos:** Utilización de grupos de nodos de Kubernetes distribuidos en varias zonas de disponibilidad para garantizar que el servicio siga funcionando incluso si una zona disponibilidadestá comprometida.
  - **Escalabilidad:** Capacidad de añadir más réplicas de pods según la demanda y de gestionar automáticamente la escalabilidad del clúster a través de Amazon EKS y auto-scalers.
  - **Rendimiento:** El balanceo de carga asegura una gestión eficiente del tráfico y la utilización de los recursos.
  - **Seguridad:** Encriptación SSL/TLS para la transmisión segura de datos y uso de HTTPS .
    - **Red:** Configuración de políticas de red para controlar el acceso a los pods y servicios dentro del clúster.
    - **Datos:** Encriptación de datos en tránsito y en reposo, utilizando las capacidades de AWS y codificación de configuraciones de seguridad en Kubernetes.
  - **Mantenibilidad:** Uso de herramientas de orquestación de Kubernetes para simplificar el despliegue, la actualización y la gestión del ciclo de vida de todas las aplicaciones, servicios y contenedores.

# 2. Diseño de Alto Nivel y Arquitectura

El sistema está diseñado para ser resiliente, escalable y seguro:

La arquitectura empleada incluye:

- **Amazon EKS (Elastic Kubernetes Service):** Clúster administrado de Kubernetes que simplifica la ejecución de Kubernetes en AWS.
- **Amazon EFS (Elastic File System):** Sistema de archivos NFS que se escala automáticamente para proporcionar almacenamiento compartido para las instancias de WordPress.
- **Ingress Controller (Nginx):** Utiliza Nginx para gestionar el balanceo de carga y y terminación SSL con directivas que aseguran el correcto enrutamiento del tráfico.
- **Pods de WordPress:** Múltiples réplicas de WordPress para garantizar la disponibilidad y la capacidad de manejar picos de tráfico.
- **Base de Datos:** MySQL como gestor de datos, desplegada dentro del clúster para mantener la integridad y disponibilidad de los datos.
- **Persistent Volume Claims (PVCs):** Configuraciones de almacenamiento que permiten almacenar datos de manera persistente y son utilizados por MySQL, las instancias de WordPress y el NFS.
- **Cert-Manager y Let's Encrypt:** Automatiza la gestión y renovación de certificados SSL utilizando Let's Encrypt como CA (Autoridad de Certificación).

# 3. Ambiente de Desarrollo y Técnico

### Kubernetes en Amazon EKS

Amazon EKS está configurado para automatizar tareas complejas como el parcheo, la escalabilidad y la administración del clúster, lo que permite centrarse en el desarrollo de aplicaciones en lugar de mantener la infraestructura.

### Almacenamiento con Amazon EFS

EFS se utiliza para almacenar los datos de WordPress y cualquier medio cargado, lo que permite que las instancias del pod de WordPress se escalen horizontalmente sin perder el acceso a sus archivos. EFS es ideal para aplicaciones que necesitan un sistema de archivos compartido accesible y consistente.

### Implementación de Ingress y Cert-Manager

El Ingress se configura con Nginx para manejar el balanceo de carga y la terminación SSL. Cert-Manager se utiliza para automatizar la creación, renovación y vinculación de certificados SSL de Let's Encrypt a los servicios, garantizando que todo el tráfico esté cifrado.

### Preparación del Ambiente

1. **Creación de la VPC:** Subnets publicas y privadas, en dos zonas de disponibilidad, Internet Gateway, Route Tables y Security Groups.
2. **Setup de EKS:** Se crea un clúster de EKS a través de la consola de AWS, y se configura utilizando `eksctl`.
3. **Despliegue de WordPress y MySQL:** Uso de manifiestos de Kubernetes para crear los deployments, services y PVCs necesarios.
4. **Despliegue de PVC con EFS:** Configuración y creación del sistema de archivo de EFS y creación de PVCs asociados para el almacenamiento persistente.
5. **Setup de Ingress Controller:** Despliegue del Nginx Ingress Controller usando Helm charts.
6. **Configuración de Cert-Manager:** Instalación y configuración para la automatización de certificados SSL.

## 4. Implementación y Despliegue

El despliegue se realiza mediante scripts de automatización que configuran el entorno EKS, despliegan los recursos de Kubernetes y configuran los servicios relacionados. El proceso está completamente documentado para garantizar la reproducibilidad y la facilidad de mantenimiento.
Para los detalles de cada uno de servicios, despliegues, pods y demás componentes consultar la documentación de cada uno:

- [Ingress controller Nginx](https://github.com/sgalle16/K8S-MonolithicCMS-LB-NFS/blob/master/ingress/README.md)
- [MySQL](https://github.com/sgalle16/K8S-MonolithicCMS-LB-NFS/blob/master/mysql/README.md)
- [WordPress](https://github.com/sgalle16/K8S-MonolithicCMS-LB-NFS/blob/master/ingress/README.md)

## Ambiente de Desarrollo y Herramientas Utilizadas

- **AWS EKS:** Clúster administrado para despliegue de contenedores.
- **AWS EFS:** Almacenamiento de archivos NFS administrado.
- **Docker:** Contenerización de la aplicación WordPress y MySQL.
- **Nginx:** Servidor web y balanceador de carga como parte del Ingress.
- **Helm:** Instalación y gestión de paquetes para Kubernetes.
- **Cert-Manager:** Gestión de certificados SSL.

_Acceso y URLs_

- **Dominio del Sitio Web:** [https://reto4.rentevo.site](https://reto4.rentevo.site)
- **Admin de WordPress:** [https://reto4.rentevo.site/wp-admin](https://reto4.rentevo.site/wp-admin)

## 5. Pantallazos

![reto4site](https://github.com/sgalle16/K8S-MonolithicCMS-LB-NFS/assets/14111169/66d977d7-9e1e-463c-b649-6dddda85b60d)
![httpsreto4site](https://github.com/sgalle16/K8S-MonolithicCMS-LB-NFS/assets/14111169/d48b417e-99b8-4e71-9bdf-55b1d8b8bb28)

## 6. Referencias

- [Amazon Elastic Kubernetes Service Documentation](https://docs.aws.amazon.com/eks/)
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Nginx Ingress Controller Documentation](https://kubernetes.github.io/ingress-nginx/)
- [Cert-Manager Documentation](https://cert-manager.io/docs/)
- [WordPress + MySQL en Kubernetes](https://www.youtube.com/watch?v=TnME3zam7Zo&t=295s)
- [Deploying WordPress and MySQL with Persistent Volumes](https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/)
- [Highly Available Wordpress on Kubernetes](https://matthewdavis.io/highly-available-wordpress-on-kubernetes/)
- [EFS CSI Driver on Kubernetes](https://github.com/kubernetes-sigs/aws-efs-csi-driver)
