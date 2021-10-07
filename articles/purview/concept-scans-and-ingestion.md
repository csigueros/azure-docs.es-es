---
title: Exámenes e ingesta
description: En este artículo se explican los exámenes y la ingesta en Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 42162519e9e8f3835498d8955adbd7c254775dd9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211722"
---
# <a name="scans-and-ingestion-in-azure-purview"></a>Exámenes e ingesta en Azure Purview

En este artículo se proporciona información general sobre las características de examen e ingesta de Azure Purview. Estas características conectan la cuenta de Purview a los orígenes para rellenar el mapa de datos y el catálogo de datos para que pueda empezar a explorar y administrar los datos mediante Purview.

## <a name="scanning"></a>Exploración

Una vez [registrados](manage-data-sources.md) los orígenes de datos en la cuenta de Purview, el siguiente paso es proceder a su examen. El proceso de examen establece una conexión con el origen de datos y captura metadatos técnicos, como nombres, tamaño de archivo, columnas, etc. También extrae el esquema de los orígenes de datos estructurados, aplica clasificaciones en esquemas y [aplica etiquetas de confidencialidad si la cuenta de Purview está conectada a un Centro de seguridad y cumplimiento de Microsoft 365 (SCC)](create-sensitivity-label.md). El proceso de examen se puede desencadenar para ejecutarse inmediatamente o se puede programar para ejecutarse periódicamente para mantener actualizada la cuenta de Purview.

En cada examen hay personalizaciones que puede aplicar para que solo se analicen los orígenes en busca de la información que necesita.

### <a name="choose-an-authentication-method-for-your-scans"></a>Elección de un método de autenticación para los exámenes

Purview es un servicio seguro de manera predeterminada. No se almacenan contraseñas ni secretos directamente en Purview, por lo que deberá elegir un método de autenticación para los orígenes. Hay cuatro maneras posibles de autenticar la cuenta de Purview, pero no todos los métodos son compatibles con todos los orígenes de datos.
 - Identidad administrada
 - Entidad de servicio
 - Autenticación de SQL
 - Clave de cuenta o autenticación básica

Siempre que sea posible, una identidad administrada es el método de autenticación preferido, porque elimina la necesidad de almacenar y administrar credenciales de orígenes de datos individuales. Así se puede reducir considerablemente el tiempo que usted y su equipo dedican a configurar y solucionar problemas de autenticación para los exámenes. Al habilitar una identidad administrada para la cuenta de Purview, se crea una identidad en Azure Active Directory y se vincula al ciclo de vida de la cuenta. 

### <a name="scope-your-scan"></a>Ámbito del examen

Al examinar un origen, tiene la opción de examinar todo el origen de datos o elegir examinar solo entidades específicas (carpetas o tablas). Las opciones disponibles dependen del origen que examine y se pueden definir para exámenes programados y puntuales.

Por ejemplo, al [crear y ejecutar un examen para una instancia de Azure SQL Database](register-scan-azure-sql-database.md#creating-and-running-a-scan), puede elegir qué tablas examinar o seleccionar toda la base de datos.

### <a name="scan-rule-set"></a>Conjunto de reglas de examen

Un conjunto de reglas de examen determina los tipos de información que buscará un examen cuando se ejecute en uno de los orígenes. Las reglas disponibles dependen del tipo de origen que examine, pero incluyen elementos como los [tipos de archivo](sources-and-scans.md#file-types-supported-for-scanning) que debe examinar y los tipos de [clasificaciones](supported-classifications.md) que necesita.

Ya hay [conjuntos de reglas de examen del sistema](create-a-scan-rule-set.md#system-scan-rule-sets) disponibles para muchos tipos de orígenes de datos, pero también puede [crear sus propios conjuntos de reglas de examen](create-a-scan-rule-set.md) para adaptar los exámenes a su organización.

### <a name="schedule-your-scan"></a>Programación del examen

Purview le ofrece la opción de examinar semanal o mensualmente en un momento específico que elija. Los exámenes semanales pueden ser adecuados para orígenes de datos con estructuras que están en desarrollo de forma activa o cambian con frecuencia. El examen mensual es más adecuado para los orígenes de datos que cambian con poca frecuencia. Un procedimiento recomendado es trabajar con el administrador del origen que desea examinar para identificar un momento en el que las demandas de proceso del origen sean bajas.

### <a name="how-scans-detect-deleted-assets"></a>Cómo detectan los exámenes los recursos eliminados

Un catálogo de Azure Purview conoce el estado de un almacén de datos solo cuando lo examina. Para que el catálogo determine si se ha eliminado un archivo, una tabla o un contenedor, compara la última salida del examen con la salida del examen actual. Por ejemplo, supongamos que la última vez que examinó una cuenta de Azure Data Lake Storage Gen2, incluía una carpeta llamada *carpeta1*. Al volver a examinar la misma cuenta, *folder1* ya no está. Por lo tanto, el catálogo supone que la carpeta se ha eliminado.

#### <a name="detecting-deleted-files"></a>Detección de archivos eliminados

La lógica para detectar archivos que faltan funciona para varios exámenes realizados por el mismo usuario y también por distintos usuarios. Por ejemplo, supongamos que un usuario ejecuta un examen puntual en un almacén de datos Data Lake Storage Gen2 en las carpetas A, B y C. Más adelante, otro usuario en la misma cuenta ejecuta un examen puntual en las carpetas C, D y E del mismo almacén de datos. Dado que la carpeta C se ha examinado dos veces, el catálogo la comprueba para detectar posibles eliminaciones. Sin embargo, las carpetas A, B, D y E solo se han examinado una vez y el catálogo no las comprobará para determinar si hay recursos eliminados.

Para mantener los archivos eliminados fuera del catálogo, es importante realizar exámenes periódicamente. El intervalo entre exámenes es importante, ya que el catálogo no puede detectar recursos eliminados hasta que se ejecute otro examen. Por lo tanto, si ejecuta exámenes una vez al mes en un almacén determinado, el catálogo no podrá detectar recursos de datos eliminados en ese almacén hasta que ejecute el siguiente examen un mes más tarde.

Al enumerar almacenes de datos de gran tamaño, como Data Lake Storage Gen2, es posible que se omita información de varias maneras (como errores de enumeración y eventos eliminados). Un examen determinado podría pasar por alto un archivo creado o eliminado. Por lo tanto, a menos que el catálogo tenga la seguridad de que se ha eliminado un archivo, no lo eliminará del catálogo. Esta estrategia implica que podría haber errores cuando un archivo que no existe en el almacén de datos examinado todavía existe en el catálogo. En algunos casos, es posible que sea necesario examinar un almacén de datos dos o tres veces antes de que se detecten determinados recursos eliminados.

> [!NOTE]
> Los recursos marcados para su eliminación se eliminan tras un examen correcto. Los recursos eliminados pueden seguir estando visibles en el catálogo durante un tiempo antes de que se procesen y se quiten.

## <a name="ingestion"></a>Ingesta de datos

Los metadatos o las clasificaciones técnicos identificados en el proceso de examen se envían a ingesta. El proceso de ingesta es responsable de rellenar el mapa de datos y lo administra Purview.  La ingesta analiza la entrada del examen, [aplica patrones de conjunto de recursos](concept-resource-sets.md#how-azure-purview-detects-resource-sets), rellena la información de [linaje](concept-data-lineage.md) disponible y, luego, carga automáticamente el mapa de datos. Los recursos o esquemas solo se pueden detectar o mantener una vez completada la ingesta. Por lo tanto, si el examen se ha completado, pero no ha visto los recursos en el mapa o el catálogo de datos, tendrá que esperar a que finalice el proceso de ingesta.

## <a name="next-steps"></a>Pasos siguientes

Para más información, o para obtener instrucciones específicas para examinar orígenes, siga los vínculos siguientes.

* Para comprender los conjuntos de recursos, consulte nuestro [artículo sobre conjuntos de recursos](concept-resource-sets.md).
* [Registro y examen de una instancia de Azure SQL Database](register-scan-azure-sql-database.md#creating-and-running-a-scan)
* [Linaje en Azure Purview](catalog-lineage-user-guide.md)
