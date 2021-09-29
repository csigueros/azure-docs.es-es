---
title: Migración a Connection Monitor desde Network Performance Monitor
titleSuffix: Azure Network Watcher
description: Aprenda a migrar a Connection Monitor desde Network Performance Monitor.
author: vinynigam
ms.service: network-watcher
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 84daa5527617584d420db02b012bec28760a146c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614569"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migración a Connection Monitor desde Network Performance Monitor

> [!IMPORTANT]
> A partir del 1 de julio de 2021, no podrá agregar nuevas pruebas en un área de trabajo existente ni habilitar un área de trabajo nueva con Network Performance Monitor. Puede seguir usando las pruebas creadas antes del 1 de julio de 2021. Para minimizar la interrupción del servicio en las cargas de trabajo actuales, migre las pruebas desde Network Performance Monitor a la nueva instancia de Connection Monitor en Azure Network Watcher antes del 29 de febrero de 2024.

Puede migrar las pruebas de Network Performance Monitor (NPM) a la nueva y mejorada característica Connection Monitor con un solo clic y sin tiempo de inactividad. Para más información sobre las ventajas, consulte [Connection Monitor](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Puntos clave a tener en cuenta

La migración ayuda a generar los siguientes resultados:

* Los agentes locales y la configuración de firewall funcionarán tal cual. No es preciso realizar cambios. Los agentes de Log Analytics instalados en Azure Virtual Machines deben reemplazarse por la [extensión de Network Watcher](../virtual-machines/extensions/network-watcher-windows.md).
* Las pruebas existentes se asignan a Connection Monitor -> Grupo de prueba -> Test format (Formato de prueba). Al seleccionar **Editar**, puede ver y modificar las propiedades de la nueva característica Connection Monitor, descargar una plantilla para realizar cambios en ella y enviar la plantilla a través de Azure Resource Manager.
* Los agentes envían datos tanto al área de trabajo de Log Analytics como a las métricas.
* Supervisión de datos:
   * **Datos en Log Analytics**: antes de la migración, los datos permanecen en el área de trabajo en la que se configura NPM en la tabla NetworkMonitoring. Después de la migración, los datos pasan a las tablas NetworkMonitoring, NWConnectionMonitorTestResult y NWConnectionMonitorPathResult en la misma área de trabajo. Una vez deshabilitadas las pruebas en NPM, los datos solo se almacenan en las tablas NWConnectionMonitorTestResult y NWConnectionMonitorPathResult.
   * **Alertas, paneles e integraciones basados en el registro**: debe editar manualmente las consultas en función de las nuevas tablas NWConnectionMonitorTestResult y NWConnectionMonitorPathResult. Para volver a crear las alertas en las métricas, consulte [Supervisión de la conectividad de red con Connection Monitor](./connection-monitor-overview.md#metrics-in-azure-monitor).
* Para la supervisión de ExpressRoute:
    * **Pérdida y latencia de un extremo a otro**: Connection Monitor realizará esta tarea y será más fácil que con NPM, ya que no es necesario que los usuarios configuren qué circuitos y emparejamientos se deben supervisar. Los circuitos de la ruta de acceso se detectarán automáticamente, los datos estarán disponibles en métricas (más rápido que LA, que era donde NPM almacenaba los resultados). La topología funcionará tal y como está.
    * **Medidas de ancho de banda**: con el lanzamiento de las métricas relacionadas con el ancho de banda, el enfoque basado en análisis de registros de NPM no era efectivo para supervisar el ancho de banda para los clientes de ExpressRoute. Esta funcionalidad no está disponible en Connection Monitor.
    
## <a name="prerequisites"></a>Requisitos previos

* Asegúrese de que Network Watcher esté habilitado en la suscripción y la región del área de trabajo de Log Analytics. En caso de que no se realice, verá un error que indica "Antes de intentar la migración, habilite la extensión de Network Watcher en la suscripción de la selección y la ubicación del área de trabajo de LA seleccionada".
* En caso de usar una máquina virtual de Azure que pertenece a una región o suscripción distintas del área de trabajo de Log Analytics como punto de conexión, asegúrese de que Network Watcher esté habilitado para esa suscripción y esa región.   
* Las máquinas virtuales de Azure con agentes de Log Analytics instalados deberán estar habilitadas con la extensión Network Watcher.

## <a name="migrate-the-tests"></a>Migración de las pruebas

Para migrar las pruebas de Network Performance Monitor a Connection Monitor, haga lo siguiente:

1. En Network Watcher, seleccione **Connection Monitor** y, a continuación, seleccione la pestaña **Import tests from NPM** (Importar pruebas desde NPM). 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Migración de pruebas de Network Performance Monitor a Connection Monitor" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. En las listas desplegables, seleccione la suscripción y el área de trabajo y, luego, seleccione la característica de NPM que quiere migrar. 
1. Seleccione **Import** (Importar) para migrar las pruebas.
* Si NPM no está habilitado en el área de trabajo, verá un error que indica "No se encontró ninguna configuración de NPM válida". 
* Si no existen pruebas en la característica que eligió en el paso 2, verá un error que indica "El área de trabajo seleccionada no tiene la configuración \<feature\>".
* Si no hay pruebas válidas, verá un error que indica "El área de trabajo seleccionada no tiene pruebas válidas".
* Las pruebas pueden contener agentes que ya no están activos, pero que pueden haber estado activos en el pasado. Verá un error que indica que "Algunas pruebas contienen agentes que ya no están activos. Lista de agentes inactivos: {0}. Estos agentes pueden estar ejecutándose en el pasado, pero se apagan o ya no se ejecutan. Habilite los agentes y migre a Connection Monitor. Haga clic en Continuar para migrar las pruebas que no contienen agentes que no están activos".

Una vez iniciada la migración, tienen lugar los siguientes cambios: 
* Se crea un recurso de monitor de conexión.
   * Se crea un monitor de conexión por región y suscripción. En el caso de las pruebas con agentes locales, el nuevo nombre del monitor de conexión tiene el formato `<workspaceName>_"workspace_region_name"`. En el caso de las pruebas con agentes de Azure, el nuevo nombre del monitor de conexión tiene el formato `<workspaceName>_<Azure_region_name>`.
   * Los datos de supervisión ahora se almacenan en la misma área de trabajo de Log Analytics donde está habilitado NPM, en dos nuevas tablas llamadas NWConnectionMonitorTestResult y NWConnectionMonitorPathResult. 
   * El nombre de la prueba se mantiene como nombre del grupo de pruebas. No se migra la descripción de la prueba.
   * Se crean puntos de conexión de origen y destino y se usan en el grupo de pruebas creado. En el caso de los agentes locales, los puntos de conexión tienen el formato `<workspaceName>_<FQDN of on-premises machine>`. No se migra la descripción del agente.
   * El puerto de destino y el intervalo de sondeo se trasladan a las configuraciones de prueba llamadas `TC_<protocol>_<port>` y `TC_<protocol>_<port>_AppThresholds`. El protocolo se establece según los valores de puerto. En el caso de ICMP, las configuraciones de prueba se denominan `TC_<protocol>` y `TC_<protocol>_AppThresholds`. Si se establecen umbrales de éxito y otras propiedades opcionales, se migran; de lo contrario, se dejan en blanco.
   * Si las pruebas que se migran contienen agentes que no están en ejecución, debe habilitar los agentes y volver a migrarlas.
* NPM no está deshabilitado, por lo que las pruebas migradas pueden seguir enviando datos a las tablas NetworkMonitoring, NWConnectionMonitorTestResult y NWConnectionMonitorPathResult. Este enfoque garantiza que las integraciones y alertas existentes basadas en el registro no resulten afectadas.
* El monitor de conexión recién creado estará visible en Connection Monitor.

Después de la migración:
* Deshabilite manualmente las pruebas en NPM. Hasta que lo haga, se le seguirá cobrando. 
* Al deshabilitar NPM, vuelva a crear las alertas en las tablas NWConnectionMonitorTestResult y NWConnectionMonitorPathResult, o bien use las métricas. 
* Migre cualquier integración externa a las tablas NWConnectionMonitorTestResult y NWConnectionMonitorPathResult. Algunos ejemplos de integración externa son los paneles de Power BI y Grafana, y las integraciones con sistemas de Administración de eventos e información de seguridad (SIEM).

## <a name="common-errors-encountered"></a>Errores comunes detectados

A continuación, se muestran algunos errores comunes que se producen durante la migración: 

| Error  |    Motivo   |
|---|---|
| No se encontró ninguna configuración de NPM válida. Vaya a la interfaz de usuario de NPM para comprobar la configuración.     |     Este error se produce cuando el usuario selecciona Import Tests from NPM (Importar pruebas desde NPM) para migrar las pruebas, pero NPM no está habilitado en el área de trabajo.   |
|El área de trabajo seleccionada no tiene la configuración "Monitor de conectividad de servicio".    |       Este error se produce cuando el usuario migra pruebas desde el monitor de conectividad de servicio de NPM a Connection Monitor, pero no hay ninguna prueba configurada en el monitor de conectividad de servicio. |
|El área de trabajo seleccionada no tiene la configuración "Supervisión de ExpressRoute".    |     Este error se produce cuando el usuario migra pruebas del monitor de ExpressRoute de NPM a Connection Monitor pero no hay ninguna prueba configurada en el monitor de ExpressRoute.  |
|El área de trabajo seleccionada no tiene la configuración "Monitor de rendimiento".    |      Este error se produce cuando el usuario migra pruebas del monitor de rendimiento de NPM a Connection Monitor pero no hay ninguna prueba configurada en el monitor de rendimiento. |
|El área de trabajo seleccionada no tiene pruebas "{0}'' válidas.    |      Este error se produce cuando el usuario migra pruebas de NPM a Connection Monitor pero no hay pruebas válidas presentes en la característica elegida por el usuario para migrar.  |
|Antes de intentar migrar, habilite la extensión de Network Watcher en la suscripción de la selección y la ubicación del área de trabajo de LA seleccionada.      |      Este error se produce cuando el usuario migra pruebas de NPM a Connection Monitor y la extensión de Network Watcher no está habilitada en el área de trabajo de LA seleccionada. El usuario debe habilitar la extensión de NW antes de migrar las pruebas. |
|Algunas pruebas {1} contienen agentes que ya no están activos. Lista de agentes inactivos: {0}. Estos agentes pueden estar ejecutándose en el pasado, pero se apagan o ya no se ejecutan. Habilite los agentes y migre a Connection Monitor. Haga clic en Continuar para migrar las pruebas que no contienen agentes que no están activos.       |    Este error se produce cuando el usuario migra pruebas de NPM a Connection Monitor y algunas pruebas seleccionadas contienen agentes de Network Watcher inactivos o dichos agentes de NW ya no están activos, pero solían estar activos en el pasado y se han apagado. El usuario puede anular la selección de estas pruebas y continuar seleccionando y migrando las pruebas que no contienen estos agentes inactivos.  |
|Las pruebas {1} contienen agentes que ya no están activos. Lista de agentes inactivos: {0}. Estos agentes pueden estar ejecutándose en el pasado, pero se apagan o ya no se ejecutan. Habilite los agentes y migre a Connection Monitor.     | Este error se produce cuando el usuario migra pruebas de NPM a Connection Monitor y las pruebas seleccionadas contienen agentes de Network Watcher inactivos o dichos agentes de NW ya no están activos, pero solían estar activos en el pasado y se han apagado. El usuario debe habilitar los agentes y, a continuación, seguir migrando estas pruebas a Connection Monitor.    |
|Se produjo un error al importar las pruebas en Connection Monitor.     |    Este error se produce cuando el usuario intenta migrar pruebas de NPM a CM, pero debido a errores, la migración no se realiza correctamente. |



## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Connection Monitor, consulte:
* [Migración de Connection Monitor (clásico) a Connection Monitor](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Creación de Connection Monitor mediante Azure Portal](./connection-monitor-create-using-portal.md)
