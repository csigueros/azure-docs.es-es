---
title: Galería de orígenes de datos
titleSuffix: Azure Cognitive Search
description: Ofrece una lista con todos los orígenes de datos admitidos para la importación en un índice Azure Cognitive Search.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
layout: LandingPage
ms.date: 06/23/2021
ms.openlocfilehash: e48c464ffa1aa5077f04ea1a7580e0d15e0fd7ca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732602"
---
# <a name="data-sources-gallery"></a>Galería de orígenes de datos

Permite buscar un conector de datos de Microsoft o un asociado para simplificar la ingesta de datos en un índice de búsqueda. Este artículo tiene las secciones siguientes:

+ [Orígenes de datos generalmente disponibles en Cognitive Search](#ga)
+ [Versión preliminar de orígenes de datos en Cognitive Search](#preview)
+ [Conectores de Power Query (versión preliminar)](#powerquery)
+ [Orígenes de datos de nuestros asociados](#partners)

<a name="ga"></a>

## <a name="generally-available-data-sources-by-cognitive-search"></a>Orígenes de datos generalmente disponibles en Cognitive Search

Acceda a contenido de otros servicios de Azure mediante indizadores y los siguientes conectores de origen de datos. 

:::row:::
:::column span="":::

---

### <a name="azure-blob-storage"></a>Azure Blob Storage

mediante [Cognitive Search](search-what-is-azure-search.md)

Extraiga metadatos y contenido de blobs serializados en documentos JSON e importados en un índice de búsqueda como documentos de búsqueda. Establezca propiedades tanto en las definiciones de origen de datos como en el indizador para optimizar distintos tipos de contenido de blob. La detección de cambios se admite automáticamente.

[Más detalles](search-howto-indexing-azure-blob-storage.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (API de SQL)

mediante [Cognitive Search](search-what-is-azure-search.md)

Conecte con Cosmos BD a través de la API de SQL para extraer elementos de un contenedor serializados en documentos JSON e importados en un índice de búsqueda como documentos de búsqueda. Configure el seguimiento de cambios para actualizar el índice de búsqueda con los cambios más recientes de la base de datos.

[Más detalles](search-howto-index-cosmosdb.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-sql-database"></a>Azure SQL Database

mediante [Cognitive Search](search-what-is-azure-search.md)

Extraiga valores de campo de una sola tabla o vista serializados en documentos JSON e importados en un índice de búsqueda como documentos de búsqueda. Configure el seguimiento de cambios para actualizar el índice de búsqueda con los cambios más recientes de la base de datos.

[Más detalles](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

:::image type="icon" source="media/search-data-sources-gallery/azuresqlconnectorlogo_medium.png":::

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-table-storage"></a>Azure Table Storage

mediante [Cognitive Search](search-what-is-azure-search.md)

Extraiga filas de una tabla de Azure serializadas en documentos JSON e importadas en un índice de búsqueda como documentos de búsqueda. 

[Más detalles](search-howto-indexing-azure-tables.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

mediante [Cognitive Search](search-what-is-azure-search.md)

Conecte con Azure Storage a través de Azure Data Laker Storage Gen2 para extraer contenido de una jerarquía de directorios y subdirectorios anidados.

[Más detalles](search-howto-index-azure-data-lake-storage.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::row-end::': null
':::row::': null
':::column span=""::':
  ':::column-end::': null
  ':::column span=""::': null
---

<a name="preview"></a>

## <a name="preview-data-sources-by-cognitive-search"></a>Versión preliminar de orígenes de datos mediante Cognitive Search

Los nuevos orígenes de datos se emiten como características en vista previa. [Inicie sesión](https://aka.ms/azure-cognitive-search/indexer-preview) para empezar.

:::row:::
:::column span="":::

---

### <a name="cosmos-db-gremlin-api"></a>Cosmos DB (API de Gremlin)

mediante [Cognitive Search](search-what-is-azure-search.md)

Conecte con Cosmos BD a través de la API de Gremlin para extraer elementos de un contenedor serializados en documentos JSON e importados en un índice de búsqueda como documentos de búsqueda. Configure el seguimiento de cambios para actualizar el índice de búsqueda con los cambios más recientes de la base de datos.

[Más detalles](search-howto-index-cosmosdb-gremlin.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::column span="":::

---

### <a name="cosmos-db-mongo-api"></a>Cosmos DB (API de Mongo)

mediante [Cognitive Search](search-what-is-azure-search.md)

Conecte con Cosmos BD a través de la API de Mongo para extraer elementos de un contenedor serializados en documentos JSON e importados en un índice de búsqueda como documentos de búsqueda. Configure el seguimiento de cambios para actualizar el índice de búsqueda con los cambios más recientes de la base de datos.

[Más detalles](search-howto-index-cosmosdb.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

mediante [Cognitive Search](search-what-is-azure-search.md)

Conecte con un sitio de SharePoint Online e indexe documentos de una o varias bibliotecas de documentos para cuentas y servicios de búsqueda en el mismo inquilino. El texto y las imágenes normalizadas se extraen de manera predeterminada. Opcionalmente, puede configurar un conjunto de aptitudes para más transformación y enriquecimiento de contenido, o bien, configurar el seguimiento de cambios para actualizar un índice de búsqueda con contenido nuevo o modificado en SharePoint.

[Más detalles](search-howto-index-sharepoint-online.md)

:::image type="icon" source="media/search-data-sources-gallery/sharepoint_online_logo.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-mysql"></a>Azure MySQL

mediante [Cognitive Search](search-what-is-azure-search.md)

Conecte a la base de datos MySQL en Azure para extraer filas de una tabla, serializadas en documentos JSON e importadas en un índice de búsqueda como documentos de búsqueda. En posteriores ejecuciones, el indexador realizará todos los cambios, cargas y eliminaciones de la base de datos MySQL y reflejará estos cambios en el índice de búsqueda.

[Más detalles](search-howto-index-mysql.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_mysql.png":::

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::row-end::': null
':::row::': null
':::column span=""::':
  ':::column-end::': null
  ':::column span=""::': null
---

<a name="powerquery"></a>

## <a name="power-query-connectors-preview"></a>Conectores de Power Query (versión preliminar)

Conecte con datos en otras plataformas en la nube mediante indizadores y un conector Power Query como origen de datos. [Inicie sesión](https://aka.ms/azure-cognitive-search/indexer-preview) para empezar.

:::row:::
:::column span="":::

---

### <a name="amazon-redshift"></a>Amazon Redshift

Con tecnología de [Power Query](/power-query/power-query-what-is-power-query)

Conecte con [Amazon Redshift](https://aws.amazon.com/redshift/) y extraiga contenido que permite búsquedas para indexación en Cognitive Search.

[Más detalles](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="elasticsearch"></a>Elasticsearch

Con tecnología de [Power Query](/power-query/power-query-what-is-power-query)

Conecte con [Elasticsearch](https://www.elastic.co/elasticsearch) en la nube y extraiga contenido que permite búsquedas para indexación en Cognitive Search.

[Más detalles](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="postgresql"></a>PostgreSQL

Con tecnología de [Power Query](/power-query/power-query-what-is-power-query)

Conecte con una base de datos [PostgreSQL](https://www.postgresql.org/) en la nube y extraiga contenido que permite búsquedas para indexación en Cognitive Search.

[Más detalles](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="salesforce-objects"></a>Objetos de Salesforce

Con tecnología de [Power Query](/power-query/power-query-what-is-power-query)

Conecte con Salesforce Objects y extraiga contenido que permite búsquedas para indexación en Cognitive Search.

[Más detalles](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="salesforce-reports"></a>Informes de Salesforce

Con tecnología de [Power Query](/power-query/power-query-what-is-power-query)

Conecte con Salesforce Reports y extraiga contenido que permite búsquedas para indexación en Cognitive Search.

[Más detalles](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="smartsheet"></a>Smartsheet

Con tecnología de [Power Query](/power-query/power-query-what-is-power-query)

Conecte con Smartsheet y extraiga contenido que permite búsquedas para indexación en Cognitive Search.

[Más detalles](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="snowflake"></a>Snowflake

Con tecnología de [Power Query](/power-query/power-query-what-is-power-query)

Extraiga datos y metadatos que permiten búsquedas de una base de datos de Snowflake y rellene un índice basado en asignaciones campo a campo entre el índice y el origen de datos. 

[Más detalles](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::column span=""::': null
---

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

<a name="partners"></a>

## <a name="data-sources-from-our-partners"></a>Orígenes de datos de nuestros asociados

Los conectores de origen de datos también los proporcionan terceros asociados de Microsoft. Consulte nuestros [Términos de uso](search-data-sources-terms-of-use.md) y compruebe las instrucciones de licencia y uso de los socios antes de utilizar una fuente de datos.

:::row:::
:::column span="":::

---

### <a name="aderant"></a>Aderant

mediante [BA Insight](https://www.bainsight.com/)

El conector Aderant respeta la seguridad del sistema de origen y proporciona rastreos completos e incrementales, por lo que los usuarios tienen la información más reciente disponible en todo momento.

[Más detalles](https://www.bainsight.com/connectors/aderant-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

mediante [Accenture](https://www.accenture.com)

Permite a la empresa rastrear contenido desde el servidor de Adobe Experience Manager, lo que proporciona límites de conexión y valores esperados o filtrado de patrones.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/AEM+Connector)

:::column-end:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

mediante [BA Insight](https://www.bainsight.com/)

El conector Adobe Experience Manager permite indexar contenido administrado por la plataforma Adobe Experience Manager (AEM) y admite el rastreo completo e incremental para garantizar que el índice esté actualizado.

[Más detalles](https://www.bainsight.com/connectors/adobe-em-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Adobe Active Experience Manager (AEM) y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de páginas, datos adjuntos y otros tipos de documentos generados en Adobe AEM casi en tiempo real. El conector es totalmente compatible con el modelo de permisos de Adobe AEM, su administración integrada de grupos y usuarios y las instalaciones de AEM basadas en Active Directory u otros servicios de directorio.

[Más detalles](https://www.raytion.com/connectors/adobe-experience-manager-aem)

:::column-end:::
:::column span="":::

---

### <a name="alfresco"></a>Alfresco

mediante [BA Insight](https://www.bainsight.com/)

El conector Alfresco está basado en el marco del conector BAI, que es la plataforma que se usa para crear todos nuestros conectores y proporciona conectividad segura con los sistemas empresariales.

[Más detalles](https://www.bainsight.com/connectors/alfresco-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="alfresco"></a>Alfresco

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Alfresco One y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de archivos, carpetas y perfiles de usuario de Alfresco One casi en tiempo real. El conector es totalmente compatible con el modelo de permisos de Alfresco One, su administración integrada de grupos y usuarios y las instalaciones de Alfresco One basadas en Active Directory y otros servicios de directorio.

[Más detalles](https://www.raytion.com/connectors/raytion-alfresco-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="amazon-aurora"></a>Amazon Aurora

mediante [BA Insight](https://www.bainsight.com/)

El conector Amazon Aurora está basado en métodos de acceso a bases de datos estándar del sector, por lo que admite igualmente bases de datos de otros sistemas, como Oracle, MySQL e IBM DB2.

[Más detalles](https://www.bainsight.com/connectors/amazon-aurora-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="amazon-rds"></a>Amazon RDS

mediante [BA Insight](https://www.bainsight.com/)

El conector Amazon RDS está basado en métodos de acceso a bases de datos estándar del sector, por lo que puede admitir igualmente bases de datos de otros sistemas, como Oracle, MySQL e IBM DB2.

[Más detalles](https://www.bainsight.com/connectors/amazon-rds-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="amazon-s3"></a>Amazon S3

mediante [BA Insight](https://www.bainsight.com/)

El conector Amazon S3 funciona con todo el contenido almacenado en S3. Su organización puede usar el conector para conectarse de forma segura a S3 e indexar el contenido desde cubos S3. Las potentes capacidades de filtrado permiten que la organización controle qué contenido encontrado en S3 se debe indexar.

[Más detalles](https://www.bainsight.com/connectors/amazon-s3-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="aspider"></a>Aspider

mediante [Accenture](https://www.accenture.com)

El conector Aspider permite rastrear contenido desde sitios web mediante autenticación HTTP, rastreos incrementales, limitación de conexiones y rastreo distribuido y HTTPS, entre otras funciones.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Aspider+Web+Crawler)

:::column-end:::
:::column span="":::

---

### <a name="atlassian-confluence-cloud"></a>Atlassian Confluence (Cloud)

mediante [BA Insight](https://www.bainsight.com/)

Nuestro conector Confluence (versión Cloud) es un conector de indexación de nivel empresarial que permite rastrear e indexar el contenido almacenado en Confluence.

[Más detalles](https://www.bainsight.com/connectors/connector-for-confluence-cloud-version/)

:::column-end:::
:::column span="":::

---

### <a name="azure-ad"></a>Azure AD

mediante [BA Insight](https://www.bainsight.com)

El conector Azure Active Directory de BA Insight hace posible que el contenido de su tenencia de Azure Active Directory aparezca en un único índice de búsqueda consolidado, junto con el contenido de otros repositorios, haciendo que las búsquedas, como la búsqueda de empleados o el localizador de expertos sean una realidad.

[Más detalles](https://www.bainsight.com/connectors/azure-active-directory-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-ad"></a>Azure AD

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Microsoft Azure Active Directory (Azure AD) y hacer búsquedas inteligentes con Azure Cognitive Search. Indexa objetos de Azure AD a través de la API de Microsoft Graph. El conector se puede usar para ingerir entidades de seguridad en Cognitive Search casi en tiempo real para implementar casos de uso como la búsqueda de expertos, la búsqueda de equipos y la búsqueda de ubicaciones, o para proporcionar un recorte de seguridad de enlace temprano junto con orígenes de datos personalizados. El conector admite la autenticación federada en Microsoft 365.

[Más detalles](https://www.raytion.com/connectors/raytion-azure-ad-connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-blobs"></a>Blobs de Azure

mediante [Accenture](https://www.accenture.com)

Proporciona la capacidad de rastrear contenido desde un contenedor de blobs de Azure, lo que permite el rastreo incremental, la seguridad de nivel de documento y el acceso a carpetas y subcarpetas.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Blob+Storage+Connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-data-lake"></a>Azure Data Lake

mediante [Accenture](https://www.accenture.com)

El conector Azure Data Lake rastrea el contenido de la nube de Azure Data Lake Store en rutas de acceso raíz o especificadas, con rastreo incremental, recuperando objetos ACL, autenticación de OAuth 2 y mucho más.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Data+Lake+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-events-hub"></a>Azure Events Hub

mediante [Accenture](https://www.accenture.com)

Rastrea el contenido desde Azure Events Hub, lo que permite el rastreo incremental y la recuperación de cualquier tipo de evento o atributo.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Events+Hub+Connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-sql-database"></a>Azure SQL Database

mediante [BA Insight](https://www.bainsight.com/)

El conector Azure SQL Database de BA Insight respeta la seguridad de la base de datos de origen y proporciona rastreos completos e incrementales para que los usuarios tengan la información más reciente disponible en todo momento.

[Más detalles](https://www.bainsight.com/connectors/azure-sql-database-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="bentley"></a>Bentley

mediante [BA Insight](https://www.bainsight.com/)

El conector BAI Bentley AssetWise permite que el contenido de AssetWise aparezca en un único índice de búsqueda consolidado, junto con el contenido de otros repositorios.

[Más detalles](https://www.bainsight.com/connectors/bentley-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="box"></a>Cuadro

mediante [Accenture](https://www.accenture.com)

El conector Box rastrea el contenido de un repositorio de Box. El conector recupera los elementos compatibles mediante la API de RESTful y proporciona rastreo completo o incremental, extracción de metadatos, recuperación de ACL, etc.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Box++Connector)

:::column-end:::
:::column span="":::

---

### <a name="box"></a>Cuadro

mediante [BA Insight](https://www.bainsight.com/)

El conector Box permite que el contenido de Box aparezca en SharePoint y otros portales, lo que permite a los usuarios obtener resultados de búsqueda integrados de SharePoint y Box.

[Más detalles](https://www.bainsight.com/connectors/box-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="box"></a>Cuadro

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Box y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de archivos, carpetas, comentarios, usuarios, grupos y tareas de Box casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de Box.

[Más detalles](https://www.raytion.com/connectors/raytion-box-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

mediante [Accenture](https://www.accenture.com)

El conector Confluence rastrea el contenido de cualquier repositorio de contenidos de Confluence. El conector recupera espacios, páginas, blogs, datos adjuntos y comentarios a través de una API de REST, lo que permite el rastreo incremental, la recuperación de ACL, la compatibilidad con HTTP y HTTPS, etc.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Atlassian+Confluence+Connector)

:::column-end:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

mediante [BA Insight](https://www.bainsight.com/)

El conector Confluence es un conector de indexación de nivel empresarial que permite rastrear e indexar el contenido almacenado en Confluence. Esto permite que SharePoint, o cualquier otro portal, actúe como punto único desde el que los usuarios pueden buscar y recuperar el contenido que necesitan de varios orígenes de contenido.

[Más detalles](https://www.bainsight.com/connectors/confluence-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Atlassian Confluence y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de páginas, entradas de blog, datos adjuntos, comentarios, espacios, perfiles y sitios centrales a partir de etiquetas de las instancias locales de Confluence casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de Atlassian Confluence, así como con instalaciones de Confluence basadas en Active Directory y otros servicios de directorio.

[Más detalles](https://www.raytion.com/connectors/raytion-confluence-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="confluence-cloud"></a>Confluence Cloud

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Atlassian Confluence Cloud y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de páginas, entradas de blog, datos adjuntos, comentarios, espacios, perfiles y sitios centrales a partir de etiquetas de instancias de Confluence Cloud casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de Atlassian Confluence Cloud.

[Más detalles](https://www.raytion.com/connectors/raytion-confluence-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="cuadrastar"></a>CuadraSTAR

mediante [BA Insight](https://www.bainsight.com/)

El conector CuadraSTAR rastrea el contenido de CuadraSTAR y crea un único índice que permite usar Azure Cognitive Search para buscar información relevante en CuadraSTAR y en más de 70 repositorios compatibles, lo que elimina la necesidad de hacer búsquedas independientes.

[Más detalles](https://www.bainsight.com/connectors/cuadrastar-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="database"></a>Base de datos

mediante [Accenture](https://www.accenture.com)

El conector Database Server rastrea el contenido de un servidor de base de datos relacional, examina todas las bases de datos del servidor y extrae información de filas y tablas.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Database+Server+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="deltek"></a>Deltek

mediante [BA Insight](https://www.bainsight.com/)

El conector Deltek Vision respeta la seguridad del sistema de origen y proporciona rastreos completos e incrementales, por lo que los usuarios siempre tienen disponible la información más reciente. Indexa contenido de Deltek Vision en Azure, SharePoint Online o SharePoint 2016/2013 y lo muestra a través de SmartHub de BA Insight para proporcionar a los usuarios resultados de búsqueda integrados.

[Más detalles](https://www.bainsight.com/connectors/deltek-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

mediante [Accenture](https://www.accenture.com)

El conector Aspire Documentum DQL rastrea el contenido de Documentum, lo que permite rastreos basados en instrucciones DQL SELECT definidas por el usuario, rastreo incremental, recuperación de ACL, expansión de grupos de permisos anidados y mucho más.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Documentum+DQL+Connector)

:::column-end:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

mediante [BA Insight](https://www.bainsight.com/)

El conector Documentum de BA Insight indexa de forma segura tanto el texto completo como los metadatos de los objetos de Documentum en Azure Cognitive Search, lo que permite un único conjunto de resultados que permiten búsquedas en el contenido de varios repositorios. Esto es diferente a otros conectores que muestran los registros de Documentum con Azure Cognitive Search de uno en uno para la administración de procesos.

[Más detalles](https://www.bainsight.com/connectors/documentum-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde OpenText Documentum y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de repositorios, carpetas y archivos junto con sus metadatos y propiedades de Documentum casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de OpenText Documentum.

[Más detalles](https://www.raytion.com/connectors/raytion-documentum-connector)

:::column-end:::
:::column span="":::

---

### <a name="egnyte"></a>egnyte

mediante [BA Insight](https://www.bainsight.com/)

El conector Egnyte admite rastreos e índices completos e incrementales con un rendimiento muy alto.

[Más detalles](https://www.bainsight.com/connectors/egnyte-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="elasticsearch"></a>Elasticsearch

mediante [Accenture](https://www.accenture.com)

El conector Elasticsearch rastrea el contenido de un índice de Elasticsearch, lo que permite el rastreo de varios índices, la compatibilidad con Slice, el uso de los métodos Get de MGet para recuperar contenido, así como la limitación de la conexión.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Elasticsearch+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="elite--e3"></a>Elite/E3

mediante [BA Insight](https://www.bainsight.com/)

El conector Elite de BA Insight proporciona un único punto de acceso para que los abogados accedan al contenido y al conocimiento de la firma en consonancia con el contenido de Elite utilizando Azure Cognitive Search.

[Más detalles](https://www.bainsight.com/connectors/elite-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="emc-eroom"></a>EMC eRoom

mediante [BA Insight](https://www.bainsight.com/)

El conector eRoom establece una conexión segura a la aplicación eRoom y asigna el contenido, incluidos los metadatos y los datos adjuntos, desde el esquema de eRoom hasta el esquema del motor de búsqueda. A continuación, extrae el contenido y lo introduce en el motor de búsqueda en un proceso denominado rastreo.

[Más detalles](https://www.bainsight.com/connectors/eroom-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="facebook-workplace"></a>Facebook Workplace

mediante [BA Insight](https://www.bainsight.com/)

Las organizaciones que usan Workplace by Facebook ahora pueden ampliar el alcance de estos datos en sus índices de búsqueda existentes a través del conector Workplace by Facebook de BA Insight.

[Más detalles](https://www.bainsight.com/connectors/connector-for-workplace-by-facebook/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="facebook-workplace"></a>Facebook Workplace

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Facebook Workplace y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de los grupos de proyectos, las conversaciones y los documentos compartidos de Facebook Workplace casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de Facebook Workplace.

[Más detalles](https://www.raytion.com/connectors/raytion-facebook-workplace-connector)

:::column-end:::
:::column span="":::
---

### <a name="file-share"></a>Recurso compartido de archivos

mediante [BA Insight](https://www.bainsight.com/)

El conector de recursos compartidos de archivos permite presentar contenido de recursos compartidos de archivos (Windows, SMB/CIFS) en un único índice de búsqueda consolidado, junto con el contenido de otros repositorios.

[Más detalles](https://www.bainsight.com/connectors/file-share-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="file-system"></a>Sistema de archivos

mediante [Accenture](https://www.accenture.com)

El conector del sistema de archivos rastrea el contenido desde una ubicación del sistema de archivos, lo que permite el rastreo incremental, la extracción de metadatos, el filtrado de documentos por ruta de acceso, soportando los sistemas de archivos de Windows/Linux/MacOS.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/File+System+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::
---

### <a name="file-system"></a>Sistema de archivos

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde sistemas de archivos locales y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de archivos y carpetas de sistemas de archivos casi en tiempo real.

[Más detalles](https://www.raytion.com/connectors/raytion-file-system-connector)

:::column-end:::
:::column span="":::

---

### <a name="firstspirit"></a>FirstSpirit

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde e-Spirit FirstSpirit y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de páginas, datos adjuntos y otros tipos de documentos generados de FirstSpirit casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios, grupos y permisos de e-Spirit FirstSpirit, así como con instalaciones de FirstSpirit basadas en Active Directory y otros servicios de directorio.

[Más detalles](https://www.raytion.com/connectors/raytion-firstspirit-connector)

:::column-end:::
:::column span="":::

---

### <a name="gitlab"></a>GitLab

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde GitLab y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de proyectos, archivos, carpetas, mensajes de confirmación, problemas y páginas wiki de GitLab casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de GitLab.

[Más detalles](https://www.raytion.com/connectors/raytion-gitlab-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="google-cloud-sql"></a>Google Cloud SQL

mediante [BA Insight](https://www.bainsight.com/)

El conector Google Cloud SQL indexa el contenido de Google Cloud SQL en el índice de Azure Cognitive Search y lo muestra a través de SmartHub de BA Insight para proporcionar a los usuarios resultados de búsqueda integrados.

[Más detalles](https://www.bainsight.com/connectors/google-cloud-sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="google-drive"></a>Google Drive

mediante [BA Insight](https://www.bainsight.com/)

El conector BAI Google Drive permite mostrar contenido de Google Drive en un único índice de búsqueda consolidado que hace referencia a contenido de Google Drive, junto con el contenido de otros repositorios.

[Más detalles](https://www.bainsight.com/connectors/google-drive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="google-drive"></a>Google Drive

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Google Drive y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de archivos, carpetas y comentarios en unidades personales y unidades de equipo de Google Drive casi en tiempo real. El conector es totalmente compatible con el modelo de permisos integrado de Google Drive y con la administración de usuarios y grupos mediante Google Admin Directory.

[Más detalles](https://www.raytion.com/connectors/raytion-google-drive-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="hp-consolidated-archive-eas"></a>HP Consolidated Archive (EAS)

mediante [BA Insight](https://www.bainsight.com/)

El conector HP Consolidated Archive de BA Insight indexa de forma segura tanto el texto completo como los metadatos de los documentos de los archivos en varios motores de búsqueda, incluidos SharePoint Search y Azure Search. Esto permite obtener un único conjunto de resultados en el que se pueden realizar búsquedas en el contenido de varios repositorios. Permite a las organizaciones aprovechar la gran cantidad de información a la que se puede acceder en Consolidated Archive, SharePoint y otros repositorios, haciendo que los usuarios puedan interactuar al instante con dichos datos a través de la búsqueda.

[Más detalles](https://www.bainsight.com/connectors/hp-consolidated-archive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

mediante [Accenture](https://www.accenture.com)

El conector IBM Connections rastrea el contenido del servidor IBM Connections, que incluye rastreo incremental, extracción de metadatos, recuperación de ACL, filtrado de documentos por patrones de expresión regular, etc.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/IBM+Connections+Connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

mediante [BA Insight](https://www.bainsight.com/)

El conector IBM Connections se desarrolló para IBM Connections y permite establecer una conexión segura a la aplicación Connections, así como asignar el contenido, incluidos los metadatos y los datos adjuntos, desde el esquema Connections al esquema del motor de búsqueda. A continuación, extrae el contenido y lo introduce en el motor de búsqueda en un proceso denominado rastreo.

[Más detalles](https://www.bainsight.com/connectors/ibm-connections-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde IBM Connections y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de archivos públicos y personales, blogs, wikis, foros, comunidades, marcadores, perfiles y actualizaciones de estado de instancias de Connections locales casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de IBM Connection, así como con instalaciones de Connections basadas en Active Directory y otros servicios de directorio.

[Más detalles](https://www.raytion.com/connectors/raytion-ibm-connections-connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections-cloud"></a>IBM Connections Cloud

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde IBM Connections Cloud y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de archivos públicos y personales, blogs, wikis, foros, comunidades, perfiles y actualizaciones de estado de Connections Cloud casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de IBM Connections Cloud.

[Más detalles](https://www.raytion.com/connectors/raytion-ibm-connections-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-content-manager"></a>IBM Content Manager

mediante [BA Insight](https://www.bainsight.com/)

El conector IBM Content Manager respeta la seguridad de las aplicaciones de origen y proporciona rastreos completos e incrementales, por lo que los usuarios siempre tienen disponible la información más reciente.

[Más detalles](https://www.bainsight.com/connectors/ibm-content-manager-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-db2"></a>IBM DB2

mediante [BA Insight](https://www.bainsight.com/)

El conector Db2 permite a las organizaciones aprovechar la gran cantidad de datos almacenados en bases de datos y aplicaciones DB2, haciendo que los usuarios puedan interactuar al instante con dichos datos a través de la búsqueda.

[Más detalles](https://www.bainsight.com/connectors/ibm-db2-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-filenet-p8"></a>IBM FileNet P8

mediante [BA Insight](https://www.bainsight.com/)

El conector IBM FileNet Content Manager permite a SharePoint y a otros usuarios del portal buscar de forma segura el contenido almacenado en repositorios de FileNet. El acceso al contenido viene determinado por la seguridad establecida en FileNet, lo que garantiza la seguridad del contenido tanto si se accede a él directamente desde FileNet como si se accede a través de cualquier otro portal.

[Más detalles](https://www.bainsight.com/connectors/ibm-filenet-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-lotus-notes"></a>IBM Lotus Notes

mediante [BA Insight](https://www.bainsight.com/)

Con el conector de correo electrónico IBM Notes de BA Insight, los usuarios pueden buscar correos electrónicos de Lotus Notes directamente desde SharePoint u otro portal. La seguridad definida en IBM Notes se refleja automáticamente en la experiencia de búsqueda, por lo que los usuarios verán los resultados de búsqueda desde su propio buzón, buzones públicos y otros buzones a los que tengan acceso.

[Más detalles](https://www.bainsight.com/connectors/lotus-notes-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-websphere"></a>IBM WebSphere

mediante [BA Insight](https://www.bainsight.com/)

El conector WebSphere de BA Insight indexa de forma segura tanto el texto completo como los metadatos de los objetos de WebSphere en el motor de búsqueda de Microsoft, lo que permite obtener un único conjunto de resultados que permiten búsquedas en el contenido de varios repositorios. Esto permite a las organizaciones aprovechar la gran cantidad de información accesible en las plataformas de Microsoft, haciendo que los usuarios puedan interactuar al instante con dichos datos a través de la búsqueda.

[Más detalles](https://www.bainsight.com/connectors/ibm-websphere-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="imanage-cloud"></a>iManage Cloud

mediante [BA Insight](https://www.bainsight.com/)

iManage Cloud Connector de BA Insight indexa de forma segura tanto el texto completo como los metadatos de los documentos de las áreas de trabajo de Work en el motor de búsqueda.

[Más detalles](https://www.bainsight.com/connectors/connector-for-imanage-work-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="imanage-work"></a>iManage Work

mediante [BA Insight](https://www.bainsight.com/)

El conector iManage Work proporciona seguridad completa y funciona con un alto rendimiento para minimizar los tiempos de rastreo y mantener al mismo tiempo un impacto de bajo rendimiento en Work. Solo requiere acceso de lectura y no es necesario instalar software cliente en ningún servidor iManage. De este modo se consigue un acceso fluido y simultáneo a todo el contenido almacenado en iManage Work.

[Más detalles](https://www.bainsight.com/connectors/imanage-work-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="jira"></a>Jira

mediante [BA Insight](https://www.bainsight.com/)

El conector Jira permite a los usuarios realizar búsquedas en todos los objetos de Jira, lo que elimina la necesidad de ir directamente a Jira.

[Más detalles](https://www.bainsight.com/connectors/jira-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="jira"></a>Jira

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Atlassian Jira y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de proyectos, incidencias, datos adjuntos, comentarios, registros de trabajo, historiales de incidencias, vínculos y perfiles de instancias locales de Jira casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de Atlassian Jira, así como con instalaciones de Jira basadas en Active Directory y otros servicios de directorio.

[Más detalles](https://www.raytion.com/connectors/raytion-jira-connector)

:::column-end:::
:::column span="":::

---

### <a name="jira-cloud"></a>Jira Cloud

mediante [BA Insight](https://www.bainsight.com/)

El conector Jira (versión en la nube) realiza búsquedas en todos los objetos de Jira, lo que elimina la necesidad de navegar directamente a Jira.

[Más detalles](https://www.bainsight.com/connectors/jira-cloud-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="jira-cloud"></a>Jira Cloud

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Atlassian Jira Cloud y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de proyectos, incidencias, datos adjuntos, comentarios, registros de trabajo, historiales de incidencias, vínculos y perfiles de Jira Cloud casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de Atlassian Jira Cloud.

[Más detalles](https://www.raytion.com/connectors/raytion-jira-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="jive"></a>Jive

mediante [BA Insight](https://www.bainsight.com/)

El conector Jive fue desarrollado para Jive y permite establecer una conexión segura con la aplicación Jive, así como asignar el contenido, incluidos los metadatos y los datos adjuntos, del esquema Jive al esquema del motor de búsqueda. A continuación, extrae el contenido y lo introduce en el motor de búsqueda en un proceso denominado rastreo.

[Más detalles](https://www.bainsight.com/connectors/jive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="jive"></a>Jive

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Jive y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de discusiones, encuestas, archivos, blogs, espacios, grupos, proyectos, tareas, vídeos, mensajes, ideas, perfiles y actualizaciones de estado de las instancias de Jive locales y hospedadas en la nube casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de Jive y admite los modelos de autenticación nativa de Jive, OAuth y autenticación básica.

[Más detalles](https://www.raytion.com/connectors/raytion-jive-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="kaltura"></a>Kaltura

mediante [BA Insight](https://www.bainsight.com/)

El conector Kaltura permite indexar no solo vídeo, sino también otros tipos de información, como categorías, datos, documentos y mucho más.

[Más detalles](https://www.bainsight.com/connectors/kaltura-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ldap"></a>LDAP

mediante [BA Insight](https://www.bainsight.com/) 

El conector LDAP permite a las organizaciones conectarse a cualquier directorio compatible con LDAP e indexar cualquier registro desde él. Las organizaciones pueden filtrar por subconjuntos específicos del directorio y recuperar solo campos específicos, lo que facilita la búsqueda de usuarios, contactos o grupos almacenados en cualquier lugar del directorio.

[Más detalles](https://www.bainsight.com/connectors/ldap-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ldap"></a>LDAP

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar de forma fiable el contenido de los servicios de directorio compatibles con el protocolo ligero de acceso a directorios (LDAP) y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de los objetos LDAP de Active Directory, Novell E-Directory y otros servicios de directorio compatibles con LDAP casi en tiempo real. El conector se puede usar para ingerir entidades de seguridad en Google Cloud Search para casos de uso como búsquedas de expertos, equipos y ubicación, o para implementar el recorte de seguridad para orígenes de datos personalizados. El conector admite LDAP a través de SSL.

[Más detalles](https://www.raytion.com/connectors/raytion-ldap-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="legalkey"></a>LegalKEY

mediante [BA Insight](https://www.bainsight.com/) 

El conector OpenText LegalKEY de BA Insight indexa de forma segura tanto el texto completo como los metadatos de los registros de clientes y asuntos en LegalKEY en el motor de búsqueda de Microsoft, lo que permite obtener un único conjunto de resultados de búsqueda en el contenido de varios repositorios. Esto permite a las organizaciones aprovechar la gran cantidad de información a la que se puede acceder en LegalKEY, SharePoint y otros repositorios, haciendo que los usuarios puedan interactuar al instante con dichos datos a través de la búsqueda.

[Más detalles](https://www.bainsight.com/connectors/legalkey-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="lexisnexis-interaction"></a>LexisNexis InterAction

mediante [BA Insight](https://www.bainsight.com/)

El conector LexisNexis InterAction facilita a los abogados y a otros empleados de la firma la búsqueda de información importante almacenada en InterAction sin necesidad de iniciar sesión directamente y realizar una búsqueda independiente.

[Más detalles](https://www.bainsight.com/connectors/lexisnexis-interaction-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="lotus-notes-databases"></a>Bases de datos de Lotus Notes

mediante [BA Insight](https://www.bainsight.com/) 

Con el conector IBM Notes Database, los usuarios pueden encontrar contenido almacenado en bases de datos de Notes mediante Azure Cognitive Search. La seguridad definida en IBM Notes se refleja automáticamente en la experiencia de búsqueda, lo que garantiza que los usuarios vean el contenido para el que están autorizados. En última instancia, los usuarios pueden encontrar todo lo que necesitan en un solo lugar.

[Más detalles](https://www.bainsight.com/connectors/ibm-lotus-notes-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="m-files"></a>M-Files

mediante [BA Insight](https://www.bainsight.com/)

El conector M-Files permite indexar contenido administrado por la plataforma M-Files y admite el rastreo completo e incremental para garantizar la actualización del índice.

[Más detalles](https://www.bainsight.com/connectors/m-files-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="mediaplatform-primetime"></a>MediaPlatform PrimeTime

mediante [BA Insight](https://www.bainsight.com/)

El conector de indexación MediaPlatform PrimeTime de BA Insight permite que el contenido sea accesible para los usuarios a través de la plataforma de búsqueda empresarial de una organización, combinando el conector con SmartHub de BA Insight. El conector MediaPlatform PrimeTime de BA Insight recupera información sobre canales y vídeos de MediaPlatform PrimeTime y los indexa a través de Azure Cognitive Search.

[Más detalles](https://www.bainsight.com/connectors/mediaplatform-primetime-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="mediawiki"></a>MediaWiki

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde MediaWiki y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de páginas, páginas de discusión y datos adjuntos de instancias de MediaWiki casi en tiempo real. El conector es totalmente compatible con el modelo de permisos integrado de MediaWiki, así como con instalaciones de MediaWiki basadas en Active Directory y otros servicios de directorio.

[Más detalles](https://www.raytion.com/connectors/raytion-mediawiki-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="micro-focus-content-manager-hpe-records-managerhp-trim"></a>Micro Focus Content Manager (HPE Records Manager/HP TRIM)

mediante [BA Insight](https://www.bainsight.com/)

El conector HP TRIM fue desarrollado para HP Records Manager y permite establecer una conexión segura con la aplicación TRIM, así como asignar el contenido, incluidos los metadatos y los datos adjuntos, del esquema TRIM al esquema del motor de búsqueda. A continuación, extrae el contenido y lo introduce en el motor de búsqueda en un proceso denominado rastreo.

[Más detalles](https://www.bainsight.com/connectors/hp-trim-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-dynamics-365"></a>Microsoft Dynamics 365

mediante [BA Insight](https://www.bainsight.com/)

Nuestro conector Microsoft Dynamics 365 CRM admite instalaciones de CRM locales y Dynamics CRM Online.

[Más detalles](https://www.bainsight.com/connectors/microsoft-dynamics-crm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-dynamics-365-cloud"></a>Microsoft Dynamics 365 (Cloud)

mediante [BA Insight](https://www.bainsight.com/)

Nuestro conector CRM de Microsoft Dynamics 365 (versión Cloud) establece una conexión segura con la aplicación CRM y asigna el contenido del esquema CRM al esquema del motor de búsqueda.

[Más detalles](https://www.bainsight.com/connectors/connector-for-microsoft-dynamics-cloud/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="microsoft-exchange-online"></a>Microsoft Exchange Online

mediante [BA Insight](https://www.bainsight.com/)

Con BA Insight Microsoft Exchange Online Connector, los usuarios pueden recuperar contenido de Exchange Online a través de varias plataformas de búsqueda.

[Más detalles](https://www.bainsight.com/connectors/microsoft-exchange-online-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-exchange-public-folders"></a>Carpetas públicas de Microsoft Exchange

mediante [BA Insight](https://www.bainsight.com/)

Con el conector BAI Microsoft Exchange Public Folders, los usuarios pueden recuperar contenido de Exchange a través de varias plataformas de búsqueda.

[Más detalles](https://www.bainsight.com/connectors/microsoft-exchange-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-exchange-server"></a>Microsoft Exchange Server

mediante [BA Insight](https://www.bainsight.com/)

Con BA Insight Microsoft Exchange Connector, los usuarios pueden recuperar contenido de Exchange a través de varios motores de búsqueda.

[Más detalles](https://www.bainsight.com/connectors/microsoft-exchange-server-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

mediante [BA Insight](https://www.bainsight.com/)

Este conector de bases de datos está basado en métodos de acceso a bases de datos estándar del sector, por lo que puede admitir igualmente bases de datos de otros sistemas, como Oracle, MySQL e IBM DB2. Respeta la seguridad de la base de datos de origen y proporciona rastreos completos e incrementales, por lo que los usuarios siempre tienen disponible la información más reciente.

[Más detalles](https://www.bainsight.com/connectors/sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-teams"></a>Microsoft Teams

mediante [BA Insight](https://www.bainsight.com/)

El conector BA Insights Microsoft Teams indexa el contenido de Microsoft Teams junto con el contenido de otros sistemas empresariales para proporcionar resultados unificados.

[Más detalles](https://www.bainsight.com/connectors/microsoft-teams-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-windows-file-server"></a>Servidor de archivos de Microsoft Windows

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar de forma fiable el contenido del servidor de archivos de Microsoft Windows, incluido su sistema de archivos distribuido (DFS) y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de archivos y carpetas del servidor de archivos de Windows casi en tiempo real. El conector es totalmente compatible con la seguridad de nivel de documento del servidor de archivos de Microsoft Windows y con las versiones más recientes de los protocolos SMB2 y SMB3.

[Más detalles](https://www.raytion.com/connectors/raytion-windows-file-server-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="mysql"></a>MySQL

mediante [BA Insight](https://www.bainsight.com/)

El conector MySQL está basado en métodos de acceso a bases de datos estándar del sector, por lo que puede admitir igualmente bases de datos de otros sistemas, como Oracle, MySQL e IBM DB2. Respeta la seguridad de la base de datos de origen y proporciona rastreos completos e incrementales, por lo que los usuarios siempre tienen disponible la información más reciente.

[Más detalles](https://www.bainsight.com/connectors/mysql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="netdocuments"></a>NetDocuments

mediante [BA Insight](https://www.bainsight.com/)

El conector NetDocuments indexa el contenido almacenado en NetDocs para que los usuarios puedan buscar y recuperar contenido de NetDocuments directamente desde su portal. El conector aplica automáticamente la seguridad de documentos de NetDocs a Azure Cognitive Search, por lo que la información del usuario permanece segura. Los metadatos almacenados en NetDocuments se pueden asignar a términos equivalentes para que los usuarios tengan una experiencia de búsqueda fluida.

[Más detalles](https://www.bainsight.com/connectors/netdocuments-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="neudesic-the-firm-directory"></a>Neudesic The Firm Directory

mediante [BA Insight](https://www.bainsight.com/)

El conector Firm Directory respeta la seguridad del sistema de origen y proporciona rastreos completos e incrementales, por lo que los usuarios tienen la información más reciente disponible en todo momento.

[Más detalles](https://www.bainsight.com/connectors/the-firm-directory-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="notes"></a>Notas

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde IBM Notes (anteriormente Lotus Note) y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de los registros de un conjunto configurable de bases de datos Notes casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de IBM Notes.

[Más detalles](https://www.raytion.com/connectors/raytion-notes-connector)

:::column-end:::
:::column span="":::

---

### <a name="nuxeo"></a>Nuxeo

mediante [BA Insight](https://www.bainsight.com/)

El conector Nuxeo permite a las organizaciones indexar su contenido de Nuxeo, incluida la información de seguridad y los metadatos estándar y personalizados establecidos en el contenido en Azure Cognitive Search junto con el contenido presente en Office 365. En última instancia, los usuarios pueden encontrar todo lo que necesitan en un solo lugar.

[Más detalles](https://www.bainsight.com/connectors/nuxeo-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="objective"></a>Objetivo

mediante [BA Insight](https://www.bainsight.com/)

Objective Connector se desarrolló para Objective y permite establecer una conexión segura con Objective, así como asignar el contenido, incluidos los metadatos, desde el esquema Objective al esquema del motor de búsqueda. A continuación, extrae el contenido y lo introduce en el motor de búsqueda en un proceso denominado rastreo.

[Más detalles](https://www.bainsight.com/connectors/objective-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="onedrive"></a>OneDrive

mediante [Accenture](https://www.accenture.com)

El conector OneDrive rastrea el contenido de Microsoft OneDrive, lo que permite el rastreo incremental, incluidos y excluidos elementos basados en patrones de expresión regular, extracción de metadatos y recuperación de varios tipos de documentos.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/OneDrive+Connector)

:::column-end:::
:::column span="":::

---

### <a name="onedrive-for-business"></a>OneDrive para la Empresa

mediante [BA Insight](https://www.bainsight.com/)

El conector BA Insight OneDrive Connector permite indexar contenido de OneDrive en varias plataformas de búsqueda, lo que proporciona a los usuarios resultados de búsqueda integrados de varios orígenes.

[Más detalles](https://www.bainsight.com/connectors/onedrive-business-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="opentext-content-server"></a>Servidor de contenido OpenText

mediante [BA Insight](https://www.bainsight.com/)

El conector indexa el contenido del servidor de contenido de la misma manera que el contenido nativo del portal y admite rastreos completos e incrementales. La seguridad definida en el servidor de contenido se refleja automáticamente en la experiencia de búsqueda, lo que garantiza que los usuarios solo vean el contenido para el que están autorizados.

[Más detalles](https://www.bainsight.com/connectors/livelink-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="opentext-content-server"></a>Servidor de contenido OpenText

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde el servidor de contenido OpenText y hacer búsquedas inteligentes con Azure Cognitive Search. Indexa archivos, carpetas, carpetas virtuales, documentos compuestos, noticias, correos electrónicos, volúmenes, colecciones, clasificaciones y muchos más objetos de instancias de servidor de contenido casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos del servidor de contenido OpenText.

[Más detalles](https://www.raytion.com/connectors/raytion-opentext-content-server-connector)

:::column-end:::
:::column span="":::

---

### <a name="opentext-documentum-cloud"></a>OpenText Documentum (Cloud)

mediante [BA Insight](https://www.bainsight.com/)

El conector OpenText Documentum Cloud de BA Insight indexa de forma segura tanto el texto completo como los metadatos de los objetos de Documentum en el motor de búsqueda, de manera que se obtiene un único conjunto de resultados que permiten búsquedas en el contenido de varios repositorios.

[Más detalles](https://www.bainsight.com/connectors/connector-for-documentum-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="opentext-documentum-eroom"></a>OpenText Documentum eRoom

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde OpenText Documentum eRoom y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de repositorios, carpetas y archivos junto con sus metadatos y propiedades de Documentum eRoom casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de OpenText Documentum eRoom.

[Más detalles](https://www.raytion.com/connectors/raytion-opentext-documentum-eroom-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="opentext-edocs-dm"></a>OpenText eDOCS DM

mediante [BA Insight](https://www.bainsight.com/)

Los usuarios del conector OpenText eDOCS DM pueden buscar contenido que se encuentre en repositorios de eDOCS directamente desde Azure Cognitive Search, lo que elimina la necesidad de realizar varias búsquedas para localizar el contenido necesario. El conector mantiene la seguridad establecida en eDOCS para asegurarse de que el contenido solo lo ven aquellos a los que se les ha concedido acceso.

[Más detalles](https://www.bainsight.com/connectors/edocs-dm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="oracle-database"></a>Base de datos de Oracle

mediante [BA Insight](https://www.bainsight.com/)

El conector Oracle Database está basado en métodos de acceso a bases de datos estándar del sector, por lo que puede admitir igualmente bases de datos de otros sistemas, como Microsoft SQL Server, MySQL e IBM DB2.

[Más detalles](https://www.bainsight.com/connectors/oracle-database-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="oracle-webcenter"></a>Oracle WebCenter

mediante [BA Insight](https://www.bainsight.com/)

El conector WebCenter integra WebCenter con Azure Cognitive Search, lo que facilita a los usuarios de toda la organización la búsqueda de información importante almacenada en WebCenter sin necesidad de iniciar sesión directamente y hacer una búsqueda independiente.

[Más detalles](https://www.bainsight.com/connectors/oracle-webcenter-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="oracle-ka-cloud"></a>Oracle KA Cloud

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Oracle Knowledge Advanced (KA) Cloud y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de páginas y datos adjuntos de Oracle KA Cloud casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de Oracle KA Cloud. En concreto, el conector controla los permisos basados en fragmentos de código de las páginas de Oracle KA Cloud.

[Más detalles](https://www.raytion.com/connectors/raytion-oracle-ka-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="oracle-webcenter-content-ucmstellent"></a>Oracle WebCenter Content (UCM/Stellent)

mediante [BA Insight](https://www.bainsight.com/)

El conector WebCenter Content admite totalmente la seguridad subyacente de todo el contenido disponible para Azure Cognitive Search y mantiene este contenido actualizado a través de rastreos programados, lo que garantiza que los usuarios obtengan las actualizaciones más recientes al realizar una búsqueda.

[Más detalles](https://www.bainsight.com/connectors/oracle-webcenter-content-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="pirobase-cms"></a>pirobase CMS

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde pirobase CMS y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de páginas, datos adjuntos y otros tipos de documentos generados de pirobase CSM casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de pirobase CMS.

[Más detalles](https://www.raytion.com/connectors/raytion-pirobase-cms-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="postgresql"></a>PostgreSQL

mediante [BA Insight](https://www.bainsight.com/)

El conector PostgreSQL de BA Insight respeta la seguridad de la base de datos de origen y proporciona rastreos completos e incrementales, por lo que los usuarios siempre tienen disponible la información más reciente. Indexa el contenido de PostgreSQL en Azure Cognitive Search y lo muestra a través de SmartHub de BA Insight para proporcionar a los usuarios resultados de búsqueda integrados.

[Más detalles](https://www.bainsight.com/connectors/postgresql-connector-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="practical-law"></a>Practical Law

mediante [BA Insight](https://www.bainsight.com/)

El conector Practical Law de BA Insight permite a los usuarios realizar búsquedas en la base de datos de Practical Law, lo que elimina la necesidad de navegar directamente en Practical Law.

[Más detalles](https://www.bainsight.com/connectors/practical-law-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="prolaw"></a>ProLaw

mediante [BA Insight](https://www.bainsight.com/)

El conector BA Insight para ProLaw conecta cualquier portal a ProLaw, lo que permite visualizar la información de ProLaw mientras se respetan los privilegios de usuario de ProLaw.

[Más detalles](https://www.bainsight.com/connectors/prolaw-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="rdb-via-snapshots"></a>RRDB via Snapshots

mediante [Accenture](https://www.accenture.com)

El conector RDB via Snapshots rastrea el contenido de cualquier base de datos relacional a la que se pueda acceder mediante JDBC y realiza rastreos incrementales mediante una base de datos de instantáneas. Extrae datos directamente a partir de instrucciones SQL.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/RDB+via+Snapshots+Connector)

:::column-end:::
:::column span="":::

---

### <a name="rdb-via-tables"></a>RDB via Tables

mediante [Accenture](https://www.accenture.com)

El conector RDB via Tables rastrea el contenido de cualquier base de datos relacional a la que se pueda acceder mediante JDBC y realiza rastreos incrementales que recuperan actualizaciones mediante tablas que incluyen identificadores de contenido actualizado. Extrae datos directamente a partir de instrucciones SQL.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/RDB+via+Table+Connector)

:::column-end:::
:::column span="":::

---

### <a name="s3"></a>S3

mediante [Accenture](https://www.accenture.com)

El conector Amazon S3 rastrea el contenido de cualquier servicio de Amazon Simple Storage Service. Realiza rastreos incrementales, recupera ACL de objetos para la seguridad de nivel de documento S3 e incluye documentos almacenados en cubos, carpetas y subcarpetas.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Amazon+S3+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

mediante [Accenture](https://www.accenture.com)

El conector Salesforce rastrea el contenido de un repositorio Salesforce. El conector admite Salesforce Knowledge Base y Chatter, recuperación de metadatos y metadatos personalizados, rastreos completos o incrementales, recuperación de ACL, tipos de elementos seleccionables y expansión de grupos.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Salesforce+Connector)

:::column-end:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

mediante [BA Insight](https://www.bainsight.com/)

Salesforce Connector integra Salesforce's Service, Sales y Marketing Cloud con Azure Cognitive Search, lo que hace que todo el contenido de Salesforce esté disponible para todos los empleados a través de este portal.

[Más detalles](https://www.bainsight.com/connectors/salesforce-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Salesforce y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de cuentas, mensajes de chat, perfiles, clientes potenciales, casos y todos los demás objetos de registro de Salesforce casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de Salesforce.

[Más detalles](https://www.raytion.com/connectors/raytion-salesforce-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sap-erp"></a>ERP de SAP

mediante [BA Insight](https://www.bainsight.com/)

El conector SAP ERP de BA Insight está diseñado para incorporar elementos de SAP a un índice de búsqueda. Esto, a su vez, aligera la interfaz de usuario y permite obtener una vista unificada de toda la información de SAP, SharePoint y otros sistemas.

[Más detalles](https://www.bainsight.com/connectors/sap-erp-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="sap-erp-cloud"></a>SAP ERP (Cloud)

mediante [BA Insight](https://www.bainsight.com/)

El conector SAP ERP de BA Insight (versión Cloud) está diseñado para incorporar elementos de SAP a un índice de búsqueda.

[Más detalles](https://www.bainsight.com/connectors/connector-for-sap-erp-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="sap-hana"></a>SAP HANA

mediante [BA Insight](https://www.bainsight.com/)

El conector SAP HANA respeta la seguridad de la base de datos de origen y proporciona rastreos completos e incrementales, por lo que los usuarios siempre tienen disponible la información más reciente. Indexa el contenido de SAP HANA en Azure Cognitive Search y lo muestra a través de SmartHub de BA Insight para proporcionar a los usuarios resultados de búsqueda integrados.

[Más detalles](https://www.bainsight.com/connectors/sap-hana-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sap-hana-cloud"></a>SAP HANA (Cloud)

mediante [BA Insight](https://www.bainsight.com/)

El conector SAP HANA (versión Cloud) respeta la seguridad de la base de datos de origen y proporciona rastreos completos e incrementales para que los usuarios tengan la información más reciente disponible en todo momento.

[Más detalles](https://www.bainsight.com/connectors/connector-sap-hana-cloud-version/)

:::column-end:::
:::column span="":::

---

### <a name="sap-netweaver-portal"></a>SAP NetWeaver Portal

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde SAP NetWeaver Portal (NWP) y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de páginas, datos adjuntos y otros tipos de documentos de SAP NWP, sus áreas Knowledge Management and Collaboration (KMC) y Portal Content Directory (PCD) casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de SAP NetWeaver Portal, así como con instalaciones de SAP NWP basadas en Active Directory y otros servicios de directorio.

[Más detalles](https://www.raytion.com/connectors/raytion-sap-netweaver-portal-connector)

:::column-end:::
:::column span="":::

---

### <a name="sap-plm-dms"></a>SAP PLM DMS

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde SAP PLM DMS y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de documentos, datos adjuntos y otros registros de SAP PLM DMS casi en tiempo real.

[Más detalles](https://www.raytion.com/connectors/raytion-sap-plm-dms-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="selenium"></a>Selenium

mediante [Accenture](https://www.accenture.com)

El conector Selenium rastrea el contenido de los sitios web mediante un explorador de Internet para recuperar distintos tipos de documentos, como páginas web, sitemaps, documentos binarios, etc. Evita problemas de compatibilidad con marcos como Angular y React.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Selenium+Crawler)

:::column-end:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

mediante [Accenture](https://www.accenture.com)

El conector de ServiceNow recupera varios tipos de documentos de un repositorio de ServiceNow, como artículos de conocimiento, elementos de catálogo y datos adjuntos. También recupera las ACL de seguridad y realiza la expansión de grupos.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/ServiceNow+Connector)

:::column-end:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

mediante [BA Insight](https://www.bainsight.com/)

 El conector ServiceNow respeta la seguridad del sistema de origen y proporciona rastreos completos e incrementales, por lo que los usuarios siempre tienen disponible la información más reciente.

[Más detalles](https://www.bainsight.com/connectors/servicenow-connector-sharepoint-azure-elasticsearch)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde ServiceNow y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de incidencias, tareas, datos adjuntos, artículos de administración del conocimiento y páginas, entre otros, de ServiceNow casi en tiempo real. El conector admite la administración integrada de usuarios y grupos de ServiceNow.

[Más detalles](https://www.raytion.com/connectors/raytion-servicenow-connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint"></a>SharePoint

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Microsoft SharePoint y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de sitios, webs, páginas modernas y clásicas (SharePoint 2016 y versiones posteriores), páginas wiki, documentos OneNote, elementos de lista, tareas, elementos de calendario, datos adjuntos y archivos de instancias locales de SharePoint casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de Microsoft SharePoint, así como con proveedores de Active Directory y OAuth como SiteMinder y Okta. El conector incluye compatibilidad con autenticación Básica, NTLM y Kerberos.

[Más detalles](https://www.raytion.com/connectors/raytion-sharepoint-connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2010"></a>SharePoint 2010

mediante [BA Insight](https://www.bainsight.com/)

El conector SharePoint 2010 de BA Insight le permite conectarse a SharePoint 2010, recuperar datos de cualquier sitio, biblioteca de documentos o lista e indexar este contenido de forma segura.

[Más detalles](https://www.bainsight.com/connectors/sharepoint-2010-connector/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-2013"></a>SharePoint 2013

mediante [Accenture](https://www.accenture.com)

El conector SharePoint 2013 rastrea el contenido de cualquier URL de colección de sitios de SharePoint 2013. Realiza rastreos incrementales mediante la marca de tiempo del registro de cambios de SharePoint o una base de datos de instantáneas, recupera ACL, admite NTLM y HTTPS y listas externas de BCS, y se ejecuta sin instalar nada en SharePoint.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+2013+Connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2013"></a>SharePoint 2013

mediante [BA Insight](https://www.bainsight.com/)

El conector SharePoint 2013 de BA Insight le permite conectarse a SharePoint 2013, recuperar datos de cualquier sitio, biblioteca de documentos o lista e indexar este contenido de forma segura.

[Más detalles](https://www.bainsight.com/connectors/sharepoint-2013-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2016"></a>SharePoint 2016

mediante [Accenture](https://www.accenture.com)

El conector SharePoint 2016 rastrea el contenido de cualquier URL de colección de sitios de SharePoint 2016. Realiza rastreos incrementales mediante la marca de tiempo del registro de cambios de SharePoint o una base de datos de instantáneas, recupera ACL, admite NTLM y HTTPS y listas externas de BCS, y se ejecuta sin instalar nada en SharePoint.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+2016+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-2016"></a>SharePoint 2016

mediante [BA Insight](https://www.bainsight.com/)

El conector SharePoint 2016 de BA Insight le permite conectarse a SharePoint 2016, recuperar datos de cualquier sitio, biblioteca de documentos o lista e indexar este contenido de forma segura.

[Más detalles](https://www.bainsight.com/connectors/sharepoint-2016-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2019"></a>SharePoint 2019

mediante [BA Insight](https://www.bainsight.com/)

El conector SharePoint 2019 de BA Insight le permite conectarse a SharePoint 2019, recuperar datos de cualquier sitio, biblioteca de documentos o lista e indexar este contenido de forma segura.

[Más detalles](https://www.bainsight.com/connectors/connector-for-sharepoint-2019/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

mediante [Accenture](https://www.accenture.com)

El conector SharePoint Online rastrea el contenido de cualquier URL de colección de sitios de SharePoint Online. El conector recupera sitios, listas, carpetas, elementos de lista y datos adjuntos, así como otras páginas (en formato .aspx). Admite SharePoint ejecutado en la oferta de Microsoft O365.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+Online+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

mediante [BA Insight](https://www.bainsight.com/)

El conector SharePoint Online de BA Insight le permite conectarse a SharePoint Online, recuperar datos de cualquier sitio, biblioteca de documentos o lista e indexar este contenido de forma segura.

[Más detalles](https://www.bainsight.com/connectors/sharepoint-online-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sitecore"></a>Sitecore

mediante [BA Insight](https://www.bainsight.com/)

El conector Sitecore respeta la seguridad del sistema de origen y proporciona rastreos completos e incrementales, por lo que los usuarios siempre tienen disponible la información más reciente.

[Más detalles](https://www.bainsight.com/connectors/sitecore-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="sitecore"></a>Sitecore

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Sitecore y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de páginas, datos adjuntos y otros tipos de documentos generados casi en tiempo real. El conector es totalmente compatible con el modelo de permisos de Sitecore y la administración de usuarios y grupos en el Active Directory asociado.

[Más detalles](https://www.raytion.com/connectors/raytion-sitecore-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="slack"></a>Slack

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Slack y hacer búsquedas inteligentes con Azure Cognitive Search. Indexa mensajes, subprocesos y archivos compartidos de todos los canales públicos de Slack casi en tiempo real.

[Más detalles](https://www.raytion.com/connectors/raytion-slack-connector)

:::column-end:::
:::column span="":::

---

### <a name="smb"></a>SMB

mediante [Accenture](https://www.accenture.com)

El conector SMB recupera archivos y directorios entre unidades compartidas. Es compatible con el sistema de archivos distribuido y la recuperación de información de seguridad, y puede acceder a documentos para la indexación sin cambiar la fecha de último acceso.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/SMB+Connector)

:::column-end:::
:::column span="":::

---

### <a name="smb-file-share"></a>Recurso compartido de archivos SMB

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde recursos compartidos SMB y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de archivos y carpetas de recursos compartidos casi en tiempo real. El conector es totalmente compatible con la seguridad de nivel de documento de SMB y con las versiones más recientes de los protocolos SMB2 y SMB3.

[Más detalles](https://www.raytion.com/connectors/raytion-smb-file-share-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sql-database"></a>SQL Database

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde bases de datos SQL, como Microsoft SQL Server u Oracle, y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de registros y campos, incluidos los documentos binarios de bases de datos SQL casi en tiempo real. El conector admite la implementación de un modelo de seguridad de nivel de documento personalizado.

[Más detalles](https://www.raytion.com/connectors/raytion-sql-database-connector)

:::column-end:::
:::column span="":::

---

### <a name="any-sql-based-crm-system"></a>Cualquier sistema CRM basado en SQL

mediante [BA Insight](https://www.bainsight.com/)

El conector SQL Server está basado en métodos de acceso a bases de datos estándar del sector, por lo que puede admitir igualmente bases de datos de otros sistemas, como Oracle, MySQL e IBM DB2. Respeta la seguridad de la base de datos de origen y proporciona rastreos completos e incrementales, por lo que los usuarios siempre tienen disponible la información más reciente.

[Más detalles](https://www.bainsight.com/connectors/sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="symantec-enterprise-vault"></a>Symantec Enterprise Vault

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Symantec Enterprise Vault y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de los datos archivados, como correos electrónicos, datos adjuntos, archivos, elementos de calendario y contactos de Enterprise Vault casi en tiempo real. El conector es totalmente compatible con los modelos de autenticación Básica, NTLM y Kerberos de Symantec Enterprise Vault.

[Más detalles](https://www.raytion.com/connectors/raytion-enterprise-vault-connector-2)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="twitter"></a>Twitter

mediante [Accenture](https://www.accenture.com)

El conector Twitter rastrea el contenido de cualquier cuenta de Twitter. Realiza rastreos completos e incrementales y admite la autenticación mediante el usuario de Twitter, la clave de consumidor y la clave secreta del consumidor.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Twitter+Connector)

:::column-end:::
:::column span="":::

---

### <a name="veeva-vault"></a>Veeva Vault

mediante [BA Insight](https://www.bainsight.com/)

El conector Veeva Vault de BA Insight indexa de forma segura tanto el texto completo como los metadatos de los objetos de Veeva Vault en Azure Cognitive Search. Esto permite a los usuarios recuperar un único conjunto de resultados para el contenido de Veeva Vault y Microsoft 365.

[Más detalles](https://www.bainsight.com/connectors/veeva-vault-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="veritas-enterprise-vault-symantec-evault"></a>Veritas Enterprise Vault (Symantec eVault)

mediante [BA Insight](https://www.bainsight.com/)

El conector Veritas Enterprise Vault respeta la seguridad del sistema de origen y proporciona rastreos completos e incrementales, por lo que los usuarios siempre tienen disponible la información más reciente.

[Más detalles](https://www.bainsight.com/connectors/enterprise-vault-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="veritas-enterprise-vault"></a>Veritas Enterprise Vault

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Veritas Enterprise Vault y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de los datos archivados, como correos electrónicos, datos adjuntos, archivos, elementos de calendario y contactos de Enterprise Vault casi en tiempo real. El conector es totalmente compatible con los modelos de autenticación Básica, NTLM y Kerberos de Veritas Enterprise Vault.

[Más detalles](https://www.raytion.com/connectors/raytion-enterprise-vault-connector)

:::column-end:::
:::column span="":::
---

### <a name="website-crawler"></a>Website Crawler

mediante [BA Insight](https://www.bainsight.com/)

El conector Website Crawler de BA Insight permite presentar contenido de cualquier sitio web en un único índice de búsqueda consolidado, junto con el contenido de otros repositorios.

[Más detalles](https://www.bainsight.com/connectors/website-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="west-km"></a>West km

mediante [BA Insight](https://www.bainsight.com/)

El conector West km de BA Insight admite la búsqueda en documentos de transacciones y litigios, incluida la creación de páginas de resultados de búsqueda personalizadas.

[Más detalles](https://www.bainsight.com/connectors/westkm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="windream-ecm-system"></a>windream ECM-System

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde windream ECM-System y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de archivos y carpetas, incluidos los conjuntos completos de metadatos asociados por windream ECM-System casi en tiempo real. El conector es totalmente compatible con el modelo de permisos de windream ECM-System y la administración de usuarios y grupos en el Active Directory asociado.

[Más detalles](https://www.raytion.com/connectors/raytion-windream-ecm-system-connector)

:::column-end:::
:::column span="":::

---

### <a name="xerox-docushare"></a>Xerox DocuShare

mediante [BA Insight](https://www.bainsight.com/)

Busca contenido alojado en repositorios de Docushare directamente desde Azure Cognitive Search, lo que elimina la necesidad de realizar varias búsquedas para localizar el contenido necesario.

[Más detalles](https://www.bainsight.com/connectors/docushare-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::
---

### <a name="xerox-docushare"></a>Xerox DocuShare

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Xerox DocuShare y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de repositorios de datos, carpetas, perfiles, grupos y archivos de DocuShare casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de Xerox DocuShare.

[Más detalles](https://www.raytion.com/connectors/raytion-xerox-docushare-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

mediante [Accenture](https://www.accenture.com)

El conector Yammer rastrea el contenido de los mensajes de Yammer. Recupera los mensajes por grupo, conversación o tema y se obtienen los detalles de usuario, grupo y conversación.

[Más detalles](https://contentanalytics.digital.accenture.com/display/aspire40/Yammer+Connector)

:::column-end:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

mediante [BA Insight](https://www.bainsight.com/)

El conector Yammer establece una conexión segura a la aplicación Yammer y asigna el contenido, incluidos los metadatos y los datos adjuntos, desde el esquema de Yammer hasta el esquema del motor de búsqueda. A continuación, extrae el contenido y lo introduce en el motor de búsqueda en un proceso denominado rastreo.

[Más detalles](https://www.bainsight.com/connectors/yammer-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

mediante [Raytion](https://www.raytion.com/contact)

Conector de búsqueda empresarial seguro para indexar contenido de forma fiable desde Microsoft Yammer y hacer búsquedas inteligentes con Azure Cognitive Search. Permite efectuar una indexación potente de canales, publicaciones, respuestas, datos adjuntos, sondeos y anuncios de Yammer casi en tiempo real. El conector es totalmente compatible con la administración integrada de usuarios y grupos de Microsoft Yammer y, en particular, con la autenticación federada en Microsoft 365.

[Más detalles](https://www.raytion.com/connectors/raytion-yammer-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---
':::column-end::': null
':::column span=""::': null
---

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::
