---
title: Migración de una cuenta de Azure Cosmos DB del modo periódico al modo de copia de seguridad continua
description: Azure Cosmos DB admite actualmente una migración unidireccional del modo periódico al modo continuo y es irreversible. Después de migrar del modo periódico al modo continuo, puede aprovechar las ventajas de este último.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/26/2021
ms.author: sngun
ms.topic: how-to
ms.reviewer: sngun
ms.openlocfilehash: 270c0fd585c2232b86011673e460737173106b09
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479083"
---
# <a name="migrate-an-azure-cosmos-db-account-from-periodic-to-continuous-backup-mode"></a>Migración de una cuenta de Azure Cosmos DB del modo periódico al modo de copia de seguridad continua
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Las cuentas de Azure CosmosDB con la directiva de copia de seguridad en modo periódico se pueden migrar al modo continuo mediante [Azure Portal](#portal), la [CLI](#cli), [PowerShell](#powershell) o las [plantillas de Resource Manager](#ARM-template). La migración del modo periódico al modo continuo es una migración unidireccional y no es reversible. Después de migrar del modo periódico al modo continuo, puede aprovechar las ventajas de este último.

Estos son los principales motivos para migrar al modo continuo:

* La capacidad de realizar un autoservicio de restauración mediante Azure Portal, la CLI o PowerShell.
* La capacidad de restaurar en el momento granularidad del segundo dentro de la ventana de los últimos 30 días.
* La capacidad de asegurarse de que la copia de seguridad es coherente entre particiones o intervalos de claves de partición dentro de un período.
* La capacidad de restaurar el contenedor, la base de datos o la cuenta completa cuando se elimina o modifica.
* La capacidad de elegir los eventos en el contenedor, la base de datos o la cuenta y decidir cuándo iniciar la restauración.

> [!NOTE]
> La funcionalidad de migración es un solo sentido y es una acción irreversible. Lo que significa que, una vez migrado del modo periódico al modo continuo, no puede volver al modo periódico.
>
> Puede migrar una cuenta al modo de copia de seguridad continua solo si se cumplen las siguientes condiciones:
>
> * Si la cuenta es del tipo SQL API o API para MongoDB.
> * Si la cuenta tiene una sola región de escritura.
> * Si la cuenta no está habilitada con claves administradas por el cliente (CMK).
> * Si la cuenta no está habilitada con el almacén analítico.

## <a name="permissions"></a>Permisos

Para realizar la migración, necesita permiso `Microsoft.DocumentDB/databaseAccounts/write` para la cuenta que se está migrando.  

## <a name="pricing-after-migration"></a>Precios después de la migración

Después de migrar la cuenta al modo de copia de seguridad continua, el costo con este modo es diferente en comparación con el modo de copia de seguridad periódica. El costo de copia de seguridad en modo continuo es significativamente más económico que el modo periódico. Para más información, consulte el ejemplo del [precio del modo de copia de seguridad continua](continuous-backup-restore-introduction.md#continuous-backup-pricing).

## <a name="migrate-using-portal"></a><a id="portal"></a>Migración mediante el portal

Siga estos pasos para migrar la cuenta del modo de copia de seguridad periódica al modo de copia de seguridad continua:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Vaya a la cuenta de Azure Cosmos DB y abra el panel **Características**. Seleccione **Copia de seguridad continua** y elija **Habilitar**.

   :::image type="content" source="./media/migrate-continuous-backup/enable-backup-migration.png" alt-text="Migración al modo continuo mediante Azure Portal" lightbox="./media/migrate-continuous-backup/enable-backup-migration.png":::

1. Cuando la migración está en curso, el estado muestra **Pendiente**. Una vez completado, el estado cambia a **On**. El tiempo de migración depende del tamaño de los datos de su cuenta.

   :::image type="content" source="./media/migrate-continuous-backup/migration-status.png" alt-text="Compruebe el estado de la migración desde Azure Portal" lightbox="./media/migrate-continuous-backup/migration-status.png":::

## <a name="migrate-using-powershell"></a><a id="powershell"></a>Migrar mediante PowerShell

Instale la [versión más reciente de Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) o una versión superior a la 6.2.0. Después, ejecute los pasos siguientes:

1. Conexión a la cuenta de Azure:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

1. Migre su cuenta del modo de copia de seguridad periódica al modo de copia de seguridad continua:

   ```azurepowershell-interactive
   Update-AzCosmosDBAccount ` 
     -ResourceGroupName "myrg" ` 
     -Name "myAccount" `
     -BackupPolicyType Continuous
   ```

## <a name="migrate-using-cli"></a><a id="cli"></a>Migración mediante la CLI

1. Instale la versión más reciente de la CLI de Azure:

   * Si no tiene la CLI, [instale](/cli/azure/) la versión más reciente de la CLI de Azure o una versión superior a la 2.26.0.
   * Si ya tiene instalada la CLI de Azure, use el comando `az upgrade` para actualizar a la versión más reciente.
   * Como alternativa, el usuario también puede usar Cloud Shell desde Azure Portal.

1. Inicie sesión en su cuenta de Azure y ejecute el siguiente comando para migrar la cuenta al modo continuo:

   ```azurecli-interactive
   az login

   az cosmosdb update -n <myaccount> -g <myresourcegroup> --backup-policy-type continuous
   ```

1. Una vez completada correctamente la migración, la salida muestra que el objeto backupPolicy tiene la propiedad type establecida en Continuous.

   ```console
    {
      "apiProperties": null,
      "backupPolicy": {
        "type": "Continuous"
      },
      "capabilities": [],
      "connectorOffer": null,
      "consistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxIntervalInSeconds": 5,
        "maxStalenessPrefix": 100
      },
    …
    }
   ```

## <a name="migrate-using-resource-manager-template"></a><a id="ARM-template"></a>Migración mediante una plantilla de Resource Manager

Para migrar al modo de copia de seguridad continua mediante la plantilla de ARM, busque la sección backupPolicy de la plantilla y actualice la propiedad `type`. Por ejemplo, si la plantilla existente tiene una directiva de copia de seguridad como el siguiente objeto JSON:

```json
"backupPolicy": {
   "type": "Periodic",
   "periodicModeProperties": {
   "backupIntervalInMinutes": 240,
   "backupRetentionIntervalInHours": 8
   }
},
```

Reemplácelo por el objeto JSON código que verá a continuación:

```json
"backupPolicy": {
   "type": "Continuous"
},
```

A continuación, implemente la plantilla mediante Azure PowerShell o la CLI. En el ejemplo siguiente se muestra cómo implementar la plantilla con un comando de la CLI:

```azurecli
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="what-to-expect-during-and-after-migration"></a>¿Qué espera durante y después de la migración?

Al migrar del modo periódico al modo continuo, no puede ejecutar ninguna operación del plano de control que realice actualizaciones o eliminaciones de nivel de cuenta. Por ejemplo, no se pueden ejecutar operaciones como las siguientes mientras la migración está en curso: incorporación o eliminación de regiones, conmutación por error de cuenta, actualización de la directiva de copia de seguridad, etc. El tiempo de migración depende del tamaño de los datos y del número de regiones de la cuenta. La acción de restauración de las cuentas migradas solo se realiza correctamente desde el momento en que la migración se completa correctamente.

Puede restaurar la cuenta una vez completada la migración. Si la migración se completa a las 13:00 PST, puede realizar una restauración a un momento dado a partir de las 13:00 PST.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

#### <a name="does-the-migration-only-happen-at-the-account-level"></a>¿La migración solo se realiza en el nivel de cuenta?
Sí.

#### <a name="which-accounts-can-be-targeted-for-backup-migration"></a>¿Qué cuentas se pueden dirigir a la migración de copia de seguridad?
Actualmente, la API de SQL y la API para cuentas de MongoDB con una única región de escritura, que tienen un rendimiento compartido, aprovisionado o aprovisionado automáticamente, admiten la migración.

Las cuentas habilitadas con almacenamiento analítico, regiones de varias escrituras y claves administradas por el cliente (CMK) no se admiten para la migración.

#### <a name="does-the-migration-take-time-what-is-the-typical-time"></a>¿La migración tarda tiempo? ¿Cuál es el tiempo típico?
La migración tarda tiempo y este depende del tamaño de los datos y del número de regiones de la cuenta. Puede obtener el estado de migración mediante la CLI de Azure o comandos de PowerShell. En el caso de cuentas grandes con decenas de terabytes de datos, la migración puede tardar hasta unos días en completarse.

#### <a name="does-the-migration-cause-any-availability-impactdowntime"></a>¿Provoca la migración algún impacto en la disponibilidad o tiempo de inactividad?
No, la operación de migración tiene lugar en segundo plano, por lo que las solicitudes de cliente no se verán afectadas. Sin embargo, es necesario realizar algunas operaciones de back-end durante la migración y puede tardar más tiempo si la cuenta está bajo una carga pesada.

#### <a name="what-happens-if-the-migration-fails-will-i-still-get-the-periodic-backups-or-get-the-continuous-backups"></a>¿Qué ocurre si la migración falla? ¿Seguiré obteniendo las copias de seguridad periódicas o las copias de seguridad continuas?
Una vez iniciado el proceso de migración, la cuenta comenzará a convertirse en un modo continuo.  Si se produce un error en la migración, debe iniciarla de nuevo hasta que se realice correctamente.

#### <a name="how-do-i-perform-a-restore-to-a-timestamp-beforeduringafter-the-migration"></a>Cómo realizar una restauración a una marca de tiempo antes, durante o después de la migración?
Supongamos que inició la migración en t1 y finalizó en t5; y no puede usar una marca de tiempo de restauración entre t1 y t5.

Para restaurar a un momento después de t5 porque la cuenta está ahora en modo continuo, puede realizar la restauración mediante Azure Portal, la CLI o PowerShell, como suele hacer con la cuenta continua. Esta solicitud de autoservicio de restauración solo se puede realizar una vez completada la migración.

Para restaurar a un momento anterior a t1, puede abrir una incidencia de soporte técnico como lo haría normalmente con la cuenta de copia de seguridad periódica. Después de la migración, tiene hasta 30 días para realizar la restauración periódica.  Durante estos 30 días, puede restaurar en función de la retención o el intervalo de copia de seguridad de la cuenta antes de la migración.  Por ejemplo, si la configuración de copia de seguridad conservaba 24 copias en un intervalo de 1 hora, puede restaurar a cualquier momento entre [t1 – 24 horas] y [t1].

#### <a name="which-account-level-control-plane-operations-are-blocked-during-migration"></a>¿Qué operaciones del plano de control de nivel de cuenta se bloquean durante la migración?
Las operaciones como agregar o quitar región, conmutación por error, cambiar la directiva de copia de seguridad o cambios de rendimiento que produzcan movimiento de datos se bloquean durante la migración.

#### <a name="if-the-migration-fails-for-some-underlying-issue-would-it-still-block-the-control-plane-operation-until-it-is-retried-and-completed-successfully"></a>Si se produce un error en la migración por algún problema subyacente, ¿seguiría bloqueando la operación del plano de control hasta que se reintente y se complete correctamente?
La migración con error no bloqueará ninguna operación del plano de control. Si se produce un error en la migración, se recomienda reintentar hasta que se realice correctamente antes de realizar cualquier otra operación del plano de control.

#### <a name="is-it-possible-to-cancel-the-migration"></a>¿Es posible cancelar la migración?
No es posible cancelar la migración porque no es una operación reversible.

#### <a name="is-there-a-tool-that-can-help-estimate-migration-time-based-on-the-data-usage-and-number-of-regions"></a>¿Hay una herramienta que pueda ayudar a calcular el tiempo de migración en función del uso de datos y el número de regiones?
No hay ninguna herramienta para calcular el tiempo. Sin embargo, nuestras ejecuciones de escalado indican que una sola región con 1 TB de datos tarda aproximadamente una hora y media.

Para las cuentas de varias regiones, calcule el tamaño total de los datos como `Number_of_regions * Data_in_single_region`.

#### <a name="since-the-continuous-backup-mode-is-now-ga-would-you-still-recommend-restoring-a-copy-of-your-account-and-try-migration-on-the-copy-before-deciding-to-migrate-the-production-account"></a>Puesto que el modo de copia de seguridad continua ahora está disponible con carácter general, ¿seguiría recomendando restaurar una copia de su cuenta e intentar la migración en dicha copia antes de decidir migrar la cuenta de producción?
Se recomienda probar la característica del modo de copia de seguridad continua para ver que funciona según lo previsto antes de migrar las cuentas de producción. El motivo es que es una operación unidireccional y no es reversible.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el modo de copia de seguridad continua, vea los siguientes artículos:

* [Introducción al modo de copia de seguridad continua con restauración a un momento dado.](continuous-backup-restore-introduction.md)

* [Modelo de recursos del modo de copia de seguridad continua](continuous-backup-restore-resource-model.md).

* Restaure una cuenta mediante [Azure Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), la [CLI](restore-account-continuous-backup.md#restore-account-cli) o [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template).

¿Intenta planear la capacidad de una migración a Azure Cosmos DB?
   * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea este artículo para [calcular las unidades de solicitud utilizando núcleos o CPU virtuales](convert-vcore-to-request-unit.md). 
   * Si conoce las velocidades de solicitud típicas de la carga de trabajo de base de datos actual, lea sobre el [cálculo de las unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).
