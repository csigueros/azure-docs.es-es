---
title: Proveedores de Azure Monitor para soluciones de SAP | Microsoft Docs
description: En este artículo se ofrecen respuestas a las preguntas más frecuentes sobre Azure Monitor para soluciones de SAP.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.date: 07/06/2021
ms.author: radeltch
ms.openlocfilehash: 5061ee2131ee708c38a68dca02c18a21085b5430
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616866"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Proveedores de Azure Monitor para soluciones de SAP (versión preliminar)

## <a name="overview"></a>Información general

En este artículo se describen los distintos proveedores que están disponibles actualmente para Azure Monitor para soluciones de SAP.

En el contexto de Azure Monitor para soluciones de SAP, un *tipo de proveedor* hace referencia a un *proveedor* específico. Por ejemplo, *SAP HANA*, que se configura para un componente específico del entorno de SAP, como la base de datos de SAP HANA. Un proveedor contiene la información de conexión del componente correspondiente y ayuda a recopilar datos de telemetría de dicho componente. Un recurso de Azure Monitor para soluciones de SAP (conocido también como recurso de supervisión de SAP) se puede configurar con varios proveedores del mismo tipo de proveedor o con varios proveedores de varios tipos de proveedor.
   
Puede optar por configurar distintos tipos de proveedor para habilitar la recopilación de datos del componente correspondiente en su entorno de SAP. Por ejemplo, puede configurar un proveedor para el tipo de proveedor de SAP HANA, otro proveedor para el tipo de proveedor de clúster de alta disponibilidad, etc.  

También puede optar por configurar varios proveedores de un tipo de proveedor específico para reutilizar el mismo recurso de monitor de SAP y el grupo administrado asociado. Para más información sobre los grupos de recursos administrados, consulte [Administración de grupos de recursos de Azure Resource Manager mediante Azure Portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md).

En la versión preliminar pública, se admiten los siguientes tipos de proveedor:   
- SAP NetWeaver
- SAP HANA
- Microsoft SQL Server
- Clúster de alta disponibilidad
- Sistema operativo (SO)

![Proveedores de Azure Monitor para soluciones de SAP](https://user-images.githubusercontent.com/75772258/115047655-5a5b2c00-9ef6-11eb-9e0c-073e5e1fcd0e.png)

Le recomendamos configurar al menos un proveedor de los tipos de proveedor disponibles en el momento de implementar el recurso de supervisión de SAP. Mediante la configuración de un proveedor, inicia la recopilación de datos del componente correspondiente para el que está configurado el proveedor.   

Si no configura ningún proveedor en el momento de implementar el recurso de supervisión de SAP, aunque este se implemente correctamente, no se recopilarán datos de telemetría. Después de la implementación, puede agregar proveedores a través del recurso de supervisión de SAP dentro de Azure Portal. Puede agregar o eliminar proveedores del recurso de supervisión de SAP en cualquier momento.

## <a name="provider-type-sap-netweaver"></a>Tipo de proveedor: SAP NetWeaver

Puede configurar uno o varios proveedores del tipo SAP NetWeaver para habilitar la recopilación de datos de la capa SAP NetWeaver. El proveedor de AMS NetWeaver utiliza la interfaz del [servicio web SAPControl](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) existente para recuperar la información de telemetría adecuada.

En el caso de la versión actual, los métodos web SOAP siguientes son los métodos estándar de fábrica que AMS invoca.

![image1](https://user-images.githubusercontent.com/75772258/114600036-820d8280-9cb1-11eb-9f25-d886ab1d5414.png)

En la versión preliminar pública, debería ver los datos siguientes con el proveedor de SAP NetWeaver: 
- Disponibilidad del sistema y de la instancia
- Uso de proceso de trabajo
- Uso de cola
- Estadísticas de bloqueos puestos en cola

![imagen](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="provider-type-sap-hana"></a>Tipo de proveedor: SAP HANA

Puede configurar uno o varios proveedores del tipo *SAP HANA* para habilitar la recopilación de datos de la base de datos de SAP HANA. El proveedor de SAP HANA se conecta a la base de datos de SAP HANA a través del puerto de SQL, extrae datos de telemetría de la base de datos y los envía al área de trabajo de Log Analytics en su suscripción. El proveedor de SAP HANA recopila datos cada minuto de la base de datos de SAP HANA.  

En la versión preliminar pública, debería ver los datos siguientes con el proveedor de SAP HANA:
- Uso de infraestructura subyacente
- Estado del host de SAP HANA
- SAP HANA System Replication (Replicación del sistema de SAP HANA)
- Datos de telemetría de la copia de seguridad de SAP HANA 

La configuración del proveedor de SAP HANA requiere:
- Dirección IP del host 
- Número de puerto de SQL de HANA
- Nombre de usuario y contraseña de SYSTEMDB

Le recomendamos configurar el proveedor de SAP HANA con SYSTEMDB, pero se pueden configurar más proveedores con otros inquilinos de base de datos.

![Proveedores de Azure Monitor para soluciones de SAP: SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-microsoft-sql-server"></a>Tipo de proveedor: Microsoft SQL Server

Puede configurar uno o varios proveedores del tipo *Microsoft SQL Server* para habilitar la recopilación de datos de [SQL Server en Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). El proveedor de SQL Server se conecta a Microsoft SQL Server a través del puerto de SQL. A continuación, extrae los datos de telemetría de la base de datos y los inserta en el área de trabajo de Log Analytics de la suscripción. Configure SQL Server para la autenticación de SQL y para iniciar sesión con el nombre de usuario y la contraseña de SQL Server. Establezca la base de datos de SAP como la base de datos predeterminada para el proveedor. El proveedor de SQL Server recopila datos de SQL Server a intervalos que van de cada 60 segundos a cada hora.  

En la versión preliminar pública, debería ver los datos siguientes con el proveedor de SQL Server:
- Uso de la infraestructura subyacente
- Instrucciones T-SQL principales
- Tabla de mayor tamaño
- Problemas registrados en el registro de errores de SQL Server
- Procesos de bloqueo y otros  

La configuración del proveedor de Microsoft SQL Server requiere:
- Identificador del sistema SAP
- Dirección IP del host
- El número de puerto de SQL Server
- Nombre de usuario y contraseña de SQL Server

![Proveedores de Azure Monitor para soluciones de SAP: SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-high-availability-cluster"></a>Tipo de proveedor: clúster de alta disponibilidad
Puede configurar uno o varios proveedores del tipo *clúster de alta disponibilidad* para habilitar la recopilación de datos de un clúster de Pacemaker en el entorno de SAP. El proveedor de clúster de alta disponibilidad se conecta a Pacemaker mediante [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) para clústeres basados en **SUSE** y mediante [Performance co-pilot](https://access.redhat.com/articles/6139852) para clústeres basados en **RHEL**. A continuación, AMS extrae los datos de telemetría de la base de datos y los inserta en el área de trabajo de Log Analytics de la suscripción. El proveedor de clúster de alta disponibilidad recopila datos cada 60 segundos desde Pacemaker.  

En la versión preliminar pública, debería ver los datos siguientes con el proveedor de clúster de alta disponibilidad:   
 - Estado del clúster representado como la acumulación del estado de los nodos y de los recursos 
 - Restricciones de ubicación
 - Tendencias
 - [Otros](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Proveedores de Azure Monitor para soluciones de SAP: clúster de alta disponibilidad](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Para configurar al proveedor de clúster de alta disponibilidad, se deben llevar a cabo dos pasos principales:

1. Instale [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) en *cada nodo* del clúster de Pacemaker.

   Tiene dos opciones para instalar ha_cluster_exporter:
   
   - Use scripts de Azure Automation para implementar un clúster de alta disponibilidad. Los scripts instalan [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) en cada nodo del clúster.  
   - Realice una [instalación manual](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build). 

2. Configure un proveedor de clúster de alta disponibilidad para *cada* nodo del clúster de Pacemaker.

   Para configurar al proveedor de clústeres de alta disponibilidad, se requiere la siguiente información:
   
   - **Nombre**. Nombre del proveedor. Debe ser único para esta instancia Azure Monitor para soluciones de SAP.
   - **Punto de conexión de Prometheus**. `http://<servername or ip address>:9664/metrics`.
   - **SID**. En el caso de los sistemas SAP, use el SID de SAP. En el caso de otros sistemas (por ejemplo, clústeres NFS), use un nombre de tres caracteres para el clúster. El SID debe ser distinto de otros clústeres que se vayan a supervisar.   
   - **Nombre del clúster**. Nombre de clúster que se usó al crear el clúster. El nombre del clúster se puede encontrar en la propiedad `cluster-name` del clúster.
   - **Hostname**. Nombre de host de Linux de la máquina virtual (VM).  

## <a name="provider-type-os-linux"></a>Tipo de proveedor: sistema operativo (Linux)
Puede configurar uno o varios proveedores del tipo SO (Linux) para habilitar la recopilación de datos de un nodo de VM o BareMetal. El proveedor de SO (Linux) se conecta a nodos de VM o BareMetal mediante el punto de conexión [Node_Exporter](https://github.com/prometheus/node_exporter) . A continuación, extrae los datos de telemetría de los nodos y los inserta en el área de trabajo de Log Analytics de la suscripción. El proveedor del sistema operativo (Linux) recopila datos cada 60 segundos para la mayoría de las métricas de los nodos. 

En la versión preliminar pública, debería ver los datos siguientes con el proveedor de SO (Linux): 
   - Uso de CPU, uso de CPU por proceso 
   - Uso de disco, lectura y escritura de E/S 
   - Distribución de la memoria, uso de la memoria, uso de la memoria de intercambio 
   - Uso de red, detalles del tráfico de entrada y salida de la red 

Para configurar un proveedor de sistema operativo (Linux), se deben llevar a cabo dos pasos principales:
1. Instale  [Node_Exporter](https://github.com/prometheus/node_exporter) en cada nodo de VM o BareMetal.
   Tiene dos opciones para instalar [Node_exporter](https://github.com/prometheus/node_exporter): 
      - Para la instalación automatizada con Ansible, use [Node_Exporter](https://github.com/prometheus/node_exporter) en cada nodo de VM o BareMetal para instalar el proveedor del sistema operativo (Linux).  
      - Realice una  [instalación manual](https://prometheus.io/docs/guides/node-exporter/).

2. Configure un proveedor de sistema operativo (Linux) para cada nodo de VM o BareMetal en su entorno. 
   Para configurar el proveedor de sistema operativo (Linux), se requiere la información siguiente: 
      - Name (Nombre). Nombre del proveedor. Debe ser único para esta instancia de Azure Monitor para soluciones de SAP. 
      - Punto de conexión de Node Exporter. Normalmente, `http://<servername or ip address>:9100/metrics`. 

> [!NOTE]
> 9100 es un puerto expuesto para el punto de conexión de Node_Exporter.

> [!Warning]
> Asegúrese de que Node Exporter sigue ejecutándose después del reinicio del nodo. 

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo implementar Azure Monitor para soluciones de SAP desde Azure Portal.

> [!div class="nextstepaction"]
> [Implementación de Azure Monitor para soluciones de SAP con Azure Portal](./azure-monitor-sap-quickstart.md)
