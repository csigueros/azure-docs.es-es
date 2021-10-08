---
title: Información general sobre la configuración de Azure Static Web Apps
description: Obtenga información sobre las diferentes formas de configurar Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: cshoe
ms.openlocfilehash: 34a93dc1203fb404d0d6c2edfd70954545e26b15
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154933"
---
# <a name="configuration-overview"></a>Información general sobre la configuración

Los diferentes conceptos que figuran a continuación se aplican a la configuración de una aplicación web estática:

- [Configuración de la aplicación:](./configuration.md) defina reglas en el archivo `staticwebapp.config.json` para controlar el comportamiento y las características de la aplicación. Use este archivo para definir reglas de ruta y seguridad, encabezados personalizados y valores de configuración de redes.

- [Configuración de compilación:](./build-configuration.md) defina la configuración que controla el proceso de compilación.

- [Configuración de la aplicación:](./application-settings.md) establezca la configuración de nivel de aplicación y las variables de entorno que pueden usar las API de back-end.

## <a name="example-scenarios"></a>Escenarios de ejemplo

| Si desea... | entonces... |
|---|---|
| Definir reglas de enrutamiento | [Cree reglas en el archivo "staticwebapps.config.json".](./configuration.md) |
| Establecer qué rama desencadena compilaciones | [Actualice el nombre de rama con seguimiento en el archivo de configuración de la compilación.](./build-configuration.md)  |
| Definir qué roles de seguridad tienen acceso a una ruta | [Proteja rutas con roles en el archivo "staticwebapps.config.json".](./configuration.md#securing-routes-with-roles) |
| Establecer qué archivo HTML se sirve si una ruta no coincide con un archivo real | [Defina la ruta de reserva en el archivo "staticwebapps.config.json".](./configuration.md#fallback-routes) |
| Establecer encabezados globales para solicitudes HTTP | [Defina encabezados globales en el "staticwebapps.config.json".](./configuration.md#global-headers)|
| Definir un comando de compilación personalizado | [Establezca un valor de comando de compilación personalizado en el archivo de configuración de la aplicación.](./build-configuration.md) |
| Establecer una variable de entorno para una compilación de front-end | [Defina una variable de entorno en el archivo de configuración de la compilación.](./build-configuration.md#environment-variables) |
| Establecer una variable de entorno para una API | [Establezca un valor de aplicación en el portal.](./application-settings.md) |

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración de aplicaciones](configuration.md)
