---
title: Registro de esquema de Azure en Azure Event Hubs
description: En este artículo se proporciona información general sobre la compatibilidad del registro de esquema de Azure Event Hubs.
ms.topic: overview
ms.date: 10/21/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: a4ecf4d9205c5110e54e00263cfb17d51ba4b482
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016314"
---
# <a name="azure-schema-registry-in-azure-event-hubs"></a>Registro de esquema de Azure en Azure Event Hubs
En muchos escenarios de streaming de eventos y mensajería, la carga de eventos o mensajes contiene datos estructurados. El formato controlado por esquemas, como [Apache Avro](https://avro.apache.org/), se suele usar para serializar o deserializar estos datos estructurados. 

:::image type="content" source="./media/schema-registry-overview/schema-driven-ser-de.png" alt-text="Serialización o deserialización basada en esquemas":::

Las aplicaciones de productor usan documentos de esquema para serializar la carga del evento y publicarla en un agente de eventos como Event Hubs. De forma similar, las aplicaciones de consumidor leen la carga del evento del agente y la deserializan con el mismo documento de esquema. Por lo tanto, tanto los productores como los consumidores pueden validar la integridad de los datos con un documento de esquema. 


## <a name="why-we-need-a-schema-registry"></a>¿Por qué necesitamos un registro de esquema? 
Cuando se usan formatos basados en esquemas, las aplicaciones de productor quieren que los esquemas de los eventos publicados estén disponibles para los consumidores. Aunque es posible compartir el esquema correspondiente con todos y cada uno de los datos de eventos, eso resulta ineficaz. Cuando los nuevos consumidores quieran consumir datos de eventos, deben tener una manera de comprender el formato de los datos que se publican. También es necesario asegurarse de que se admite la evolución del esquema para que tanto los productores como los consumidores puedan evolucionar a velocidades diferentes. 


## <a name="azure-schema-registry"></a>Registro de esquema de Azure
El **registro de esquema de Azure** es una característica de Event Hubs, que proporciona un repositorio central para los documentos de esquema de aplicaciones controladas por eventos y basadas en mensajería. Ofrece la flexibilidad para que las aplicaciones de productor y consumidor intercambien datos sin tener que administrar y compartir el esquema. El registro de esquema también proporciona un marco de gobierno sencillo para los esquemas reutilizables y define la relación entre los esquemas a través de una construcción de agrupación (grupos de esquemas).

:::image type="content" source="./media/schema-registry-overview/schema-registry.png" alt-text="Registro de esquema":::

Con marcos de serialización orientados a esquemas, como Apache Avro, la externalización de los metadatos de serialización en esquemas compartidos también puede ayudar a reducir drásticamente la sobrecarga por mensaje de información de tipo y nombres de campo incluidos con cada conjunto de datos, como es el caso de los formatos etiquetados como JSON. Al almacenar los esquemas junto con los eventos y dentro de la infraestructura de eventos se garantiza que los metadatos necesarios para la serialización o la deserialización estén siempre al alcance y los esquemas no se puedan perder. 

> [!NOTE]
> La característica no está disponible en el nivel **básico**.

## <a name="schema-registry-information-flow"></a>Flujo de información del registro de esquema 
El flujo de información cuando se usa el registro de esquema es el mismo para todo el protocolo que se emplea para publicar o consumir eventos de Azure Event Hubs. En el diagrama siguiente se muestra el flujo de información de un escenario de consumidor y productor de eventos de Kafka que usa el registro de esquema. 

:::image type="content" source="./media/schema-registry-overview/information-flow.png" lightbox="./media/schema-registry-overview/information-flow.png" alt-text="Imagen que muestra el flujo de información del registro de esquemas.":::


El flujo de información comienza desde el productor, donde los productores de Kafka serializan los datos mediante el documento de esquema. 
- La aplicación de productor de Kafka usa ``KafkaAvroSerializer`` para serializar los datos de eventos mediante el esquema especificado en el cliente. 
- La aplicación de productor debe proporcionar los detalles del punto de conexión del registro de esquema y otros parámetros opcionales necesarios para la validación del esquema. 
- El serializador realiza una búsqueda en el registro de esquema mediante el contenido del esquema que el productor usa para serializar los datos de eventos. 
- Si encuentra este tipo de esquema, se devuelve el identificador de esquema correspondiente. Puede configurar la aplicación de productor en "If the schema does not exist" (Si el esquema no existe) y la aplicación de productor puede configurar el cliente del registro de esquema para registrar el esquema. 
- A continuación, el serializador usa ese identificador de esquema y lo antepone a los datos serializados que se publican en Event Hubs. 
- En el consumidor, ``KafkaAvroDeserializer`` usa el identificador de esquema para recuperar el contenido del esquema del registro de esquema. 
- Luego, el deserializador usa el contenido del esquema para deserializar los datos de eventos que lee en el centro de eventos. 
- Los clientes del registro de esquema usan el almacenamiento en caché para evitar búsquedas redundantes del registro de esquema.  


## <a name="schema-registry-elements"></a>Elementos del registro de esquema

Un espacio de nombres de Event Hubs ahora puede hospedar grupos de esquemas junto con Event Hubs (o temas de Kafka). Hospeda un registro de esquema y puede tener varios grupos de esquemas. A pesar de estar hospedado en Azure Event Hubs, el registro de esquema se puede usar universalmente con todos los servicios de mensajería de Azure y cualquier otro agente de mensajes o eventos. Cada uno de estos grupos de esquemas es un repositorio protegible de forma independiente para un conjunto de esquemas. Los grupos se pueden alinear con una aplicación determinada o una unidad organizativa. 

:::image type="content" source="./media/schema-registry-overview/elements.png" alt-text="Imagen que muestra los elementos del registro de esquemas.":::


### <a name="schema-groups"></a>Grupos de esquemas
El grupo de esquemas es un grupo lógico de esquemas similares basados en los criterios empresariales. Un grupo de esquemas puede contener varias versiones de un esquema. La configuración del cumplimiento de compatibilidad de un grupo de esquemas puede ayudar a garantizar que las versiones del esquema más recientes sean compatibles con versiones anteriores.

El límite de seguridad impuesto por el mecanismo de agrupación ayuda a garantizar que los secretos comerciales no se filtran accidentalmente a través de los metadatos en situaciones en las que el espacio de nombres se comparte entre varios asociados. También permite a los propietarios de aplicaciones administrar esquemas independientes de otras aplicaciones que comparten el mismo espacio de nombres.

### <a name="schemas"></a>Esquemas
Los esquemas definen el contrato entre los productores y los consumidores. Un esquema definido en un registro de esquema de Event Hubs ayuda a administrar el contrato fuera de los datos de eventos, lo que elimina la sobrecarga. Un esquema tiene un nombre, un tipo (por ejemplo, registro, matriz, etc.), el modo de compatibilidad (ninguna, con versiones anteriores o posteriores, completa) y el tipo de serialización (solo Avro por ahora). Puede crear varias versiones de un esquema y recuperar y usar una versión específica de un esquema. 

## <a name="schema-evolution"></a>Evolución del esquema 

Los esquemas deben evolucionar con el requisito empresarial de productores y consumidores. El registro de esquema de Azure admite la evolución del esquema mediante la introducción de modos de compatibilidad a nivel de grupo de esquemas. Al crear un grupo de esquemas, puede especificar el modo de compatibilidad de los esquemas que incluya en ese grupo. Al actualizar un esquema, el cambio debe cumplir con el modo de compatibilidad asignado y, así, solo se crea una nueva versión del esquema. 

El registro de esquema de Azure para Event Hubs admite los siguientes modos de compatibilidad. 

### <a name="backward-compatibility"></a>compatibilidad con versiones anteriores
El modo de compatibilidad con versiones anteriores permite que el código de consumidor use una nueva versión del esquema, pero se pueden procesar mensajes con una versión anterior del esquema. Cuando se usa el modo de compatibilidad con versiones anteriores en un grupo de esquemas, se permiten los siguientes cambios en un esquema. 

- Eliminar campos. 
- Agregar campos opcionales. 


### <a name="forward-compatibility"></a>Compatibilidad con versiones posteriores
La compatibilidad con versiones posteriores permite que el código de consumidor use una versión anterior del esquema, pero puede leer mensajes con el nuevo esquema. El modo de compatibilidad con versiones posteriores permite realizar los siguientes cambios en un esquema. 
- Adición de campos 
- Eliminar campos opcionales 


### <a name="no-compatibility"></a>Sin compatibilidad
Cuando se usa el modo de compatibilidad ``None``, el registro de esquema no comprueba la compatibilidad al actualizar los esquemas. 

## <a name="client-sdks"></a>SDK de cliente

Puede usar una de las siguientes bibliotecas que incluyen un serializador de Avro, que puede emplear para serializar y deserializar las cargas que contienen identificadores de esquema del registro de esquema y datos codificados con Avro.

- [.NET: Microsoft.Azure.Data.SchemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java: azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/schemaregistry/azure-data-schemaregistry-apacheavro)
- [Python: azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript: @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/): ejecute los serializadores y deserializadores de Apache Avro integrados en Kafka con el respaldo del registro de esquema de Azure. El serializador de cliente de Apache Kafka del cliente de Java para el registro de esquema de Azure se puede usar en cualquier escenario de Apache Kafka y con cualquier servicio en la nube o implementación basada en Apache Kafka®. 

## <a name="limits"></a>Límites
Para conocer los límites (por ejemplo, el número de grupos de esquemas de un espacio de nombres) de Event Hubs, consulte [Cuotas y límites de Event Hubs](event-hubs-quotas.md).

## <a name="azure-role-based-access-control"></a>Control de acceso basado en roles de Azure
Al acceder al registro de esquema mediante programación, debe registrar una aplicación en Azure Active Directory (Azure AD) y agregar la entidad de seguridad de la aplicación a uno de los roles de control de acceso basado en rol de Azure (Azure RBAC):

| Role | Descripción | 
| ---- | ----------- | 
| Propietario | Leer, escribir y eliminar esquemas y grupos de registros de esquema. |
| Colaborador | Leer, escribir y eliminar esquemas y grupos de registros de esquema. |
| [Lector del registro de esquema](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Leer y enumerar grupos y esquemas del registro de esquemas. |
| [Colaborador del registro de esquema](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Leer, escribir y eliminar esquemas y grupos de registros de esquema. |

Para obtener instrucciones sobre cómo crear el registro de una aplicación mediante Azure Portal, consulte [Registro de una aplicación con Azure AD](../active-directory/develop/quickstart-register-app.md). Anote el id. de cliente (id. de aplicación), el id de inquilino y el secreto que se usarán en el código. 

## <a name="next-steps"></a>Pasos siguientes

- Para información sobre cómo crear un registro de esquema mediante Azure Portal, vea [Creación de un registro de esquema de Event Hubs con Azure Portal](create-schema-registry.md).
- Vea los siguientes ejemplos de la **biblioteca de cliente Avro del registro de esquema**.
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/schemaregistry/azure-data-schemaregistry-apacheavro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Integración de Kafka Avro para el registro de esquema de Azure](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
