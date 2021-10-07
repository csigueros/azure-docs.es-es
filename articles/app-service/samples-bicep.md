---
title: Ejemplos de Bicep
description: Busque ejemplos de Bicep para algunos de los escenarios de App Service más habituales. Aprenda a automatizar las tareas de administración o implementación de App Service.
author: seligj95
tags: azure-service-management
ms.topic: sample
ms.date: 8/26/2021
ms.author: jordanselig
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: de66f32afb86da526765016e9b8feca7d5ada337
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059106"
---
# <a name="bicep-files-for-app-service"></a>Archivos Bicep para App Service

En la tabla siguiente se incluyen vínculos a archivos de Bicep para Azure App Service. Para obtener inicios rápidos e información adicional sobre Bicep, consulte la [documentación de Bicep](/azure/azure-resource-manager/bicep/).

Para más información sobre la sintaxis de Bicep y las propiedades para los recursos de App Services, consulte [Tipos de recursos Microsoft.Web ](/azure/templates/microsoft.web/allversions).

| Implementación de una aplicación | Descripción |
|-|-|
| [App Service plan and basic Linux app](https://github.com/Azure/bicep/tree/main/docs/examples/101/web-app-linux) (Plan de App Service y aplicación básica para Linux) | Implementa una aplicación de App Service configurada para Linux. |
| [App Service plan and basic Windows web app](https://github.com/Azure/bicep/tree/main/docs/examples/101/web-app-windows) (Plan de App Service y aplicación web básica para Windows) | Implementa una aplicación de App Service configurada para Windows. |
| [Sitio web con contenedor](https://github.com/Azure/bicep/tree/main/docs/examples/101/website-with-container) | Implementa un sitio web desde una imagen de Docker configurada para Linux. |
| **Configuración de una aplicación** | **Descripción** |
| [Aplicación con registro condicional](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-conditional-log)| Implementa una aplicación de App Service con un condicional para la habilitación del registro. |
| [Aplicación con un módulo de Log Analytics](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-loganalytics-mod)| Implementa una aplicación de App Service con Log Analytics. |
| [Aplicación con integración de una red virtual regional](https://github.com/Azure/bicep/tree/main/docs/examples/101/app-service-regional-vnet-integration)| Implementa una aplicación de App Service con la integración de una red virtual regional habilitada. |
|**App with connected resources** (Aplicación con recursos conectados)| **Descripción** |
| [Aplicación con CosmosDB](https://github.com/Azure/bicep/tree/main/docs/examples/101/cosmosdb-webapp)| Implementa una aplicación de App Service en Linux con CosmosDB. |
| [App with MySQL](https://github.com/Azure/bicep/tree/main/docs/examples/101/webapp-managed-mysql) (Aplicación con MySQL)| Implementa una aplicación de App Service en Windows con Azure Database for MySQL. |
| [Creación de una base de datos de Azure SQL Database](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-sql-database)| Implementa una aplicación de App Service y una base de datos de Azure SQL Database en el nivel de servicio Básico. |
| [Aplicación conectada a una aplicación web de back-end](https://github.com/Azure/bicep/tree/main/docs/examples/101/webapp-privateendpoint-vnet-injection)| Implementa dos aplicaciones web (front-end y back-end) conectadas de forma segura junto con inserción de red virtual y un punto de conexión privado. |
| [Aplicación con una base de datos, identidad administrada y supervisión](https://github.com/Azure/bicep/tree/main/docs/examples/301/web-app-managed-identity-sql-db)| Implementa una aplicación de App Service con una base de datos, identidad administrada y supervisión. |
|**entorno de App Service**| **Descripción** |
| [Create an App Service environment v2](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-asev2-create) (Creación de un entorno de App Service v2) | Crea un entorno de App Service v2 en la red virtual. |
| | |