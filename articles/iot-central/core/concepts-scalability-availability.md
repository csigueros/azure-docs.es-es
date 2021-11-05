---
title: Escalabilidad de Azure IoT Central y alta disponibilidad | Microsoft Docs
description: En este artículo se describe cómo IoT Central escala automáticamente para controlar más dispositivos y su alta disponibilidad.
author: dominicbetts
ms.author: dobett
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: bea4e5d7dcb4349f17a4a967d8e0ef15e6f737b4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093273"
---
# <a name="scalability-and-high-availability"></a>Alta disponibilidad y escalabilidad

IoT Central aplicaciones usan internamente varios servicios de Azure, como IoT Hub y Device Provisioning Service (DPS). Muchos de estos servicios subyacentes son multiinquilino. Sin embargo, para garantizar el aislamiento completo de los datos de los clientes, IoT Central los centros de IoT de un solo inquilino. IoT Central administra automáticamente varias instancias de sus servicios subyacentes para escalar las aplicaciones IoT Central y hacer que estén de alta disponibilidad.

IoT Central escala automáticamente sus centros de IoT en función de los perfiles de carga de la aplicación. IoT Central puede escalar verticalmente centros de IoT individuales y escalar horizontalmente el número de centros de IoT. Para la conectividad de dispositivos de alta disponibilidad, IoT Central siempre tiene al menos dos centros de IoT. Aunque IoT Central administra sus centros de IoT, tener varios centros de IoT afecta a la implementación del firmware del dispositivo.

Los centros de IoT de una IoT Central se encuentran en la misma región de Azure. Este es el motivo por el que la arquitectura de varios centros de IoT proporciona conectividad de dispositivos de alta disponibilidad si se produce una interrupción aislada. Si toda una región de Azure deja de estar disponible, los procedimientos de recuperación ante desastres conmutan por error IoT Central aplicaciones a otra región.

## <a name="device-provisioning"></a>Aprovisionamiento de dispositivos

Antes de que un dispositivo se conecte IoT Central, debe registrarse y aprovisionarse en los servicios subyacentes. Al agregar un dispositivo a una aplicación IoT Central, IoT Central agrega una entrada a un grupo de inscripción de DPS. La información del grupo de inscripción, como el ámbito de identificador, el identificador de dispositivo y las claves, se muestra en la interfaz IoT Central usuario.

Cuando un dispositivo se conecta por primera vez IoT Central aplicación, DPS aprovisiona el dispositivo en uno de los centros de IoT vinculados del grupo de inscripciones. DPS usa una directiva de asignación para equilibrar la carga del aprovisionamiento en los centros de IoT de la aplicación. Este proceso se asegura de que cada centro de IoT tenga un número similar de dispositivos aprovisionados.

Para obtener más información sobre el registro y el aprovisionamiento en IoT Central, consulte [Conectarse a Azure IoT Central.](concepts-get-connected.md)

## <a name="device-connections"></a>Conexiones de dispositivos

Una vez que DPS aprovisiona un dispositivo en un centro de IoT, el dispositivo siempre intenta conectarse a ese centro. Si un dispositivo no puede acceder al centro de IoT en el que se aprovisiona, no se puede conectar a la IoT Central aplicación. Para controlar este escenario, el firmware del dispositivo debe incluir una estrategia de reintentos.

Para más información sobre cómo el firmware del dispositivo debe controlar los errores de conexión y conectarse a un centro diferente, consulte [Procedimientos recomendados para el desarrollo de dispositivos.](concepts-best-practices.md)

Para más información sobre cómo comprobar que el firmware del dispositivo puede controlar los errores de conexión, consulte [Test failover capabilities (Probar funcionalidades de conmutación por error)](concepts-best-practices.md#test-failover-capabilities).

## <a name="data-export"></a>Exportación de datos

IoT Central aplicaciones suelen usar otros servicios configurados por el usuario. Por ejemplo, puede configurar la aplicación IoT Central para exportar continuamente datos a servicios como Azure Event Hubs y Azure Blob Storage.

Si una exportación de datos configurada no puede escribir en su destino, IoT Central intenta retransmitir los datos durante un máximo de 15 minutos, después de lo cual IoT Central marca el destino como con errores. Los destinos con errores se comprueban periódicamente para comprobar si se pueden escribir.

Puede forzar a IoT Central para reiniciar las exportaciones con errores deshabilitando y habilitando de nuevo la exportación de datos.

Revise los procedimientos recomendados de alta disponibilidad y escalabilidad para el servicio de destino de exportación de datos que usa:

- Azure Blob Storage: [Redundancia de Azure Storage](../../storage/common/storage-redundancy.md) y [Lista de comprobación de rendimiento y escalabilidad para el almacenamiento Blob](../../storage/blobs/storage-performance-checklist.md)
- Azure Event Hubs: [disponibilidad y consistencia en Event Hubs](../../event-hubs/event-hubs-availability-and-consistency.md) y [Escalado con Event Hubs](../../event-hubs/event-hubs-scalability.md)
- Azure Service Bus: [procedimientos recomendados para aislar las aplicaciones frente Service Bus interrupciones](../../service-bus-messaging/service-bus-outages-disasters.md) y [desastres y actualizar automáticamente las unidades de mensajería de un espacio de nombres de Azure Service Bus](../../service-bus-messaging/automate-update-messaging-units.md)

## <a name="limitations"></a>Limitaciones

Actualmente, hay algunas aplicaciones de IoT Central heredadas que se crearon antes de abril de 2021 que aún no se han migrado a la arquitectura de varios centros de IoT. Use el `az iot central device manual-failover` comando para comprobar si la aplicación todavía usa un único centro de IoT.

Actualmente, IoT Edge dispositivos no se pueden mover entre centros de IoT.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido sobre la escalabilidad y alta disponibilidad de Azure IoT Central, el siguiente paso sugerido es obtener información sobre [la conectividad de dispositivos](concepts-get-connected.md) en Azure IoT Central.
