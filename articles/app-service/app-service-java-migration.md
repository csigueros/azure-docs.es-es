---
title: Migración de aplicaciones Java a Azure App Service
description: Descubra los recursos de migración de Java disponibles para Azure App Service.
author: msangapu-msft
ms.topic: article
ms.date: 03/29/2021
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f876445673d0237af33f10e3e5b599032ba28bd7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093186"
---
# <a name="java-migration-resources-for-azure-app-service"></a>Recursos de migración de Java para Azure App Service

Azure App Service proporciona herramientas para detectar aplicaciones web implementadas en servidores web locales. Puede evaluar estas aplicaciones para la preparación y, a continuación, migrarlas a App Service. Tanto el contenido de la aplicación web como la configuración admitida se pueden migrar a App Service. Estas herramientas se desarrollan para admitir diferentes tipos de escenarios, centrados en la detección, la evaluación y la migración.

## <a name="java-tomcat-migration-linux"></a>Migración de Tomcat de Java (Linux)

[Descargue el asistente](https://azure.microsoft.com/services/app-service/migration-assistant/) para migrar una aplicación de Java que se ejecuta en el servidor web de Apache Tomcat. También puede utilizar Azure Container Registry para migrar contenedores de Docker de Linux locales a App Service.

| Recursos |
|-----------|
| **Procedimientos** |
| [Wiki de Migration Assistant de Java App Service](https://github.com/Azure/App-Service-Migration-Assistant/wiki/TOMCAT-Java-Information) |
| [Wiki de Migration Assistant de Azure/App Service](https://github.com/Azure/App-Service-Migration-Assistant/wiki/Linux-Notes) |
| **Vídeos** |
|[Apuntar y migrar aplicaciones Java a Azure App Service mediante el sistema de migración](https://www.youtube.com/watch?v=Mpxa0KE0X9k) |

## <a name="standalone-tomcat-web-app-migration-windows-os"></a>Migración de aplicaciones web de Tomcat independiente (Sistema operativo Windows)

Descargue esta [herramienta de versión preliminar](https://azure.microsoft.com/services/app-service/migration-assistant/) para migrar una aplicación web de Java en Apache Tomcat a App Service en Windows. Para obtener más información, vea el [vídeo](https://channel9.msdn.com/Shows/The-Launch-Space/Updates-on-Migrating-to-Azure-App-Service) y consulte la [guía paso a paso](https://github.com/Azure/App-Service-Migration-Assistant/wiki/TOMCAT-Java-Information).

## <a name="containerize-standalone-tomcat-web-app"></a>Almacenamiento en contenedores de una aplicación web de Tomcat independiente

La contenedorización de aplicaciones ofrece un enfoque sencillo para volver a empaquetar aplicaciones como contenedores con cambios mínimos en el código. Actualmente, la herramienta admite la contenedorización de aplicaciones de ASP.NET y aplicaciones Java de Apache Tomcat. Para obtener más información, vea [el blog](https://azure.microsoft.com/blog/accelerate-application-modernization-with-azure-migrate-app-containerization/) y la [guía paso a paso](../migrate/tutorial-app-containerization-java-app-service.md).

## <a name="next-steps"></a>Pasos siguientes

[Migración de aplicaciones de Tomcat a Tomcat en Azure App Service](/azure/developer/java/migration/migrate-tomcat-to-tomcat-app-service)
