# Ingress en Kubernetes EKS

Este documento describe la implementación de un Ingress Controller en Kubernetes, utilizando Nginx como balanceador de carga. El uso de un Ingress Controller en Kubernetes permite una gestión más eficiente del acceso externo a los servicios dentro del cluster, facilitando la configuración de reglas de enrutamiento, seguridad SSL/TLS y mucho más. Este enfoque centraliza muchas de las operaciones de red que normalmente se manejarían a nivel de servicio o pod.

## Componentes

- **Ingress Controller**: Utiliza Nginx como controlador de ingreso para manejar las solicitudes HTTP y HTTPS hacia los servicios dentro del clúster.
- **ClusterIssuer:** Configurado con Cert-Manager para automatizar la obtención y renovación de certificados TLS de Let's Encrypt.
- **Ingress Resource:** Define las reglas específicas de enrutamiento para dirigir el tráfico a los servicios apropiados, en este caso, a la aplicación WordPress.

## Detalles de la Implementación

### ClusterIssuer

- **Let's Encrypt ACME Server**: Utiliza la ACME v2 API de Let's Encrypt para la emisión de certificados.

### Ingress Resource

- **Annotations:** Utiliza anotaciones para especificar el reescritor de URL y el emisor del certificado.
- **IngressClassName:** Especifica que se utiliza Nginx como controlador de ingreso.
- **TLS:** Configura los detalles para el certificado TLS, incluyendo el nombre del secreto que almacena el certificado.
- **Rules:** Define las reglas para dirigir el tráfico entrante a los servicios, en este caso, direcciona todo el tráfico del dominio reto4.rentevo.site al servicio de WordPress.

#### Comandos

Para aplicar la configuración del Ingress y los recursos relacionados, se utilizan los siguientes comandos:

```bash
kubectl apply -f ingress/manifest/cluster-issuer.yaml
kubectl apply -f ingress/manifest/nginx-ingress.yaml
```

o para aplicar todos los manifiesto estando en el directorio donde se encuentre:

```bash
kubectl apply -k .
```

Para verificar el estado del `Service`, `Deployment`, `Pod`, y `PVC`, respectivamente se pueden utilizar:

```bash
kubectl get ingress
kubectl describe ingress nginx-ingress
kubectl get pods --namespace cert-manager
kubectl get certificates
```

### Seguridad y Gestión

- Certificados TLS Automatizados: Cert-Manager maneja automáticamente la renovación y aplicación de los certificados TLS, lo que garantiza que la comunicación hacia el sitio de WordPress sea segura.
- Aislamiento de Tráfico: El controlador de Ingress actúa como un punto de entrada unificado, permitiendo políticas de seguridad más estrictas y simplificando la administración del tráfico.
