---
title: Restauración de una base de datos en una SQL Managed Instance habilitado para Azure Arc a un momento dado anterior
description: Aquí se explica cómo restaurar una base de datos a un momento específico en el tiempo en SQL Managed Instance habilitado para Azure Arc.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 51235a67d6f00410ee2b3db753e892cf05992baa
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563271"
---
#  <a name="perform-a-point-in-time-restore"></a>Realización de una restauración a un momento dado

Use la restauración a un momento dado (PITR) para crear una base de datos como una copia de otra base de datos de algún momento del pasado que se encuentre dentro del período de retención. En este artículo se describe cómo realizar una restauración a un momento dado de una base de datos en SQL Managed Instance habilitado para Azure Arc.

La restauración a un momento dado puede restaurar una base de datos:

- De una base de datos existente
- A una nueva base de datos en la misma instancia de SQL Managed Instance habilitado para Azure Arc

Puede restaurar una base de datos a un momento dado en una configuración de retención configurada previamente.
Puede comprobar la configuración de retención de una instancia de SQL Managed Instance habilitado para Azure Arc como se muestra a continuación:

Para el modo de conexión **directa**:
```
az sql mi-arc show --name <SQL instance name> --resource-group <resource-group>
#Example
az sql mi-arc show --name sqlmi --resource-group myresourcegroup
```
Para el modo de conexión **indirecta**:
```
az sql mi-arc show --name <SQL instance name> --k8s-namespace <SQL MI namespace> --use-k8s
#Example
az sql mi-arc show --name sqlmi --k8s-namespace arc --use-k8s
```

En este momento, la restauración a un momento dado puede restaurar una base de datos:

- Desde una base de datos existente en una instancia
- A una nueva base de datos en la misma instancia

## <a name="automatic-backups"></a>Copias de seguridad automáticas

La instancia de SQL Managed Instance habilitado para Azure Arc tiene habilitada la característica de copias de seguridad automáticas integradas. Cada vez que crea o restaura una nueva base de datos, la instancia de SQL Managed Instance habilitado para Azure Arc inicia una copia de seguridad completa de inmediato y programa automáticamente copias de seguridad diferenciales y del registro de transacciones. SQL Managed Instance almacena estas copias de seguridad en la clase de almacenamiento especificada durante la implementación. 

La restauración a un momento dado permite restaurar una base de datos a un momento dado específico, dentro del período de retención. Para restaurar una base de datos a un momento específico, los servicios de datos habilitados para Azure Arc aplican los archivos de copia de seguridad en un orden específico. Por ejemplo:

1. Copia de seguridad completa
2. Copia de seguridad diferencial 
3. Una o varias copias de seguridad del registro de transacciones

:::image type="content" source="media/point-in-time-restore/point-in-time-restore.png" alt-text="Restauración a un momento dado":::

Actualmente, las copias de seguridad completas se realizan una vez a la semana; las copias de seguridad diferenciales se realizan cada 12 horas; y las copias de seguridad del registro de transacciones, cada 5 minutos.

## <a name="retention-period"></a>Período de retención

El período de retención predeterminado para una nueva instancia de SQL Managed Instance habilitado para Azure Arc es de siete días, y se puede ajustar con valores de 0 o de 1 a 35 días. El período de retención se puede establecer durante la implementación de SQL Managed Instance al especificar la propiedad `--retention-days`. Los archivos de copia de seguridad anteriores al período de retención configurado se eliminan automáticamente.


## <a name="create-a-database-from-a-point-in-time-using-az"></a>Creación de una base de datos desde un momento dado mediante az

```azurecli
az sql midb-arc restore --managed-instance <SQL managed instance> --name <source DB name> --dest-name <Name for new db> --k8s-namespace <namespace of managed instance> --time "YYYY-MM-DDTHH:MM:SSZ" --use-k8s
#Example
az sql midb-arc restore --managed-instance sqlmi1 --name Testdb1 --dest-name mynewdb --k8s-namespace arc --time "2021-10-29T01:42:14.00Z" --use-k8s
```

También puede usar la opción `--dry-run` para validar la operación de restauración sin restaurar realmente la base de datos. 

```azurecli
az sql midb-arc restore --managed-instance <SQL managed instance> --name <source DB name> --dest-name <Name for new db> --k8s-namespace <namespace of managed instance> --time "YYYY-MM-DDTHH:MM:SSZ" --use-k8s --dry-run
#Example
az sql midb-arc restore --managed-instance sqlmi1 --name Testdb1 --dest-name mynewdb --k8s-namespace arc --time "2021-10-29T01:42:14.00Z" --use-k8s --dry-run
```


## <a name="create-a-database-from-a-point-in-time-using-azure-data-studio"></a>Creación de una base de datos desde un momento dado mediante Azure Data Studio

También puede restaurar una base de datos a un momento dado desde Azure Data Studio como se muestra a continuación:
1. Inicie Azure Data Studio.
2. Asegúrese de tener las extensiones de Arc necesarias, como se describe en [Herramientas](install-client-tools.md).
3. Conexión al controlador de datos de Azure Arc
4. Expanda el nodo del controlador de datos y haga clic con el botón derecho en SQL Managed Instance habilitado para Azure Arc y seleccione Administrar. Azure Data Studio inicia el panel de SQL Managed Instance.
5. Haga clic en la pestaña **Copias de seguridad** del panel.
6. Debería ver una lista de bases de datos en la instancia de SQL Managed Instance y sus ventanas de tiempo de restauración Más antiguo y Más reciente, y un icono para comenzar a **restaurar**.
7. Haga clic en el icono de la base de datos desde la que desea restaurar. Azure Data Studio abre una hoja hacia el lado derecho.
8. Escriba la entrada necesaria en la hoja y haga clic en **Restaurar**.

### <a name="monitor-progress"></a>Supervisión de progreso

Cuando se inicia una restauración, se crea una tarea en el clúster de Kubernetes que ejecuta las operaciones de restauración reales de copias de seguridad completas, diferenciales y de registro. El progreso de esta actividad se puede supervisar desde el clúster de Kubernetes como se muestra a continuación:

```console
kubectl get sqlmirestoretask -n <namespace>
#Example
kubectl get sqlmirestoretask -n arc
```

Para obtener más detalles de la tarea, ejecute `kubectl describe` en la tarea. Por ejemplo:

```console
kubectl describe sqlmirestoretask <nameoftask> -n <namespace>
```

## <a name="configure-retention-period"></a>Configuración del período de retención

El período de retención de una instancia de SQL Managed Instance habilitado para Azure Arc se puede volver a configurar a partir de su configuración original como se muestra a continuación:

> [!WARNING] 
> Si reduce el período de retención actual, perderá la capacidad de restaurar a puntos en el tiempo más antiguos que el nuevo período de retención. Se eliminan las copias de seguridad que ya no son necesarias para proporcionar PITR dentro del nuevo período de retención. Si aumenta el período de retención actual, no obtendrá de forma inmediata la capacidad de restaurar a puntos en el tiempo más antiguos dentro del nuevo período de retención. Obtiene esa capacidad a lo largo del tiempo, ya que el sistema empieza a conservar las copias de seguridad durante más tiempo.

### <a name="change-retention-period-for-direct-connected-sql-managed-instance"></a>Cambio del período de retención para una instancia de SQL Managed Instance con conexión **directa**

```azurecli
az sql mi-arc edit  --name <SQLMI name> --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 10
#Example
az sql mi-arc edit  --name sqlmi --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 10
```

### <a name="change-retention-period-for-indirect-connected-sql-managed-instance"></a>Cambio del período de retención para una instancia de SQL Managed Instance con conexión **indirecta**

```azurecli
az sql mi-arc edit  --name <SQLMI name> --k8s-namespace <namespace>  --use-k8s --retention-days <retentiondays>
#Example
az sql mi-arc edit  --name sqlmi --k8s-namespace arc  --use-k8s --retention-days 10
```

## <a name="disable-automatic-backups"></a>Deshabilitación de copias de seguridad automáticas

Para deshabilitar las copias de seguridad automatizadas de una instancia específica de SQL Managed Instance habilitado para Azure Arc, establezca la propiedad `--retention-days` en 0, como se indica a continuación.

> [!WARNING]
> Si deshabilita las copias de seguridad automáticas para una instancia de SQL Managed Instance habilitado para Azure Arc, se eliminarán las copias de seguridad automáticas configuradas, y se perderá la capacidad de realizar una restauración a un momento dado. Si es necesario, puede cambiar la propiedad `retention-days` para volver a iniciar copias de seguridad automáticas.

### <a name="disable-automatic-backups-for-direct-connected-sql-managed-instance"></a>Deshabilitación de copias de seguridad automáticas para una instancia de SQL Managed Instance con conexión **directa**

```azurecli
az sql mi-arc edit  --name <SQLMI name> --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 0
#Example
az sql mi-arc edit  --name sqlmi --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 0
```

### <a name="disable-automatic-backups-for-indirect-connected-sql-managed-instance"></a>Deshabilitación de copias de seguridad automáticas para una instancia de SQL Managed Instance con conexión **indirecta**

```azurecli
az sql mi-arc edit  --name <SQLMI name> --k8s-namespace <namespace>  --use-k8s --retention-days 0
#Example
az sql mi-arc edit  --name sqlmi --k8s-namespace arc  --use-k8s --retention-days 0
```

## <a name="monitor-backups"></a>Supervisión de copias de seguridad

Las copias de seguridad se almacenan en la carpeta `/var/opt/mssql/backups/archived/<dbname>/<datetime>`, donde `<dbname>` es el nombre de la base de datos y `<datetime>` sería una marca de tiempo en formato UTC, para el principio de cada copia de seguridad completa. Cada vez que se inicia una copia de seguridad completa, se crea una nueva carpeta con la copia de seguridad completa y todas las copias de seguridad diferenciales y de registros de transacciones posteriores dentro de esa carpeta. La copia de seguridad completa más reciente y sus copias de seguridad diferenciales y de registros de transacciones posteriores se almacenan en la carpeta `/var/opt/mssql/backups/current/<dbname><datetime>`.

## <a name="limitations"></a>Limitaciones

La restauración a un momento dado en SQL Managed Instance habilitado para Azure Arc tiene las siguientes limitaciones:

- No es posible la restauración a un momento dado de una instancia completa de SQL Managed Instance habilitado para Azure Arc. 
- Una instancia de SQL Managed Instance habilitado para Azure Arc que se implementa con alta disponibilidad (versión preliminar) no admite actualmente la restauración a un momento dado.
- Solo puede restaurar a la misma instancia de SQL Managed Instance habilitado para Azure Arc.
- La colocación y creación de bases de datos diferentes con los mismos nombres no se controla correctamente en este momento.
- Si se proporciona una fecha futura al ejecutar la operación de restauración mediante ```--dry-run```, se producirá un error.




## <a name="next-steps"></a>Pasos siguientes

[Más información sobre las características y funcionalidades de SQL Managed Instance habilitado para Azure Arc](managed-instance-features.md)

[Comienzo con la creación de un controlador de datos](create-data-controller.md)

[¿Ya ha creado un controlador de datos? Creación de una instancia de SQL Managed Instance habilitada para Azure Arc](create-sql-managed-instance.md)
