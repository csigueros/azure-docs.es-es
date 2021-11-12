---
title: Aspectos internos de Service Connector
description: Obtenga información sobre los aspectos internos de Service Conector, la arquitectura, las conexiones y cómo se transmiten los datos.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fda89409a39644057ebd76d949665b281d32dc2a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501673"
---
# <a name="service-connector-internals"></a>Aspectos internos de Service Connector

Service Connector es un proveedor de recursos de extensión que proporciona una manera sencilla de crear y administrar conexiones entre servicios.
- Admite bases de datos principales, almacenamiento, servicios en tiempo real, estados y almacenes secretos que se usan junto con la aplicación nativa en la nube (la lista se expande activamente).
- Establezca la configuración de red, la autenticación y administre las variables o propiedades del entorno de conexión mediante la creación de una conexión de servicio con un solo comando o unos pocos clics.
- Valide las conexiones y busque las sugerencias correspondientes para corregir una conexión de servicio. 

## <a name="service-connection-overview"></a>Información general sobre el servicio

La conexión de servicio es el concepto clave en el modelo de recursos de Service Connector. En Service Connector, una conexión de servicio representa una abstracción del vínculo entre dos servicios. Las siguientes propiedades se definen en la conexión de servicio.

| Propiedad | Descripción |
|--------|-----------|
| Nombre de la conexión | Nombre único de la conexión de servicio.  |
| Tipo de servicio de origen | Los servicios de origen suelen ser servicios de proceso de Azure. Las funcionalidades de Service Connector se pueden encontrar en los servicios de proceso admitidos mediante la extensión de estos proveedores de servicios de proceso de Azure.  |
| Tipo de servicio de destino | Los servicios de destino son servicios de respaldo o servicios de dependencia a los que se conectan los servicios de proceso. Service Connector admite varios tipos de servicio de destino, como bases de datos principales, almacenamiento, servicios en tiempo real, estados y almacenes secretos. |
| Tipo de cliente | El tipo de cliente hace referencia a la pila de tiempo de ejecución de proceso, el marco de desarrollo o el tipo de biblioteca cliente específico, que acepta el formato específico de las propiedades o variables de entorno de conexión. |
| Tipo de autenticación | Tipo de autenticación utilizado de la conexión de servicio. Podría ser una cadena de conexión o secreto puro, una identidad administrada o una entidad de servicio. |

Puede crear varias conexiones de servicio desde una instancia de servicio de origen si la instancia necesita conectar varios recursos de destino. Y el mismo recurso de destino se puede conectar desde varias instancias de origen. Service Connector administrará todas las conexiones en las propiedades de su instancia de origen. Esto significa que puede crear, obtener, actualizar y eliminar las conexiones en el portal o el comando de la CLI de su instancia de servicio de origen. 

La conexión admite entre suscripciones o inquilinos. El servicio de origen y destino puede pertenecer a diferentes suscripciones o inquilinos. Cuando se crea una nueva conexión de servicio, el recurso de conexión se encuentra en la misma región con la instancia del servicio de proceso de forma predeterminada.

## <a name="create-or-update-a-service-connection"></a>Crear o actualizar una conexión de servicio

Service Connector realizará varios pasos al crear o actualizar una conexión, entre los que se incluyen:

- Configurar la red de recursos de destino y la configuración del firewall, asegurando de que los servicios de origen y de destino pueden hablar entre sí en el nivel de red.
- Configurar la información de conexión en el recurso de origen
- Configurar la información de autenticación sobre el origen y el destino si es necesario
- Si se produce un error, crear o actualizar la compatibilidad con la conexión.

La creación y actualización de una conexión contiene varios pasos. Si se ha dado un error en algún paso, Service Connector revertirá todos los pasos anteriores para mantener la configuración inicial en las instancias de origen y de destino.

## <a name="connection-configurations"></a>Configuraciones de conexión

Una vez creada una conexión de servicio, la configuración de conexión se establecerá en el servicio de origen.
En el portal, vaya a la página de **Service Connector (versión preliminar)** . Puede expandir cada conexión y ver las configuraciones de conexión.

:::image type="content" source="media/tutorial-java-spring-confluent-kafka/portal-list-connection-config.png" alt-text="Configuración del portal de la lista":::

En la CLI, puede usar el comando `list-configuration` para ver la configuración de conexión.

```azurecli
az webapp connection list-configuration -g {webapp_rg} -n {webapp_name} --connection {service_connection_name}
```

```azurecli
az spring-cloud connection list-configuration -g {spring_cloud_rg} -n {spring_cloud_name} --connection {service_connection_name}
```

## <a name="configuration-naming-convention"></a>Convención de nomenclatura de configuración

Service Connector establece la configuración (variables de entorno o configuraciones de Spring Boot) al crear una conexión. El tipo de cliente y el tipo de autenticación determinan los pares clave-valor de la variable de entorno. Por ejemplo, el uso del SDK de Azure con identidad administrada requiere el identificador de cliente, el secreto de cliente, etc. El uso del controlador JDBC requiere una cadena de conexión de base de datos. La regla de nomenclatura de la configuración es la siguiente.

Si usa **Spring Boot** como el tipo de cliente:

* La biblioteca de Spring Boot para cada servicio de destino tiene su propia convención de nomenclatura. Por ejemplo, la configuración de conexión de MySQL sería `spring.datasource.url`, `spring.datasource.username`, `spring.datasource.password`. La configuración de conexión de Kafka sería `spring.kafka.properties.bootstrap.servers`.

Si usa **otros tipos de cliente** excepto Spring Boot:

* Cuando se conecta a un servicio de destino, el nombre de clave de la primera configuración de conexión tiene el formato `{Cloud}_{Type}_{Name}`. Por ejemplo, `AZURE_STORAGEBLOB_RESOURCEENDPOINT`, `CONFLUENTCLOUD_KAFKA_BOOTSTRAPSERVER`. 
* Para el mismo tipo de recurso de destino, el nombre de clave de la segunda configuración de conexión tendrá el formato `{Cloud}_{Type}_{Connection Name}_{Name}`. Por ejemplo, `AZURE_STORAGEBLOB_CONN2_RESOURCEENDPOINT`, `CONFLUENTCLOUD_KAFKA_CONN2_BOOTSTRAPSERVER`.

## <a name="validate-a-service-connection"></a>Validar una conexión de servicio
Se comprobarán los siguientes elementos al validar la conexión:

* Validar si existen recursos de origen y de destino
* Validar la configuración de firewall y red de recursos de destino
* Validar la información de conexión en el recurso de origen
* Validar la información de autenticación sobre el origen y el destino si es necesario

## <a name="delete-connection"></a>Eliminar conexión

La información de conexión sobre el recurso de origen se eliminará al eliminar la conexión. 
