---
title: Configuración de los nodos de Azure Kubernetes Service (AKS) con un proxy HTTP
description: Use la característica de configuración del proxy HTTP para los nodos de Azure Kubernetes Service (AKS).
services: container-service
author: nickomang
ms.topic: article
ms.date: 09/09/2021
ms.author: nickoman
ms.openlocfilehash: 43ee8a41ad6c487f5998760396b05a3ec56206d7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004756"
---
# <a name="http-proxy-support-in-azure-kubernetes-service-preview"></a>Compatibilidad del proxy HTTP en Azure Kubernetes Service (versión preliminar)

Los clústeres de Azure Kubernetes Service clústeres (AKS), ya sean implementados en una red virtual administrada o personalizada, tienen ciertas dependencias de salida necesarias para funcionar correctamente. Anteriormente, en entornos que requerían acceso a Internet para enrutar mediante servidores proxy HTTP, esto era un problema. Los nodos no tenían ninguna manera de arrancar la configuración, las variables de entorno y los certificados necesarios para acceder a los servicios de Internet.

Esta característica agrega compatibilidad con el proxy HTTP a los clústeres de AKS y expone una interfaz sencilla que los operadores del clúster pueden usar para proteger el tráfico de red requerido por AKS en entornos dependientes del proxy.

Algunas soluciones más complejas pueden requerir la creación de una cadena de confianza para establecer comunicaciones protegidas a través de la red. La característica también permite la instalación de una entidad de certificación de confianza en los nodos como parte del arranque de un clúster.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="limitations-and-other-details"></a>Limitaciones y otros detalles

Los siguientes escenarios **no** son compatibles:
- Diferentes configuraciones de proxy por grupo de nodos
- Actualización de la configuración del proxy después de la creación del clúster
- Autenticación de contraseña/usuario
- CA personalizadas para la comunicación del servidor de la API
- Clústeres basados en Windows
- Grupos de nodos que usan conjuntos de disponibilidad de máquinas virtuales (VMAS)

De manera predeterminada, *httpProxy*, *httpsProxy* y *trustedCa* no tienen ningún valor.

## <a name="prerequisites"></a>Prerrequisitos

* Suscripción a Azure. Si no tiene una suscripción a Azure, puede crear una [cuenta gratuita](https://azure.microsoft.com/free).
* [La CLI de Azure instalada](/cli/azure/install-azure-cli).

### <a name="register-the-httpproxyconfigpreview-preview-feature"></a>Registro de la característica en vista previa (GB) `HTTPProxyConfigPreview`

Para usar la característica, también debe habilitar la marca de característica `HTTPProxyConfigPreview` en la suscripción.

Registre la marca de la característica `HTTPProxyConfigPreview` con el comando [az feature register][az-feature-register], como se muestra en el siguiente ejemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "HTTPProxyConfigPreview"
```

Tarda unos minutos en que el estado muestre *Registrado*. Puede comprobar el estado de registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/HTTPProxyConfigPreview')].{Name:name,State:properties.state}"
```

Cuando haya terminado, actualice el registro del proveedor de recursos *Microsoft.ContainerService* con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="configuring-an-http-proxy-using-azure-cli"></a>Configuración de un proxy HTTP mediante la CLI de Azure 

El uso de AKS con un proxy HTTP se realiza durante la creación del clúster, mediante el comando [az aks create][az-aks-create] y pasando la configuración como un archivo JSON o YAML.

El esquema del archivo de configuración tiene el siguiente aspecto:

```json
"httpProxyConfig": {
    "httpProxy": "string",
    "httpsProxy": "string",
    "noProxy": [
        "string"
    ],
    "trustedCa&quot;: &quot;string"
}
```

`httpProxy`: dirección URL de proxy que se usará para crear conexiones HTTP fuera del clúster. El esquema de dirección URL debe ser `http`.
`httpsProxy`: dirección URL de proxy que se usará para crear conexiones HTTPS fuera del clúster. Si no se especifica, se usa `httpProxy` para las conexiones HTTP y HTTPS.
`noProxy`: lista de nombres de dominio de destino, dominios, direcciones IP u otros CIDR de red para excluir el proxy.
`trustedCa`: cadena que contiene el contenido del certificado de entidad de certificación alternativo `base64 encoded`. Por ahora, solo se admite el formato `PEM`. Otra cosa que hay que tener en cuenta es que, para la compatibilidad con los componentes basados en Go que forman parte del sistema k8s, el certificado DEBE admitir `Subject Alternative Names(SANs)` en lugar de los certificados de nombre común en desuso.

Entrada de ejemplo: tenga en cuenta que el certificado de entidad de certificación debe ser la cadena codificada en Base64 del contenido del certificado de formato PEM.

```json
"httpProxyConfig": { 
     "httpProxy": "http://myproxy.server.com:8080/", 
     "httpsProxy": "https://myproxy.server.com:8080/", 
     "noProxy": [
         "localhost",
         "127.0.0.1"
     ],
     "trustedCA": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUgvVENDQmVXZ0F3SUJB...b3Rpbk15RGszaWFyCkYxMFlscWNPbWVYMXVGbUtiZGkvWG9yR2xrQ29NRjNURHg4cm1wOURCaUIvCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0="
}
```

Cree un archivo y proporcione valores para *httpProxy*, *httpsProxy* y *noProxy*. Si el entorno lo requiere, proporcione también un valor para *trustedCa*. A continuación, para implementar un clúster, pase el nombre del archivo a través de la marca `http-proxy-config`.

```azurecli
az aks create -n $clusterName -g $resourceGroup --http-proxy-config aks-proxy-config.json
```

El clúster se inicializará con el proxy HTTP configurado en los nodos.

## <a name="configuring-an-http-proxy-using-azure-resource-manager-arm-templates"></a>Configuración de un proxy HTTP mediante plantillas de Azure Resource Manager (ARM)

La implementación de un clúster de AKS con un proxy HTTP configurado mediante una plantilla de ARM es sencilla. El mismo esquema usado para la implementación con la CLI existe en la definición `Microsoft.ContainerService/managedClusters` en las propiedades:

```json
"properties": {
    ...,
    "httpProxyConfig": {
        "httpProxy": "string",
        "httpsProxy": "string",
        "noProxy": [
            "string"
        ],
        "trustedCa": "string"
    }
}
```

En la plantilla, proporcione valores para *httpProxy*, *httpsProxy* y *noProxy*. Si es necesario, proporcione también un valor para *trustedCa*. Implemente la plantilla y el clúster debe inicializarse con el proxy HTTP configurado en los nodos.

## <a name="handling-ca-rollover"></a>Control de la sustitución de la entidad de certificación

Los valores de *httpProxy*, *httpsProxy* y *noProxy* no se pueden cambiar después de la creación del clúster. Sin embargo, para admitir la implementación de certificados de la entidad de certificación, el valor de *trustedCa* se puede cambiar y aplicar al clúster con el comando [az aks update][az-aks-update].

Por ejemplo, suponiendo que se haya creado un nuevo archivo con la cadena codificada en Base64 del nuevo certificado de la entidad de certificación denominado *aks-proxy-config-2.json*, la acción que se incluye a continuación actualizará el clúster:

```azurecli
az aks update -n $clusterName -g $resourceGroup --http-proxy-config aks-proxy-config-2.json
```

## <a name="next-steps"></a>Pasos siguientes
- Para obtener más información sobre los requisitos de red de los clústeres de AKS, consulte [Control del tráfico de salida de los nodos de clúster en AKS][aks-egress].


<!-- LINKS - internal -->
[aks-egress]: ./limit-egress-traffic.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register