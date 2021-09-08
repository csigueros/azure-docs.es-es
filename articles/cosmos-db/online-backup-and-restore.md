---
title: Copia de seguridad en línea y restauración de datos a petición en Azure Cosmos DB
description: En este artículo se describe cómo funcionan la copia de seguridad automática y la restauración de datos a petición. También se explica la diferencia entre los modos de copia de seguridad continua y periódica.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/21/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2793cd0e3b2d43a2a227cd170d1173c536b41098
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725415"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Copias de seguridad en línea y restauración de datos a petición en Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB crea automáticamente copias de seguridad de los datos a intervalos regulares. Las copias de seguridad automáticas se crean sin afectar al rendimiento o a la disponibilidad de las operaciones de las bases de datos. Todas las copias de seguridad se almacenan por separado en un servicio de almacenamiento. Las copias de seguridad automáticas son útiles en escenarios en los se elimina accidentalmente o se actualiza su cuenta, base de datos o contenedor de Azure Cosmos y más tarde se requiere la recuperación de datos. Hay dos modos de copia de seguridad:

* **Modo de copia de seguridad periódica**: este modo es el modo de copia de seguridad predeterminado para todas las cuentas existentes. En este modo, la copia de seguridad se realiza a intervalos periódicos y los datos se restauran mediante la creación de una solicitud con el equipo de soporte técnico. En este modo, debe configurar un intervalo de copia de seguridad y la retención de la cuenta. El período de retención máximo se amplía a un mes. El intervalo mínimo de la copia de seguridad puede ser de una hora.  Para obtener más información, consulte el artículo [Modo de copia de seguridad periódica](configure-periodic-backup-restore.md).

* **Modo de copia de seguridad continua**: elija este modo al crear la cuenta de Azure Cosmos DB. Este modo permite realizar la restauración en cualquier punto temporal de los últimos 30 días. Para más información, consulte [Introducción al modo de copia de seguridad continua](continuous-backup-restore-introduction.md) y aprovisione la copia de seguridad continua según los artículos de [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), la [CLI](provision-account-continuous-backup.md#provision-cli) y [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).

  > [!NOTE]
  > Si configura una cuenta nueva con el modo de copia de seguridad continua, puede realizar la restauración de autoservicio a través de Azure Portal, PowerShell o la CLI. Si la cuenta está configurada en modo continuo, no se puede volver a cambiar al modo periódico. Actualmente, las cuentas existentes con el modo de copia de seguridad periódica no se pueden cambiar al modo continuo.  

En las cuentas habilitadas para Azure Synapse Link, los datos del almacén analítico no se incluyen en las copias de seguridad y restauraciones. Cuando Synapse Link esté habilitado, Azure Cosmos DB seguirá haciendo copias de seguridad automáticamente de los datos del almacén de transacciones en el intervalo programado de copias de seguridad. En este momento, no se admite la copia de seguridad ni la restauración automáticas de los datos del almacén analítico.

## <a name="next-steps"></a>Pasos siguientes

A continuación, puede obtener información sobre cómo configurar y administrar los modos de copia de seguridad periódica y continua para su cuenta:

* Directiva de [configuración y administración de la copia de seguridad periódica](configure-periodic-backup-restore.md).
* ¿Qué es el modo de [copia de seguridad continua](continuous-backup-restore-introduction.md)?
* Aprovisione la copia de seguridad continua mediante [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), la [CLI](provision-account-continuous-backup.md#provision-cli) o [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).
* Restaure la cuenta de copia de seguridad continua mediante [Azure Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), la [CLI](restore-account-continuous-backup.md#restore-account-cli) o [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template).
* [Realice la migración a una cuenta desde una copia de seguridad periódica a una copia de seguridad continua](migrate-continuous-backup.md).
* [Administre los permisos](continuous-backup-restore-permissions.md) necesarios para restaurar datos con el modo de copia de seguridad continua.
* [Modelo de recursos del modo de copia de seguridad continua](continuous-backup-restore-resource-model.md)
