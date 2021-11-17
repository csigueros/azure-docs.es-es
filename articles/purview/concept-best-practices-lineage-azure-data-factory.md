---
title: Procedimientos recomendados de linaje de datos de Azure Purview
description: En este artículo se proporcionan procedimientos recomendados para el linaje de datos de varios orígenes de datos en Azure Purview.
author: amberz
ms.author: amberz
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/25/2021
ms.openlocfilehash: 0036004e8222ce79fc43e5d6603abecc2e375b1a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478157"
---
# <a name="azure-purview-data-lineage-best-practices"></a>Procedimientos recomendados de linaje de datos de Azure Purview

El linaje de los datos se entiende, en términos generales, como el ciclo de vida que abarca el origen de los datos y su desplazamiento en el tiempo a través del patrimonio de datos. Purview puede capturar el linaje de los datos en diferentes partes del patrimonio de datos de su organización y en diferentes niveles de preparación, que incluyen lo siguiente: 
* Datos completamente sin procesar de varias plataformas almacenados provisionalmente. 
* Datos transformados y preparados. 
* Datos que usan las plataformas de visualización.

 
## <a name="intended-audience"></a>Destinatarios

* Ingeniero de datos 
* Científico de datos 
* Propietario de datos 

## <a name="why-do-you-need-adopt-lineage"></a>¿Por qué necesita adoptar el linaje?  

El linaje de datos es el proceso de describir qué datos existen, dónde se almacenan y cómo fluyen entre sistemas. Hay muchas razones por las que el linaje de datos es importante, pero, a nivel general, todas ellas se pueden reducir a tres categorías que exploraremos aquí: 
* Seguimiento de datos en informes 
* Análisis de impacto 
* Captura de los cambios y dónde han residido los datos a lo largo de su ciclo de vida 

## <a name="azure-data-factory-lineage-best-practice-and-considerations"></a>Procedimientos recomendados y consideraciones sobe el linaje de Azure Data Factory 

### <a name="azure-data-factory-instance"></a>Instancia de Azure Data Factory 

* El linaje de datos no se notifica automáticamente al catálogo hasta que el estado de conexión de Data Factory es Conectado. El resto de los estados, Desconectado y CannotAccess, no pueden capturar linaje. 

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Captura de pantalla que muestra una lista de conexiones de Data Factory." lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

* Cada instancia de Data Factory solo puede conectarse a una cuenta de Purview. Puede establecer una nueva conexión en otra cuenta de Purview, pero la conexión existente pasará a estar desconectada.  

    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Captura de pantalla que muestra la advertencia de desconexión de Azure Data Factory.":::

* La identidad administrada de Data Factory se usa para autenticar el linaje en la cuenta de Purview; se requiere el rol de conservador de datos de identidad administrada de la factoría de datos en la colección raíz de Purview. 
* No se admiten más de 10 factorías de datos a la vez. Si quiere agregar más de 10 factorías de datos a la vez, rellene una incidencia de soporte técnico. 

### <a name="azure-data-factory-activities"></a>Actividades de Azure Data Factory  

* Azure Purview captura el linaje en tiempo de ejecución de las siguientes actividades de Azure Data Factory: 
    * [Actividad de copia](../data-factory/copy-activity-overview.md)
    * [Actividad Data Flow](../data-factory/concepts-data-flow-overview.md)
    * [Ejecutar una actividad de paquete SSIS](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

* Azure Purview anula el linaje si el origen o el destino usan un sistema de almacenamiento de datos no admitido.  
    * Los orígenes de datos admitidos en la actividad de copia se enumeran en **Compatibilidad de la actividad de copia** de [Cómo conectar Azure Data Factory](how-to-link-azure-data-factory.md).
    * Los orígenes de datos admitidos en la actividad de flujo de datos se enumeran en **Compatibilidad de Data Flow** de [Cómo conectar Azure Data Factory](how-to-link-azure-data-factory.md).
    * Los orígenes de datos admitidos en SSIS se enumeran en **Compatibilidad de ejecución de paquetes SSIS** de [Linaje de SQL Server Integration Services](how-to-lineage-sql-server-integration-services.md).

* Purview no puede capturar linaje si la actividad de copia de Azure Data Factory usa características de la actividad de copia enumeradas en **Limitaciones en el linaje de la actividad de copia** de [Cómo conectar Azure Data Factory](how-to-link-azure-data-factory.md).  

* Para el linaje de la actividad de flujo de datos, solo se admiten el origen y el receptor de Purview. Todavía no se admite el linaje de transformación del flujo de datos. 

* El linaje del flujo de datos no se integra con el conjunto de recursos de Purview. 

    **Ejemplo 1 del conjunto de recursos**    

    Nombre completo: https://myblob.blob.core.windows.net/sample-data/data{N}.csv 

    Nombre para mostrar: "data" 

* En cuanto al linaje de la actividad de ejecución de paquetes SSIS, solo se admiten el origen y el destino. Todavía no se admite el linaje de transformación. 

    :::image type="content" source="./media/concept-best-practices-lineage/ssis-lineage.png" alt-text="Captura de pantalla del linaje de ejecución de SSIS en Purview." lightbox="./media/concept-best-practices-lineage/ssis-lineage.png":::

* Consulte la siguiente guía paso a paso para [insertar linaje de Azure Data Factory en Purview](../data-factory/tutorial-push-lineage-to-purview.md).  

## <a name="next-steps"></a>Pasos siguientes
-  [Administración de orígenes de datos](./manage-data-sources.md)
