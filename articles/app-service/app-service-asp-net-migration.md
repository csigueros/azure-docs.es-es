---
title: Migración de aplicaciones .NET a Azure App Service
description: Descubra los recursos de migración de .NET disponibles para Azure App Service.
author: msangapu-msft
ms.topic: article
ms.date: 03/29/2021
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8918c32e03c6e2c27621dd5c05b9abe976ded511
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093087"
---
# <a name="net-migration-cases-for-azure-app-service"></a>Casos de migración de .NET para Azure App Service

Azure App Service proporciona herramientas fáciles de usar para detectar rápidamente aplicaciones web .NET locales, evaluar la preparación y migrar tanto el contenido como las configuraciones admitidas a App Service.

Estas herramientas se desarrollan para admitir diferentes tipos de escenarios, centrados en la detección, la evaluación y la migración. A continuación se muestra una lista de herramientas de migración de .NET y casos de uso.

## <a name="migrate-from-multiple-servers-at-scale-preview"></a>Migración desde varios servidores a escala (versión preliminar)

<!-- Intent: discover how to assess and migrate at scale. -->

Azure Migrate anunció recientemente la detección y la evaluación a escala y sin agentes de las aplicaciones web de ASP.NET. Ahora puede detectar fácilmente las aplicaciones web de ASP.NET que se ejecutan en servidores Internet Information Services (IIS) en un entorno de VMware y evaluarlas para la migración a Azure App Service. Las valoraciones le ayudarán a determinar la preparación para la migración de aplicaciones web, los bloques de migración, la guía de corrección, la SKU recomendada y los costos de hospedaje. A continuación se encuentran los recursos de migración a escala para.

### <a name="at-scale-migration-resources"></a>Recursos de migración a escala

| Temas procedimentales |
|----------------|
| [Descubrir aplicaciones web e instancias de SQL Server](../migrate/how-to-discover-sql-existing-project.md)                              |
| [Creación de una valoración de Azure App Service](../migrate/how-to-create-azure-app-service-assessment.md)                            |
| [Tutorial para evaluar aplicaciones web para su migración a Azure App Service](../migrate/tutorial-assess-webapps.md)                       |
| [Detección de inventario de software en servidores locales con Azure Migrate](../migrate/how-to-discover-applications.md)           |
| **Blog** |
| [Detectar y evaluar aplicaciones de ASP.NET a escala con Azure Migrate](https://azure.microsoft.com/blog/discover-and-assess-aspnet-apps-atscale-with-azure-migrate/) |
| **P+F** |
| [Evaluaciones de Azure App Service en la herramienta de detección y evaluación de Azure Migrate](../migrate/concepts-azure-webapps-assessment-calculation.md) |
| **procedimientos recomendados** |
| [Procedimiento recomendado de evaluaciones en la herramienta de detección y evaluación de Azure Migrate](../migrate/best-practices-assessment.md) |
| **Vídeo** |
| [Detección y evaluación a escala para la migración de aplicaciones ASP.NET con Azure Migrate](https://channel9.msdn.com/Shows/Inside-Azure-for-IT/At-scale-discovery-and-assessment-for-ASPNET-app-migration-with-Azure-Migrate) |

## <a name="migrate-from-an-iis-server"></a>Migración desde un servidor IIS

<!-- Intent: discover how to assess and migrate from a single IIS server  -->

Puede migrar aplicaciones web de ASP.NET desde un único servidor IIS detectado a través de la experiencia de detección a escala de Azure Migrate mediante [scripts de PowerShell](https://github.com/Azure/App-Service-Migration-Assistant/wiki/PowerShell-Scripts) [(descarga)](https://appmigration.microsoft.com/api/download/psscriptpreview/AppServiceMigrationScripts.zip). Vea el vídeo para [obtener actualizaciones sobre la migración a Azure App Service](https://channel9.msdn.com/Shows/The-Launch-Space/Updates-on-Migrating-to-Azure-App-Service).

## <a name="aspnet-web-app-migration"></a>Migración de aplicaciones web de ASP.NET
<!-- Intent: migrate a single web app -->

Con App Service Migration Assistant, puede [migrar la aplicación web local independiente ASP.NET a Azure App Service](https://www.youtube.com/watch?v=9LBUmkUhmXU). Además, App Service Migration Assistant está diseñado para simplificar la migración a la nube mediante una solución gratuita, sencilla y rápida que migra las aplicaciones desde el entorno local a la nube. Para más información sobre la herramienta Migration Assistant, consulte las preguntas [más frecuentes](https://github.com/Azure/App-Service-Migration-Assistant/wiki).

## <a name="containerize-an-aspnet-web-app"></a>Contenedor de una aplicación web de ASP.NET

Algunas aplicaciones web de .NET Framework pueden tener dependencias de bibliotecas y otras funcionalidades que no están disponibles en Azure App Service. Estas aplicaciones pueden basarse en otros componentes de la caché global de ensamblados. Anteriormente, solo podía ejecutar estas aplicaciones en máquinas virtuales. Sin embargo, ahora puede ejecutarlos en Azure App Service Windows Containers.

La [herramienta de contenedorización de aplicaciones](https://azure.microsoft.com/blog/accelerate-application-modernization-with-azure-migrate-app-containerization/) puede reempaquetar aplicaciones como contenedores con cambios mínimos. Actualmente, la herramienta admite la contenedorización de aplicaciones de ASP.NET y aplicaciones Java de Apache Tomcat. Para obtener más información sobre la migración y la contenedorización, consulte [el tutorial](../migrate/tutorial-app-containerization-aspnet-app-service.md).

## <a name="next-steps"></a>Pasos siguientes

[Migración de una aplicación web local a Azure App Service](/learn/modules/migrate-app-service-migration-assistant/)
