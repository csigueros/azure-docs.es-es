---
title: Restauración de una base de datos en una SQL Managed Instance habilitado para Azure Arc a un momento dado anterior
description: Aquí se explica cómo restaurar una base de datos a un momento específico en el tiempo en SQL Managed Instance habilitado para Azure Arc.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 0d091e9c90217ff9df6fba45d308296a80970053
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401112"
---
#  <a name="perform-a-point-in-time-restore"></a>Realización de una restauración a un momento dado

Use la restauración a un momento dado (PITR) para crear una base de datos como una copia de otra base de datos de algún momento del pasado. En este artículo se describe cómo realizar una restauración a un momento dado de una base de datos en SQL Managed Instance habilitado para Azure Arc.

La restauración a un momento dado puede restaurar una base de datos:

- De una base de datos existente
- A una nueva base de datos en el SQL Managed Instance habilitado para Azure Arc

Puede restaurar una base de datos a un momento dado en una configuración de retención configurada previamente.

La restauración a un momento dado es una configuración de nivel de instancia con dos propiedades: un objetivo de punto de recuperación (RPO) y el tiempo de retención. El objetivo de punto de recuperación determina la frecuencia con la que se realizan las copias de seguridad del registro de transacciones. También es la cantidad de tiempo durante la que se espera la pérdida de datos. El valor de RPO debe especificarse en minutos. El período de retención determina cuánto tiempo (en número de días) quiere almacenar las copias de seguridad de la base de datos en los servicios de datos habilitados para Azure Arc. El período de retención se aplica a los archivos de copia de seguridad completos, diferenciales y de registro de transacciones.  

En este momento, la restauración a un momento dado puede restaurar una base de datos:

- Desde una base de datos existente en una instancia
- A una nueva base de datos en la misma instancia

## <a name="limitations"></a>Limitaciones

La restauración a un momento dado en SQL Managed Instance habilitado para Azure Arc tiene las siguientes limitaciones:

- No es posible la restauración a un momento dado de una instancia completa de SQL Managed Instance habilitado para Azure Arc. 
- Una instancia de SQL Managed Instance habilitado para Azure Arc que se implementa con alta disponibilidad (versión preliminar) no admite actualmente la restauración a un momento dado.
- Solo puede restaurar a la misma instancia de SQL Managed Instance habilitado para Azure Arc.
- La restauración a un momento dado solo se puede realizar a través de un archivo yaml.
- Los archivos de copia de seguridad anteriores que superen el período de retención preconfigurado deben limpiarse manualmente.
- El cambio de nombre de una base de datos inicia una nueva cadena de copia de seguridad en una carpeta nueva.
- La colocación y creación de bases de datos diferentes con los mismos nombres no se controla correctamente en este momento.

## <a name="description"></a>Descripción

La restauración a un momento dado restaura una base de datos a un momento específico. Para restaurar una base de datos a un momento específico, los servicios de datos habilitados para Azure Arc aplican los archivos de copia de seguridad en un orden específico. Por ejemplo:

1. Copia de seguridad completa
2. Copia de seguridad diferencial 
3. Una o varias copias de seguridad del registro de transacciones

:::image type="content" source="media/point-in-time-restore/point-in-time-restore.png" alt-text="Restauración a un momento dado":::

SQL Managed Instance habilitado para Azure Arc incluye la funcionalidad integrada para realizar una restauración a un momento dado. Cada vez que se crea o restaura una nueva base de datos en SQL Managed Instance habilitado para Azure Arc, se crean copias de seguridad automáticamente. 

Hay dos parámetros que afectan a la funcionalidad de restauración a un momento dado:

- Objetivo de punto de recuperación 
- Período de retención

## <a name="create-a-database-from-a-point-in-time"></a>Creación de una base de datos desde un momento dado

A continuación se indican los pasos para restaurar una base de datos en la misma base de datos habilitada para SQL Managed Instance habilitado para Azure Arc mediante `kubectl`:

1. Cree una tarea para la operación de restauración. Cree un archivo .yaml con los parámetros de restauración.

   Por ejemplo:

   ```json
   apiVersion: tasks.sql.arcdata.microsoft.com/v1beta1
      kind: SqlManagedInstanceRestoreTask
   metadata:
     name: sql01-restore-20210909
   namespace: arc
   spec:
     source:
       name: sql01
       database: db01
     restorePoint: "2021-09-09T02:00:00Z"
     destination:
       name: sql01
       database: db02
   ```

   Edite el archivo yaml anterior:

   - `metadata` > `name`: nombre del recurso personalizado de la tarea (CR).
   - `metadata` > `namespace`: espacio de nombres de la instancia de SQL Managed Instance habilitado para Azure Arc.
   - `source` > `name`: nombre de la instancia de SQL Managed Instance habilitado para Azure Arc.
   - `source` > `database`: nombre de la base de datos de **origen** de la instancia de SQL Managed Instance habilitado para Azure Arc de la cual se realizará la restauración.
   - `restorePoint`: momento dado en el que realizará la restauración, en la fecha y hora "UTC".
   - `destination` > `name`: nombre de la instancia de SQL Managed Instance habilitado para Azure Arc.
   - `destination` > `database`: nombre de la base de datos de **destino** de la instancia de SQL Managed Instance habilitado para Azure Arc.


   > [!NOTE] 
   > El nombre del origen y el destino de SQL Managed Instance habilitado para Azure Arc debe ser el mismo.

2. Creación de una tarea para iniciar la operación de restauración a un momento dado

   ```console
   kubectl apply -f sql-restore-task.yaml
   ```

3. Compruebe el estado de la restauración.

   Ejecute el siguiente comando para comprobar el estado de la operación de restauración.

   ```console
   kubectl get sqlmirestoretask -n <namespace>
   ```

   Reemplace `<namespace>` por el espacio de nombres que hospeda la instancia de SQL.

Una vez que el estado de la tarea de restauración muestra **Completado**, la nueva base de datos estará disponible. 

## <a name="troubleshoot-failed-restore-operations"></a>Solución de problemas para las operaciones de restauración con errores

Si el estado de la tarea de restauración muestra **Error**, ejecute el siguiente comando para buscar la causa principal en los eventos.

```console
kubectl describe sqlmirestoretask <taskname> -n <namespace>
```

Por ejemplo:
```console
kubectl describe sqlmirestoretask sql01-restore-20210909 -n arc
```

## <a name="enabledisable-automated-backups"></a>Habilitar o deshabilitar las copias de seguridad automáticas

El servicio de restauración a un momento dado (PITR) está habilitado de forma predeterminada con la siguiente configuración:

- Objetivo de punto de recuperación (RPO) = 300 segundos. Los valores aceptados son 0, o entre 300 y 600 (en segundos).

El RPO configura el servicio para realizar copias de seguridad de registros de todas las bases de datos en la instancia de SQL Managed Instance habilitado para Azure Arc cada 300 segundos o 5 minutos de forma predeterminada. Este valor se puede cambiar a 0 para deshabilitar las copias de seguridad realizadas o a un valor superior en segundos, según el requisito de RPO necesario para las bases de datos de la instancia de SQL. 

Puede deshabilitar las copias de seguridad automatizadas de una instancia específica de SQL Managed Instance habilitado para Azure Arc, o cambiar la configuración predeterminada. El propio servicio PITR no se puede deshabilitar.

El RPO se puede editar si cambia el valor de la propiedad `recoveryPointObjectiveInSeconds` de la manera siguiente:

```console
kubectl edit sqlmi <sqlinstancename>  -n <namespace> -o yaml
```

Este comando abre la especificación de recursos personalizados para SQL Managed Instance habilitado para Azure Arc en el editor predeterminado. Busque la configuración `backup` en `spec`:

```json
backup:
  recoveryPointObjectiveInSeconds: 300
```

Edite el valor de `recoveryPointObjectiveInSeconds` en el editor y guarde los cambios para que la nueva configuración surta efecto. 

> [!NOTE]
> La edición de la configuración de RPO reiniciará el pod que contiene la instancia de SQL Managed Instance habilitado para Azure Arc. 

## <a name="monitor-backups"></a>Supervisión de copias de seguridad

Las copias de seguridad se almacenan en la carpeta `/var/opt/mssql/backups/archived/<dbname>/<datetime>`, donde `<dbname>` es el nombre de la base de datos y `<datetime>` sería una marca de tiempo en formato UTC, para el principio de cada copia de seguridad completa. Cada vez que se inicia una copia de seguridad completa, se crea una nueva carpeta con la copia de seguridad completa y todas las copias de seguridad diferenciales y de registros de transacciones posteriores dentro de esa carpeta. La copia de seguridad completa más reciente y sus copias de seguridad diferenciales y de registros de transacciones posteriores se almacenan en la carpeta `/var/opt/mssql/backups/current/<dbname><datetime>`.

### <a name="clean-up"></a>Limpieza 

Si necesita eliminar copias de seguridad anteriores para crear espacio o ya no las necesita, puede eliminar cualquiera de las carpetas de la carpeta `/var/opt/mssql/backups/archived/`. La eliminación de carpetas en medio de una escala de tiempo podría afectar a la capacidad de restaurar a un momento dado durante esa ventana. Elimine primero las carpetas más antiguas, para permitir una escala de tiempo continua de capacidad de restauración. 

## <a name="next-steps"></a>Pasos siguientes

[Más información sobre las características y capacidades de SQL Managed Instance habilitado para Azure Arc](managed-instance-features.md)

[Comienzo con la creación de un controlador de datos](create-data-controller.md)

[¿Ya ha creado un controlador de datos? Creación de una instancia de SQL Managed Instance habilitado para Azure Arc](create-sql-managed-instance.md)
