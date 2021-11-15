---
title: Modelo de metadatos compartidos
description: Azure Synapse Analytics permite que los diferentes motores de cálculo de áreas de trabajo compartan bases de datos y tablas entre sus grupos de Apache Spark sin servidor y el grupo de SQL sin servidor.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 10/05/2021
author: ma77b
ms.author: maburd
ms.reviewer: wiassaf
ms.openlocfilehash: 4f9feff8a88e5f8058af808b22ca07191193e41d
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129987126"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Metadatos compartidos de Azure Synapse Analytics

Azure Synapse Analytics permite que los diferentes motores de cálculo de áreas de trabajo compartan bases de datos y tablas entre grupos de Apache Spark y el grupo de SQL sin servidor.

El uso compartido es compatible con el llamado modelo de almacenamiento de datos moderno y proporciona a los motores de SQL del área de trabajo acceso a las bases de datos y las tablas creadas con Spark. También permite que los motores de SQL creen sus propios objetos que no se comparten con los demás motores.

## <a name="support-the-modern-data-warehouse"></a>Compatibilidad con el almacenamiento de datos moderno

El modelo de metadatos compartidos admite el moderno patrón de almacenamiento de datos de la siguiente manera:

1. Los datos del lago de datos se preparan y estructuran de manera eficaz con Spark mediante el almacenamiento de los datos preparados en tablas respaldadas por Parquet (posiblemente particionadas) contenidas en posiblemente varias bases de datos.

2. Las bases de datos creadas por Spark y todas sus tablas se vuelven visibles en cualquiera de las instancias del grupo de Spark del área de trabajo de Azure Synapse y se pueden usar desde cualquiera de los trabajos de Spark. Esta funcionalidad está sujeta a los [permisos](#security-model-at-a-glance), dado que todos los grupos de Spark de un área de trabajo comparten el mismo almacén de metadatos del catálogo subyacente.

3. Las bases de datos creadas por Spark y las tablas con formato Parquet o CSV se vuelven visibles en el grupo de SQL sin servidor del área de trabajo. Las [bases de datos](database.md) se crean automáticamente en los metadatos del grupo de SQL sin servidor y las [tablas externas y administradas](table.md) creadas mediante un trabajo de Spark se hacen accesibles como tablas externas en los metadatos del grupo de SQL sin servidor en el esquema `dbo` de la base de datos correspondiente. 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

La sincronización de objetos se produce de forma asincrónica. Los objetos tendrán un ligero retraso de unos segundos hasta que aparezcan en el contexto de SQL. Una vez que aparecen, los motores de SQL que tienen acceso a ellos pueden consultarlos, pero no actualizarlos ni cambiarlos.

## <a name="shared-metadata-objects"></a>Objetos de metadatos compartidos

Spark permite crear bases de datos, tablas externas, tablas administradas y vistas. Dado que las vistas de Spark requieren que un motor de Spark procese la instrucción SQL de Spark definitoria y que un motor SQL no las pueden procesar, las bases de datos y las tablas externas y administradas que contiene que usen el formato de almacenamiento de Parquet o CSV son las únicas que se comparten con el motor SQL del área de trabajo. Las vistas de Spark solo se comparten entre las instancias de grupo de Spark.

## <a name="security-model-at-a-glance"></a>Modelo de seguridad de un vistazo

Tanto las bases de datos como las tablas de Spark, junto con sus representaciones sincronizadas en el motor de SQL, están protegidas en el nivel de almacenamiento subyacente. Cuando la tabla se consulta mediante alguno de los motores que el emisor de consultas tiene derecho a usar, la entidad de seguridad del emisor de consultas se pasa a los archivos subyacentes. Los permisos se comprueban en el nivel del sistema de archivos.

Para más información, consulte [Base de datos compartida de Azure Synapse Analytics](database.md).

## <a name="change-maintenance"></a>Cambio del mantenimiento

Si se elimina o cambia un objeto de metadatos con Spark, los cambios se recogen y propagan al grupo de SQL sin servidor. La sincronización es asincrónica y los cambios se reflejan en el motor SQL tras un breve retraso.

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre las bases de datos de metadatos compartidos de Azure Synapse Analytics](database.md)
- [Más información sobre las tablas de metadatos compartidos de Azure Synapse Analytics](table.md)

