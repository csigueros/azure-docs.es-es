---
title: Ejecución de Glowroot en Cassandra API de Azure Cosmos DB (versión preliminar)
description: En este artículo se detalla cómo ejecutar Glowroot en Cassandra API de Azure Cosmos DB.
author: IriaOsara
ms.author: IriaOsara
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 1808e3df0b70f61c3cfabf96e61b6675dff6b011
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093510"
---
# <a name="run-glowroot-on-azure-cosmos-db-cassandra-api"></a>Ejecutar Glowroot en Cassandra API de Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Glowroot es una herramienta de administración del rendimiento de aplicaciones que se usa para optimizar y supervisar el rendimiento de las aplicaciones. En este artículo se explica cómo puede usar Glowroot en Cassandra API de Azure Cosmos DB para supervisar el rendimiento de la aplicación.

## <a name="prerequisites-and-setup"></a>Requisitos previos y configuración

* [Creación de una cuenta de Cassandra API de Azure Cosmos DB](manage-data-java.md#create-a-database-account).
* [Instalación de JAVA (versión 8) para Windows](https://developers.redhat.com/products/openjdk/download)
> [!NOTE]
> Tenga en cuenta que hay determinados destinos de compilación incompatibles conocidos con versiones más recientes. Si ya tiene una versión más reciente de JAVA, todavía puede descargar JDK8.
> Si tiene java más reciente instalado además de JDK8: establezca la variable %JAVA_HOME% en el símbolo del sistema local para que tenga como destino JDK8. Esto solo cambiará la versión de Java para la sesión actual y dejará intacta la configuración global de la máquina. 
* [Instalación de Maven](https://maven.apache.org/download.cgi)
    * Comprobación de instalación correcta mediante la ejecución: `mvn --version`

## <a name="run-glowroot-central-collector-with-cosmos-db-endpoint"></a>Ejecución del recopilador central de Glowroot con el punto de conexión de Cosmos DB
Una vez completada la configuración del punto de conexión. 
1. [Descarga de la distribución del recopilador central de Glowroot](https://github.com/glowroot/glowroot/wiki/Central-Collector-Installation#central-collector-installation)
2. En el archivo glowroot-central.properties, rellene las siguientes propiedades desde el punto de conexión Cassandra API de Cosmos DB
    * cassandra.contactPoints
    * cassandra.username
    * cassandra.password
3. Establezca las propiedades en `cassandra.ssl=true`, `cassandra.gcGraceSeconds=0` y `cassandra.port=10350`.
4. Asegúrese de que glowroot-central.properties está en la misma carpeta que glowroot-central.jar.
5. Ejecute `java -jar glowroot-central.jar` para empezar a ejecutar Glowroot.

## <a name="faqs"></a>Preguntas más frecuentes
Abra una incidencia de soporte técnico si tiene problemas para ejecutar o probar Glowroot. Proporcionar el identificador de suscripción y el nombre de la cuenta donde se va a ejecutar la prueba de Glowroot.

## <a name="next-steps"></a>Pasos siguientes
- Introducción a la [creación de una cuenta de Cassandra API, una base de datos y una tabla](create-account-java.md) mediante una aplicación de Java.
- Más información sobre las [características admitidas](cassandra-support.md) en Cassandra API de Azure Cosmos DB.
