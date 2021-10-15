---
title: Planeamiento de una implementación de servicios de datos habilitados para Azure Arc
description: En este artículo se explican las consideraciones para planear la implementación de servicios de datos habilitados para Azure Arc.
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: ffb57d83493aee607fa89720d978a00b25e44e24
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426856"
---
# <a name="plan-an-azure-arc-enabled-data-services-deployment"></a>Planeamiento de una implementación de servicios de datos habilitados para Azure Arc

En este artículo se describe cómo planear la implementación de servicios de datos habilitados para Azure Arc.

Cuando empiece a planear la implementación de servicios de datos habilitados para Azure Arc, es importante comprender correctamente las cargas de trabajo de la base de datos y los requisitos empresariales para esas cargas de trabajo. Por ejemplo, considere la disponibilidad, la continuidad empresarial y los requisitos de capacidad de memoria, la CPU y el almacenamiento para esas cargas de trabajo. Además, debe preparar cuidadosamente la infraestructura para admitir las cargas de trabajo de base de datos, en función de sus requisitos empresariales.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, asegúrese de haber cumplido determinados requisitos previos y de tener preparada la información y los antecedentes necesarios. Para garantizar una implementación correcta, el entorno de infraestructura debe configurarse correctamente con el nivel correcto de acceso y la capacidad adecuada para el almacenamiento, la CPU y la memoria. 

Consulte los artículos siguientes:

- [Guía para el ajuste de tamaño](sizing-guidance.md)
- [Configuración de almacenamiento](storage-configuration.md)
- [Modos de conectividad y requisitos](connectivity.md)

Compruebe lo siguiente:

- La [extensión de la CLI de Azure Arc](install-arcdata-extension.md) está instalada.
- Las demás [herramientas de cliente](install-client-tools.md) están instaladas.
- Tiene acceso al clúster de Kubernetes.
- El archivo *kubeconfig* está configurado. Debe apuntar al clúster de Kubernetes en el que desea realizar la implementación. Ejecute el siguiente comando para comprobar el contexto actual del clúster:
   ```console
   kubectl cluster-info
   ```  
- Tiene una suscripción de Azure en la que se proyectarán y facturarán recursos como un controlador de datos de Azure Arc, una instancia administrada de SQL habilitada para Azure Arc o un servidor de Hiperescala de PostgreSQL habilitada para Azure Arc.

Una vez preparada la infraestructura, implemente los servicios de datos habilitados para Azure Arc de la siguiente manera:
1. Cree un controlador de datos habilitado para Azure Arc en una de las distribuciones validadas de un clúster de Kubernetes.
1. Creación de SQL Managed Instance habilitado para Azure Arc o de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc

> [!CAUTION]
> Algunos de los modos y niveles de servicio de datos están [disponibles con carácter general](release-notes.md) y otros están en versión preliminar. Se recomienda no mezclar servicios de disponibilidad general y versión preliminar en el mismo controlador de datos. Si combina servicios de disponibilidad general y versión preliminar en el mismo controlador de datos, no puede realizar una actualización local. En ese escenario, cuando quiera actualizar, debe quitar y volver a crear el controlador de datos y los servicios de datos.

## <a name="overview-create-an-azure-arc-enabled-data-controller"></a>Introducción: Creación del controlador de datos habilitado para Azure Arc

Puede crear servicios de datos habilitados para Azure Arc en varios tipos de clústeres de Kubernetes y puede crear servicios de Kubernetes administrados mediante diversos enfoques.

Actualmente, la lista validada de servicios y distribuciones de Kubernetes incluye:

- Amazon Elastic Kubernetes Service (Amazon EKS)
- Azure Kubernetes Service (AKS)
- Azure Kubernetes Service en Azure Stack HCl
- Red Hat OpenShift en Azure
- Google Kubernetes Engine (GKE)
- Kubernetes de código abierto ascendente (normalmente implementado mediante kubeadm)
- OpenShift Container Platform (OCP)

> [!IMPORTANT]
> * La versión mínima admitida de Kubernetes es la versión 1.19. Para obtener más información, consulte la sección de problemas conocidos de [Notas de la versión&nbsp;: Servicios de datos habilitados para Azure Arc](./release-notes.md#known-issues).
> * La versión mínima admitida de OCP es la 4.7.
> * Si usa Azure Kubernetes Service, el tamaño de la máquina virtual del nodo de trabajo del clúster debe ser al menos Standard_D8s_v3 y usar discos Premium. 
> * El clúster no debe abarcar varias zonas de disponibilidad. 
> * Para obtener más información, consulte la sección de problemas conocidos de [Notas de la versión&nbsp;: Servicios de datos habilitados para Azure Arc](./release-notes.md#known-issues).

Al crear servicios de datos habilitados para Azure Arc, independientemente de la opción de servicio o distribución que elija, deberá proporcionar la siguiente información:

- **Nombre del controlador de datos**: nombre descriptivo del controlador de datos (por ejemplo, *production-dc*, *seattle-dc*). El nombre debe cumplir los [estándares de nomenclatura de Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).

- **Nombre de usuario**: nombre de usuario para los usuarios administradores de Kibana/Grafana.
- **Contraseña**: contraseña del usuario administrador de Kibana/Grafana.
- **Nombre del espacio de nombres de Kubernetes**: nombre del espacio de nombres de Kubernetes en el que desea crear el controlador de datos.
- **Modo de conectividad**: determina el grado de conectividad desde el entorno de los servicios de datos habilitados para Azure Arc a Azure. *Indirecto*: el modo de conectividad indirecta está generalmente disponible y el modo de conectividad *directa* está en versión preliminar. La elección del modo de conectividad determina las opciones de los métodos de implementación. Para más información, consulte los [modos y requisitos de conectividad](./connectivity.md).
- **Identificador de suscripción de Azure**: GUID de la suscripción de Azure en el que desea que se cree el recurso del controlador de datos en Azure. Todas las instancias administradas de SQL habilitadas para Azure Arc y los grupos de servidores de Hiperescala de Azure Database for PostgreSQL también se crean en esta suscripción y se facturan a ella.
- **Nombre del grupo de recursos de Azure**: nombre del grupo de recursos en el que desea que se cree el recurso del controlador de datos en Azure. Todas las instancias administradas de SQL habilitadas para Azure Arc y los grupos de servidores de Hiperescala de Azure Database for PostgreSQL también se crean en este grupo de recursos.
- **Ubicación de Azure**: ubicación de Azure en la que se almacenarán los metadatos del recurso del controlador de datos en Azure. Para obtener una lista de las regiones disponibles, consulte la página [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) de la infraestructura global de Azure. Los metadatos y la información de facturación sobre los recursos de Azure administrados por el controlador de datos que va a implementar solo se almacenarán en la ubicación de Azure que especifique como parámetro de ubicación. Si va a implementar en modo de conectividad directa, el parámetro de ubicación para el controlador de datos es el mismo que la ubicación del recurso de ubicación personalizada de destino.
- **Información de la entidad de servicio** 
   - Si va a implementar en modo de conectividad directa durante la creación del controlador de datos de Azure Arc, necesita la información de la entidad de servicio. Para más información, consulte la sección "Asignación de roles a la entidad de servicio" de [Carga de datos de uso, métricas y registros en Azure](upload-metrics-and-logs-to-azure-monitor.md). 
   - Para el modo de conexión indirecta, todavía necesita la entidad de servicio para exportar y cargar manualmente, pero después que se haya creado el controlador de datos de Azure Arc.
- **Infraestructura**: para fines de facturación, es necesario indicar la infraestructura en la que se ejecutan los servicios de datos habilitados para Azure Arc. Las opciones son *alibaba*, *aws*, *azure*, *gcp*, *onpremises* u *other*.

## <a name="additional-concepts-for-direct-connectivity-mode"></a>Conceptos adicionales para el modo de conexión directa

Como se ha descrito en [Modos y requisitos de conectividad](./connectivity.md), puede implementar el controlador de datos Azure Arc en modo de conectividad directa o indirecta. La implementación de los servicios de datos de Azure Arc en el modo de conexión directa requiere conocer un par de conceptos y consideraciones adicionales:

* En primer lugar, el clúster de Kubernetes en el que se implementarán los servicios de datos habilitados para Azure Arc debe ser un [clúster de Kubernetes habilitado para Azure Arc](../kubernetes/overview.md). La implementación del clúster de Kubernetes en Azure Arc proporciona conectividad de Azure con funcionalidades como la carga automática de información de uso, registros y métricas. Al conectar el clúster de Kubernetes a Azure, también puede implementar y administrar servicios de datos de Azure Arc directamente en el clúster desde Azure Portal. Para obtener información sobre cómo hacerlo, consulte [Conexión del clúster a Azure](../kubernetes/quickstart-connect-cluster.md).

* Una vez implementado el clúster de Kubernetes en Azure Arc, implemente los servicios de datos habilitados para Azure Arc en un clúster de Kubernetes habilitado para Azure Arc de la manera siguiente:
   1. Cree la extensión de servicios de datos de Arc. Para saber cómo hacerlo, consulte [Extensiones de clústeres en Kubernetes con Azure Arc habilitado](../kubernetes/conceptual-extensions.md).
   1. Crear una ubicación personalizada. Para saber cómo hacerlo, consulte [Ubicaciones personalizadas basadas en Kubernetes habilitado para Azure Arc](../kubernetes/conceptual-custom-locations.md).
   1. Cree el controlador de datos de Azure Arc.

   Puede ejecutar los tres pasos se pueden realizar en un solo paso mediante el asistente para la creación de controladores de datos de Azure Arc en Azure Portal.

Después de haber instalado el controlador de datos de Azure Arc, puede acceder a los servicios de datos como SQL Managed Instance habilitado para Azure Arc o un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.


## <a name="next-steps"></a>Pasos siguientes

Tiene varias opciones adicionales para crear el controlador de datos de Azure Arc:

> **¿Solo desea realizar algunas pruebas?**
> Comience a trabajar rápidamente con [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) en AKS, Amazon EKS o GKE, o en una máquina virtual de Azure.
>
- [Creación de un controlador de datos en modo de conexión directa con Azure Portal](create-data-controller-direct-prerequisites.md)
- [Creación de un controlador de datos en modo de conexión indirecta con la CLI](create-data-controller-indirect-cli.md)
- [Creación de un controlador de datos en modo de conexión indirecta con Azure Data Studio](create-data-controller-indirect-azure-data-studio.md)
- [Creación de un controlador de datos en modo de conectividad indirecta desde Azure Portal mediante un cuaderno de Jupyter Notebook en Azure Data Studio](create-data-controller-indirect-azure-portal.md)
- [Creación de un controlador de datos en modo de conectividad indirecta con herramientas de Kubernetes como kubectl u oc](create-data-controller-using-kubernetes-native-tools.md)
