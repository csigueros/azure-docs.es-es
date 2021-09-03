---
title: Información general y arquitectura de Azure Monitor para soluciones de SAP | Microsoft Docs
description: En este artículo se ofrecen respuestas a las preguntas más frecuentes sobre Azure Monitor para soluciones de SAP.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.date: 07/06/2021
ms.author: radeltch
ms.openlocfilehash: 4b5bcb4aecab0247f47aede883e1bfe771262026
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746204"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure Monitor para soluciones de SAP (versión preliminar)

## <a name="overview"></a>Información general

En este artículo se proporciona información general sobre cómo funciona Azure Monitor para soluciones de SAP y sus funcionalidades.

Azure Monitor para soluciones de SAP es un producto de supervisión nativo de Azure para cualquier persona que ejecuta entornos de SAP en Azure. El producto funciona con [SAP en Azure Virtual Machines](./hana-get-started.md) y con [SAP en Azure (instancias grandes)](./hana-overview-architecture.md).

Con Azure Monitor para soluciones de SAP, puede recopilar datos de telemetría de la infraestructura y las bases de datos de Azure en una ubicación central y correlacionar visualmente esos datos para una solución de problemas más rápida.

Azure Monitor para soluciones de SAP se ofrece a través de Azure Marketplace. Proporciona una experiencia de instalación sencilla e intuitiva y solo se requieren unos clics para implementar el recurso de Azure Monitor para soluciones de SAP (conocido como **recurso de supervisión de SAP**).

Puede supervisar distintos componentes de un entorno de SAP, como Azure Virtual Machines (VM), un clúster de alta disponibilidad, una base de datos de SAP HANA, SAP NetWeaver, con solo agregar el **proveedor** correspondiente al componente.

Infraestructura admitida:

- Máquina virtual de Azure
- Azure (instancias grandes)

Bases de datos admitidas:
- Base de datos de SAP HANA
- Microsoft SQL Server

Azure Monitor para soluciones de SAP usa la eficacia de las capacidades existentes de [Azure Monitor](../../../azure-monitor/overview.md), como Log Analytics y [Workbooks](../../../azure-monitor/visualize/workbooks-overview.md), para proporcionar más capacidades de supervisión. Puede crear [visualizaciones personalizadas](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) editando los libros de trabajo predeterminados que proporciona Azure Monitor para soluciones de SAP. Escriba [consultas personalizadas](../../../azure-monitor/logs/log-analytics-tutorial.md) y cree [alertas personalizadas](../../../azure-monitor/alerts/alerts-log.md) mediante el área de trabajo de Azure Log Analytics. Aproveche el [período de retención flexible](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) y conecte los datos de supervisión con el sistema de incidencias.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>¿Qué datos recopila Azure Monitor para soluciones de SAP?

La recopilación de datos en Azure Monitor para soluciones de SAP depende de los proveedores que configure. Durante la versión preliminar pública, se recopilan los datos siguientes.

Telemetría del clúster de Pacemaker de alta disponibilidad:
- Estado del nodo, recurso y dispositivo de bloqueo STONITH (SBD)
- Restricciones de ubicación de Pacemaker
- Votos de cuórum y estado del anillo
- [Otros](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

Telemetría de SAP HANA:
- Uso de CPU, memoria, disco y red
- Replicación del sistema HANA (HSR)
- Copia de seguridad de HANA
- Estado del host de HANA
- Roles del servidor de nombres e indexación

Telemetría de Microsoft SQL Server:
- Uso de CPU, memoria y disco
- Nombre del host, nombre de la instancia de SQL e identificador del sistema SAP
- Solicitudes por lotes, compilaciones y duración prevista de la página a lo largo del tiempo
- Las 10 instrucciones SQL más caras a lo largo del tiempo
- Las 12 tablas más grandes del sistema SAP
- Problemas registrados en el registro de errores de SQL Server
- Procesos de bloqueo y estadísticas de espera de SQL a lo largo del tiempo

Telemetría del sistema operativo (Linux) 
- Uso de CPU, número de bifurcaciones, procesos en ejecución y bloqueados 
- Uso de memoria y distribución entre en uso, en caché y en búfer 
- Uso de intercambio, paginación y tasa de intercambio 
- Uso de sistemas de archivos, número de bytes leídos y escritos por dispositivo de bloqueo 
- Latencia de lectura y escritura por dispositivo de bloqueo 
- Recuento de E/S en curso, bytes de lectura y escritura de memoria persistente 
- Paquetes de red entrantes y salientes, bytes de red entrantes y salientes 

Telemetría de SAP NetWeaver:

- Disponibilidad del sistema SAP y del servidor de aplicaciones, incluida la disponibilidad del proceso de la instancia de Dispatcher, ICM, Gateway, Message Server, Enqueue Server e IGS Watchdog
- Estadísticas y tendencias de uso del proceso de trabajo
- Poner en cola las estadísticas y las tendencias de bloqueo
- Estadísticas y tendencias de uso de la cola

## <a name="data-sharing-with-microsoft"></a>Uso compartido de datos con Microsoft

Azure Monitor para soluciones de SAP recopila metadatos del sistema para proporcionar una compatibilidad mejorada a SAP en Azure. No se recopila información de identificación personal ni información de identificación del usuario final.

Puede habilitar el uso compartido de datos con Microsoft cuando cree el recurso de Azure Monitor para soluciones de SAP si seleccionan *Compartir* en el menú desplegable. Se recomienda que habilite el uso compartido de los datos. El uso compartido de los datos proporciona a los equipos de soporte técnico e ingeniería de Microsoft información sobre el entorno, lo que nos ayuda a proporcionar un mejor soporte técnico para su solución de SAP en Azure crítica.

## <a name="architecture-overview"></a>Información general sobre la arquitectura

En el diagrama siguiente se muestra, a grandes rasgos, la manera en que Azure Monitor para soluciones de SAP recopila telemetría a partir de una base de datos de SAP HANA. La arquitectura es la misma si SAP HANA está implementado en Azure Virtual Machines o en Azure (instancias grandes).

![Arquitectura de Azure Monitor para soluciones de SAP](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

Los componentes clave de la arquitectura son:
- Azure Portal: su punto de partida. Puede ir a Marketplace desde Azure Portal y descubrir Azure Monitor para soluciones de SAP.
- Recurso de Azure Monitor para soluciones de SAP: un sitio de aterrizaje para ver la telemetría de supervisión.
- Grupo de recursos administrado: se implementa automáticamente como parte de la implementación del recurso de Azure Monitor para soluciones de SAP. Los recursos implementados en el grupo de recursos administrado ayudan a recopilar telemetría. Los recursos clave implementados y su finalidad son:
   - Máquina virtual de Azure: también conocida como *VM del recopilador*, es una VM Standard_B2ms. La finalidad principal de esta VM consiste en hospedar la *carga de supervisión*. La carga de supervisión hace referencia a la lógica de recopilación de telemetría de los sistemas de origen y a la transferencia de los datos al marco de supervisión. En el diagrama anterior, la carga de supervisión contiene la lógica para conectarse a la base de datos de SAP HANA a través del puerto de SQL.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md): este recurso se implementa para conservar de forma segura las credenciales de la base de datos de SAP HANA y para almacenar información sobre los [proveedores](./azure-monitor-providers.md).
   - Área de trabajo de Log Analytics: destino en el que se almacenan los datos de telemetría.
      - La visualización se basa en la telemetría de Log Analytics mediante [Azure Workbooks](../../../azure-monitor/visualize/workbooks-overview.md). Puede personalizar la visualización. También puede anclar los libros de trabajo o la visualización específica dentro de ellos al panel de Azure para que se actualicen automáticamente. La frecuencia máxima de actualización es cada 30 minutos.
      - Puede usar el área de trabajo existente dentro de la misma suscripción como recurso de supervisión de SAP si elige esta opción en el momento de la implementación.
      - Puede usar el lenguaje de consulta Kusto (KQL) para ejecutar [consultas](../../../azure-monitor/logs/log-query-overview.md) en las tablas sin procesar dentro del área de trabajo de Log Analytics. Consulte los *registros personalizados*.

> [!Note]
> Usted es responsable de las revisiones y el mantenimiento de la VM implementada en el grupo de recursos administrado.

> [!Tip]
> Puede optar por usar un área de trabajo de Log Analytics existente para la recopilación de telemetría, si está implementada en la misma suscripción de Azure que el recurso de Azure Monitor para soluciones de SAP.

### <a name="architecture-highlights"></a>Aspectos destacados de la arquitectura

Aquí se indican los principales aspectos de la arquitectura:
 - **Instancias múltiples**: puede crear la supervisión para varias instancias de un tipo de componente determinado (por ejemplo, la base de datos de HANA, el clúster de alta disponibilidad, Microsoft SQL Server, SAP NetWeaver) en varios SID de SAP dentro de una red virtual con un único recurso de Azure Monitor para soluciones de SAP.
 - **Proveedores múltiples**: en el diagrama de arquitectura anterior se muestra el proveedor de SAP HANA a modo de ejemplo. Asimismo, puede configurar más proveedores para los componentes correspondientes (por ejemplo, una base de datos de HANA, un clúster de alta disponibilidad, Microsoft SQL Server, SAP NetWeaver) a fin de recopilar datos de esos componentes.
 - **Código abierto**: el código fuente de Azure Monitor para soluciones de SAP está disponible en [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions). Puede consultar el código del proveedor y obtener más información sobre el producto, realizar contribuciones o compartir sus comentarios.
 - **Marco de consulta extensible**: las consultas SQL para recopilar datos de telemetría se escriben en [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json). Se pueden agregar fácilmente más consultas SQL para recopilar más datos de telemetría. Puede solicitar la incorporación de datos de telemetría específicos a Azure Monitor para soluciones de SAP. Para ello, debe dejar sus comentarios a través del vínculo que aparece al final de este artículo. También puede dejar comentarios comunicándose con el equipo de cuentas.

## <a name="pricing"></a>Precios
Azure Monitor para soluciones de SAP es un producto gratuito (sin cuota de licencias). Usted es responsable de pagar el costo de los componentes subyacentes en el grupo de recursos administrado y los costos de consumo asociados con la ingesta y retención de datos. Consulte los documentos de precios estándar de Azure para obtener más información sobre los [precios de VM de Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/), los [precios de Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/), los [precios de las cuentas de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/queues/) y los [precios de Azure Log Analytics y las alertas](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información acerca de los proveedores de Azure Monitor para soluciones de SAP.

> [!div class="nextstepaction"]
> [Proveedores de Azure Monitor para soluciones de SAP](azure-monitor-providers.md)
