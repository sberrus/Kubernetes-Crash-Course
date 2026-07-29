Claro. De hecho, te lo estructuraría como una documentación técnica de referencia, eliminando detalles repetitivos y centrándome en la arquitectura, los conceptos y las buenas prácticas.

---

# Gestión del tráfico de entrada en Kubernetes

## Introducción

En Kubernetes, el tráfico procedente de Internet puede gestionarse mediante diferentes componentes, cada uno con responsabilidades distintas. Los más habituales son los **Services de tipo LoadBalancer** y los **Ingress Controllers**.

Es importante entender que estos componentes **no compiten entre sí**, sino que normalmente trabajan juntos.

Arquitectura típica:

```text
                    Internet
                        │
                        ▼
              Cloud Load Balancer
                        │
                        ▼
               Ingress Controller
                        │
            ┌───────────┴───────────┐
            ▼                       ▼
      ClusterIP Service      ClusterIP Service
            │                       │
            ▼                       ▼
          Pods                    Pods
```

---

# Service tipo LoadBalancer

Un **Service** de tipo `LoadBalancer` solicita al proveedor Cloud (AWS, Azure, GCP, etc.) la creación de un balanceador de carga público.

Su función es exponer un Service interno del clúster a Internet.

Ejemplo:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: api

spec:

  type: LoadBalancer

  selector:
    app: api

  ports:
  - port: 80
    targetPort: 8080
```

Características:

* Balanceo de tráfico TCP/UDP (L4).
* IP pública.
* Alta disponibilidad proporcionada por el proveedor Cloud.
* Health Checks automáticos.
* Muy sencillo de configurar.

Limitaciones:

* Un LoadBalancer por aplicación.
* Coste elevado cuando existen muchos servicios.
* No permite routing por host o path.

---

# Ingress

Un **Ingress** permite publicar múltiples aplicaciones utilizando una única IP pública.

No implementa ninguna lógica por sí mismo; simplemente define reglas de enrutamiento.

Ejemplo:

```text
api.pepito.com      → API
grafana.pepito.com → Grafana
auth.pepito.com    → Auth
```

o

```text
pepito.com/api     → API
pepito.com/admin   → Admin
pepito.com/docs    → Documentación
```

El componente que realmente implementa estas reglas es el **Ingress Controller**.

---

# Ingress Controller

El Ingress Controller es una aplicación desplegada dentro del clúster que observa continuamente los recursos de Kubernetes y genera automáticamente la configuración del proxy (NGINX, Traefik, HAProxy, etc.).

Arquitectura:

```text
                Internet
                    │
                    ▼
          Cloud LoadBalancer
                    │
                    ▼
        Service (LoadBalancer)
                    │
                    ▼
      Ingress Controller (Deployment)
        │          │           │
        ▼          ▼           ▼
      Pod 1      Pod 2      Pod 3
                    │
                    ▼
          ClusterIP Services
                    │
                    ▼
                  Pods
```

El controlador observa continuamente:

* Ingress
* Services
* EndpointSlices
* Secrets TLS
* ConfigMaps
* IngressClass

Cuando alguno cambia, regenera automáticamente la configuración del proxy sin reiniciar el servicio.

---

# Routing por subdominios

Ejemplo:

```text
api.pepito.com
grafana.pepito.com
auth.pepito.com
admin.pepito.com
```

Cada hostname apunta a un Service distinto.

Ventajas:

* Muy limpio.
* Escalable.
* Recomendado para microservicios.

---

# Routing por paths

Ejemplo:

```text
pepito.com/api

pepito.com/auth

pepito.com/admin

pepito.com/docs
```

Todas las aplicaciones comparten el mismo dominio.

Es especialmente útil cuando:

* Solo se dispone de un dominio.
* Se desea mantener una única URL pública.

---

# Routing basado en Query Parameters

Los Ingress Controllers **no realizan routing utilizando query parameters**.

No pueden distinguir entre:

```text
/app?id=10

/app?id=20
```

El routing puede realizarse mediante:

* Host
* Path
* Método HTTP
* Puerto
* TLS (SNI)
* Cabeceras HTTP (según el controlador)

Los parámetros de la URL normalmente son procesados por la propia aplicación.

---

# Routing mediante cabeceras HTTP

Es posible dirigir tráfico utilizando cabeceras.

Ejemplo:

```text
X-Version: beta
```

↓

```
Servicio Beta
```

Mientras que:

```text
X-Version: stable
```

↓

```
Producción
```

Este mecanismo suele utilizarse para:

* Canary Deployments.
* Versionado.
* Pruebas internas.
* Multi-tenancy.

---

# Gestión del TLS

## TLS en LoadBalancer

El certificado puede instalarse directamente en el balanceador.

```text
Cliente HTTPS

↓

LoadBalancer

↓

HTTP

↓

Pods
```

También puede configurarse TLS extremo a extremo.

---

## TLS en Ingress

Lo habitual es terminar TLS en el Ingress Controller.

```text
Cliente HTTPS

↓

Ingress Controller

↓

HTTP interno

↓

Servicios
```

Los certificados se almacenan en Secrets.

La solución recomendada es utilizar **cert-manager** para renovar automáticamente los certificados mediante Let's Encrypt o una CA corporativa.

---

# Autenticación

El Ingress Controller puede encargarse de la autenticación antes de que la petición llegue a la aplicación.

Tipos habituales:

## Basic Authentication

Protección mediante usuario y contraseña almacenados en un Secret.

Uso habitual:

* Grafana
* Prometheus
* Dashboards internos

---

## OAuth2

Integración con:

* Google
* GitHub
* Microsoft

Normalmente mediante OAuth2 Proxy.

---

## OpenID Connect (OIDC)

Integración con:

* Keycloak
* Azure AD
* Okta
* Auth0

Es la solución recomendada en entornos empresariales.

---

## JWT

Validación de tokens Bearer antes de acceder al backend.

Muy utilizado en APIs REST.

---

## mTLS

Autenticación mediante certificados cliente.

Muy recomendable para APIs internas.

---

## Restricción por IP

Permite limitar el acceso a determinados rangos IP.

Muy útil para paneles administrativos.

---

## External Authentication

Delega completamente la autenticación en un servicio externo.

Ejemplo:

```text
Cliente

↓

Ingress

↓

Servicio de autenticación

↓

Aplicación
```

---

# Gestión de cabeceras HTTP

El Ingress puede modificar completamente las cabeceras.

Permite:

* Añadir cabeceras.
* Eliminar cabeceras.
* Reescribir cabeceras.
* Propagar cabeceras entre servicios.

Cabeceras habituales:

* X-Request-ID
* X-Correlation-ID
* X-Forwarded-For
* X-Forwarded-Proto

---

# Cabeceras de seguridad

Es recomendable configurar al menos:

* Strict-Transport-Security (HSTS)
* X-Frame-Options
* X-Content-Type-Options
* Content-Security-Policy
* Referrer-Policy
* Permissions-Policy

Además, se recomienda ocultar:

```
Server

X-Powered-By
```

para evitar exponer información sobre la infraestructura.

---

# Control del tráfico

Los Ingress Controllers permiten aplicar numerosas políticas.

## Rate Limiting

Limita el número de peticiones por segundo.

Protege frente a:

* Fuerza bruta.
* Bots.
* Ataques DoS simples.

---

## Connection Limiting

Limita el número de conexiones simultáneas por cliente.

---

## Timeouts

Configuración de:

* Proxy Connect Timeout
* Proxy Read Timeout
* Proxy Send Timeout

Evitan conexiones abiertas indefinidamente.

---

## Tamaño máximo de subida

Permite limitar el tamaño de los ficheros enviados.

---

## URL Rewrite

Reescritura transparente de rutas.

Ejemplo:

```
/app/login

↓

/login
```

---

## Sticky Sessions

Mantiene la sesión del usuario en el mismo Pod mediante cookies.

Actualmente se recomienda utilizar aplicaciones stateless siempre que sea posible.

---

# Configuración del Ingress Controller

El controlador suele desplegarse mediante Helm.

Componentes principales:

```text
Ingress Controller

├── Deployment
├── Service
├── ConfigMap
├── ServiceAccount
├── ClusterRole
├── Admission Webhook
└── Secrets
```

Los parámetros globales suelen configurarse mediante un ConfigMap.

Ejemplos:

* Versiones TLS permitidas.
* Timeouts.
* Tamaño máximo de subida.
* KeepAlive.
* Server Tokens.
* Forwarded Headers.

Cada recurso Ingress puede sobrescribir parte de la configuración mediante anotaciones.

Orden habitual de precedencia:

```text
Helm Values

↓

ConfigMap Global

↓

Configuración del Ingress

↓

Annotations

↓

Snippets (si están habilitados)
```

---

# Alta Disponibilidad (HA)

Para producción se recomienda:

* Mínimo 3 réplicas del Ingress Controller.
* Distribución entre nodos mediante Pod Anti-Affinity.
* Distribución entre Availability Zones mediante Topology Spread Constraints.
* Pod Disruption Budget para evitar interrupciones durante actualizaciones.
* Load Balancer distribuido entre varias zonas.
* Health Checks configurados correctamente.
* Readiness y Liveness Probes.
* Varias réplicas de cada aplicación.

Arquitectura recomendada:

```text
                 Internet
                     │
                     ▼
          Cloud Load Balancer (HA)
                     │
         ┌───────────┴───────────┐
         ▼                       ▼
  Ingress Controller      Ingress Controller
         │                       │
         └───────────┬───────────┘
                     ▼
             ClusterIP Services
         ┌─────────┼─────────┐
         ▼         ▼         ▼
       API      Frontend   Auth
         │         │         │
      Pods distribuidos entre varias zonas
```

---

# Recomendaciones mínimas de seguridad

Antes de publicar aplicaciones en Internet es recomendable configurar como mínimo:

| Configuración                            | Recomendación    |
| ---------------------------------------- | ---------------- |
| TLS 1.2 / TLS 1.3                        | Obligatorio      |
| Redirección HTTP → HTTPS                 | Sí               |
| HSTS                                     | Sí               |
| Rate Limiting                            | Sí               |
| Connection Limiting                      | Sí               |
| Timeouts                                 | Sí               |
| Cabeceras de seguridad                   | Sí               |
| Eliminar cabeceras Server                | Sí               |
| Restricción IP para paneles internos     | Sí               |
| JWT u OIDC para APIs                     | Recomendado      |
| cert-manager                             | Recomendado      |
| Network Policies                         | Recomendado      |
| Varias réplicas del Ingress              | Obligatorio      |
| Pod Anti-Affinity                        | Recomendado      |
| Topology Spread                          | Recomendado      |
| Pod Disruption Budget                    | Recomendado      |
| Observabilidad (logs, métricas y trazas) | Recomendado      |
| WAF delante del Ingress                  | Muy recomendable |

---

# Componentes alternativos

Además de Ingress, Kubernetes dispone de otras soluciones para gestionar el tráfico:

| Componente                    | Uso principal                                                                       |
| ----------------------------- | ----------------------------------------------------------------------------------- |
| Service tipo LoadBalancer     | Exposición L4 de servicios                                                          |
| Gateway API                   | Evolución moderna de Ingress con mayor flexibilidad                                 |
| NGINX Ingress                 | Reverse Proxy L7                                                                    |
| Traefik                       | Reverse Proxy e Ingress Controller                                                  |
| HAProxy Ingress               | Alto rendimiento                                                                    |
| Kong                          | API Gateway con autenticación y plugins                                             |
| Envoy Gateway                 | Gateway basado en Envoy                                                             |
| Service Mesh (Istio, Linkerd) | Gestión avanzada del tráfico entre microservicios (mTLS, observabilidad, políticas) |

## Conclusión

En una arquitectura Kubernetes moderna, la combinación recomendada para exponer aplicaciones de forma segura y altamente disponible suele ser:

```text
Internet
    │
Cloud Load Balancer (HA)
    │
Ingress Controller (3 o más réplicas)
    │
ClusterIP Services
    │
Pods distribuidos entre múltiples nodos y zonas
```

Sobre esta base es recomendable añadir:

* TLS gestionado automáticamente mediante `cert-manager`.
* Políticas de red (`NetworkPolicy`).
* Autenticación centralizada (OIDC/JWT).
* Rate limiting y control de conexiones.
* Cabeceras HTTP de seguridad.
* Observabilidad (logs, métricas y trazas).
* WAF para proteger aplicaciones expuestas a Internet.

Esta arquitectura ofrece un equilibrio entre **alta disponibilidad, seguridad, escalabilidad y facilidad de operación**, y constituye uno de los patrones más utilizados en despliegues de Kubernetes en producción.
