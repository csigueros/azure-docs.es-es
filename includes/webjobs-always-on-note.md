---
title: Archivo de inclusión
description: Archivo de inclusión
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5c3a653d66ddfb00833f2b90d61f08a51697fd46
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113131995"
---
> [!NOTE]
> Una aplicación web puede agotar el tiempo de espera después de 20 minutos de inactividad y solo las solicitudes a la aplicación web real pueden restablecer el temporizador. Al ver la configuración de la aplicación en Azure Portal o realizar solicitudes en el sitio de herramientas avanzadas (`https://<app_name>.scm.azurewebsites.net`), no se restablece el temporizador. Si configura la aplicación web para que se ejecute de forma continua, se ejecute según una programación o utilice desencadenadores basados en eventos, habilite la opción **Always On** en la página **Configuración** de Azure de la aplicación web. La configuración Always On ayuda a asegurarse de que estos tipos de WebJob se ejecuten de forma confiable. Esta característica solo está disponible en los [planes de tarifa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Básico, Estándar y Premium.
