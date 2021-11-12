---
title: Visualización del recurso de controlador de datos de Azure Arc en Azure Portal
description: Visualización del recurso de controlador de datos de Azure Arc en Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: fe594e7459f32d0639c39b9f694f31e7816ca118
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558751"
---
# <a name="view-azure-arc-data-controller-resource-in-azure-portal"></a>Visualización del recurso de controlador de datos de Azure Arc en Azure Portal

Para ver el controlador de datos de Azure Arc en Azure Portal, los datos de uso, métricas o registros del clúster de Kubernetes deben exportarse y cargarse en Azure. 

## <a name="direct-connected-mode"></a>Modo de conexión directa
Si el controlador de datos de Azure Arc se implementa en modo de conexión **directa**, los datos de uso se cargan automáticamente en Azure y los recursos de Kubernetes se proyectan en Azure.

## <a name="indirect-connected-mode"></a>Modo conectado indirecto
En el modo de conexión **indirecta**, debe exportar y cargar los datos de uso, métricas o registros en Azure, como se explica en [Carga de datos de uso, métricas y registros en Azure](upload-metrics-and-logs-to-azure-monitor.md). Esta acción crea los recursos adecuados en Azure.

## <a name="azure-portal"></a>Azure Portal

Después de completar la primera [carga de métricas o registros en Azure](upload-metrics-and-logs-to-azure-monitor.md) o de haber [cargado el uso](view-billing-data-in-azure.md), puede ver el controlador de datos de Azure Arc y cualquier instancia administrada de SQL habilitada para Azure Arc o recursos de servidor de Hiperescala de Postgres habilitados para Azure Arc en Azure Portal.

Abra Azure Portal con la dirección URL: [https://portal.azure.com](https://portal.azure.com).

Busque el controlador de datos por su nombre en la barra de búsqueda y haga clic en él.

