---
title: Grupos de equipos en las consultas de registros de Azure Monitor | Microsoft Docs
description: Los grupos de equipos en Azure Monitor permiten limitar las consultas de registros a un conjunto concreto de equipos.  En este artículo se describen los distintos métodos que puede utilizar para crear grupos de equipos y cómo usar estos grupos en una consulta de registros.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2021
ms.openlocfilehash: dd724da3d200f26122ae780c740aa6fe4c84b08c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244969"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Grupos de equipos en las consultas de registros de Azure Monitor
Los grupos de equipos en Azure Monitor permiten limitar las [consultas de registros](./log-query-overview.md) a un conjunto concreto de equipos.  Cada grupo se rellena con equipos mediante una consulta que defina o a través de la importación de grupos de diferentes orígenes.  Cuando el grupo se incluye en una consulta de registros, los resultados se limitan a los registros que coinciden con los equipos del grupo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Creación de un grupo de equipos
Puede crear un grupo de equipos en Azure Monitor mediante cualquiera de los métodos de la tabla siguiente.  En las secciones siguientes se proporcionan detalles sobre cada método. 

| Método | Descripción |
|:--- |:--- |
| Consulta de registro |Cree una consulta de registros que devuelva una lista de equipos. |
| API de búsqueda de registros |Use la API de búsqueda de registros para crear mediante programación un grupo de equipos basándose en los resultados de una consulta de registros. |
| Active Directory |Analice automáticamente la pertenencia al grupo de cualquier equipo agente que sea miembro de un dominio de Active Directory y cree un grupo en Azure Monitor para cada grupo de seguridad. (solo máquinas de Windows)|
| Administrador de configuración | Importe colecciones desde Microsoft Endpoint Configuration Manager y cree un grupo en Azure Monitor para cada una. |
| Windows Server Update Services |Analice automáticamente clientes o servidores WSUS para grupos de destino y cree un grupo en Azure Monitor para cada uno. |

### <a name="log-query"></a>Consulta de registro
Los grupos de equipos creados a partir de una consulta de registros contendrán todos los equipos devueltos por una consulta que defina.  Esta consulta se ejecuta cada vez que se usa el grupo de equipos de forma que se reflejen todos los cambios que se hayan producido desde la creación del grupo.  

Puede usar cualquier consulta para un grupo de equipos, pero debe devolver un conjunto distinto de equipos mediante el uso de `distinct Computer`.  A continuación, se muestra una consulta de ejemplo típica que puede usar para un grupo de equipos.

```kusto
Heartbeat | where Computer contains "srv" | distinct Computer
```

Utilice el procedimiento siguiente para crear un grupo de equipos a partir de una búsqueda de registros en Azure Portal.

1. Haga clic en **Registros** en el menú **Azure Monitor** de Azure Portal.
1. Cree y ejecute una consulta que devuelva los equipos que desee en el grupo.
1. Haga clic en **Guardar** en la parte superior de la pantalla.
1. Cambie **Guardar como** a **Función** y seleccione **Guardar esta consulta como un grupo de equipos**.
1. Proporcione valores para cada propiedad del grupo de equipos que se describe en la tabla y haga clic en **Guardar**.

En la tabla siguiente, se describen las propiedades que definen un grupo de equipos.

| Propiedad | Descripción |
|:---|:---|
| Nombre   | Nombre de la consulta que se va a mostrar en el portal. |
| Alias de función | Un alias único usado para identificar el grupo de equipos en una consulta. |
| Category       | Categoría para organizar las consultas en el portal. |


### <a name="active-directory"></a>Active Directory
Al configurar Azure Monitor para importar pertenencias a grupos de Active Directory, se analiza la pertenencia a grupos de todos los equipos unidos al dominio de Windows con el agente de Log Analytics.  En Azure Monitor se crea un grupo de equipos para cada grupo de seguridad de Active Directory y cada equipo de Windows se agrega a los grupos de equipos correspondientes a los grupos de seguridad de los que son miembros.  Esta pertenencia se actualiza continuamente cada 4 horas.  

> [!NOTE]
> Los grupos importados de Active Directory solo contienen las máquinas de Windows.

Configure Azure Monitor para importar los grupos de seguridad de Active Directory desde el elemento de menú **Grupos de equipos** en el área de trabajo de Log Analytics de Azure Portal.  Seleccione la pestaña **Active Directory** y, luego, **Importar pertenencias a grupos de Active Directory desde los equipos**.  Una vez importados los grupos, el menú muestra el número de equipos con la pertenencia a grupos detectada y el número de grupos de importados.  Puede hacer clic en cualquiera de estos vínculos para devolver los registros de **ComputerGroup** con esta información.

### <a name="windows-server-update-service"></a>Windows Server Update Services
Al configurar Azure Monitor para importar pertenencias a grupos de WSUS, se analiza la pertenencia a grupos de destino de todos los equipos con el agente de Log Analytics.  Si utiliza destinatarios del lado cliente, se importará a Azure Monitor la pertenencia a grupos de todos los equipos conectados a Azure Monitor que formen parte de cualquier grupo de destino de WSUS. Si usa destinatarios del lado servidor, le recomendamos que instale el agente de Log Analytics en el servidor WSUS para que se importe la información de pertenencia a grupos en Azure Monitor.  Esta pertenencia se actualiza continuamente cada 4 horas. 

Configurará Azure Monitor para importar los grupos de WSUS desde el elemento de menú **Grupos de equipos** en el área de trabajo de Log Analytics de Azure Portal.  Seleccione la pestaña **Windows Server Update Service** y, luego, **Importar pertenencias a grupos de WSUS**.  Una vez importados los grupos, el menú muestra el número de equipos con la pertenencia a grupos detectada y el número de grupos de importados.  Puede hacer clic en cualquiera de estos vínculos para devolver los registros de **ComputerGroup** con esta información.

### <a name="configuration-manager"></a>Administrador de configuración
Al configurar Azure Monitor para importar miembros de la recopilación de Configuration Manager, este crea un grupo de equipos para cada colección.  La información de los miembros de la colección se recupera cada tres horas para mantener actualizados los grupos de equipos. Para poder importar colecciones de Configuration Manager, debe [conectar Configuration Manager a Azure Monitor](collect-sccm.md).  

Configurará Azure Monitor para importar los grupos de WSUS desde el elemento de menú **Grupos de equipos** en el área de trabajo de Log Analytics de Azure Portal.  Seleccione la pestaña **System Center Configuration Manager** y, luego, **Importar pertenencias de la recopilación de Configuration Manager**. Una vez importadas las colecciones, el menú muestra el número de equipos con la pertenencia a grupos detectada y el número de grupos de importados.  Puede hacer clic en cualquiera de estos vínculos para devolver los registros de **ComputerGroup** con esta información.

## <a name="managing-computer-groups"></a>Administración de grupos de equipos
Puede ver los grupos de equipos que se crearon a partir de una consulta de registros o la API de búsqueda de registros en el elemento de menú **Grupos de equipos** en el área de trabajo de Log Analytics de Azure Portal.  Seleccione la pestaña **Grupos guardados** para ver la lista de grupos.  

Haga clic en la **x** de la columna **Quitar** para eliminar el grupo de equipos.  Haga clic en el icono **Ver miembros** de un grupo para ejecutar la búsqueda de registros del grupo que devuelve sus miembros.  No se puede modificar un grupo de equipos, se debe eliminar y, a continuación, volver a crear con la configuración modificada.

![Grupos de equipos guardados](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Uso de un grupo de equipos de una consulta de registros
Puede utilizar un grupo de equipos creado a partir de una consulta de registros en una consulta tratando su alias como una función, normalmente con la sintaxis siguiente:

```kusto
Table | where Computer in (ComputerGroup)
```

Por ejemplo, podría usar lo siguiente para devolver registros UpdateSummary solo para los equipos de un grupo de equipos llamado mycomputergroup.

```kusto
UpdateSummary | where Computer in (mycomputergroup)
```

Los grupos de equipos importados y sus equipos incluidos se almacenan en la tabla **ComputerGroup**.  Por ejemplo, la siguiente consulta devolvería una lista de equipos en el grupo Equipos del dominio de Active Directory. 

```kusto
ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer
```

La siguiente consulta devolvería registros UpdateSummary solo para equipos del grupo Equipos del dominio.

```kusto
let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
```

## <a name="computer-group-records"></a>Registros de grupos de equipos
En el área de trabajo de Log Analytics se crea un registro para cada pertenencia a grupos de equipos creada mediante Active Directory o WSUS.  Estos registros tienen el tipo **ComputerGroup** y sus propiedades son las que aparecen en la tabla siguiente.  Para los grupos de equipos basados en consultas de registros no se crean registros.

| Propiedad | Descripción |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Nombre del equipo miembro. |
| `Group` |Nombre del grupo. |
| `GroupFullName` |Ruta de acceso completa al grupo, incluidos el origen y el nombre de origen. |
| `GroupSource` |Origen desde el que se ha recopilado el grupo. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Nombre del origen desde el que se recopiló el grupo.  En el caso de Active Directory, es el nombre del dominio. |
| `ManagementGroupName` |Nombre del grupo de administración de agentes SCOM.  En el caso de los otros agentes, es AOI-\<workspace ID\> |
| `TimeGenerated` |Fecha y hora en la que se creó o actualizó el grupo de equipos. |

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información acerca de las [consultas de registros](./log-query-overview.md) para analizar los datos recopilados de soluciones y orígenes de datos.