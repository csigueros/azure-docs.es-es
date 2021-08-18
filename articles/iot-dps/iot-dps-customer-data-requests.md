---
title: Características de las solicitudes de datos de clientes para dispositivos de Azure DPS
description: En este artículo se muestra a los administradores cómo exportar o eliminar datos personales de los dispositivos administrados en Device Provisioning Service (DPS) de Azure que son personales.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: aed9a608dd8a958a298b3a7f502dac6d587b2a2e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114466754"
---
# <a name="summary-of-customer-data-request-features"></a>Resumen de características de solicitud de datos del cliente

Azure IoT Hub Device Provisioning Service es un servicio en la nube basado en la API de REST destinado a los clientes empresariales. Este servicio permite un aprovisionamiento de dispositivos sin intervención del usuario, automatizado y continuo a Azure IoT Hub con seguridad, que empieza en el dispositivo y termina en la nube.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Un administrador de inquilinos asigna un id. de registro y un id. de dispositivo a los dispositivos individuales. Los datos de y acerca de estos dispositivos se basan en estos identificadores. Microsoft no retiene ninguna información y no tiene acceso a datos que permitan la correlación entre estos dispositivos y una persona.

Muchos de los dispositivos administrados en Device Provisioning Service no son dispositivos personales; por ejemplo, el termostato de una oficina o el robot de una fábrica. Sin embargo, los clientes pueden considerar que algunos dispositivos se pueden identificar a nivel personal y, a su entera discreción, pueden mantener sus propios métodos de seguimiento de activos o de inventario que vinculan dispositivos a personas. Device Provisioning Service administra y almacena todos los datos asociados con dispositivos como si fueran datos personales.

Los administradores de inquilinos pueden usar Azure Portal o las API de REST del servicio para atender las solicitudes de información mediante la exportación o la eliminación de datos asociados con el id. de un dispositivo o registro.

> [!NOTE]
> Los dispositivos que se han aprovisionado en Azure IoT Hub a través de Device Provisioning Service tienen datos adicionales almacenados en el servicio de Azure IoT Hub. Consulte la [documentación de referencia de Azure IoT Hub](../iot-hub/iot-hub-customer-data-requests.md) para saber cómo realizar una solicitud completa de un dispositivo determinado.

## <a name="deleting-customer-data"></a>Eliminación de datos del cliente

Device Provisioning Service almacena inscripciones y datos de registros. Las inscripciones contienen información sobre los dispositivos que se pueden aprovisionar y los datos de registros muestran los dispositivos que ya pasaron por el proceso de aprovisionamiento.

Los administradores de inquilinos pueden quitar las inscripciones de Azure Portal, lo que, a su vez, quita todos los datos de registro asociados.

Para obtener más información, consulte [Administración de inscripciones de dispositivos con Azure Portal](how-to-manage-enrollments.md).

También se pueden realizar operaciones para eliminar inscripciones y datos de registros mediante las API de REST:

* Para eliminar la información de inscripción de un único dispositivo, puede usar [Device Enrollment - Delete](/rest/api/iot-dps/service/individual-enrollment/delete) (Inscripción de dispositivos: Eliminar).
* Para eliminar la información de inscripción de un grupo de dispositivos, puede usar [Device Enrollment Group - Delete](/rest/api/iot-dps/service/enrollment-group/delete) (Grupo de inscripción de dispositivos: Eliminar).
* Para eliminar la información sobre los dispositivos que se hayan aprovisionado, puede usar [Registration State - Delete Registration State](/rest/api/iot-dps/service/device-registration-state/delete) (Estado de registro: Eliminar estado de registro).

## <a name="exporting-customer-data"></a>Exportación de datos del cliente

Device Provisioning Service almacena inscripciones y datos de registros. Las inscripciones contienen información sobre los dispositivos que se pueden aprovisionar y los datos de registros muestran los dispositivos que ya pasaron por el proceso de aprovisionamiento.

Los administradores de inquilinos pueden ver las inscripciones y los datos de registro a través de Azure Portal y pueden exportarlos mediante el uso de las funciones de copiar y pegar.

Consulte [Administración de inscripciones de dispositivos con Azure Portal](how-to-manage-enrollments.md) para obtener más información al respecto.

También es posible realizar operaciones de exportación para inscripciones y datos de registro a través de las API de REST:

* Para exportar la información de inscripción de un único dispositivo, puede usar [Device Enrollment - Get](/rest/api/iot-dps/service/individual-enrollment/get) (Inscripción de dispositivos: Obtener).
* Para exportar la información de inscripción de un grupo de dispositivos, puede usar [Device Enrollment Group - Get](/rest/api/iot-dps/service/enrollment-group/get) (Grupo de inscripción de dispositivos: Obtener).
* Para eliminar la información sobre los dispositivos que se hayan aprovisionado, puede usar [Registration State - Get Registration State](/rest/api/iot-dps/service/device-registration-state/get) (Estado de registro: Eliminar estado de registro).

> [!NOTE]
> Cuando se usan servicios para empresas de Microsoft, Microsoft genera cierta información, conocida como registros generados por el sistema. Los administradores de inquilinos no pueden acceder a ciertos registros generados por el sistema de Device Provisioning Service ni exportarlos. Estos registros constituyen acciones objetivas que se realizan en el servicio y datos de diagnóstico relacionados con dispositivos individuales.

## <a name="links-to-additional-documentation"></a>Vínculos a documentación adicional

Se puede encontrar documentación completa sobre las API de Device Provisioning Service en [https://docs.microsoft.com/rest/api/iot-dps](/rest/api/iot-dps).

[Características de la solicitud de datos de cliente](../iot-hub/iot-hub-customer-data-requests.md) de Azure IoT Hub.