---
title: 'Servicios de datos habilitados para Azure Arc: notas de la versión'
description: Registro de versiones por fecha de lanzamiento para servicios de datos habilitados para Azure Arc
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/04/2021
ms.topic: conceptual
ms.openlocfilehash: ca1b31abcfb9985dadda1ece8fced0a9f70c3b04
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893925"
---
# <a name="version-log"></a>Registro de versiones

En este artículo se identifican las versiones de componentes con cada versión de Servicios de datos habilitados para Azure Arc.

## <a name="november-2-2021"></a>2 de noviembre de 2021

En la tabla siguiente se describen los componentes de esta versión.

|Componente  |Value  |
|--------------------------------------------------------|---------|
|Etiqueta de imágenes de contenedor                                    | v1.1.0_2021-11-02 |
|Nombres y versiones de CRD                                  | `datacontrollers.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`monitors.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`postgresqls.arcdata.microsoft.com`: v1beta1, v2beta2 <br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`: v1beta1, v1 <br/>`dags.sql.arcdata.microsoft.com`: v1beta1, v2beta2 |
|Versión de API de ARM                                         | 2021-11-01 |
|Versión de la extensión de la CLI de Azure `arcdata`                   | 1.1.0, (Nov 3),</br>1.1.1 (Nov4) |
|Versión de la extensión de gráfico de Helm de Kubernetes habilitado para Arc     | 1.0.17551005 (obligatorio si se actualiza desde GA) <br/><br/> 1.1.17561007 (GA+1/gráfico de versiones de noviembre) |
|Extensión de datos de Arc para Azure Data Studio                | 0.9.7 |

## <a name="august-3-2021"></a>3 de agosto de 2021

Esta versión actualiza a la extensión de Azure Arc para que Azure Data Studio se alinee con la disponibilidad general del 30 de julio. En la tabla siguiente se describen los componentes de las actualizaciones de esta versión. 

|Componente  |Value  |
|--------------------------------------------------------|---------|
|Extensión de datos de Arc para Azure Data Studio                | 0.9.6 |

Todos los demás componentes son los mismos que los publicados anteriormente.

## <a name="july-30-2021"></a>30 de julio de 2021

Esta versión presenta la disponibilidad general de SQL Managed Instance habilitado para Azure Arc de uso general y SQL Server habilitado para Azure Arc. En la tabla siguiente se describen los componentes de esta versión.

|Componente  |Value  |
|--------------------------------------------------------|---------|
|Etiqueta de imágenes de contenedor                                    | v1.0.0_2021-07-30 |
|Nombres y versiones de CRD                                  |`datacontrollers.arcdata.microsoft.com`: v1beta1, v1 <br/>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1, v1 <br/>`monitors.arcdata.microsoft.com`: v1beta1, v1 <br/>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1, v1 <br/>`postgresqls.arcdata.microsoft.com`: v1beta1 <br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`: v1beta1 <br/>`dags.sql.arcdata.microsoft.com`: v1beta1 <br/> |
|Versión de API de ARM                                         | 2021-08-01 (estable) |
|Versión de la extensión de la CLI de Azure `arcdata`                   | 1,0 |
|Versión de la extensión de gráfico de Helm de Kubernetes habilitado para Arc     | 1.0.16701001, entrenamiento de la versión: estable |
|Extensión de datos de Arc para Azure Data Studio                | 0.9.5 |
