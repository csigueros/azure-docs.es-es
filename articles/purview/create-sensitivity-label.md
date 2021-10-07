---
title: Etiquetado en Azure Purview
description: Empiece a usar etiquetas de confidencialidad y clasificaciones para mejorar los recursos de Purview.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 7a94d82c3ec2a47869c64520e20bed6cdf58ebdb
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210539"
---
# <a name="labeling-in-azure-purview"></a>Etiquetado en Azure Purview

> [!IMPORTANT]
> Las etiquetas de confidencialidad de Azure Purview se encuentran actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.
>

Para trabajar, las personas de la organización colaboran con otras dentro y fuera de la organización. Los datos no siempre permanecen en la nube y, a menudo, se mueven por todas partes, en dispositivos, aplicaciones y servicios. Cuando los datos usen un perfil itinerante, deseará que estén protegidos de forma que se satisfagan las directivas empresariales y de cumplimiento de la organización.</br>

La aplicación de etiquetas de confidencialidad al contenido le permite proteger los datos de su organización indicando la sensibilidad de algunos de ellos. También se sintetizan los propios datos, por lo que puede usar etiquetas para realizar un seguimiento del tipo de datos, sin exponer datos confidenciales en otra plataforma.</br>

Por ejemplo, la aplicación de una etiqueta de confidencialidad "extremadamente confidencial" a un documento que contiene números de tarjeta de crédito y el número del seguro social ayuda a identificar la confidencialidad del documento sin conocer los datos reales del mismo.

## <a name="benefits-of-labeling-in-azure-purview"></a>Ventajas del etiquetado en Azure Purview

Azure Purview permite aplicar etiquetas de confidencialidad a los recursos, de modo que puede clasificar y proteger los datos.

* **La etiqueta acompaña a los datos:** las etiquetas de confidencialidad usadas en Purview se reconocen actualmente en Microsoft 365, SharePoint, Teams, Power BI y SQL. Si se aplica una etiqueta en un recurso de Purview y los datos viajan a cualquiera de las otras plataformas, como Power BI u Office, la etiqueta también los acompaña. De forma similar, si se aplica una etiqueta en un documento de Word y, a continuación, se examina con Purview, la etiqueta se transmitirá a Purview.
* **Información general sobre el patrimonio de datos:** Purview proporciona información sobre los datos a través de informes predefinidos. Al examinar datos en Purview, los informes se hidratan con información sobre los recursos que tiene, el historial de análisis, las clasificaciones encontradas en los datos, las etiquetas aplicadas, los términos del glosario, etc.
* **Etiquetado automático:** las etiquetas se pueden aplicar automáticamente en función de la confidencialidad de los datos. Cuando se examina un recurso en busca de datos confidenciales, se usan reglas de etiquetado automático para decidir qué etiqueta de confidencialidad se va a aplicar. A fin de crear reglas de etiquetado automático para cada etiqueta de confidencialidad, defina qué tipo de clasificación o información confidencial constituye una etiqueta.
* **Aplicación de etiquetas a columnas de base de datos y archivos:** las etiquetas se pueden aplicar a archivos en almacenamientos como Azure Data Lake, Azure Files, etc., y a datos esquematizados, como columnas de Azure SQL DB, Cosmos DB, etc.

Las etiquetas de confidencialidad son etiquetas que se pueden aplicar en los recursos para clasificar y proteger los datos. Obtenga más información sobre las [etiquetas de confidencialidad aquí](/microsoft-365/compliance/create-sensitivity-labels.md).

## <a name="how-to-apply-labels-to-assets-in-azure-purview"></a>Aplicación de etiquetas a recursos en Azure Purview

:::image type="content" source="media/create-sensitivity-label/apply-label-flow.png" alt-text="Aplicación de etiquetas a los recursos en el flujo de Purview. Crear etiquetas, registrar recursos, examinar recursos, clasificaciones encontradas, etiquetas aplicadas.":::

Para poder aplicar etiquetas al recurso en Azure Purview, es necesario realizar los pasos siguientes:

1. [Cree o extienda las etiquetas de confidencialidad existentes a Azure Purview](how-to-automatically-label-your-content.md) en el Centro de cumplimiento de Microsoft 365. La creación de etiquetas de confidencialidad incluye reglas de etiquetado automático que indican qué etiqueta se debe aplicar en función de las clasificaciones encontradas en los datos.
1. [Registre y examine el recurso](how-to-automatically-label-your-content.md#scan-your-data-to-apply-sensitivity-labels-automatically) en Azure Purview.
1. Azure Purview aplica clasificaciones: al programar un examen en un recurso, Azure Purview examina el tipo de datos del recurso y le aplica clasificaciones en el catálogo de datos. Azure Purview realiza automáticamente la aplicación de clasificaciones, usted no tiene que realizar ninguna acción.
1. Azure Purview aplica etiquetas: una vez que se encuentran las clasificaciones en un recurso, Azure Purview le aplicará etiquetas en función de las reglas de etiquetado automático. Azure Purview realiza automáticamente la aplicación de etiquetas; usted no tiene que realizar ninguna acción siempre que haya creado etiquetas con reglas de etiquetado automático en el paso 1.

> [!NOTE]
> Las reglas de etiquetado automático son condiciones que especifica el usuario, que indica cuándo se debe aplicar una etiqueta determinada. Cuando se cumplan estas condiciones, la etiqueta se asigna automáticamente a los datos. Al crear las etiquetas, asegúrese de definir reglas de etiquetado automático para los archivos y las columnas de bases de datos para aplicar las etiquetas automáticamente con cada examen de datos.
>

## <a name="supported-data-sources"></a>Orígenes de datos admitidos

Las etiquetas de confidencialidad se admiten en Azure Purview para los siguientes orígenes de datos:

|Tipo de datos  |Orígenes  |
|---------|---------|
|Etiquetado automático de archivos     |    : Blob Storage de Azure</br>- Azure Files</br>- Azure Data Lake Storage Gen 1 y Gen 2</br>- Amazon S3|
|Etiquetado automático de columnas de bases de datos     |  - SQL Server</br>- Azure SQL Database</br>- Instancia administrada de Azure SQL Database</br>- Áreas de trabajo de Azure Synapse Analytics</br>- Azure Cosmos Database (SQL API)</br> - Azure Database for MySQL</br> - Azure Database for PostgreSQL</br> - Azure Data Explorer</br>  |
| | |

## <a name="labeling-for-sql-databases"></a>Etiquetado de bases de datos SQL

Además del etiquetado de Purview para las columnas de bases de datos, Microsoft también admite el etiquetado de columnas de bases de datos SQL mediante la clasificación de datos de SQL en [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Aunque Purview usa las [etiquetas de confidencialidad MIP](/microsoft-365/compliance/sensitivity-labels) globales, SSMS solo usa etiquetas definidas localmente.

El etiquetado en Purview y el etiquetado en SSMS son procesos independientes que actualmente no interactúan entre sí. Por lo tanto, **las etiquetas que se aplican en SSMS no se muestran en Purview, y viceversa**. Se recomienda Azure Purview para etiquetar las bases de datos SQL, ya que usa etiquetas MIP globales que se pueden aplicar en varias plataformas.

Para obtener más información, consulte la [documentación de Clasificación y detección de datos de SQL](/sql/relational-databases/security/sql-data-discovery-and-classification). </br></br>

> [!div class="nextstepaction"]
> [Etiquetado automático del contenido](./how-to-automatically-label-your-content.md)

> [!div class="nextstepaction"]
> [Conclusiones de etiquetas de confidencialidad](sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Preguntas más frecuentes sobre el etiquetado](sensitivity-labels-frequently-asked-questions.yml)
