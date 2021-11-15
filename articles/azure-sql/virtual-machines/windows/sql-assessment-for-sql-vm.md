---
title: Evaluación de SQL
description: Identifique los problemas de rendimiento y evalúe si la VM con SQL Server está configurada para seguir los procedimientos recomendados mediante la característica SQL Assessment en Azure Portal.
author: ebruersan
ms.author: ebrue
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/02/2021
ms.reviewer: mathoma
ms.custom: ignite-fall-2021
ms.openlocfilehash: b0c163f3ea101ec47eda25bc81f78033893cad6a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500286"
---
# <a name="sql-assessment-for-sql-server-on-azure-vms-preview"></a>SQL Assessment para SQL Server en máquinas virtuales de Azure (versión preliminar)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

La característica SQL Assessment de Azure Portal identifica posibles problemas de rendimiento y evalúa si SQL Server en máquinas virtuales (VM) de Azure está configurado para seguir los procedimientos recomendados mediante el [conjunto de reglas enriquecido](https://github.com/microsoft/sql-server-samples/blob/master/samples/manage/sql-assessment-api/DefaultRuleset.csv) proporcionado por la [API de SQL Assessment](/sql/sql-assessment-api/sql-assessment-api-overview). 

La característica SQL Assessment está actualmente en versión preliminar.

## <a name="overview"></a>Información general

Una vez habilitada la característica SQL Assessment, se examina la instancia de SQL Server y las bases de datos para proporcionar recomendaciones para aspectos como índices, características en desuso, marcas de seguimiento habilitadas o que faltan, estadísticas, etc. Las recomendaciones se muestran en la [página de administración de máquinas virtuales de SQL](manage-sql-vm-portal.md) de [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 


Los resultados de la evaluación se cargan en el [área de trabajo de Log Analytics](../../../azure-monitor/logs/quick-create-workspace.md) mediante [Microsoft Monitoring Agent (MMA)](../../../azure-monitor/agents/log-analytics-agent.md). Si la máquina virtual ya está configurada para usar Log Analytics, la característica SQL Assessment usa la conexión existente.  De lo contrario, la extensión MMA se instala en la VM con SQL Server y se conecta al área de trabajo de Log Analytics especificada.

El tiempo de ejecución de la evaluación depende del entorno (número de bases de datos, objetos, entre otros), con una duración de unos minutos, hasta una hora. Del mismo modo, el tamaño del resultado de la evaluación también depende de su entorno. La evaluación se ejecuta en la instancia y en todas las bases de datos de esa instancia.

## <a name="prerequisites"></a>Prerrequisitos

Para usar la característica SQL Assessment, debe tener los siguientes requisitos previos: 

- La VM con SQL Server debe estar registrada con la [extensión de IaaS de SQL Server en modo completo](sql-agent-extension-manually-register-single-vm.md#full-mode). 
- Un [área de trabajo de Log Analytics](../../../azure-monitor/logs/quick-create-workspace.md) en la misma suscripción que la VM con SQL Server en la que se van a cargar los resultados de la evaluación. 
- SQL Server debe ser la versión 2012 o posterior.


## <a name="enable"></a>Habilitar

Para habilitar SQL Assessment, siga estos pasos: 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya al [recurso de VM con SQL Server](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Seleccione **SQL Assessment** en **Configuración**. 
1. Seleccione **Enable SQL Assessment** (Habilitar SQL Assessment) o **Configuración** para ir a la página **Configuración**. 
1. Active la casilla **Enable SQL Assessment** (Habilitar SQL Assessment) y proporcione lo siguiente:
    1. El [área de trabajo de Log Analytics](../../../azure-monitor/logs/quick-create-workspace.md) en la que se cargarán las evaluaciones. Si la VM con SQL Server no se ha asociado previamente a un área de trabajo, elija un área de trabajo existente en la suscripción en la lista desplegable. De lo contrario, el área de trabajo asociada previamente ya está rellenada.  
    1. **Run schedule** (Ejecutar programación). Puede optar por ejecutar evaluaciones a petición o automáticamente según una programación. Si elige una programación, proporcione la frecuencia (semanal o mensual), el día de la semana, la periodicidad (cada 1-6 semanas) y la hora del día en que se deben iniciar las evaluaciones (hora local de la máquina virtual). 
1. Seleccione **Aplicar** para guardar los cambios e implementar Microsoft Monitoring Agent en la VM con SQL Server si aún no se ha implementado. Una notificación de Azure Portal le indicará que la característica SQL Assessment está lista para la VM con SQL Server. 
    

## <a name="assess-sql-server-vm"></a>Evaluación de VM con SQL Server

Ejecución de evaluaciones:
- Según una programación
- A petición

### <a name="run-scheduled-assessment"></a>Ejecución de la evaluación programada

Si establece una programación en la hoja de configuración, se ejecuta automáticamente una evaluación en la fecha y hora especificadas. Elija **Configuración** para modificar la programación de evaluación. Una vez que proporcione una nueva programación, se sobrescribe la programación anterior.  

### <a name="run-on-demand-assessment"></a>Ejecución de la evaluación a petición

Una vez que la característica SQL Assessment esté habilitada para VM con SQL Server, es posible ejecutar una evaluación a petición. Para ello, seleccione **Ejecutar valoración** en la hoja SQL Assessment de la página [Recurso de VM con SQL Server de Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines).


## <a name="view-results"></a>Vista de resultados

En la sección **Assessments results** (Resultados de las evaluaciones) de la página **SQL Assessment** se muestra una lista de las ejecuciones de evaluación más recientes. En cada fila se muestra la hora de inicio de una ejecución y el estado: programada, en ejecución, cargando resultados, completada o con errores. Cada ejecución de evaluación tiene dos partes: evalúa la instancia y carga los resultados en el área de trabajo de Log Analytics. El campo de estado abarca ambas partes. Los resultados de la evaluación se muestran en los libros de Azure. 

Puede acceder al libro de Azure de resultados de la evaluación de tres maneras: 
- Seleccione el botón **View latest successful assessment** (Ver la evaluación correcta más reciente) en la página **SQL Assessment**.
- Elija una ejecución completada en la sección **Assessments results** (Resultados de las evaluaciones) de la página **SQL Assessment**. 
- Seleccione **View assessment results** (Ver los resultados de la evaluación) en **Top 10 recommendations** (10 recomendaciones principales) que se muestran en la página **Información general** de la página de recursos de VM con SQL. 

Una vez abierto el libro, puede usar la lista desplegable para seleccionar ejecuciones anteriores. Puede ver los resultados de una sola ejecución mediante la página **Resultados** o revisar las tendencias históricas mediante la página **Tendencias**.

### <a name="results-page"></a>Results page

En la página **Resultados** se organizan las recomendaciones con pestañas para *todas, nuevas y resueltas*. Use estas pestañas para ver todas las recomendaciones de la ejecución actual, todas las recomendaciones nuevas (las diferencias frente a las ejecuciones anteriores) o las recomendaciones resueltas de ejecuciones anteriores. Las pestañas le ayudan a realizar un seguimiento del progreso entre ejecuciones. La pestaña *Información* identifica los problemas más recurrentes y las bases de datos con más problemas. Se pueden usar para decidir dónde se deben centrar los esfuerzos. 

El gráfico agrupa los resultados de la evaluación en diferentes categorías de gravedad: alta, media y baja. Seleccione cada categoría para ver la lista de recomendaciones o busque frases clave en el cuadro de búsqueda. Es mejor empezar con las recomendaciones más graves e ir bajando en la lista.

Ordene por **Nombre** en la tabla para ver las recomendaciones agrupadas por una base de datos o instancia determinadas. Use el cuadro de búsqueda para ver determinados tipos de recomendaciones en función del valor de etiqueta o la frase clave, como el rendimiento. Use la flecha abajo de la parte superior derecha de la tabla para obtener resultados de experto en un archivo Excel. 

La sección **correcto** del gráfico identifica las recomendaciones que el sistema ya sigue. 

Para ver información detallada de cada recomendación, seleccione el campo **Mensaje**, como una descripción larga y los recursos en línea pertinentes. 
 
### <a name="trends-page"></a>Página Tendencias

Hay tres gráficos en la página **Tendencias** para mostrar los cambios a lo largo del tiempo: todos los problemas, los nuevos y los resueltos. Los gráficos le ayudan a ver el progreso. Idealmente, el número de recomendaciones debería bajar mientras el número de problemas resueltos sube. La leyenda muestra el número medio de problemas para cada nivel de gravedad. Mantenga el puntero sobre las barras para ver los vales individuales de cada ejecución. 

Si hay varias ejecuciones en un solo día, solo se incluye la ejecución más reciente en los gráficos de la página **Tendencias**. 

## <a name="known-issues"></a>Problemas conocidos

Puede encontrar algunos de los siguientes problemas conocidos al usar SQL Assessment. 

### <a name="configuration-error-for-enable-assessment"></a>Error de configuración al habilitar la evaluación

Si la máquina virtual ya está asociada a un área de trabajo de Log Analytics a la que no tiene acceso o que se encuentra en otra suscripción, verá un error en la hoja de configuración. Para lo primero, puede obtener permisos para esa área de trabajo o cambiar la máquina virtual a otra área de trabajo de Log Analytics según [estas instrucciones](../../../azure-monitor/agents/agent-manage.md) para quitar Microsoft Monitoring Agent. Estamos trabajando para habilitar el escenario en el que el área de trabajo de Log Analytics está en otra suscripción.

### <a name="deployment-failure-for-enable-or-run-assessment"></a>Error de implementación para habilitar o ejecutar evaluación 

Consulte el [historial de implementación](../../../azure-resource-manager/templates/deployment-history.md) del grupo de recursos que contiene la VM con SQL para ver el mensaje de error asociado a la acción con error. 
 
### <a name="failed-assessments"></a>Evaluaciones con error 

**Error en la ejecución de evaluación**: indica que la extensión de IaaS de SQL encontró un problema al ejecutar la evaluación. El mensaje de error detallado estará disponible en el registro de extensiones dentro de la máquina virtual en `C:\WindowsAzure\Logs\Plugins\Microsoft.SqlServer.Management.SqlIaaSAgent\2.0.X.Y`, donde `2.0.X.Y `es la carpeta de versión más reciente presente.  

**Error al cargar el resultado en el área de trabajo de Log Analytics**: indica que Microsoft Monitoring Agent (MMA) no pudo cargar los resultados de una manera limitada en el tiempo. Asegúrese de que la extensión de MMA esté [aprovisionada correctamente](../../../azure-monitor/visualize/vmext-troubleshoot.md) y consulte los problemas de conectividad y los problemas de recopilación de datos enumerados en esta [guía de solución de problemas](../../../azure-monitor/agents/agent-windows-troubleshoot.md). 

>[!TIP]
>Si ha aplicado TLS 1.0 o posterior en Windows y ha deshabilitado los protocolos SSL anteriores, como se describe [aquí](/troubleshoot/windows-server/windows-security/restrict-cryptographic-algorithms-protocols-schannel#schannel-specific-registry-keys), también debe asegurarse de que .NET Framework esté [configurado](../../../azure-monitor/agents/agent-windows.md#configure-agent-to-use-tls-12) para usar criptografía segura. 

**El resultado expiró debido a la retención de datos del área de trabajo de Log Analytics**: indica que los resultados ya no se conservan en el área de trabajo de Log Analytics en función de su directiva de retención. Puede [cambiar el período de retención](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) del área de trabajo

## <a name="next-steps"></a>Pasos siguientes

- Para registrar la VM con SQL Server con la extensión de IaaS de SQL Server en SQL Server en las máquinas virtuales de Azure, consulte los artículos sobre la [instalación automática](sql-agent-extension-automatic-registration-all-vms.md), las [máquinas virtuales únicas](sql-agent-extension-manually-register-single-vm.md) o las [máquinas virtuales en masa](sql-agent-extension-manually-register-vms-bulk.md).
- Para más información sobre las funcionalidades disponibles por la extensión IaaS de SQL Server para SQL Server en máquinas virtuales de Azure, consulte [Administración de VM con SQL Server mediante Azure Portal](manage-sql-vm-portal.md).
