---
title: Plan de eventos de mantenimiento de Azure
description: Obtenga información sobre cómo prepararse para los eventos de mantenimiento planeado en Azure SQL Database e Instancia administrada de Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: mathoma
ms.date: 3/23/2021
ms.openlocfilehash: a697c0a3095963760d6a95159790c02cbab14d71
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121625"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Planeación de eventos de mantenimiento de Azure en Azure SQL Database e Instancia administrada de Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Obtenga información sobre cómo prepararse para los eventos de mantenimiento planeado en la base de datos en Azure SQL Database e Instancia administrada de Azure SQL.

## <a name="what-is-a-planned-maintenance-event"></a>¿Qué es un evento de mantenimiento planeado?

Para mantener la seguridad, la compatibilidad, la estabilidad y el rendimiento de los servicios Azure SQL Database y Azure SQL Managed Instance, se realizan actualizaciones por medio de los componentes del servicio casi continuamente. Gracias a la moderna y sólida arquitectura de servicio y a tecnologías innovadoras como la [aplicación de revisiones en caliente](https://aka.ms/azuresqlhotpatching), la mayoría de las actualizaciones son completamente transparentes y sin impacto en lo que respecta a la disponibilidad del servicio. Aun así, algunos tipos de actualizaciones causan interrupciones breves del servicio y requieren tratamiento especial. 

Durante el mantenimiento planeado, los miembros del cuórum de la base de datos se quedarán sin conexión una a la vez, con la intención de que haya una réplica principal respondiendo. En Crítico para la empresa y Premium, al menos una réplica secundaria también estará en línea para garantizar que no haya tiempo de inactividad del cliente. Cuando sea necesario que la réplica principal esté sin conexión, se producirá un proceso de reconfiguración. Para Crítico para la empresa y Premium, una de las réplicas secundarias se convertirá en la nueva réplica principal. En las bases de datos De uso general, Estándar y Básico, la réplica principal se moverá a otro nodo de proceso sin estado con suficiente capacidad libre.

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Qué esperar durante un evento de mantenimiento planeado

El evento de mantenimiento puede producir una o varias reconfiguraciones, en función de la constelación de las réplicas principal y secundaria al principio del evento de mantenimiento. De promedio, se producen 1,7 reconfiguraciones por evento de mantenimiento planeado. Las reconfiguraciones suelen finalizar en 30 segundos. El promedio es de 8 segundos. Si ya está conectada, la aplicación debe volver a conectarse a la nueva réplica principal de la base de datos. Si se intenta establecer una nueva conexión mientras la base de datos está realizando una reconfiguración antes de que la nueva réplica esté en línea, se devolverá el error 40613 (base de datos no disponible): *La "base de datos '{nombre de la base de datos}' del servidor '{nombre del servidor}' no está disponible actualmente. Vuelva a intentar la conexión más tarde."* Si la base de datos tiene una consulta de larga duración, esta consulta se interrumpirá durante la reconfiguración y deberá reiniciarse.

## <a name="how-to-simulate-a-planned-maintenance-event"></a>Simulación de un evento de mantenimiento planeado

Asegurarse de que la aplicación cliente sea resistente a los eventos de mantenimiento antes de la implementación en producción ayudará a mitigar el riesgo de errores de la aplicación y contribuirá a la disponibilidad de la aplicación para los usuarios finales. Puede probar el comportamiento de la aplicación cliente durante los eventos de mantenimiento planeados mediante la [Prueba de la resistencia a errores de la aplicación](./high-availability-sla.md#testing-application-fault-resiliency) a través de PowerShell, la CLI o la API REST. Consulte también el [inicio de la conmutación por error manual](https://aka.ms/mifailover-techblog) para Instancia administrada. Producirá un comportamiento idéntico al evento de mantenimiento y dejará sin conexión la réplica principal.

## <a name="retry-logic"></a>Lógica de reintento

Todas las aplicaciones cliente de producción que se conecten a un servicio de base de datos en la nube deben implementar una [lógica de reintento](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) de conexión sólida. Esto permitirá que las conmutaciones por error sean transparentes para los usuarios finales o al menos le ayudará a minimizar los efectos negativos.

### <a name="service-health-alert"></a>Alerta de Service Health

Si quiere recibir alertas de problemas de servicio o actividades de mantenimiento planeado, puede usar alertas de Service Health en Azure Portal con el tipo de evento y los grupos de acciones adecuados. Para obtener más información, vea [Recepción de alertas sobre notificaciones del servicio de Azure](../../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal).

## <a name="resource-health"></a>Estado de los recursos

Si la base de datos experimenta errores de inicio de sesión, compruebe la ventana de [Resource Health](../../service-health/resource-health-overview.md#get-started) en [Azure Portal](https://portal.azure.com) para conocer el estado actual. La sección del historial de estado contiene el motivo del tiempo de inactividad de cada evento (si está disponible).

## <a name="maintenance-window-feature"></a>Característica de ventana de mantenimiento

La característica de ventana de mantenimiento permite configurar las programaciones de ventanas de mantenimiento predecibles para las bases de datos de Azure SQL y las instancias administrada de SQL aptas. Para más información, consulte [Ventana de mantenimiento](maintenance-window.md).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre [Resource Health](resource-health-to-troubleshoot-connectivity.md) para Azure SQL Database e Instancia administrada de Azure SQL.
- Para obtener más información sobre la lógica de reintento, vea [Lógica de reintento para errores transitorios](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- Configure las programaciones de ventana de mantenimiento con la característica de [ventana de mantenimiento](maintenance-window.md).