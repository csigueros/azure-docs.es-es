---
title: Conceptos de directivas de acceso de Azure Purview
description: Descripción de las directivas de acceso de Azure Purview
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b4d3d635b3f38f344ca0ae6cf89e16b0e6714073
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090966"
---
# <a name="concepts-for-azure-purview-data-access-policies"></a>Conceptos de las directivas de acceso a datos de Azure Purview

Este artículo le ayuda a comprender las directivas de acceso a datos de Azure Purview que le permiten administrar el acceso a través del patrimonio de datos desde Azure Purview.

> [!Note]
> Esta funcionalidad es diferente del control de acceso de Azure Purview, que se describe en [Control de acceso en Azure Purview](catalog-permissions.md).

> [!IMPORTANT]
> Las directivas de acceso de Azure Purview se encuentran actualmente en versión preliminar. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="overview"></a>Información general

Las directivas de acceso de Azure Purview permiten administrar el acceso a diferentes sistemas de datos en todo el patrimonio de datos. Por ejemplo:

Un usuario necesita acceso de lectura a una cuenta de Azure Storage que se ha registrado en Azure Purview. Puede conceder este acceso directamente en Azure Purview creando una directiva de acceso a datos a través de la aplicación de **administración de directivas** en Purview Studio.

Las directivas de acceso a datos se pueden aplicar a través de Purview en sistemas de datos que se han registrado para la directiva.

## <a name="azure-purview-policy-concepts"></a>Conceptos de directivas de Azure Purview

### <a name="azure-purview-policy"></a>Directiva de Azure Purview

Una **directiva** es una colección con nombre de instrucciones de directiva. Cuando una directiva se publica en uno o varios sistemas de datos bajo la gobernanza de Purview, estos la aplican. Una definición de directiva incluye un nombre de directiva, una descripción y una lista de una o varias instrucciones de directiva.

### <a name="policy-statement"></a>Instrucción de la directiva

Una **instrucción de directiva** es una instrucción legible que determina cómo el origen de datos debe controlar una operación de datos específica. La instrucción de directiva consta de **Efecto**, **Acción, Recurso de datos** y **Asunto**.

#### <a name="action"></a>Acción

Una **acción** es la operación que se permite o se deniega como parte de esta directiva. Por ejemplo: Lectura o Escritura. Estas acciones lógicas de alto nivel se asignan a una (o varias) acciones de datos en el sistema de datos donde se aplican.

#### <a name="effect"></a>Efecto

El **efecto** indica cuál debe ser el efecto resultante de esta directiva. Actualmente, el único valor admitido es **Permitir**.

#### <a name="data-resource"></a>Recurso de datos

El **recurso de datos** es la ruta de acceso completa del recurso de datos a la que se aplica una instrucción de directiva. Cumple con el siguiente formato:

*/subscription/\<subscription-id>/resourcegroups/\<resource-group-name>/providers/\<provider-name>/\<data-asset-path>*

Formato de ruta de acceso del recurso de datos de Azure Storage:

*Microsoft.Storage/storageAccounts/\<account-name>/blobservice/default/containers/\<container-name>*

Formato de ruta de acceso del recurso de datos de Azure SQL DB:

*Microsoft.Sql/servers/\<server-name>*

#### <a name="subject"></a>Asunto

La identidad del usuario final de Azure Active Directory (AAD) para los que es aplicable esta instrucción de directiva. Esta identidad puede ser una entidad de servicio, un usuario individual, un grupo o una identidad de servicio administrada (MSI).

### <a name="example"></a>Ejemplo

Denegar la lectura en el recurso de datos: */subscription/finance/resourcegroups/prod/providers/Microsoft.Storage/storageAccounts/finDataLake/blobservice/default/containers/FinData to group Finance-analyst*

En la instrucción de directiva anterior, el efecto es *Denegar*, la acción es *Lectura*, el recurso de datos es el contenedor de Azure Storage *FinData* y el asunto es el grupo de AAD *Finance-analyst*. Si algún usuario que pertenezca a este grupo intenta leer datos del contenedor de almacenamiento *FinData*, se denegará la solicitud.

### <a name="hierarchical-enforcement-of-policies"></a>Cumplimiento jerárquico de directivas

El recurso de datos especificado en una instrucción de directiva es jerárquico de forma predeterminada. Esto significa que la instrucción de directiva se aplica al propio objeto de datos y a **todos** los objetos secundarios contenidos en el objeto de datos. Por ejemplo, una instrucción de directiva en el contenedor de Azure Storage se aplica a todos los blobs contenidos en él.

### <a name="policy-combining-algorithm"></a>Algoritmo de combinación de directivas 

Azure Purview puede tener diferentes instrucciones de directiva que hacen referencia al mismo recurso de datos. Al evaluar una decisión sobre el acceso a los datos, Azure Purview combina todas las directivas aplicables y proporciona una decisión consolidada. La estrategia de combinación es elegir la directiva más restrictiva.
Por ejemplo, supongamos dos directivas diferentes en un contenedor de Azure Storage *FinData* como se muestra a continuación:

Directiva 1: *Permitir la lectura en el recurso de datos /subscription/..../containers/FinData al grupo Finance-analyst*

Directiva 2: *Denegar la lectura en el recurso de datos /subscription/..../containers/FinData al grupo Finance-contractors*

Después, supongamos que el usuario "user1", que forma parte de dos grupos: *Finance-analyst* y *Finance-contractors*, ejecuta una llamada a la API de lectura de blobs. Puesto que ambas directivas serán aplicables, Azure Purview elegirá la más restrictiva, que es *Denegar* la *lectura*. Por lo tanto, se denegará la solicitud de acceso.

## <a name="policy-publishing"></a>Publicación de directivas

Existe una directiva recién creada en el estado de modo de borrador, solo visible en Azure Purview. El acto de publicación inicia la aplicación de una directiva en los sistemas de datos especificados. Se trata de una acción asincrónica que puede tardar hasta 2 minutos en ser eficaz en los orígenes de datos subyacentes.

Una directiva publicada en un origen de datos podría contener referencias a un recurso que pertenece a un origen de datos diferente. Estas referencias se omitirán, ya que el recurso en cuestión no existe en el origen de datos donde se aplica la directiva.

## <a name="azure-purview-to-data-source-action-mapping"></a>Asignación de acciones de Azure Purview a origen de datos

En la tabla siguiente se muestra cómo las acciones de las directivas de datos de Azure Purview se asignan a acciones específicas en sistemas de datos.

| **Acción de directiva de Purview** | **Acciones específicas del origen de datos**                                                                |
|---------------------------|-------------------------------------------------------------------------------------------------|
|||
| *Lectura*                      |<sub>Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read                        |
|                           |<sub>Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery                      |
|                           |<sub>Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed                    |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                            |
|||
| *Modificar*                    |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                            |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write                           |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action                      |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action                     |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete                          |
|||
| *Supervisión del rendimiento de SQL*   |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerMetadata/rows/select                     |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseMetadata/rows/select         |
|                           |<sub>Microsoft.Sql/sqlservers/Connect                                                                |
|                           |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerState/rows/select                        |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseState/rows/select            |
|                           |<sub>Microsoft.Sql/sqlservers/databases/Connect                                                      |
|||
| *Auditoría de SQL*               |<sub>Microsoft.Sql/sqlservers/databases/Connect                                                      |
|                           |<sub>Microsoft.Sql/sqlservers/Connect                                                                |
|                           |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerSecurityState/rows/select                |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseSecurityState/rows/select    |
|                           |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerSecurityMetadata/rows/select             |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseSecurityMetadata/rows/select |

## <a name="next-steps"></a>Pasos siguientes
Consulte los tutoriales sobre cómo crear directivas en Azure Purview que funcionen en sistemas de datos específicos, como Azure Storage:

* [Aprovisionamiento de conjuntos de datos por propietario de datos para Azure Storage](how-to-access-policies-storage.md)

<!--
[Dataset provisioning by data owner for Azure SQL DB](how-to-access-policies-sql.md)
-->
