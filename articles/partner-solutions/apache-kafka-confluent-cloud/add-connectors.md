---
title: 'Incorporación de conectores para Confluent Cloud: soluciones para asociados de Azure'
description: En este artículo se describe cómo instalar conectores para Confluent Cloud que se usan con recursos de Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: c4a15a54c5b9fb3b73a3fcaf81d63a9e176b49c5
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112534892"
---
# <a name="add-connectors-for-confluent-cloud"></a>Incorporación de conectores para Confluent Cloud

En este artículo se describe cómo instalar conectores a recursos de Azure para Confluent Cloud.

## <a name="connector-to-azure-cosmos-db"></a>Conector de Azure Cosmos DB

Desde dentro del cliente del concentrador de complementos, instale el conector de Cosmos DB como se recomienda en la [lista del centro de complementos](https://www.confluent.io/hub/microsoftcorporation/kafka-connect-cosmos). 

Para instalar el conector manualmente, descargue primero un archivo JAR de uber desde la [Página de versiones de Cosmos DB](https://github.com/microsoft/kafka-connect-cosmosdb/releases). Como alternativa, puede [crear su propio archivo JAR de uber directamente desde el código fuente](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector). Para completar la instalación, siga las instrucciones descritas en la documentación del complemento para la [instalación manual de los conectores](https://docs.confluent.io/home/connect/install.html#install-connector-manually).  

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda con la solución de problemas, consulte [Solución de problemas de las soluciones de Apache Kafka para Confluent Cloud](troubleshoot.md).
