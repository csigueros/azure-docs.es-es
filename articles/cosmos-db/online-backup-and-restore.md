---
title: Copia de seguridad en línea y restauración de datos a petición en Azure Cosmos DB
description: En este artículo se describe cómo funcionan la copia de seguridad automática y la restauración de datos a petición. También se explica la diferencia entre los modos de copia de seguridad continua y periódica.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/15/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1a87371e697d39b73a647e7dea1dbbceb1e028d2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523227"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Copias de seguridad en línea y restauración de datos a petición en Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB crea automáticamente copias de seguridad de los datos a intervalos regulares. Las copias de seguridad automáticas se crean sin afectar al rendimiento o a la disponibilidad de las operaciones de las bases de datos. Todas las copias de seguridad se almacenan por separado en un servicio de almacenamiento. Las copias de seguridad automáticas son útiles en escenarios en los se elimina accidentalmente o se actualiza su cuenta, base de datos o contenedor de Azure Cosmos y más tarde se requiere la recuperación de datos. Las copias de seguridad de Azure Cosmos DB se cifran con claves de servicio administradas por Microsoft. Estas copias de seguridad se transfieren a través de una red no pública segura. Lo que significa que los datos de copia de seguridad permanecen cifrados mientras se transfieren a través de la conexión y cuando están en reposo. Las copias de seguridad de una cuenta de una región determinada se cargan en cuentas de almacenamiento de la misma región.

## <a name="backup-modes"></a>Modos de copia de seguridad

Hay dos modos de copia de seguridad:

* **Modo de copia de seguridad periódica**: este modo es el modo de copia de seguridad predeterminado para todas las cuentas existentes. En este modo, la copia de seguridad se realiza a intervalos periódicos y los datos se restauran mediante la creación de una solicitud con el equipo de soporte técnico. En este modo, debe configurar un intervalo de copia de seguridad y la retención de la cuenta. El período de retención máximo se amplía a un mes. El intervalo mínimo de la copia de seguridad puede ser de una hora.  Para obtener más información, consulte el artículo [Modo de copia de seguridad periódica](configure-periodic-backup-restore.md).

* **Modo de copia de seguridad continua**: elija este modo al crear la cuenta de Azure Cosmos DB. Este modo permite realizar la restauración en cualquier punto temporal de los últimos 30 días. Para más información, consulte [Introducción al modo de copia de seguridad continua](continuous-backup-restore-introduction.md) y aprovisione la copia de seguridad continua según los artículos de [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), la [CLI](provision-account-continuous-backup.md#provision-cli) y [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).

  > [!NOTE]
  > Si configura una cuenta nueva con el modo de copia de seguridad continua, puede realizar la restauración de autoservicio a través de Azure Portal, PowerShell o la CLI. Si la cuenta está configurada en modo continuo, no se puede volver a cambiar al modo periódico.

En las cuentas habilitadas para Azure Synapse Link, los datos del almacén analítico no se incluyen en las copias de seguridad y restauraciones. Cuando Synapse Link esté habilitado, Azure Cosmos DB seguirá haciendo copias de seguridad automáticamente de los datos del almacén de transacciones en el intervalo programado de copias de seguridad. En este momento, no se admite la copia de seguridad ni la restauración automáticas de los datos del almacén analítico.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="can-i-restore-from-an-account-a-in-subscription-s1-to-account-b-in-a-subscription-s2"></a>¿Puedo restaurar desde una cuenta A de una suscripción S1 a una cuenta B de una suscripción S2?

No. Solo puede restaurar entre cuentas dentro de la misma suscripción.

### <a name="can-i-restore-into-an-account-that-has-fewer-partitions-or-low-provisioned-throughput-than-the-source-account"></a>¿Puedo restaurar en una cuenta que tenga menos particiones o un rendimiento aprovisionado menor que la cuenta de origen?

No. No se puede restaurar en una cuenta con RU/s menores o menos particiones.

## <a name="next-steps"></a>Pasos siguientes

A continuación, puede obtener información sobre cómo configurar y administrar los modos de copia de seguridad periódica y continua para su cuenta:

* Directiva de [configuración y administración de la copia de seguridad periódica](configure-periodic-backup-restore.md).
* ¿Qué es el modo de [copia de seguridad continua](continuous-backup-restore-introduction.md)?
* Aprovisione la copia de seguridad continua mediante [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), la [CLI](provision-account-continuous-backup.md#provision-cli) o [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).
* Restaure la cuenta de copia de seguridad continua mediante [Azure Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), la [CLI](restore-account-continuous-backup.md#restore-account-cli) o [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template).
* [Realice la migración a una cuenta desde una copia de seguridad periódica a una copia de seguridad continua](migrate-continuous-backup.md).
* [Administre los permisos](continuous-backup-restore-permissions.md) necesarios para restaurar datos con el modo de copia de seguridad continua.
* [Modelo de recursos del modo de copia de seguridad continua](continuous-backup-restore-resource-model.md)
