---
title: Archivo de inclusión
description: archivo de inclusión
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: 63c9c98f7b85c18cde58d65a8d1fd94eff2b1985
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128560173"
---
1. Para crear una instancia de caché, inicie sesión en [Azure Portal](https://portal.azure.com) y seleccione **Crear un recurso**.

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="Crear un recurso está resaltado en el panel de navegación izquierdo.":::

1. En la página **Nuevo**, seleccione **Base de datos** y, a continuación, seleccione **Azure Cache for Redis**.

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="En Nuevo, están resaltados Bases de datos y Azure Cache for Redis.":::

1. En la página **Nueva instancia de Redis Cache**, configure las opciones de la nueva caché.

   | Configuración      | Elegir un valor  | Descripción |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Suscripción** | Desplácese hacia abajo y seleccione su suscripción. | La suscripción en la que se creará esta nueva instancia de Azure Cache for Redis. |
   | **Grupos de recursos** | Desplácese hacia abajo y seleccione un grupo de recursos o **Crear nuevo** y escriba un nombre nuevo para el grupo de recursos. | Nombre del grupo de recursos en el que se van a crear la caché y otros recursos. Al colocar todos los recursos de la aplicación en un grupo de recursos, puede administrarlos o eliminarlos fácilmente. |
   | **Nombre DNS** | Escriba un nombre único. | El nombre de la memoria caché debe ser una cadena de entre 1 y 63 caracteres, y solo puede contener números, letras o guiones. El nombre debe comenzar y terminar por un número o una letra y no puede contener guiones consecutivos. El *nombre de host* de la instancia de caché será *\<DNS name>.redis.cache.windows.net*. |
   | **Ubicación** | Desplácese hacia abajo y seleccione una ubicación. | Seleccione una [región](https://azure.microsoft.com/regions/) cerca de otros servicios que vayan a usar la memoria caché. |
   | **Tipo de caché** | Desplácese hacia abajo y seleccione un [nivel](https://azure.microsoft.com/pricing/details/cache/). |  El nivel determina el tamaño, rendimiento y características disponibles para la memoria caché. Para más información, consulte la [introducción a Azure Redis Cache](../cache-overview.md). |

1. Seleccione la pestaña **Redes** o elija el botón **Redes** situado en la parte inferior de la página.

1. En la pestaña **Redes**, seleccione el método de conectividad.

1. Seleccione la pestaña **Siguiente: Opciones avanzadas** o seleccione el botón **Siguiente: Opciones avanzadas** en la parte inferior de la página.

1. En la pestaña **Opciones avanzadas** de una instancia de caché básica o estándar, seleccione el botón de alternancia de habilitación si desea habilitar un puerto que no sea TLS. También puede seleccionar la versión de Redis que quiere usar, la 4 o (VERSIÓN PRELIMINAR) la 6.

    :::image type="content" source="media/redis-cache-create/redis-version.png" alt-text="Versiones 4 o 6 de Redis.":::

1. En la pestaña **Opciones avanzadas** de la instancia de caché Premium, configure el puerto no TLS, la agrupación en clústeres y la persistencia de datos. También puede seleccionar la versión de Redis que quiere usar, la 4 o (VERSIÓN PRELIMINAR) la 6.

1. Seleccione el botón **Siguiente: Opciones avanzadas** o elija el botón **Siguiente: Etiquetas** situado en la parte inferior de la página.

1. Opcionalmente, en la pestaña **Etiquetas**, escriba el nombre y el valor si desea clasificar el recurso.

1. Seleccione **Revisar + crear**. Pasará a la pestaña Revisar y crear, donde Azure validará la configuración.

1. Tras aparecer el mensaje verde Validación superada, seleccione **Crear**.

La caché tarda un tiempo en crearse. Puede supervisar el progreso en la página **Información general** de Azure Cache for Redis. Cuando **Estado** se muestra como **En ejecución**, la memoria caché está lista para su uso.
