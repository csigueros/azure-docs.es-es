---
title: ¿Qué es el conector de servicio?
description: Comprenda mejor los escenarios de casos de uso típicos para usar el conector de servicio y conozca sus principales ventajas.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: overview
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 799385d85e83f42835eb6e70ffb0b5a2b868e1f9
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372650"
---
# <a name="what-is-service-connector"></a>¿Qué es el conector de servicio?

El servicio del conector de servicio le ayuda a conectar el servicio de proceso de Azure a otros servicios de respaldo fácilmente. Este servicio establece la configuración de red y la información de conexión (por ejemplo, la generación de variables de entorno) entre el servicio de proceso y el servicio de respaldo de destino en el plano de administración. Los desarrolladores solo usan el SDK o la biblioteca preferidos que consumen la información de conexión para realizar operaciones del plano de datos en el servicio de respaldo de destino. 

En este artículo se proporciona información general sobre el servicio del conector de servicio.

## <a name="what-is-service-connector-used-for"></a>¿Para qué se usa el conector de servicio?

Cualquier aplicación que se ejecute en los servicios de proceso de Azure y requiera un servicio de respaldo, puede usar el conector de servicio. Enumeramos algunos ejemplos que pueden usar el conector de servicio para simplificar la experiencia de conexión de servicio a servicio.

* **WebApp + DB:** use el conector de servicio para conectar PostgreSQL, MySQL, SQL DB o Cosmos DB a su App Service.  
* **WebApp + Storage:** use el conector de servicio para conectarse a cuentas de Azure Storage y usar fácilmente sus productos de almacenamiento preferidos en su App Service.
* **Spring Cloud + Database:** use el conector de servicio para conectar PostgreSQL, MySQL, SQL DB o Cosmos DB a la aplicación Spring Cloud.
* **Spring Cloud + Apache Kafka:** el conector de servicio puede ayudarle a conectar la aplicación Spring Cloud a Apache Kafka en Confluent Cloud.

Consulte [¿Qué servicios se admiten en el conector de servicio?](#what-services-are-supported-in-service-connector) para ver más servicios y patrones de aplicación admitidos.

## <a name="what-are-the-benefits-using-service-connector"></a>¿Cuáles son las ventajas de usar el conector de servicio?

**Conectar el servicio de respaldo de destino con un solo comando o unos pocos clics:**

El conector de servicio está diseñado para facilitar su uso. Solicita tres parámetros necesarios, como la instancia del servicio de destino, el tipo de autenticación entre el servicio de proceso y el servicio de destino y el tipo de cliente de aplicación para crear una conexión. Los desarrolladores pueden usar la CLI de Conexión de Azure o la experiencia de Azure Portal para crear conexiones fácilmente.

**Use Estado de conexión para supervisar o identificar el problema de conexión:**

Una vez creada una conexión de servicio. Los desarrolladores pueden validar y comprobar el estado de mantenimiento de la conexión. El conector de servicio puede sugerir acciones para corregir las conexiones rotas.

## <a name="what-services-are-supported-in-service-connector"></a>¿Qué servicios se admiten en el conector de servicio?

> [!NOTE]
> El conector de servicio está versión preliminar pública. El equipo del producto está agregando activamente más tipos de servicio admitidos en la lista.

**Servicio de proceso:**

* Azure App Service
* Azure Spring Cloud

**Servicio de destino:**

* Azure Database for PostgreSQL
* Azure Database for MySQL
* Azure Cosmos DB (SQL, MangoDB, Gremlin, Cassandra, Table)
* Azure Storage (Blob, Queue, File y Table Storage)
* Azure Key Vault
* Servicio Azure SignalR
* Apache Kafka en Confluent Cloud

## <a name="how-to-use-service-connector"></a>¿Cómo usar el conector de servicio?

Hay dos maneras principales de usar el conector de servicio para la aplicación de Azure:

* **CLI de conexión de Azure:** cree, enumere valide y elimine conexiones de servicio a servicio con el grupo de comandos de conexión de la CLI de Azure.
* **Experiencia del conector de servicio en Azure Portal:** use la experiencia del portal guiado para crear conexiones de servicio a servicio y administrar conexiones con una lista de jerarquías.

## <a name="next-steps"></a>Pasos siguientes

Complete los tutoriales que se indican a continuación para empezar a compilar su propia aplicación con el conector de servicio.

> [!div class="nextstepaction"]
> [Inicio rápido: conector de servicio en App Service mediante la CLI de Azure](./quickstart-cli-app-service-connection.md)

> [!div class="nextstepaction"]
> [Inicio rápido: conector de servicio en App Service mediante Azure Portal](./quickstart-portal-app-service-connection.md)

> [!div class="nextstepaction"]
> [Inicio rápido: conector de servicio en Spring Cloud Service mediante la CLI de Azure](./quickstart-cli-spring-cloud-connection.md)

> [!div class="nextstepaction"]
> [Inicio rápido: conector de servicio en Spring Cloud Service mediante Azure Portal](./quickstart-portal-spring-cloud-connection.md)

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)
