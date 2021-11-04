---
title: Integración de Azure SignalR Service con Service Connector
description: Integración de Azure SignalR Service en la aplicación con Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 688321101923577fb5ef44ac8d7f23a63bf4b08c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017440"
---
# <a name="integrate-azure-signalr-service-with-service-connector"></a>Integración de Azure SignalR Service con Service Connector

En esta página se muestran los tipos de autenticación admitidos y los tipos de cliente de Azure SignalR Service mediante Service Connector. Es posible que todavía pueda conectarse a Azure SignalR Service en otros lenguajes de programación sin usar Service Connector. Esta página también muestra el nombre y el valor predeterminados de la variable de entorno (o configuración de Spring Boot) que se obtiene al crear la conexión de servicio. Puede obtener más información sobre la [convención de nomenclatura de variables de entorno de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Servicio de proceso admitido

- Azure App Service

## <a name="supported-authentication-types-and-client-types"></a>Tipos de autenticación y tipos de cliente admitidos

| Tipo de cliente | Identidad administrada asignada por el sistema | Identidad administrada asignada por el usuario | Secret/ConnectionString | Entidad de servicio |
| --- | --- | --- | --- | --- |
| .Net | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |

## <a name="default-environment-variable-names-or-application-properties"></a>Nombres de variables de entorno o propiedades de aplicación predeterminados

### <a name="net"></a>.NET

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | Cadena de conexión de SignalR Service | `Endpoint=https://{signalrName}.service.signalr.net;AccessKey={};Version=1.0;` |

**Identidad administrada asignada por el sistema**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | Cadena de conexión de SignalR Service con identidad administrada | `Endpoint=https://{signalrName}.service.signalr.net;AuthType=aad;ClientId={};Version=1.0;` |

**Identidad administrada asignada por el usuario**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | Cadena de conexión de SignalR Service con identidad administrada | `Endpoint=https://{signalrName}.service.signalr.net;AuthType=aad;ClientId={};Version=1.0;` |

**Entidad de seguridad de servicio**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | Cadena de conexión de SignalR Service con entidad de servicio | `Endpoint=https://{signalrName}.service.signalr.net;AuthType=aad;ClientId={};ClientSecret={};TenantId={};Version=1.0;` |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Service Connector, siga los tutoriales que se enumeran a continuación.

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)
