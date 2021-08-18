---
title: Historial de lanzamientos de versiones
titleSuffix: Microsoft Genomics
description: El historial de versiones de actualizaciones al cliente de Python de Microsoft Genomics para obtener correcciones y funcionalidades nuevas.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 8bcf14b64b5e6417c3a1d90fd36651350efac879
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113551729"
---
# <a name="version-release-history"></a>Historial de lanzamientos de versiones
El equipo de Microsoft Genomics actualiza de manera habitual el cliente Python de Microsoft Genomics para obtener correcciones y funcionalidades nuevas. 

## <a name="latest-release"></a>Versión más reciente
La versión del cliente Python actual es la 0.9.0. Se lanzó el 6 febrero de 2019 y admite la ejecución de flujos de trabajo con GATK 3.5 y GATK4. Admite la salida de gVCF y puede aceptar un argumento opcional para la compresión de salida.


## <a name="release-history"></a>Historial de versiones 
Las versiones nuevas del cliente Python de Microsoft Genomics se lanzan una vez al año. Cuando se lanzan versiones nuevas del cliente Python de Microsoft Genomics, aquí se actualiza una lista de las correcciones y características. Cuando se lanzan versiones nuevas, se seguirá brindando soporte técnico a las versiones anteriores al menos por 90 días. En esta página se indicará cuando ya no se brinde soporte técnico a las versiones anteriores. 

### <a name="version-090"></a>Versión 0.9.0
La versión 0.9.0 incluye compatibilidad con la compresión de salida. Esto es equivalente a ejecutar `-bgzip` seguido de `-tabix` en la salida de vcf o gvcf. Para más información, consulte [Preguntas más frecuentes](frequently-asked-questions-genomics.yml). 

### <a name="version-081"></a>Versión 0.8.1
La versión 0.8.1 incluye correcciones de errores menores.  

### <a name="version-080"></a>Versión 0.8.0
La versión 0.8.0 incluye compatibilidad con GATK4 y generación de salidas gVCF.  

### <a name="version-074"></a>Versión 0.7.4
La versión 0.7.4 incluye compatibilidad para aceptar los tokens de SAS en lugar de las claves de cuenta en la entrada `config.txt`. Para más información, consulte la [guía de inicio rápido de tokens de SAS de entrada](quickstart-input-sas.md). 

### <a name="version-073"></a>Versión 0.7.3
La versión 0.7.3 incluye correcciones de errores menores.

### <a name="version-072"></a>Versión 0.7.2
La versión 0.7.2 es la versión inicial. Se publicó el 1 de noviembre de 2017.
