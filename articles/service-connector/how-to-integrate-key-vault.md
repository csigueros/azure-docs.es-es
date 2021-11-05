---
title: Integración de Azure Key Vault con Service Connector
description: Integración de Azure Key Vault en la aplicación con Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7b46f9c0080fb13f178ec1dca96fb49316c2f617
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090775"
---
# <a name="integrate-azure-key-vault-with-service-connector"></a>Integración de Azure Key Vault con Service Connector

En esta página se muestran los tipos de autenticación admitidos y los tipos de cliente de Azure Key Vault mediante el uso de Service Connector. Es posible que todavía pueda conectarse a Azure Key Vault en otros lenguajes de programación sin usar Service Connector. Esta página también muestra el nombre y el valor predeterminados de la variable de entorno (o configuración de Spring Boot) que se obtiene al crear la conexión de servicio. Puede obtener más información sobre la [convención de nomenclatura de variables de entorno de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Servicio de proceso admitido

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Tipos de autenticación y tipos de cliente admitidos

| Tipo de cliente | Identidad administrada asignada por el sistema | Identidad administrada asignada por el usuario | Secret/ConnectionString | Entidad de servicio |
| --- | --- | --- | --- | --- |
| .Net | | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |
| Java | | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |
| Java: Spring Boot | | | | ![icono sí](./media/green-check.png) |
| Node.js | | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |
| Python | | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |

## <a name="default-environment-variable-names-or-application-properties"></a>Nombres de variables de entorno o propiedades de aplicación predeterminados

### <a name="net-java-nodejs-python"></a>.NET, Java, Node.JS, Python

**Identidad administrada asignada por el sistema**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Su ámbito de RBAC de Azure | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Su punto de conexión de Key Vault | `https://{yourKeyVault}.vault.azure.net/` |

**Identidad administrada asignada por el usuario**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Su ámbito de RBAC de Azure | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Su punto de conexión de Key Vault | `https://{yourKeyVault}.vault.azure.net/` |
| AZURE_KEYVAULT_CLIENTID | Su identificador de cliente | `{yourClientID}` |

**Entidad de seguridad de servicio**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Su ámbito de RBAC de Azure | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Su punto de conexión de Key Vault | `https://{yourKeyVault}.vault.azure.net/` |
| AZURE_KEYVAULT_CLIENTID | Su identificador de cliente | `{yourClientID}` |
| AZURE_KEYVAULT_CLIENTSECRET | Su secreto de cliente | `{yourClientSecret}` |
| AZURE_KEYVAULT_TENANTID | Su identificador de inquilino | `{yourTenantID}` |

### <a name="java---spring-boot"></a>Java: Spring Boot

**Entidad de seguridad de servicio**
| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| azure.keyvault.uri | Su dirección URL del punto de conexión de Key Vault | `"https://{yourKeyVaultName}.vault.azure.net/"` |
| azure.keyvault.client-id | Su identificador de cliente | `{yourClientID}` |
| azure.keyvault.client-key | Su secreto de cliente | `{yourClientSecret}` |
| azure.keyvault.tenant-id |  Su identificador de inquilino | `{yourTenantID}` |
| azure.keyvault.scope | Su ámbito de RBAC de Azure | `https://management.azure.com/.default` |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Service Connector, siga los tutoriales que se enumeran a continuación.

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)
