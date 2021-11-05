---
title: Integración de Azure Queue Storage con el conector de servicio
description: Integración de Azure Queue Storage en la aplicación con el conector de servicio
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: eec504cadbe4c039219ca58bd23b283c40d3785e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090772"
---
# <a name="integrate-azure-queue-storage-with-service-connector"></a>Integración de Azure Queue Storage con el conector de servicio

En esta página se muestran los tipos de autenticación admitidos y los tipos de cliente de Azure Queue Storage que usan el conector de servicio. Es posible que pueda conectarse igualmente a Azure Queue Storage en otros lenguajes de programación sin usar el conector de servicio. Esta página también muestra el nombre y el valor predeterminados de la variable de entorno (o configuración de Spring Boot) que se obtiene al crear la conexión de servicio. Puede obtener más información sobre la [convención de nomenclatura de variables de entorno de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Servicio de proceso admitido

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Tipos de autenticación y tipos de cliente admitidos

| Tipo de cliente | Identidad administrada asignada por el sistema | Identidad administrada asignada por el usuario | Secret/ConnectionString | Entidad de servicio |
| --- | --- | --- | --- | --- |
| .Net | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |
| Java | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |
| Java: Spring Boot | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |
| Node.js | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |
| Python | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |


## <a name="default-environment-variable-names-or-application-properties"></a>Nombres de variables de entorno o propiedades de aplicación predeterminados

### <a name="net-java-nodejs-python"></a>.NET, Java, Node.JS, Python

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_STORAGEQUEUE_CONNECTIONSTRING | Cadena de conexión de Queue Storage | `DefaultEndpointsProtocol=https;AccountName={accountName};AccountKey={****};EndpointSuffix=core.windows.net` |

**Identidad administrada asignada por el sistema**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_STORAGEQUEUE_RESOURCEENDPOINT | Punto de conexión de Queue Storage | `https://{StorageAccountName}.queue.core.windows.net/` |

**Identidad administrada asignada por el usuario**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_STORAGEQUEUE_RESOURCEENDPOINT | Punto de conexión de Queue Storage | `https://{storageAccountName}.queue.core.windows.net/` |
| AZURE_STORAGEQUEUE_CLIENTID | Id. de cliente | `{yourClientID}` |

**Entidad de seguridad de servicio**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_STORAGEQUEUE_RESOURCEENDPOINT | Punto de conexión de Queue Storage | `https://{storageAccountName}.queue.core.windows.net/` |
| AZURE_STORAGEQUEUE_CLIENTID | Id. de cliente | `{yourClientID}` |
| AZURE_STORAGEQUEUE_CLIENTSECRET | Secreto de cliente | `{yourClientSecret}` |
| AZURE_STORAGEQUEUE_TENANTID | El identificador de inquilino | `{yourTenantID}` |

### <a name="java---spring-boot"></a>Java: Spring Boot

**Secret/ConnectionString**

| Propiedades de la aplicación | Descripción | Valor de ejemplo |
| --- | --- | --- |
| azure.storage.account-name | Nombre de cuenta de Queue Storage | `{storageAccountName}` |
| azure.storage.account-key | Clave de cuenta de Queue Storage | `{yourSecret}` |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Service Connector, siga los tutoriales que se enumeran a continuación.

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)
