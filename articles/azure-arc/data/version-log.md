---
title: 'Servicios de datos habilitados para Azure Arc: notas de la versión'
description: Registro de versiones por fecha de lanzamiento para servicios de datos habilitados para Azure Arc
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 08/06/2021
ms.topic: conceptual
ms.openlocfilehash: 3e636fe5ff6477f78f09d940b70099b8cbf41fb9
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862852"
---
# <a name="version-log"></a>Registro de versiones

En la tabla siguiente, se describen las distintas versiones a lo largo del tiempo a medida que cambian:

|Date|Nombre de la versión.|Etiqueta de imágenes de contenedor|Prefijos y versiones de CRD|Versión de API de ARM|Versión de la extensión de la CLI de Azure `arcdata`|Versión de la extensión de gráfico de Helm de Kubernetes habilitado para Arc|Extensión de datos de Arc para Azure Data Studio|
|---|---|---|---|---|---|---|---|
|30 de julio de 2021|Uso general de SQL Managed Instance habilitado para Arc y disponibilidad general de SQL Server habilitado para Arc|v1.0.0_2021-07-30|`datacontrollers`: v1beta1, v1 <br/>`exporttasks.tasks`: v1beta1, v1 <br/>`monitors`: v1beta1, v1 <br/>`sqlmanagedinstances.sql`: v1beta1, v1 <br/>`postgresqls`: v1beta1 <br/>`sqlmanagedinstancerestoretasks.tasks.sql`: v1beta1 <br/>`dags.sql`: v1beta1 <br/>|2021-08-01 (estable)|1.0|1.0.16701001, entrenamiento de la versión: estable|0.9.5|
|3 de agosto de 2021|Actualización a la extensión de Azure Arc para que Azure Data Studio se alinee con la disponibilidad general del 30 de julio|Sin cambios|Sin cambios|Sin cambios|Sin cambios|Sin cambios|0.9.6|

Para obtener el CRD completo, anexe `.arcdata.microsoft.com` al prefijo. 
