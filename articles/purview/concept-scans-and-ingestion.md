---
title: Exámenes e ingesta
description: En este artículo se explican los exámenes y la ingesta en Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 140e87f4e03081825fe75ba73b7c5ebd33b20ada
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122398134"
---
# <a name="scans-and-ingestion-in-azure-purview"></a>Exámenes e ingesta en Azure Purview

En este artículo se proporciona información general sobre las características de examen e ingesta de Azure Purview. Estas características conectan la cuenta de Purview a los orígenes para rellenar el mapa de datos y el catálogo de datos para que pueda empezar a explorar y administrar los datos mediante Purview.

## <a name="scanning"></a>Exploración

Una vez [registrados](manage-data-sources.md) los orígenes de datos en la cuenta de Purview, el siguiente paso es proceder a su examen. El proceso de examen establece una conexión con el origen de datos y captura metadatos técnicos, como nombres, tamaño de archivo, columnas, etc. También extrae el esquema de los orígenes de datos estructurados, aplica clasificaciones en esquemas y [aplica etiquetas de confidencialidad si la cuenta de Purview está conectada a un Centro de seguridad y cumplimiento de Microsoft 365 (SCC)](create-sensitivity-label.md). El proceso de examen se puede desencadenar para ejecutarse inmediatamente o se puede programar para ejecutarse periódicamente para mantener actualizada la cuenta de Purview.

En cada examen hay personalizaciones que puede aplicar para que solo se analicen los orígenes en busca de la información que necesita.

### <a name="scope-your-scan"></a>Ámbito del examen

Al examinar un origen, tiene la opción de examinar todo el origen de datos o elegir examinar solo entidades específicas (carpetas o tablas). Las opciones disponibles dependen del origen que examine y se pueden definir para exámenes programados y puntuales.

Por ejemplo, al [crear y ejecutar un examen para una instancia de Azure SQL Database](register-scan-azure-sql-database.md#creating-and-running-a-scan), puede elegir qué tablas examinar o seleccionar toda la base de datos.

### <a name="scan-rule-set"></a>Conjunto de reglas de examen

Un conjunto de reglas de examen determina los tipos de información que buscará un examen cuando se ejecute en uno de los orígenes. Las reglas disponibles dependen del tipo de origen que examine, pero incluyen elementos como los [tipos de archivo](sources-and-scans.md#file-types-supported-for-scanning) que debe examinar y los tipos de [clasificaciones](supported-classifications.md) que necesita.

Ya hay [conjuntos de reglas de examen del sistema](create-a-scan-rule-set.md#system-scan-rule-sets) disponibles para muchos tipos de orígenes de datos, pero también puede [crear sus propios conjuntos de reglas de examen](create-a-scan-rule-set.md) para adaptar los exámenes a su organización.

## <a name="ingestion"></a>Ingesta de datos

Los metadatos o las clasificaciones técnicos identificados en el proceso de examen se envían a ingesta. El proceso de ingesta es responsable de rellenar el mapa de datos y lo administra Purview.  La ingesta analiza la entrada del examen, [aplica patrones de conjunto de recursos](concept-resource-sets.md#how-azure-purview-detects-resource-sets), rellena la información de [linaje](concept-data-lineage.md) disponible y, luego, carga automáticamente el mapa de datos. Los recursos o esquemas solo se pueden detectar o mantener una vez completada la ingesta. Por lo tanto, si el examen se ha completado, pero no ha visto los recursos en el mapa o el catálogo de datos, tendrá que esperar a que finalice el proceso de ingesta.

## <a name="next-steps"></a>Pasos siguientes

Para más información, o para obtener instrucciones específicas para examinar orígenes, siga los vínculos siguientes.

* Para comprender los conjuntos de recursos, consulte nuestro [artículo sobre conjuntos de recursos](concept-resource-sets.md).
* [Registro y examen de una instancia de Azure SQL Database](register-scan-azure-sql-database.md#creating-and-running-a-scan)
* [Linaje en Azure Purview](catalog-lineage-user-guide.md)
