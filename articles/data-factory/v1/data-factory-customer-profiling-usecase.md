---
title: Caso de uso - Generación de perfiles de clientes
description: Obtenga información sobre cómo se usa Azure Data Factory para crear un flujo de trabajo orientado a datos (canalización) para generar perfiles de clientes de juegos.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f763c2ca499d68808c70318d2e3651c99b5b09af
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664214"
---
# <a name="use-case---customer-profiling"></a>Caso de uso - Generación de perfiles de clientes
Azure Data Factory es uno de los muchos servicios que se usan para implementar el conjunto de aplicaciones Cortana Intelligence de aceleradores de soluciones.  Para más información sobre Cortana Intelligence, visite [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics)(conjunto de aplicaciones de Cortana Intelligence). En este documento se describe un caso de uso sencillo para que pueda comprender cómo Azure Data Factory puede resolver los problemas comunes de análisis.

## <a name="scenario"></a>Escenario
Contoso es una empresa de juegos que crea juegos para varias plataformas: consolas de juegos, dispositivos portátiles y PC. A medida que los usuarios juegan a estos juegos, se generan grandes volúmenes de datos de registro que realizan el seguimiento de los patrones de uso, estilo de juegos y preferencias del usuario.  Cuando se combinan con datos demográficos, regionales y de productos, Contoso realiza análisis para guiar a los usuarios sobre cómo mejorar su experiencia y orientarles sobre actualizaciones y compras en el juego. 

El objetivo de Contoso es identificar oportunidades de aumento de ventas potenciales y ventas cruzadas basadas en el historial de juegos de sus usuarios y agregar características atractivas para impulsar el crecimiento del negocio y ofrecer una mejor experiencia a los clientes. Para este caso de uso, usamos una empresa de juegos como ejemplo de empresa. La empresa desea optimizar sus juegos según el comportamiento de los jugadores. Estos principios se aplican a cualquier empresa que desee atraer a sus clientes hacia sus productos y servicios y mejorar la experiencia de estos.

En esta solución, Contoso quiere evaluar la eficacia de una campaña de marketing que ha lanzado recientemente. Empezaremos con los registros de juegos sin procesar, los procesaremos y enriqueceremos con los datos de ubicación geográfica, los combinaremos con datos de referencia de anuncios y, por último, los copiaremos en una base de datos de Azure SQL para analizar el impacto de la campaña.

## <a name="deploy-solution"></a>Implementación de la solución
Todo lo que necesita para acceder a este caso de uso sencillo y probarlo es una [suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/), una [cuenta de Azure Blob Storage](../../storage/common/storage-account-create.md) y [Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md). Implementará la canalización de generación de perfiles de cliente desde el icono de **Canales de muestras** de la página principal de la factoría de datos.

1. Cree una factoría de datos o abra una. Vea [Copia de datos de Blob Storage a SQL Database con Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener los pasos para crear una factoría de datos.
2. En la hoja **FACTORÍA DE DATOS** de la factoría de datos, haga clic en el icono **Canalizaciones de ejemplo**.

    :::image type="content" source="./media/data-factory-samples/SamplePipelinesTile.png" alt-text="Icono Canalizaciones de ejemplo":::
3. En la hoja **Canales de muestras**, haga clic en el **Generación de perfiles de cliente** que quiere implementar.

    :::image type="content" source="./media/data-factory-samples/SampleTile.png" alt-text="Hoja Canalizaciones de ejemplo":::
4. Especifique las opciones de configuración para el ejemplo. Por ejemplo, el nombre de la cuenta de Azure Storage y la clave, el nombre del servidor de SQL lógico, la base de datos, el identificador de usuario y la contraseña.

    :::image type="content" source="./media/data-factory-samples/SampleBlade.png" alt-text="Hoja de ejemplo":::
5. Cuando haya terminado de especificar las opciones de configuración, haga clic en **Crear** para crear o implementar las canalizaciones de ejemplo y los servicios o las tablas vinculados que usan las canalizaciones.
6. Verá el estado de implementación en el icono de ejemplo en el que hizo clic anteriormente en la hoja **Canalizaciones de ejemplo canalizaciones**.

    :::image type="content" source="./media/data-factory-samples/DeploymentStatus.png" alt-text="Estado de la implementación":::
7. Cuando vea el mensaje **La implementación se realizó correctamente** en el icono del ejemplo, cierre la hoja **Canalizaciones de ejemplo**.  
8. En la hoja **FACTORÍA DE DATOS** , verá que los servicios vinculados, los conjuntos de datos y las canalizaciones se han agregado a la factoría de datos.  

    :::image type="content" source="./media/data-factory-samples/DataFactoryBladeAfter.png" alt-text="Hoja de la Factoría de datos":::

## <a name="solution-overview"></a>Información general de la solución
Este caso de uso sencillo puede usarse como un ejemplo de cómo puede usar Azure Data Factory para recopilar, preparar, transformar, analizar y publicar datos.

:::image type="content" source="./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png" alt-text="Flujo de trabajo de un extremo a otro":::

Esta ilustración describe cómo las canalizaciones de datos aparecen en Azure Portal una vez implementadas.

1. **PartitionGameLogsPipeline** lee los eventos de juegos sin procesar de un almacenamiento de blobs y crea particiones basadas en el año, el mes y el día.
2. **EnrichGameLogsPipeline** combina eventos de juegos con particiones con datos de referencia de código geográfico y enriquece los datos mediante la asignación de direcciones IP a las ubicaciones geográficas correspondientes.
3. La canalización **AnalyzeMarketingCampaignPipeline** utiliza los datos enriquecidos y los procesa con los datos de publicidad para crear el resultado final que contiene la eficacia de la campaña de marketing.

En este ejemplo, Data Factory se utiliza para coordinar las actividades que copian los datos de entrada, transforman y procesan los datos y envían los datos finales a una base de datos de Azure SQL .  También puede visualizar la red de canalizaciones de datos, administrarla y supervisar su estado desde la interfaz de usuario.

## <a name="benefits"></a>Ventajas
Al optimizar el análisis de su perfil de usuario y armonizarlo con los objetivos empresariales, la empresa de juegos puede recopilar rápidamente los patrones de uso y analizar la eficacia de sus campañas de marketing.