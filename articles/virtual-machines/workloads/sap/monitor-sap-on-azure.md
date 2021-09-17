---
title: Supervisión de SAP en Azure (versión preliminar) | Microsoft Docs
description: Comience aquí para obtener información sobre cómo supervisar SAP en Azure.
author: Ajayan1008
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.custom: subject-monitoring
ms.date: 08/24/2021
ms.author: v-hborys
ms.openlocfilehash: 779a70c4a0386420389937864be01295f985f5b8
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123104463"
---
# <a name="monitor-sap-on-azure-preview"></a>Supervisión de SAP en Azure (versión preliminar)

Si tiene aplicaciones y procesos empresariales críticos que dependen de recursos de Azure, querrá supervisar esos recursos para su disponibilidad, rendimiento y funcionamiento. 

En este artículo se describe cómo supervisar SAP en ejecución en Azure mediante Azure Monitor para soluciones de SAP. Azure Monitor para soluciones de SAP utiliza partes específicas de la infraestructura de [Azure Monitor](../../../azure-monitor/overview.md).

## <a name="overview"></a>Información general

Azure Monitor para soluciones de SAP es un producto de supervisión nativo de Azure para cualquier persona que ejecuta entornos de SAP en la plataforma. El producto funciona con [SAP en Azure Virtual Machines](./hana-get-started.md) y con [SAP en Azure (instancias grandes)](./hana-overview-architecture.md).

Con Azure Monitor para soluciones de SAP, puede recopilar datos de telemetría de la infraestructura y las bases de datos de Azure en una ubicación central y correlacionar visualmente esos datos para una solución de problemas más rápida.

Puede supervisar distintos componentes de un entorno de SAP, como máquinas virtuales de Azure, un clúster de alta disponibilidad, una base de datos de SAP HANA, SAP NetWeaver, etc., con solo agregar el **proveedor** correspondiente al componente. Para obtener más información, consulte [Implementación de Azure Monitor para soluciones de SAP con Azure Portal](azure-monitor-sap-quickstart.md).

Infraestructura admitida:

- Máquina virtual de Azure
- Azure (instancias grandes)

Bases de datos admitidas:
- Base de datos de SAP HANA
- Microsoft SQL Server

Azure Monitor para soluciones de SAP usa las funcionalidades de [Azure Monitor](../../../azure-monitor/overview.md) de [Log Analytics](../../../azure-monitor/logs/log-analytics-overview.md) y [libros](../../../azure-monitor/visualize/workbooks-overview.md).

Por ejemplo, puede:

- Crear [visualizaciones personalizadas](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) editando los libros predeterminados que proporciona Azure Monitor para soluciones de SAP. 
- Escribir [consultas personalizadas](../../../azure-monitor/logs/log-analytics-tutorial.md).
- Crear [alertas personalizadas](../../../azure-monitor/alerts/alerts-log.md) mediante un área de trabajo de Azure Log Analytics. 
- Aprovechar el [período de retención flexible](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) en registros de Azure Monitor o Log Analytics. 
- Conectar los datos de supervisión con su sistema de control de vales.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>¿Qué datos recopila Azure Monitor para soluciones de SAP?

A diferencia de otros muchos recursos de Azure, Azure Monitor para soluciones de SAP no recopila métricas de Azure Monitor ni datos de registro de recursos. En su lugar, envía registros personalizados directamente al sistema de registros de Azure Monitor, donde puede usar las características integradas de Log Analytics.

La recopilación de datos en Azure Monitor para soluciones de SAP depende de los proveedores que configure. Durante la versión preliminar pública, se recopilan los siguientes datos:

Telemetría del clúster de Pacemaker de alta disponibilidad:
- Estado del nodo, recurso y dispositivo de bloques STONITH (SBD)
- Restricciones de ubicación de Pacemaker
- Votos de cuórum y estado del anillo
- [Otros](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

Telemetría de SAP HANA:
- Uso de CPU, memoria, disco y red
- Replicación del sistema HANA (HSR)
- Copia de seguridad de HANA
- Estado del host de HANA
- Roles de los servidores de indexación y de nombres
- Crecimiento de las bases de datos
- Tablas principales
- Uso de sistemas de archivos

Telemetría de Microsoft SQL Server:
- Uso de CPU, memoria y disco
- Nombre del host, nombre de la instancia de SQL e identificador del sistema SAP
- Solicitudes por lotes, compilaciones y duración prevista de la página a lo largo del tiempo
- Las 10 instrucciones SQL más caras a lo largo del tiempo
- Las 12 tablas más grandes del sistema SAP
- Problemas registrados en el registro de errores de SQL Server
- Procesos de bloqueo y estadísticas de espera de SQL a lo largo del tiempo

Datos de telemetría del sistema operativo (Linux): 
- Uso de CPU, número de bifurcaciones y procesos en ejecución y bloqueados 
- Uso de memoria y distribución entre en uso, en caché y en búfer 
- Uso de intercambio, paginación y tasa de intercambio 
- Uso de sistemas de archivos y número de bytes leídos y escritos por dispositivo de bloques 
- Latencia de lectura y escritura por dispositivo de bloques 
- Recuento de E/S en curso y bytes de lectura y escritura de memoria persistente 
- Paquetes de red entrantes y salientes, bytes de red entrantes y salientes 

Telemetría de SAP NetWeaver:

- Disponibilidad del sistema SAP y del servidor de aplicaciones, incluida la disponibilidad del proceso de la instancia de Dispatcher, ICM, Gateway, Message Server, Enqueue Server e IGS Watchdog
- Estadísticas y tendencias de uso de procesos de trabajo
- Poner en cola las estadísticas y las tendencias de bloqueo
- Estadísticas y tendencias de uso de colas

## <a name="data-sharing-with-microsoft"></a>Uso compartido de datos con Microsoft

Azure Monitor para soluciones de SAP recopila metadatos del sistema para proporcionar una compatibilidad mejorada a SAP en Azure. No se recopila información de identificación personal ni información de identificación del usuario final.

Para habilitar el uso compartido de datos con Microsoft cuando cree el recurso de Azure Monitor para soluciones de SAP, elija *Compartir* en el menú desplegable. Se recomienda habilitar el uso compartido de datos. El uso compartido de datos proporciona a los equipos de soporte técnico e ingeniería de Microsoft información sobre su entorno, por lo que nos ayuda a proporcionar un mejor soporte técnico para su solución de SAP en Azure crítica.

## <a name="architecture-overview"></a>Información general sobre la arquitectura

En el siguiente diagrama se muestra, a grandes rasgos, la manera en que Azure Monitor para soluciones de SAP recopila datos de telemetría a partir de una base de datos de SAP HANA. La arquitectura es la misma si SAP HANA se implementa en máquinas virtuales o en instancias grandes de Azure.

![Arquitectura de Azure Monitor para soluciones de SAP](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

Los componentes clave de la arquitectura son:
- **Azure Portal:** su punto de partida. Puede ir a Marketplace dentro de Azure Portal y descubrir Azure Monitor para soluciones de SAP.
- **Recurso de Azure Monitor para soluciones de SAP:** espacio de aterrizaje para consultar datos de telemetría de supervisión.
- **Grupo de recursos administrados:** se implementa automáticamente como parte de la implementación del recurso de Azure Monitor para soluciones de SAP. Los recursos implementados en el grupo ayudan a recopilar datos de telemetría. Estos son los recursos clave implementados y su finalidad:
   - *Máquina virtual de Azure:* también conocida como **VM del recopilador**, es una VM Standard_B2ms. La finalidad principal de esta VM es hospedar la *carga de supervisión*. La carga de supervisión hace referencia a la lógica de recopilación de datos de telemetría de los sistemas de origen y a la transferencia de los datos al marco de supervisión. En el diagrama anterior, la carga de supervisión contiene la lógica para conectarse a la base de datos de SAP HANA a través del puerto de SQL.
   - **[Azure Key Vault:](../../../key-vault/general/basic-concepts.md)** este recurso se implementa para conservar de forma segura las credenciales de la base de datos de SAP HANA y para almacenar información sobre los [proveedores](./azure-monitor-providers.md).
   - **Área de trabajo de Log Analytics**: destino en el que se almacenan los datos de telemetría.
      - La visualización se basa en la telemetría de Log Analytics mediante [Azure Workbooks](../../../azure-monitor/visualize/workbooks-overview.md). Puede personalizar la visualización. También puede anclar sus libros o una visualización específica dentro de estos al panel de Azure para que se actualicen automáticamente. La frecuencia máxima de actualización es de 30 minutos.
      - Para usar su área de trabajo existente dentro de la misma suscripción como recurso de supervisión de SAP, elija esta opción en el momento de la implementación.
      - Puede usar el lenguaje de consulta Kusto (KQL) para ejecutar [consultas](../../../azure-monitor/logs/log-query-overview.md) en las tablas sin procesar dentro del área de trabajo de Log Analytics. Consulte los *registros personalizados*.

> [!Note]
> Usted es responsable de las revisiones y el mantenimiento de la VM implementada en el grupo de recursos administrados.

> [!Tip]
> Puede optar por usar un área de trabajo de Log Analytics existente para la recopilación de datos de telemetría si está implementada en la misma suscripción de Azure que el recurso de Azure Monitor para soluciones de SAP.

### <a name="architecture-highlights"></a>Aspectos destacados de la arquitectura

A continuación se indican los aspectos más destacados de la arquitectura:
 - **Instancias múltiples:** puede supervisar varias instancias de un tipo de componente determinado (por ejemplo, la base de datos de HANA, el clúster de alta disponibilidad, Microsoft SQL Server o SAP NetWeaver) en varios SID de SAP dentro de una red virtual, con un único recurso de Azure Monitor para soluciones de SAP.
 - **Proveedores múltiples:** en el diagrama de arquitectura anterior se muestra el proveedor de SAP HANA a modo de ejemplo. Del mismo modo, puede configurar más proveedores de los componentes correspondientes, con el fin de recopilar datos de estos (por ejemplo, la base de datos de HANA, el clúster de alta disponibilidad, Microsoft SQL Server y SAP NetWeaver). 
 - **Código abierto**: el código fuente de Azure Monitor para soluciones de SAP está disponible en [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions). Puede consultar el código del proveedor y obtener más información sobre el producto, realizar contribuciones o compartir sus comentarios.
 - **Marco de consulta extensible**: las consultas SQL para recopilar datos de telemetría se escriben en [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json). Se pueden agregar fácilmente más consultas SQL para recopilar más datos de telemetría. Puede solicitar que se agreguen a Azure Monitor para soluciones de SAP datos de telemetría específicos. Para ello, deje sus comentarios usando el vínculo que encontrará al final de este artículo. También puede dejar comentarios poniéndose en contacto con el equipo de su cuenta.

## <a name="analyzing-metrics"></a>Análisis de métricas

Azure Monitor para soluciones de SAP no admite métricas. 

## <a name="analyzing-logs"></a>Análisis de datos

Azure Monitor para soluciones de SAP no admite registros de recursos ni de actividad. Para obtener una lista de las tablas utilizadas por los registros de Azure Monitor a las que Log Analytics puede realizar consultas, vea [Referencia de datos de supervisión de SAP en Azure](monitor-sap-on-azure-reference.md#azure-monitor-logs-tables). 

### <a name="sample-kusto-queries"></a>Ejemplos de consultas de Kusto

> [!IMPORTANT]
> Al seleccionar **Registros** en el menú de Azure Monitor para soluciones de SAP, se abre Log Analytics con el ámbito de la consulta establecido en el recurso actual de Azure Monitor para soluciones de SAP. Esto significa que las consultas de registro solo incluirán datos de ese recurso. Si quiere ejecutar una consulta que incluya datos de otras cuentas o de otros servicios de Azure, seleccione **Registros** en el menú **Azure Monitor**. Consulte [Ámbito e intervalo de tiempo de una consulta de registro en Log Analytics de Azure Monitor](../../../azure-monitor/logs/scope.md) para obtener más información.

Puede usar consultas de Kusto para supervisar los recursos de supervisión de SAP en Azure. Esta es una consulta de ejemplo que proporciona datos de un registro personalizado con relación a un intervalo de tiempo especificado. Especifique el intervalo de tiempo y el número de filas. En este ejemplo, se obtienen cinco filas de datos con relación al intervalo de tiempo seleccionado. 

```Kusto
custom log name 
| take 5

```

## <a name="alerts"></a>Alertas

Las alertas de Azure Monitor le informan de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Permiten identificar y solucionar las incidencias en el sistema antes de que los clientes puedan verlos.

Puede configurar alertas en Azure Monitor para soluciones de SAP desde Azure Portal. Para obtener más información, consulte [Configuración de alertas en Azure Monitor para soluciones de SAP con Azure Portal](azure-monitor-alerts-portal.md).

## <a name="create-azure-monitor-for-sap-solutions-resources"></a>Creación de recursos de Azure Monitor para soluciones de SAP

Tiene varias opciones para implementar Azure Monitor para soluciones de SAP y configurar proveedores: 
- Puede implementar Azure Monitor para soluciones de SAP desde Azure Portal. Para obtener más información, consulte [Implementación de Azure Monitor para soluciones de SAP con Azure Portal](azure-monitor-sap-quickstart.md).
- Usar Azure PowerShell. Para obtener más información, consulte [Implementación de Azure Monitor para soluciones de SAP con Azure PowerShell](azure-monitor-sap-quickstart-powershell.md).
- Use la extensión de la CLI. Para obtener más información, consulte [Módulo de comandos de SAP HANA](https://github.com/Azure/azure-hanaonazure-cli-extension#sapmonitor).

## <a name="pricing"></a>Precios
Azure Monitor para soluciones de SAP es un producto gratuito (sin cuota de licencias). Usted debe correr con el coste de los componentes subyacentes del grupo de recursos administrados. También debe correr con los costes de consumo asociados con el uso y la retención de datos. Para obtener más información, consulte la documentación estándar sobre precios de Azure:
- [Precios de máquinas virtuales de Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Precios de Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)
- [Precios de las cuentas de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/queues/)
- [Precios de Azure Log Analytics y alertas](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener una lista de los registros personalizados pertinentes para Azure Monitor para soluciones de SAP e información sobre los tipos de datos relacionados, consulte [Referencia de datos de supervisión de SAP en Azure](monitor-sap-on-azure-reference.md).
- Para obtener información sobre los proveedores disponibles, consulte [Proveedores de Azure Monitor para soluciones de SAP](azure-monitor-providers.md).
