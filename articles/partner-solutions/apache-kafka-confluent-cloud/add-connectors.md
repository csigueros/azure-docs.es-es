---
title: 'Incorporación de conectores para Confluent Cloud: soluciones para asociados de Azure'
description: En este artículo se describe cómo instalar conectores para Confluent Cloud que se usan con recursos de Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 09/03/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: a5ef809be7518c4d99ff208e769af918fe579d4f
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480703"
---
# <a name="add-connectors-for-confluent-cloud"></a>Incorporación de conectores para Confluent Cloud

En este artículo se describe cómo instalar conectores a recursos de Azure para Confluent Cloud.

## <a name="connector-to-azure-cosmos-db"></a>Conector de Azure Cosmos DB

**El conector totalmente administrado Azure Cosmos DB Sink Connector** está disponible con carácter general en Confluent Cloud. El conector totalmente administrado elimina la necesidad del desarrollo y la administración de integraciones personalizadas y reduce la carga operativa general de la conexión de los datos entre Confluent Cloud y Azure Cosmos DB. Microsoft Azure Cosmos Sink Connector for Confluent Cloud lee y escribe datos en una base de datos de Microsoft Azure Cosmos. El conector sondea datos de Kafka y los escribe en contenedores de base de datos.

Para configurar el conector, vea [Azure Cosmos DB Sink Connector for Confluent Cloud](https://docs.confluent.io/cloud/current/connectors/cc-azure-cosmos-sink.html).

**El conector autoadministrado de Azure Cosmos DB** se debe instalar manualmente. En primer lugar, descargue un archivo JAR de uber desde la [Página de versiones de Cosmos DB](https://github.com/microsoft/kafka-connect-cosmosdb/releases). O bien, puede [crear un archivo JAR de uber propio directamente desde el código fuente](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector). Para completar la instalación, siga las instrucciones descritas en la documentación del complemento para la [instalación manual de los conectores](https://docs.confluent.io/home/connect/install.html#install-connector-manually).  

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda con la solución de problemas, consulte [Solución de problemas de las soluciones de Apache Kafka para Confluent Cloud](troubleshoot.md).
