---
title: Novedades de Azure Data Factory
description: En esta página se resaltan las nuevas características y mejoras de Azure Data Factory. Azure Data Factory es un servicio en la nube administrado creado para complejos proyectos híbridos de extracción, transformación y carga (ETL), extracción, carga y transformación (ELT) e integración de datos.
author: pennyzhou-msft
ms.author: xupzhou
ms.service: data-factory
ms.subservice: concepts
ms.topic: overview
ms.date: 07/14/2021
ms.openlocfilehash: 7509be190afd0354f730f72dd97c5b5a118f20dd
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187350"
---
# <a name="whats-new-in-azure-data-factory"></a>Novedades de Azure Data Factory

El servicio Azure Data Factory se mejora de forma continua. Para mantenerse al día de los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

- Versiones más recientes
- Problemas conocidos
- Corrección de errores
- Funciones obsoletas
- Planes de cambios

Esta página se actualiza mensualmente, por lo que se recomienda visitarla con frecuencia. 

## <a name="august-2021"></a>Agosto de 2021
<br>
<table>
<tr><td><b>Categoría del servicio</b></td><td><b>Mejoras en el servicio</b></td><td><b>Detalles</b></td></tr>
  <tr><td><b>Integración continua y entrega continua (CI/CD)</b></td><td>Mejoras de CI/CD con compatibilidad de GitHub en Azure Government y Azure China</td><td>Se ha agregado compatibilidad con GitHub en Azure Government y Azure China.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/cicd-improvements-with-github-support-in-azure-government-and/ba-p/2686918">Más información</a></td></tr>
<tr><td rowspan=2><b>Movimiento de datos</b></td><td>La API de Azure Cosmos DB de para el conector MongoDB es ahora compatible con las versiones 3.6 y 4.0 de Azure Data Factory.</td><td>La API de Cosmos DB de Azure Data Factory para el conector MongoDB es ahora compatible con las versiones del servidor 3.6 y 4.0.<br><a href="connector-azure-cosmos-db-mongodb-api.md">Más información</a></td></tr>
<tr><td>Mejore el uso de la instrucción COPY para cargar datos en Azure Synapse Analytics.</td><td>El conector de Azure Synapse Analytics de Azure Data Factory ahora admite la copia por fases y el origen de copia con *.* como wildcardFilename para la instrucción COPY.<br><a href="connector-azure-sql-data-warehouse.md#use-copy-statement">Más información</a></td></tr>
<tr><td><b>Flujo de datos</b></td><td>Los puntos de conexión REST están disponibles como origen y receptor en Data Flow.</td><td>Los flujos de datos de Azure Data Factory y Azure Synapse Analytics ahora admiten puntos de conexión REST como origen y receptor con compatibilidad total con cargas útiles JSON y XML.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/rest-source-and-sink-now-available-for-data-flows/ba-p/2596484">Más información</a></td></tr>
<tr><td><b>Integration Runtime</b></td><td>La herramienta de diagnóstico está disponible para el entorno de ejecución de integración autohospedado.</td><td>Una herramienta de diagnóstico para el entorno de ejecución de integración auto-hospedado está diseñada para proporcionar una mejor experiencia de usuario y ayudar a los usuarios a encontrar posibles problemas. La herramienta ejecuta una serie de escenarios de prueba en la máquina del entorno de ejecución de integración autohospedado y cada escenario tiene casos de comprobación de estado típicos para problemas comunes.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/diagnostic-tool-for-self-hosted-integration-runtime/ba-p/2634905">Más información</a></td></tr>
<tr><td><b>Orquestación</b></td><td>El desencadenador de eventos personalizado con la opción de filtrado avanzado está disponible de forma general.</td><td>Ahora puede crear un desencadenador que responda a un tema personalizado publicado en Event Grid. Además, puede aprovechar el filtrado avanzado para obtener un control preciso sobre los eventos a los que responder.<br><a href="how-to-create-custom-event-trigger.md">Más información</a></td></tr>
</table>

## <a name="july-2021"></a>Julio de 2021
<br>
<table>
<tr><td><b>Categoría del servicio</b></td><td><b>Mejoras en el servicio</b></td><td><b>Detalles</b></td></tr>
<tr><td><b>Movimiento de datos</b></td><td>Obtención de canalizaciones de ingesta de datos controladas por metadatos en la herramienta Copiar datos de ADF en 10 minutos (versión preliminar pública)</td><td>Con esto, puede compilar canalizaciones de copia de datos a gran escala con un enfoque basado en metadatos en la herramienta de copiar datos (versión preliminar pública) en 10 minutos.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/get-metadata-driven-data-ingestion-pipelines-on-adf-within-10/ba-p/2528219">Más información</a></td></tr>
<tr><td><b>Flujo de datos</b></td><td>Nuevas funciones de asignación agregadas en las funciones de transformación de flujo de datos</td><td>Se ha agregado un nuevo conjunto de funciones de transformación de flujo de datos para permitir que los ingenieros de datos generen, lean y actualicen fácilmente tipos de datos de asignación y estructuras de asignación complejas.<br><a href="data-flow-expression-functions.md#map-functions">Más información</a></td></tr>
<tr><td><b>Integration Runtime</b></td><td>Cinco nuevas regiones disponibles en la VNET administrada de Azure Data Factory (versión preliminar pública)</td><td>Estas cinco nuevas regiones (Este de China 2, Norte de China 2, US Gov Arizona, US Gov Texas, US Gov Virginia) están disponibles en una red virtual administrada de Azure Data Factory (versión preliminar pública).<br><a href="managed-virtual-network-private-endpoint.md#azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions">Más información</a></td></tr>
<tr><td rowspan=2><b>Productividad de los desarrolladores</b></td><td>Página principal de ADF rediseñada con algunas sesiones agregadas</td><td>La página principal de Data Factory se ha rediseñado con mejores funcionalidades de contraste y reflujo. Además, se han introducido algunas secciones en la página principal para ayudarle a mejorar la productividad en el recorrido de integración de datos.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/the-new-and-refreshing-data-factory-home-page/ba-p/2515076">Más información</a></td></tr>
<tr><td>Nueva página de aterrizaje para Azure Data Factory Studio</td><td>Página de aterrizaje de la hoja Data Factory en Azure Portal<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/the-new-and-refreshing-data-factory-home-page/ba-p/2515076">Más información</a></td></tr>
</table>

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
