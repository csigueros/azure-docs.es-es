---
title: Introducción a Kubernetes habilitado para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/25/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: En este artículo se proporciona una introducción a Kubernetes habilitado para Azure Arc.
keywords: Kubernetes, Arc, Azure, containers
ms.openlocfilehash: 7a3d2c4465961984371abf17bff98d33f7b185a8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812554"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>¿Qué es Kubernetes habilitado para Azure Arc?

Con Kubernetes habilitado para Azure Arc se pueden asociar y configurar clústeres de Kubernetes ubicados dentro o fuera de Azure. Cuando conecte un clúster de Kubernetes a Azure Arc, este:
* Aparecerá en Azure Portal con un identificador de Azure Resource Manager y una identidad administrada. 
* Se colocará en una suscripción de Azure y un grupo de recursos.
* Recibirá etiquetas como cualquier otro recurso de Azure. 

Para conectar un clúster de Kubernetes a Azure, es preciso que el administrador de clústeres implemente agentes. Estos agentes:
* Se ejecutan en el espacio de nombres de Kubernetes `azure-arc` como implementaciones estándar de Kubernetes.
* Controlan la conectividad con Azure.
* Recopilan registros y métricas de Azure Arc.
* Inspeccionan las solicitudes de configuración. 

Kubernetes con Azure Arc habilitado admite SSL estándar del sector para proteger los datos en tránsito. Además, los datos en reposo se almacenan cifrados en una base de datos de Azure Cosmos DB para garantizar la confidencialidad de los datos.

## <a name="supported-kubernetes-distributions"></a>Distribuciones de Kubernetes admitidas

Kubernetes habilitado para Azure Arc funciona con todos los clústeres de Kubernetes con la certificación de Cloud Native Computing Foundation (CNCF). El equipo de Azure Arc ha trabajado con los [más importantes asociados del sector para validar la conformidad](./validation-program.md) de sus distribuciones de Kubernetes con Kubernetes habilitado para Azure Arc.

## <a name="supported-scenarios"></a>Escenarios admitidos 

Kubernetes habilitado para Azure Arc admite los siguientes escenarios: 

* Conexión de Kubernetes cuando se ejecuta fuera de Azure para la realización de tareas de inventario, agrupación y etiquetado.

* Implementación de aplicaciones y aplicación de una configuración mediante una administración de configuraciones basada en GitOps. 

* Visualización y supervisión de clústeres mediante Azure Monitor para contenedores.

* Aplicación de la protección contra amenazas con Azure Defender para Kubernetes.

* Aplicación de las definiciones de directiva mediante Azure Policy para Kubernetes.

* Cree [ubicaciones personalizadas](./custom-locations.md) como ubicaciones de destino para implementar Data Services habilitado para Azure Arc, [App Services en Azure Arc](../../app-service/overview-arc-integration.md) (incluido web, funciones y aplicaciones lógicas) y [Event Grid en Kubernetes](../../event-grid/kubernetes/overview.md).

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="next-steps"></a>Pasos siguientes

Aprenda a conectar un clúster a Azure Arc.
> [!div class="nextstepaction"]
> [Conexión de un clúster a Azure Arc](./quickstart-connect-cluster.md)
