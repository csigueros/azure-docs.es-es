---
title: Configuración de la entrada HTTPS en la versión preliminar de Azure Container Apps
description: Habilitación de puntos de conexión públicos y privados en la aplicación con Azure Container Apps
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: e4b465b593fadcacce59a14c9fbced0780c252a9
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578299"
---
# <a name="set-up-https-ingress-in-azure-container-apps-preview"></a>Configuración de la entrada HTTPS en la versión preliminar de Azure Container Apps

Azure Container Apps permite exponer la aplicación de contenedor a la web pública mediante la habilitación de la entrada. Al habilitar la entrada, no es necesario crear un Azure Load Balancer, una dirección IP pública ni ningún otro recurso de Azure para habilitar las solicitudes HTTPS entrantes.

Con la entrada habilitada, la aplicación de contenedor presenta las siguientes características:

- Admite la terminación TLS
- Admite HTTP/1.1 y HTTP/2
- Los puntos de conexión siempre usan TLS 1.2, finalizado en el punto de entrada
- Los puntos de conexión siempre exponen los puertos 80 (para HTTP) y 443 (para HTTPS).
  - De forma predeterminada, las solicitudes HTTP al puerto 80 se redirigen automáticamente a HTTPS en 443.

## <a name="configuration"></a>Configuración

La entrada es una configuración para toda la aplicación. Los cambios en la configuración de entrada se aplican a todas las revisiones simultáneamente y no generan nuevas revisiones.

La sección de configuración de entrada tiene el formato siguiente:

```json
{
  ...
  "configuration": {
      "ingress": {
          "external": true,
          "targetPort": 80,
          "transport": auto
      }
  }
}
```

Las siguientes opciones están disponibles al configurar la entrada:

| Propiedad | Descripción | Valores | Obligatorio |
|---|---|---|---|
| `external` | La dirección IP de entrada y el nombre de dominio completo pueden ser visibles externamente a Internet o internamente dentro de una red virtual. |`true` para la visibilidad externa, `false` para la visibilidad interna (valor predeterminado) | Yes |
| `targetPort` | El puerto en el que escucha el contenedor para las solicitudes entrantes. | Establezca este valor en el número de puerto que usa el contenedor. El punto de conexión de entrada de la aplicación siempre se expone en el puerto `443`. | Yes |
| `transport` | Puede usar HTTP/1.1 o HTTP/2, o bien puede establecerlo para detectar automáticamente el tipo de transporte. | `http` para HTTP/1, `http2` para HTTP/2, `auto` para detectar automáticamente el tipo de transporte (valor predeterminado) | No |
| `allowInsecure` | Permite el tráfico no seguro a la aplicación de contenedor. | `false` (valor predeterminado), `true`<br><br>Si se establece en `true`, las solicitudes HTTP al puerto 80 no se redirigen automáticamente al puerto 443 mediante HTTPS, lo que permite conexiones no seguras. | No |

> [!NOTE]
> Para deshabilitar la entrada de la aplicación, puede omitir completamente la propiedad de configuración `ingress`.

## <a name="ip-addresses-and-domain-names"></a>Direcciones IP y nombres de dominio

Con la entrada habilitada, a la aplicación se le asigna un nombre de dominio completo (FQDN). El nombre de dominio toma las formas siguientes:

|Configuración de visibilidad de entrada | Nombre de dominio completo |
|---|---|
| Externo | `<APP_NAME>.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io`|
| Interno | `<APP_NAME>.internal.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io` |

El entorno de Container Apps tiene una única dirección IP pública para las aplicaciones con visibilidad de entrada `external` y una única dirección IP interna para las aplicaciones con visibilidad de entrada `internal`. Por lo tanto, todas las aplicaciones dentro de un entorno de Container Apps con visibilidad de entrada `external` comparten una única dirección IP pública. De una forma parecida, todas las aplicaciones dentro de un entorno de Container Apps con visibilidad de entrada `internal` comparten una única dirección IP interna. El tráfico HTTP se enruta a aplicaciones concretas según el FQDN del encabezado de host.

Puede obtener acceso al identificador único del entorno consultando la configuración del entorno.

[!INCLUDE [container-apps-get-fully-qualified-domain-name](../../includes/container-apps-get-fully-qualified-domain-name.md)]

> [!div class="nextstepaction"]
> [Administración del escalado](scale-app.md)
