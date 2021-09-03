---
title: Restauración de una base de datos a un momento dado
description: Se explica cómo realizar una operación de restauración a un momento dado.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: fe4d250035c58bde2ba52e71046ea2f88854ff78
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743832"
---
#  <a name="perform-a-point-in-time-restore"></a>Realización de una restauración a un momento dado


SQL Managed Instance habilitado para Azure Arc viene integrado con muchas funcionalidades, como PaaS. Una de estas funcionalidades es la capacidad de restaurar una base de datos a un momento dado, dentro de la configuración de retención establecida previamente. En este artículo se describe cómo realizar una restauración a un momento dado de una base de datos en SQL Managed Instance habilitado para Azure Arc.

La restauración a un momento dado es una configuración de nivel de instancia con dos propiedades: objetivo de punto de recuperación (RPO) y tiempo de retención (RT). La configuración del objetivo de punto de recuperación determina la frecuencia con la que se realizan las copias de seguridad del registro de transacciones. También es la cantidad de tiempo durante la que se espera la pérdida de datos. El tiempo de retención se corresponde con el tiempo durante el cual se mantienen las copias de seguridad (completa, diferencial y registro de transacciones).  

La restauración a un momento dado puede restaurar una base de datos:

- desde una base de datos existente en una instancia de SQL,
- a una nueva base de datos en la misma instancia de SQL.

### <a name="limitations"></a>Limitaciones

La restauración a un momento dado en SQL Managed Instance habilitado para Azure Arc tiene las siguientes limitaciones:

- Solo puede restaurar a la misma instancia de SQL Managed Instance habilitado para Azure Arc.
- La restauración a un momento dado solo se puede realizar a través de un archivo yaml. 
- Los archivos de copia de seguridad anteriores que superen el período de retención preconfigurado deben limpiarse manualmente.
- El cambio de nombre de una base de datos inicia una nueva cadena de copia de seguridad en una carpeta nueva.
- La colocación y creación de bases de datos diferentes con los mismos nombres no se controla correctamente en este momento.

### <a name="edit-pitr-settings"></a>Edición de la configuración de PITR

##### <a name="enabledisable-automated-backups"></a>Habilitar o deshabilitar las copias de seguridad automáticas

El servicio de restauración a un momento dado (PITR) está habilitado de forma predeterminada con la siguiente configuración:

- Objetivo de punto de recuperación (RPO) = 300 segundos. Los valores aceptados son 0, o entre 300 y 600 (en segundos).

Esto implica que las copias de seguridad de registros de todas las bases de datos de SQL Managed Instance habilitado para Azure Arc se realizarán cada 300 segundos o 5 minutos de forma predeterminada. Este valor se puede cambiar a 0 para deshabilitar las copias de seguridad realizadas o a un valor superior en segundos, según el requisito de RPO necesario para las bases de datos de la instancia de SQL. 

El propio servicio PITR no se puede deshabilitar, pero las copias de seguridad automatizadas de una instancia específica de SQL Managed Instance habilitado para Azure Arc se pueden deshabilitar, o bien se puede modificar la configuración predeterminada.

El RPO se puede editar cambiando el valor de la propiedad ```recoveryPointObjectiveInSeconds``` de la manera siguiente:

```
kubectl edit sqlmi <sqlinstancename>  -n <namespace> -o yaml
```

Esto debería abrir la especificación de recursos personalizados para SQL Managed Instance habilitado para Azure Arc en el editor predeterminado. Busque la configuración ```backup``` en ```spec```:

```
backup:
  recoveryPointObjectiveInSeconds: 300
```

Edite el valor de ```recoveryPointObjectiveInSeconds``` en el editor y guarde los cambios para que la nueva configuración surta efecto. 

> [!NOTE]
> La edición de la configuración de RPO reiniciará el pod que contiene la instancia de SQL Managed Instance habilitado para Azure Arc. 

### <a name="restore-a-database-to-a-point-in-time"></a>Restauración de una base de datos a un momento dado

Se puede realizar una operación de restauración en una instancia de SQL Managed Instance habilitado para Azure Arc para restaurar desde una base de datos de origen a un momento dado dentro del período de retención. 
**(1) Cree un archivo yaml como se indica a continuación en el editor:**

```
apiVersion: tasks.sql.arcdata.microsoft.com/v1beta1
kind: SqlManagedInstanceRestoreTask
metadata:
  name: sql01-restore-20210707
  namespace: arc
spec:
  source:
    name: sql01
    database: db01
  restorePoint: "2021-07-01T02:00:00Z"
  destination:
    name: sql01
    database: db02
```

- name: cadena única para cada recurso personalizado que es un requisito para kubernetes.
- namespace: espacio de nombres donde se ejecuta la instancia de SQL Managed Instance habilitado para Azure Arc.
- source > name: nombre de la instancia de SQL Managed Instance habilitado para Azure Arc.
- source > database: nombre de la base de datos de origen de la instancia de SQL Managed Instance habilitado para Azure Arc.
- restorePoint: momento dado para la operación de restauración en la fecha y hora "UTC".
- destination > name: nombre de la instancia de SQL Managed Instance habilitado para Azure Arc a la que se realizará la restauración. Actualmente solo se admiten restauraciones en las mismas instancias.
- destination > database: nombre de la nueva base de datos a la que se aplicaría la restauración.

**(2) Aplicar el archivo yaml para crear una tarea para iniciar la operación de restauración**

Ejecute el comando como se muestra a continuación para iniciar la operación de restauración:

```
kubectl apply -f sql01-restore-task.yaml
```

> [!NOTE]
> El nombre de la tarea dentro del recurso personalizado y el nombre de archivo no tienen que ser iguales.


**Comprobación del estado de la restauración**

- El estado de la tarea de restauración se actualiza aproximadamente cada 10 segundos, y el estado cambia de "En espera" --> "Restaurando" --> "Completado"/"Error". 
- Mientras se restaura una base de datos, el estado reflejaría "Restaurando".

El estado de la tarea se puede recuperar de la siguiente manera:

```
kubectl get sqlmirestoretask -n arc
``` 

### <a name="monitor-your-backups"></a>Supervisión de las copias de seguridad

Las copias de seguridad se almacenan en la carpeta ```/var/opt/mssql/backups/archived/<dbname>/<datetime>```, donde ```<dbname>``` es el nombre de la base de datos y ```<datetime>``` sería una marca de tiempo en formato UTC, para el principio de cada copia de seguridad completa. Cada vez que se inicia una copia de seguridad completa, se crea una nueva carpeta con la copia de seguridad completa y todas las copias de seguridad diferenciales y de registros de transacciones posteriores dentro de esa carpeta. La copia de seguridad completa más reciente y sus copias de seguridad diferenciales y de registros de transacciones posteriores se almacenan en la carpeta ```/var/opt/mssql/backups/current/<dbname><datetime>```.


### <a name="clean-up"></a>Limpieza 

Si necesita eliminar copias de seguridad anteriores para crear espacio o ya no las necesita, puede eliminar cualquiera de las carpetas de la carpeta ```/var/opt/mssql/backups/archived/```. La eliminación de carpetas en medio de una escala de tiempo podría afectar a la capacidad de restaurar a un momento dado durante esa ventana. Se recomienda eliminar primero las carpetas más antiguas, lo que permite una escala de tiempo continua de capacidad de restauración. 


