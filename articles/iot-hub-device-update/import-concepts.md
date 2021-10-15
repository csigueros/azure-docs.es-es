---
title: Información sobre la importación de Device Update para IoT Hub | Microsoft Docs
description: Conceptos clave para la importación de una nueva actualización a Device Update para IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 07e5b78b5ae9029ed4ab33f37e35105dfb5e5f26
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358411"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>Importación de actualizaciones a Device Update para IoT Hub
Para implementar una actualización en dispositivos desde Device Update para IoT Hub, primero hay que _importar_ esa actualización al servicio Device Update. A continuación se ofrece información general sobre algunos conceptos importantes que se deben conocer en relación con la importación de actualizaciones.

## <a name="limits-on-importing-updates"></a>Límites en la importación de actualizaciones
En todas las instancias de Device Update for IoT Hub, se aplican los determinados límites. Si aún no los ha revisado, consulte [Límites de actualización de dispositivos](./device-update-limits.md).

## <a name="import-manifest"></a>Manifiesto de importación

Un manifiesto de importación es un archivo JSON que define información importante acerca de la actualización que va a importar. Como parte del proceso de importación, se enviarán el archivo de manifiesto de importación y los archivos de actualización asociados (por ejemplo, un paquete de actualización de firmware). Los metadatos definidos en el manifiesto de importación se usarán para ingerir la información de la actualización. Parte de los metadatos también se usan en el momento de la implementación. Por ejemplo, para validar si una actualización se ha instalado correctamente.

El manifiesto de importación contiene varios elementos que representan conceptos importantes sobre Device Update para IoT Hub. Estos conceptos se explican a continuación.

### <a name="update-identity-update-id"></a>Identidad de actualización (Id. de actualización)

La identidad de actualización representa el identificador único de una actualización. Define propiedades relevantes sobre una actualización que se está importando. La identidad de actualización consta de tres elementos:
* Proveedor: se trata de la entidad que está creando la actualización o es directamente responsable de ella. Suele ser un nombre de empresa.
* Nombre: es un identificador de una clase de actualizaciones. La clase puede ser cualquier cosa que elija. Suele ser un nombre de dispositivo o de modelo.
* Versión: es un número de versión que diferencia a esta actualización de otras con el mismo proveedor y mismo nombre. Esta versión la usa el servicio Device Update para IoT Hub y puede o no coincidir con una versión de un componente de software individual en el dispositivo. 

### <a name="compatibility"></a>Compatibilidad

Para simplificar las implementaciones de actualizaciones, Device Update para IoT Hub compara las propiedades de compatibilidad de una actualización, que se definen en el manifiesto de importación, con las propiedades de dispositivo correspondientes. Solo las actualizaciones con propiedades que coinciden se devolverán y estarán disponibles para su implementación.

### <a name="installedcriteria"></a>InstalledCriteria

El agente de actualización usa InstalledCriteria en un dispositivo para determinar si una actualización se ha instalado correctamente.


## <a name="next-steps"></a>Pasos siguientes

Cuando está listo, pruebe la [guía de procedimientos de importación](./import-update.md), que le guiará a través del proceso de importación paso a paso.


