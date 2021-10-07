---
title: Planeamiento de la implementación de servicios de datos habilitados para Azure Arc
description: Explica las consideraciones para planear la implementación de servicios de datos habilitados para Azure Arc.
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 0d9ae624ddc0a4e5a2f5d9ac38428f4f3c10a01e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832410"
---
# <a name="plan-to-deploy-azure-arc-enabled-data-services"></a>Planeamiento de la implementación de servicios de datos habilitados para Azure Arc

En este artículo se describe cómo planear la implementación de servicios de datos habilitados para Azure Arc.


En primer lugar, la implementación de servicios de datos de Azure Arc implica un conocimiento adecuado de las cargas de trabajo de base de datos y los requisitos empresariales para esas cargas de trabajo. Por ejemplo, considere aspectos como la disponibilidad, la continuidad empresarial y los requisitos de capacidad de memoria, CPU y almacenamiento para esas cargas de trabajo. En segundo lugar, la infraestructura para admitir esas cargas de trabajo de base de datos debe prepararse en función de los requisitos empresariales.

## <a name="prerequisites"></a>Requisitos previos

Antes de implementar los servicios de datos habilitados para Azure Arc, es importante comprender los requisitos previos y tener toda la información necesaria lista, el entorno de infraestructura configurado correctamente con el nivel de acceso apropiado y la capacidad adecuada de almacenamiento, CPU y memoria, para que pueda tener una implementación correcta al final.

Revise las secciones siguientes:
- [Guía para el ajuste de tamaño](sizing-guidance.md)
- [Configuración de almacenamiento](storage-configuration.md)
- [Modos de conectividad y requisitos](connectivity.md)

Compruebe que tiene lo siguiente:
- La [extensión de la CLI `arcdata`](install-arcdata-extension.md) instalada.
- Las demás [herramientas de cliente](install-client-tools.md) instaladas.
- Acceso al clúster de Kubernetes.
- El archivo `kubeconfig` configurado. Debe apuntar al clúster de Kubernetes en el que desea realizar la implementación. Ejecute el siguiente comando para comprobar el contexto actual del clúster en el que va a realizar la implementación:
   ```console
   kubectl cluster-info
   ```
- Una suscripción de Azure en la que se proyectarán y facturarán recursos como un controlador de datos de Azure Arc, SQL Managed Instance habilitado para Azure Arc o un servidor de Hiperescala de PostgreSQL habilitada para Azure Arc.

Una vez preparada la infraestructura, implemente los servicios de datos habilitados para Azure Arc de la siguiente manera:
1. Creación de un controlador de datos habilitado para Azure Arc para pruebas en una de las distribuciones validadas de un clúster de Kubernetes
1. Creación de SQL Managed Instance habilitado para Azure Arc o de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc

> [!CAUTION]
> Algunos de los modos y niveles de servicio de datos están [disponibles con carácter general](release-notes.md) y otros están en versión preliminar. Se recomienda no mezclar servicios de disponibilidad general y versión preliminar en el mismo controlador de datos. Si combina servicios de disponibilidad general y versión preliminar en el mismo controlador de datos, no puede realizar una actualización local. En ese escenario, cuando quiera actualizar, debe quitar y volver a crear el controlador de datos y los servicios de datos.

## <a name="overview-create-the-azure-arc-enabled-data-controller"></a>Introducción: Creación del controlador de datos habilitado para Azure Arc

Puede crear servicios de datos habilitados para Azure Arc en varios tipos diferentes de clústeres de Kubernetes y servicios de Kubernetes administrado mediante varios enfoques diferentes.

Actualmente, la lista validada de servicios y distribuciones de Kubernetes incluye:


- AWS Elastic Kubernetes Service (EKS)
- Azure Kubernetes Service (AKS)
- Azure Kubernetes Service en Azure Stack HCl
- Azure RedHat OpenShift (ARO)
- Google Cloud Kubernetes Engine (GKE)
- Código abierto de nivel superior de Kubernetes implementado normalmente mediante kubeadm
- OpenShift Container Platform (OCP)

> [!IMPORTANT]
> * La versión mínima admitida de Kubernetes es la versión 1.19. Consulte [Problemas conocidos](./release-notes.md#known-issues) para más información.
> * La versión mínima admitida de OCP es la 4.7.
> * si usa Azure Kubernetes Service, el tamaño de la máquina virtual del nodo de trabajo del clúster debe ser al menos **Standard_D8s_v3** y usar **discos Premium.** 
> * El clúster no debe abarcar varias zonas de disponibilidad. 
> * Consulte [Problemas conocidos](./release-notes.md#known-issues) para más información.

Independientemente de la opción que elija, durante el proceso de creación tendrá que proporcionar la siguiente información:

- **Nombre del controlador de datos**: nombre descriptivo del controlador de datos; por ejemplo, "production-dc", "seattle-dc". El nombre debe cumplir los [estándares de nomenclatura de Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).
- **Nombre de usuario**: nombre de usuario para los usuarios administradores de Kibana/Grafana.
- **Contraseña**: contraseña del usuario administrador de Kibana/Grafana.
- **Nombre del espacio de nombres de Kubernetes**: nombre del espacio de nombres de Kubernetes en el que desea crear el controlador de datos.
- **Modo de conectividad**: el modo de conectividad determina el grado de conectividad desde el entorno de los servicios de datos habilitados para Azure Arc a Azure. El modo de conexión indirecta está disponible con carácter general. El modo de conexión directa está en versión preliminar.  La elección del modo de conectividad determina las opciones de los métodos de implementación.  Para más información, consulte[Modo de conectividad](./connectivity.md).
- **Identificador de suscripción de Azure**: GUID de la suscripción de Azure en el que desea que se cree el recurso del controlador de datos en Azure.  Todas las instancias de SQL Managed Instance habilitado para Azure Arc y todos los grupos de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc también se crearán y facturarán en esa suscripción.
- **Nombre del grupo de recursos de Azure**: nombre del grupo de recursos en el que desea que se cree el recurso del controlador de datos en Azure.  Todas las instancias de SQL Managed Instance habilitado para Azure Arc y todos los grupos de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc también se crearán en este grupo de recursos.
- **Ubicación de Azure**: ubicación de Azure en la que se almacenarán los metadatos del recurso del controlador de datos en Azure. Para obtener una lista de las regiones disponibles, consulte [Infraestructura global de Azure/Productos por región](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc). Los metadatos y la información de facturación sobre los recursos de Azure administrados por el controlador de datos que va a implementar solo se almacenarán en la ubicación de Azure que especifique como parámetro de ubicación. Si va a implementar en el modo conectado directamente, el parámetro de ubicación para el controlador de datos será el mismo que la ubicación del recurso de ubicación personalizado de destino.
- **Información de la entidad de servicio**: como se describe en el artículo [Carga de requisitos previos](upload-metrics-and-logs-to-azure-monitor.md), necesitará la información de la entidad de servicio durante la creación de un controlador de datos de Azure Arc al implementar en el modo de conexión *directa*. Para el modo de conexión *indirecta*, la entidad de servicio sigue siendo necesaria para exportar y cargar manualmente, pero después que se haya creado el controlador de datos de Azure Arc.
- **Infraestructura**: para fines de facturación, es necesario indicar la infraestructura en la que se ejecutan los servicios de datos habilitados para Azure Arc.  Las opciones son `alibaba`, `aws`, `azure`, `gcp`, `onpremises` o `other`.

## <a name="additional-concepts-for-direct-connected-mode"></a>Conceptos adicionales para el modo de conexión directa

Como se describe en los [modos de conectividad](./connectivity.md), el controlador de datos de Azure Arc se puede implementar en los modos de conexión **directa** e **indirecta**. La implementación de los servicios de datos de Azure Arc en el modo de conexión **directa** requiere conocer algunos conceptos y consideraciones adicionales.
En primer lugar, el clúster de Kubernetes en el que se implementarán los servicios de datos habilitados para Azure Arc debe ser un [clúster de Kubernetes habilitado para Azure Arc](../kubernetes/overview.md). La incorporación del clúster de Kubernetes a Azure Arc proporciona conectividad de Azure que se utiliza para funcionalidades como la carga automática de información de uso, registros, métricas, etc. La conexión del clúster de Kubernetes a Azure también le permite implementar y administrar servicios de datos de Azure Arc en el clúster directamente desde Azure Portal.

La conexión del clúster de Kubernetes a Azure conlleva los pasos siguientes:
- [Conexión del clúster a Azure](../kubernetes/quickstart-connect-cluster.md)

En segundo lugar, una vez incorporado el clúster de Kubernetes a Azure Arc, la implementación de servicios de datos habilitados para Azure Arc en un clúster de Kubernetes habilitado para Azure Arc implica lo siguiente:
- Creación de la extensión de servicios de datos de Arc y más información sobre las [extensiones de clúster](../kubernetes/conceptual-extensions.md)
- Creación de una ubicación personalizada y más información sobre [ubicaciones personalizadas](../kubernetes/conceptual-custom-locations.md)
- Creación del controlador de datos de Azure Arc

Estos tres pasos se pueden realizar en un solo paso mediante el asistente para la creación de controladores de datos de Azure Arc en Azure Portal.

Una vez instalado el controlador de datos de Azure Arc, se pueden crear servicios de datos como SQL Managed Instance habilitado para Azure Arc o un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.


## <a name="next-steps"></a>Pasos siguientes

Hay varias opciones para crear el controlador de datos de Azure Arc:

> **¿Solo desea realizar algunas pruebas?**
> Empiece a trabajar rápidamente con [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) en Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o en una máquina virtual de Azure.
>
- [Creación de un controlador de datos en modo de conexión directa con Azure Portal](create-data-controller-direct-prerequisites.md)
- [Creación de un controlador de datos en modo de conexión indirecta con la CLI](create-data-controller-indirect-cli.md)
- [Creación de un controlador de datos en modo de conexión indirecta con Azure Data Studio](create-data-controller-indirect-azure-data-studio.md)
- [Creación de un controlador de datos en modo de conexión indirecta desde Azure Portal mediante un cuaderno de Jupyter Notebook en Azure Data Studio](create-data-controller-indirect-azure-portal.md)
- [Creación de un controlador de datos en modo de conexión indirecta con herramientas de Kubernetes como kubectl u oc](create-data-controller-using-kubernetes-native-tools.md)
