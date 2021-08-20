---
title: Novedades en Azure Data Factory
description: En esta página Novedades se resaltan las nuevas características y mejoras de Azure Data Factory.
author: pennyzhou-msft
ms.author: xupzhou
ms.reviewer: xupzhou
ms.service: data-factory
ms.topic: overview
ms.date: 07/14/2021
ms.openlocfilehash: fe4c5fb72ce3cd32e14bad211683e01ac6edf3ab
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342625"
---
# <a name="whats-new-in-azure-data-factory"></a>Novedades en Azure Data Factory

Azure Data Factory se mejora de forma continua. Para mantenerse al día de los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

- Versiones más recientes
- Problemas conocidos
- Corrección de errores
- Funciones obsoletas
- Planes de cambios

Esta página se actualiza mensualmente, por lo que se recomienda visitarla con frecuencia. 

## <a name="june-2021"></a>Junio de 2021
<br>
<table>
<tr><td><b>Categoría del servicio</b></td><td><b>Mejoras en el servicio</b></td><td><b>Detalles</b></td></tr>
<tr><td rowspan=4 valign="middle"><b>Movimiento de datos</b></td><td>Nueva experiencia del usuario con la herramienta Copiar datos de Azure Data Factory</td><td>La herramienta Copiar datos rediseñada ya está disponible con una experiencia de ingesta de datos mejorada.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/a-re-designed-copy-data-tool-experience/ba-p/2380634">Más información</a></td></tr>
<tr><td>MongoDB y MongoDB Atlas se admiten como origen y receptor</td><td>Esta mejora admite la copia de datos entre cualquier almacén de datos compatible y la base de datos de MongoDB o MongoDB Atlas.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-connectors-available-in-adf-mongodb-and-mongodb-atlas-are/ba-p/2441482">Más información</a></td></tr>
<tr><td>Always Encrypted es compatible con los conectores de Azure SQL Database, Azure SQL Managed Instance y SQL Server como origen y receptor</td><td>Always Encrypted está disponible en Azure Data Factory para los conectores de Azure SQL Database, Azure SQL Managed Instance y SQL Server para la actividad de copia.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/azure-data-factory-copy-now-supports-always-encrypted-for-both/ba-p/2461346">Más información</a></td></tr>
<tr><td>La configuración de metadatos personalizados se admite en la actividad de copia si la recepción tiene lugar en ADLS Gen2 o Azure Blob</td><td>Cuando se escribe en ADLS Gen2 o Azure Blob, la actividad de copia admite la configuración de metadatos personalizados o el almacenamiento de la información de fecha de la última modificación del archivo de origen como metadatos.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/support-setting-custom-metadata-when-writing-to-blob-adls-gen2/ba-p/2545506#M490">Más información</a></td></tr>
<tr><td rowspan=4 valign="middle"><b>Flujo de datos</b></td><td>SQL Server se admite ahora como origen y receptor en los flujos de datos</td><td>SQL Server se admite ahora como origen y receptor en los flujos de datos. Siga el vínculo para obtener instrucciones sobre cómo configurar las redes mediante la característica de red virtual administrada de Azure Integration Runtime para hablar con las instancias de SQL Server locales y basadas en máquinas virtuales en la nube.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-data-flow-connector-sql-server-as-source-and-sink/ba-p/2406213">Más información</a></td></tr>
<tr><td>La reutilización rápida del clúster de flujo de datos ahora está habilitada de forma predeterminada para todas las nuevas instancias de Azure Integration Runtime.</td><td>ADF se complace en anunciar la disponibilidad general de la popular característica de reutilización de inicio rápido de flujo de datos. Ahora, todas las nuevas instancias de Azure Integration Runtime dispondrán de la reutilización rápida habilitada de manera predeterminada.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/how-to-startup-your-data-flows-execution-in-less-than-5-seconds/ba-p/2267365">Más información</a></td></tr>
<tr><td>Actividad de Power Query en la versión preliminar pública de Azure Data Factory</td><td>Ahora puede crear asignaciones de campos complejas en el receptor de Power Query mediante la limpieza y transformación de datos de Azure Data Factory. El receptor ahora está configurado en la canalización de la actividad de Power Query (versión preliminar) para dar cabida a esta actualización.<br><a href="wrangling-tutorial.md">Más información</a></td></tr>
<tr><td>Se ha actualizado la interfaz de usuario de supervisión de flujos de datos en Azure Data Factory.</td><td>Azure Data Factory tiene una nueva actualización de la interfaz de usuario de supervisión para facilitar la visualización de las ejecuciones de trabajos de ETL del flujo de datos e identificar rápidamente áreas para la optimización del rendimiento.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/updated-data-flows-monitoring-ui-in-adf-amp-synapse/ba-p/2432199">Más información</a></td></tr>
<tr><td><b>SQL Server Integration Services (SSIS)</b></td><td>Ejecute cualquier instancia de SQL en 3 pasos sencillos con SSIS en Azure Data Factory</td><td>En esta publicación se proporcionan tres pasos sencillos para ejecutar cualquier instancia de SQL o script en cualquier lugar con SSIS en Azure Data Factory.<ol><li>Prepare el entorno de ejecución de integración autohospedado o la instancia de SSIS Integration Runtime.</li><li>Prepare una actividad de ejecución de paquetes SSIS en la canalización de Azure Data Factory.</li><li>Ejecute la actividad de ejecución de paquetes SSIS en el entorno de ejecución de integración autohospedado o en la instancia de SSIS Integration Runtime.</li></ol><a href="https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244">Más información</a></td></tr>
</table>

## <a name="more-information"></a>Información adicional

- [Blog: Azure Data Factory](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
- [Foro Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter](https://twitter.com/AzDataFactory?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)
- [Vídeos](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/featured)





