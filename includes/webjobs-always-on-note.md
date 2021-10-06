---
title: Archivo de inclusión
description: Archivo de inclusión
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 09/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 0d6363861a8412c7732a697f7e869504ddb106c6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128558526"
---
> [!NOTE]
> Una aplicación web puede agotar el tiempo de espera después de 20 minutos de inactividad y solo las solicitudes a la aplicación web real pueden restablecer el temporizador. Al ver la configuración de la aplicación en Azure Portal o realizar solicitudes en el sitio de herramientas avanzadas (`https://<app_name>.scm.azurewebsites.net`), no se restablece el temporizador. Si configura la aplicación web en la que se hospeda el trabajo para que se ejecute de forma continua, se ejecute según una programación o utilice desencadenadores basados en eventos, habilite la opción **Siempre activado** en la página **Configuración** de Azure de la aplicación web. La configuración Always On ayuda a asegurarse de que estos tipos de WebJob se ejecuten de forma confiable. Esta característica solo está disponible en los [planes de tarifa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Básico, Estándar y Premium.
