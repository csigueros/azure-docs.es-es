---
title: Notas de la versión de Azure Stack Edge Pro con FPGA 2101 | Microsoft Docs
description: Se describen las incidencias críticas pendientes, y sus soluciones, de la versión 2101 de Azure Stack Edge Pro con FPGA.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 3c8882706bdbb6719dbc2550df350096a4970fd8
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110461228"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Notas de la versión de Azure Stack Edge Pro con FPGA 2101

En las siguientes notas de la versión se identifican las incidencias críticas pendientes y las incidencias resueltas de la versión 2101 de Azure Stack Edge Pro con una matriz de puertas programables (FPGA) integrada.

Las notas de la versión se actualizan continuamente. A medida que se descubran incidencias críticas que requieran una solución alternativa, se irán agregando. Antes de implementar el dispositivo Azure Stack Edge, revise detenidamente la información de las notas de la versión.  

Esta versión se corresponde con la versión de software:

- **Azure Stack Edge 2101 (1.6.1475.2528)** : KB 4599267

> [!NOTE]
> La actualización 2101 solo se puede aplicar a los dispositivos que ejecutan las versiones de disponibilidad general (GA) del software o versiones posteriores.

## <a name="whats-new"></a>Novedades

Esta versión contiene la siguiente corrección de errores:

- **Problema de carga**: Esta versión corrige un problema de carga en el que los reinicios de carga debido a errores pueden ralentizar la velocidad de finalización de la carga. Este problema puede producirse al cargar un conjunto de datos que se compone principalmente de archivos grandes en relación con el ancho de banda disponible, en especial, entre otras cosas, cuando está activo el límite de ancho de banda. Este cambio garantiza una oportunidad suficiente para completar la carga antes de reiniciar la carga de un archivo determinado.

Esta versión también contiene las siguientes actualizaciones:

- Todas las actualizaciones acumulativas de Windows y actualizaciones de .NET Framework que se lanzaron hasta octubre de 2020.
- La versión de firmware del controlador de administración de placa base (BMC) se actualizó de 3.32.32.32 a 3.36.36.36 durante la instalación de fábrica para solucionar la incompatibilidad con las unidades de fuente de alimentación de Dell más recientes.
- Esta versión admite IoT Edge 1.0.9.3 en dispositivos de Azure Stack Edge.

## <a name="known-issues-in-this-release"></a>Problemas conocidos en esta versión

No se ha notificado ningún problema nuevo para esta versión. Todos los problemas notificados de la versión provienen de versiones anteriores. Para ver una lista de los problemas conocidos, vaya a [Problemas conocidos en la versión de disponibilidad general](../databox-gateway/data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Pasos siguientes

- [Preparación de la implementación de Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)