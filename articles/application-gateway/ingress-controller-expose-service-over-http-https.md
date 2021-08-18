---
title: Exponer un servicio de AKS a través de HTTP o HTTPS mediante Application Gateway
description: Este artículo ofrece información sobre cómo exponer un servicio de AKS a través de HTTP o HTTPS mediante Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2764624d4f29432c10e0e7aa3ab49bb5a678d5e3
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178459"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Exponer un servicio de AKS a través de HTTP o HTTPS mediante Application Gateway 

Estos tutoriales ayudan a ilustrar el uso de [los recursos de entrada de Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) para exponer un servicio de Kubernetes de ejemplo mediante [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) a través de HTTP o HTTPS.

## <a name="prerequisites"></a>Requisitos previos

- Haber instalado el gráfico de Helm `ingress-azure`.
  - [**Implementación Greenfield**](ingress-controller-install-new.md): si empieza desde cero, consulte estas instrucciones de instalación, en las que se describen los pasos para implementar un clúster de AKS con Application Gateway e instalar el controlador de entrada de Application Gateway en el clúster de AKS.
  - [**Implementación Brownfield**](ingress-controller-install-existing.md): si ya tiene un clúster de AKS y una instancia de Application Gateway, consulte estas instrucciones para instalar el controlador de entrada de Application Gateway en el clúster de AKS.
- Si desea usar HTTPS en esta aplicación, necesitará un certificado X509 y su clave privada.

## <a name="deploy-guestbook-application"></a>Implementación de la aplicación `guestbook`

La aplicación del libro de visitas es una aplicación canónica de Kubernetes que se compone de un front-end de interfaz de usuario Web, un back-end y una base de datos de Redis. De forma predeterminada, `guestbook` expone su aplicación a través de un servicio con el nombre `frontend` en el puerto `80`. Sin un recurso de entrada de Kubernetes, no se puede acceder al servicio desde fuera del clúster de AKS. Usaremos los recursos de entrada de la aplicación y la configuración para acceder a la aplicación a través de HTTP y HTTPS.

Para implementar la aplicación del libro de visitas, siga estas instrucciones.

1. Descargue `guestbook-all-in-one.yaml` de [aquí](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Implemente `guestbook-all-in-one.yaml` en el clúster de AKS mediante la ejecución de

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Ahora, la aplicación `guestbook` se ha implementado.

## <a name="expose-services-over-http"></a>Exponer servicios a través de HTTP

Para exponer la aplicación del libro de visitas, usaremos el siguiente recurso de entrada:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Esta entrada expondrá el servicio `frontend` de la implementación de `guestbook-all-in-one` como un back-end predeterminado de la instancia de Application Gateway.

Guarde el recurso de entrada anterior como `ing-guestbook.yaml`.

1. Implemente `ing-guestbook.yaml` mediante la ejecución de:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Compruebe el registro del estado de implementación del controlador de entrada.

Ahora la aplicación `guestbook` debe estar disponible. Para comprobarlo, visite la dirección pública de la instancia de Application Gateway.

## <a name="expose-services-over-https"></a>Exponer servicios a través de HTTPS

### <a name="without-specified-hostname"></a>Sin nombre de host especificado

Sin especificar el nombre de host, el servicio del libro de visitas estará disponible en todos los nombres de host que apunten a la instancia de Application Gateway.

1. Antes de implementar la entrada, debe crear un secreto de Kubernetes para hospedar el certificado y la clave privada. Puede crear un secreto de Kubernetes mediante la ejecución de

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Defina la siguiente entrada. En la entrada, especifique el nombre del secreto en la sección `secretName`.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Reemplace `<guestbook-secret-name>` en el recurso de entrada anterior por el nombre de su secreto. Almacene el recurso de entrada anterior en un archivo bajo el nombre de `ing-guestbook-tls.yaml`.

1. Implemente ing-guestbook-tls.yaml mediante la ejecución de

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Compruebe el registro del estado de implementación del controlador de entrada.

Ahora, la aplicación `guestbook` estará disponible en HTTP y HTTPS.

### <a name="with-specified-hostname"></a>Con nombre de host especificado

También puede especificar el nombre de host en la entrada para multiplexar las configuraciones y los servicios de TLS.
Al especificar el nombre de host, el servicio del libro de visitas solo estará disponible en el host especificado.

1. Defina la siguiente entrada.
    En la entrada, especifique el nombre del secreto en la sección `secretName` y reemplace el nombre de host en la sección `hosts` según corresponda.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Implemente `ing-guestbook-tls-sni.yaml` mediante la ejecución de

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Compruebe el registro del estado de implementación del controlador de entrada.

Ahora, la aplicación `guestbook` estará disponible en HTTP y HTTPS solo en el host especificado (`<guestbook.contoso.com>` en este ejemplo).

## <a name="integrate-with-other-services"></a>Integración con otros servicios

La entrada siguiente le permitirá agregar más rutas de acceso a esta entrada y redirigir esas rutas de acceso a otros servicios:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: </other/*>
        backend:
          serviceName: <other-service>
          servicePort: 80
       - backend:
          serviceName: frontend
          servicePort: 80
```
