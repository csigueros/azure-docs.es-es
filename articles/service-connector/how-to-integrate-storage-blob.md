---
title: Integración de Azure Blob Storage con el conector de servicio
description: Integración de Azure Blob Storage en la aplicación con el conector de servicio
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5b0fa624b21125ec6cb6a319e9526f1787565a7f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090773"
---
# <a name="integrate-azure-blob-storage-with-service-connector"></a>Integración de Azure Blob Storage con el conector de servicio

En esta página se muestran los tipos de autenticación admitidos y los tipos de cliente de Azure Blob Storage mediante el uso de Service Connector. Es posible que todavía pueda conectarse a Azure Blob Storage en otros lenguajes de programación sin usar Service Connector. Esta página también muestra el nombre y el valor predeterminados de la variable de entorno (o configuración de Spring Boot) que se obtiene al crear la conexión de servicio. Puede obtener más información sobre la [convención de nomenclatura de variables de entorno de Service Connector](concept-service-connector-internals.md).

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
| AZURE_STORAGEBLOB_CONNECTIONSTRING | Cadena de conexión de Blob Storage | `DefaultEndpointsProtocol=https;AccountName={accountName};AccountKey={****};EndpointSuffix=core.windows.net` |

**Identidad administrada asignada por el sistema**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_STORAGEBLOB_RESOURCEENDPOINT | Punto de conexión de Blob Storage | `https://{storageAccountName}.blob.core.windows.net/` |

**Identidad administrada asignada por el usuario**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_STORAGEBLOB_RESOURCEENDPOINT | Punto de conexión de Blob Storage | `https://{storageAccountName}.blob.core.windows.net/` |
| AZURE_STORAGEBLOB_CLIENTID | Id. de cliente | `{yourClientID}` |

**Entidad de seguridad de servicio**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_STORAGEBLOB_RESOURCEENDPOINT | Punto de conexión de Blob Storage | `https://{storageAccountName}.blob.core.windows.net/` |
| AZURE_STORAGEBLOB_CLIENTID | Id. de cliente | `{yourClientID}` |
| AZURE_STORAGEBLOB_CLIENTSECRET | Secreto de cliente | `{yourClientSecret}` |
| AZURE_STORAGEBLOB_TENANTID | El identificador de inquilino | `{yourTenantID}` |

### <a name="java---spring-boot"></a>Java: Spring Boot

**Secret/ConnectionString**

| Propiedades de la aplicación | Descripción | Valor de ejemplo |
| --- | --- | --- |
| azure.storage.account-name | Nombre de la cuenta de Blob Storage | `{storageAccountName}` |
| azure.storage.account-key | Clave de cuenta de Blob Storage | `{yourSecret}` |
| azure.storage.blob-endpoint | Punto de conexión de Blob Storage | `https://{storageAccountName}.blob.core.windows.net/` |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Service Connector, siga los tutoriales que se enumeran a continuación.

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)
