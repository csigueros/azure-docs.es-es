---
title: Integración de Apache Kafka en Confluent Cloud con Service Connector
description: Integración de Apache Kafka en Confluent Cloud en la aplicación con Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: c0b5c47982fb0b3af7b2ecac6fde12f1e2f2f101
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090776"
---
# <a name="integrate-apache-kafka-on-confluent-cloud-with-service-connector"></a>Integración de Apache Kafka en Confluent Cloud con Service Connector

En esta página se muestran los tipos de autenticación admitidos y los tipos de cliente de Apache kafka en Confluent Cloud con servicio que usan Service Connector. Es posible que todavía pueda conectarse a Apache Kafka en Confluent Cloud en otros lenguajes de programación sin usar Service Connector. Esta página también muestra el nombre y el valor predeterminados de la variable de entorno (o configuración de Spring Boot) que se obtiene al crear la conexión de servicio. Puede obtener más información sobre la [convención de nomenclatura de variables de entorno de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Servicio de proceso admitido

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Tipos de autenticación y tipos de cliente admitidos

| Tipo de cliente | Identidad administrada asignada por el sistema | Identidad administrada asignada por el usuario | Secret/ConnectionString | Entidad de servicio |
| --- | --- | --- | --- | --- |
| .Net | | | ![icono sí](./media/green-check.png) | |
| Java | | | ![icono sí](./media/green-check.png) | |
| Java: Spring Boot | | | ![icono sí](./media/green-check.png) | |
| Node.js | | | ![icono sí](./media/green-check.png) | |
| Python | | | ![icono sí](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>Nombres de variables de entorno o propiedades de aplicación predeterminados

### <a name="net-java-nodejs-and-python"></a>.NET, Java, Node.JS y Python

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_CONFLUENTCLOUDKAFKA_BOOTSTRAPSERVER | Su servidor de arranque de Kafka | `pkc-{serverName}.eastus.azure.confluent.cloud:9092` |
| AZURE_CONFLUENTCLOUDKAFKA_KAFKASASLCONFIG | Su configuración de SASL de Kafka | `org.apache.kafka.common.security.plain.PlainLoginModule required username='{bootstrapServerKey}' password='{bootstrapServerSecret}';` |
| AZURE_CONFLUENTCLOUDSCHEMAREGISTRY_URL | Su dirección URL de registro de Confluent | `https://psrc-{serverName}.westus2.azure.confluent.cloud` |
| AZURE_CONFLUENTCLOUDSCHEMAREGISTRY_USERINFO |  Su información de usuario de registro de Confluent | `{schemaRegistryKey} + ":" + {schemaRegistrySecret}` |

### <a name="spring-boot"></a>Spring Boot

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| spring.kafka.properties.bootstrap.servers | Su servidor de arranque de Kafka | `pkc-{serverName}.eastus.azure.confluent.cloud:9092` |
| spring.kafka.properties.sasl.jaas.config | Su configuración de SASL de Kafka | `org.apache.kafka.common.security.plain.PlainLoginModule required username='{bootstrapServerKey}' password='{bootstrapServerSecret}';` |
| spring.kafka.properties.schema.registry.url | Su dirección URL de registro de Confluent | `https://psrc-{serverName}.westus2.azure.confluent.cloud` |
| spring.kafka.properties.schema.registry.basic.auth.user.info | Su información de usuario de registro de Confluent | `{schemaRegistryKey} + ":" + {schemaRegistrySecret}` |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Service Connector, siga los tutoriales que se enumeran a continuación.

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)
