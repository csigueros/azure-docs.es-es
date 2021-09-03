---
title: Acceso del indexador a los recursos protegidos
titleSuffix: Azure Cognitive Search
description: Información general conceptual de las opciones de seguridad de nivel de red para el acceso a datos de Azure mediante indexadores en Azure Cognitive Search.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: dc89bfcd3d89e6987c2c8b742f5fe2453c273fc7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746282"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features"></a>Acceso del indizador a orígenes de datos mediante las características de seguridad de red de Azure

Los indexadores de Azure Cognitive Search pueden realizar llamadas salientes a varios recursos de Azure durante la ejecución. En este artículo se explican los conceptos relativos al acceso del indexador al contenido protegido por firewalls de IP, puntos de conexión privados y otros mecanismos de seguridad de nivel de red de Azure. 

Un indizador realiza llamadas salientes en dos situaciones:

- Al conectarse a orígenes de datos externos durante la indexación
- Al conectarse a código encapsulado externo por medio de un conjunto de aptitudes

En la tabla siguiente se enumeran los posibles tipos de recursos a los que puede acceder un indexador en una ejecución habitual.

| Recurso | Propósito dentro de la ejecución del indexador |
| --- | --- |
| Azure Storage (blobs, tablas, ADLS Gen 2) | Origen de datos |
| Azure Storage (blobs, tablas) | Conjuntos de aptitudes (almacenamiento en caché de documentos enriquecidos y almacenamiento de proyecciones del almacén de conocimiento) |
| Azure Cosmos DB (varias API) | Origen de datos |
| Azure SQL Database | Origen de datos |
| SQL Server en Azure Virtual Machines | Origen de datos |
| Instancia administrada de SQL | Origen de datos |
| Azure Functions | Host para aptitudes de API web personalizadas |
| Cognitive Services | Está asociado al conjunto de habilidades que se usará para facturar el enriquecimiento por encima del límite de 20 documentos gratuitos |

> [!NOTE]
> El recurso de Cognitive Services asociado a un conjunto de aptitudes se usa para la facturación, en función de los enriquecimientos realizados y escritos en el índice de búsqueda. No se utiliza para el acceso a las API de Cognitive Services. El acceso desde la canalización de enriquecimiento de un indexador hasta las API de Cognitive Services se produce a través de un canal de comunicación seguro interno, donde los datos en tránsito están fuertemente cifrados y nunca se almacenan en reposo.

Los clientes pueden proteger estos recursos a través de varios mecanismos de aislamiento de red que ofrece Azure. A excepción de los recursos de Cognitive Services, los indexadores tienen una capacidad limitada para acceder a todos los demás recursos, incluso si están aislados de la red, como se describe en la tabla siguiente.

| Recurso | Restricción de IP | Punto de conexión privado |
| --- | --- | ---- |
| Azure Storage (blobs, tablas, ADLS Gen 2) | Solo se admite si la cuenta de almacenamiento y el servicio de búsqueda se encuentran en regiones diferentes. | Compatible |
| Azure Cosmos DB: SQL API | Compatible | Compatible |
| Azure Cosmos DB: MongoDB y Gremlin API | Compatible | No compatible |
| Azure SQL Database | Compatible | Compatible |
| SQL Server en Azure Virtual Machines | Compatible | N/D |
| Instancia administrada de SQL | Compatible | N/D |
| Azure Functions | Compatible | Se admite solo con determinados niveles de funciones de Azure. |

> [!NOTE]
> Además de las opciones enumeradas anteriormente, en el caso de las cuentas de Azure Storage protegidas por red, los clientes pueden aprovechar el hecho de que Azure Cognitive Search es un [servicio de Microsoft de confianza](../storage/common/storage-network-security.md#trusted-microsoft-services). Esto significa que un servicio de búsqueda concreto puede omitir las restricciones de red virtual o IP sobre la cuenta de almacenamiento, y puede que acceder a los datos que esta contiene si tiene habilitado el control de acceso basado en rol adecuado. Para más información, consulte [Conexiones del indexador mediante la excepción del servicio de confianza](search-indexer-howto-access-trusted-service-exception.md). Esta opción puede usarse en lugar de la ruta de restricciones de IP; en cualquier caso, la cuenta de almacenamiento o el servicio de búsqueda no se puedan mover a otra región.

Al elegir un mecanismo de acceso seguro, tenga en cuenta las restricciones siguientes:

- Un indexador no puede conectarse a un [punto de conexión de servicio de red virtual](../virtual-network/virtual-network-service-endpoints-overview.md). Los puntos de conexión públicos con credenciales, puntos de conexión privados, servicio de confianza y direccionamiento IP son las únicas metodologías admitidas para las conexiones de indexador.
- Un servicio de búsqueda siempre se ejecuta en la nube y no se puede aprovisionar en una red virtual específica que se ejecute de forma nativa en una máquina virtual. Esta funcionalidad no se ofrecerá en Azure Cognitive Search.
- Cuando los indexadores usan puntos de conexión privados (salientes) para acceder a los recursos, se pueden aplicar [cargos de vínculo privado](https://azure.microsoft.com/pricing/details/search/) adicionales.

## <a name="indexer-execution-environment"></a>Entorno de ejecución de los indexadores

Los indexadores de Azure Cognitive Search son capaces de extraer contenido de orígenes de datos de forma eficaz, agregar enriquecimientos al contenido extraído y, opcionalmente, generar proyecciones antes de escribir los resultados en el índice de búsqueda. En función del número de responsabilidades asignadas a un indexador, puede ejecutarse en dos entornos:

- Un entorno privado para un servicio de búsqueda específico. Los indexadores que se ejecutan en estos entornos comparten recursos con otras cargas de trabajo (por ejemplo, las de indexación o consulta iniciada por el cliente). Normalmente, solo los indexadores que realizan la indexación basada en texto (por ejemplo, no usan un conjunto de aptitudes) se ejecutan en este entorno.

- Un entorno de varios inquilinos que hospeda indexadores que consumen muchos recursos, como los que tienen conjuntos de aptitudes. Este entorno se usa para descargar el procesamiento intensivo a nivel computacional, lo que permite que los recursos específicos del servicio estén disponibles para las operaciones rutinarias. Microsoft administra y protege el entorno de varios inquilinos, sin costo adicional para el cliente.

Con cada ejecución de un indexador dado, Azure Cognitive Search determina el mejor entorno en el que ejecutarlo. Si usa un firewall IP para controlar el acceso a los recursos de Azure, conocer los entornos de ejecución le ayudará a configurar un intervalo de direcciones IP que sea inclusivo en ambos.

## <a name="granting-access-to-indexer-ip-ranges"></a>Concesión de acceso a los intervalos IP del indexador

Si el recurso al que intenta acceder el indexador está restringido a solo un determinado conjunto de intervalos IP, deberá expandir el conjunto para incluir los posibles intervalos IP desde los que se puede originar una solicitud del indexador. Como se indicó anteriormente, existen dos entornos posibles en los que se ejecutan los indexadores y de los que pueden originarse solicitudes de acceso. Tendrá que agregar las direcciones IP de **ambos** entornos para que el acceso del indexador funcione.

- Para obtener la dirección IP del entorno privado específico del servicio de búsqueda, use `nslookup` (o `ping`) con el nombre de dominio completo (FQDN) del servicio de búsqueda. Por ejemplo, el nombre de dominio completo de un servicio de búsqueda en la nube pública sería `<service-name>.search.windows.net`. Esta información está disponible en Azure Portal.
- Las direcciones IP de los entornos multiinquilino están disponibles a través de la etiqueta de servicio `AzureCognitiveSearch`. Las [etiquetas de servicio de Azure](../virtual-network/service-tags-overview.md) tienen un intervalo de direcciones IP publicado para cada servicio, que está disponible a través de una [API de detección (versión preliminar)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) o un [archivo JSON descargable](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). En cualquier caso, los intervalos IP se desglosan por región: solo puede elegir aquellos asignados para la región en la que se aprovisiona su servicio de búsqueda.

En el caso de determinados orígenes de datos, la propia etiqueta de servicio se puede usar directamente en lugar de enumerar la lista de intervalos IP (la dirección IP del servicio de búsqueda se debe seguir usando explícitamente). Estos orígenes de datos restringen el acceso por medio de la configuración de una [regla de grupo de seguridad de red](../virtual-network/network-security-groups-overview.md) que admite de forma nativa la adición de una etiqueta de servicio, a diferencia de las reglas IP, como las que ofrecen Azure Storage, Cosmos DB, Azure SQL, etc. Los orígenes de datos que admiten la posibilidad de utilizar la etiqueta de servicio `AzureCognitiveSearch` directamente, además de la dirección IP del servicio de búsqueda, son:

- [SQL Server en Azure Virtual Machines](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [Instancias administradas de SQL](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

Para más información sobre esta opción de conectividad, consulte [Conexiones del indexador mediante un firewall de IP](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Concesión de acceso a través de puntos de conexión privados

Los indexadores pueden utilizar [puntos de conexión privados](../private-link/private-endpoint-overview.md) para acceder a los recursos que están bloqueados para la selección de redes virtuales o que no tienen habilitado el acceso público.

Esta funcionalidad solo está disponible en los servicios de búsqueda facturables, con límites en el número de puntos de conexión privados que se crean. Para más información, consulte [Límites de servicio](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Paso 1: Creación de un punto de conexión privado al recurso seguro

Los clientes deben llamar a la operación de administración de búsqueda, [API CreateOrUpdate](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update) de un **recurso de vínculo privado compartido**, a fin de crear una conexión de punto de conexión privado a su recurso seguro (por ejemplo, una cuenta de almacenamiento). El tráfico que atraviesa esta conexión de punto de conexión privado (saliente) solo se originará en la red virtual que se encuentre en el entorno de ejecución "privado" del indexador específico del servicio de búsqueda.

Azure Cognitive Search validará que los autores de llamada de esta API tengan permisos RBAC de Azure para aprobar las solicitudes de conexión de punto de conexión privado al recurso seguro. Por ejemplo, si solicita una conexión de punto de conexión privado a una cuenta de almacenamiento con permisos de solo lectura, se rechazará esta llamada.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Paso 2: Aprobación de la conexión del punto de conexión privado

Cuando se complete la operación (asincrónica) que crea un recurso de vínculo privado compartido, se creará una conexión de punto de conexión privado en un estado "pendiente". Todavía no fluye el tráfico por la conexión.

Se espera entonces que el cliente encuentre esta solicitud en su recurso seguro y la apruebe. Normalmente, esta operación se puede hacer a través de Azure Portal o de la [API REST](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>Paso 3: Forzar la ejecución de los indexadores en el entorno "privado"

Un punto de conexión privado aprobado permite llamadas salientes del servicio de búsqueda a un recurso que tenga algún tipo de restricciones de acceso de nivel de red (por ejemplo, un origen de datos de la cuenta de almacenamiento que está configurado para que solo se pueda acceder a él desde determinadas redes virtuales).

Esto significa que cualquier indexador que pueda llegar a un origen de datos así a través del punto de conexión privado se ejecutará correctamente.
Si el punto de conexión privado no está aprobado, o si el indexador no utiliza la conexión de punto de conexión privado, la ejecución del indexador terminará en `transientFailure`.

Para permitir que los indexadores accedan a los recursos a través de conexiones de punto de conexión privado, es obligatorio establecer el valor de `executionEnvironment` del indexador en `"Private"` a fin de garantizar que todas las ejecuciones del indexador puedan usar el punto de conexión privado. El motivo es que los puntos de conexión privados se aprovisionan dentro del entorno privado específico del servicio de búsqueda.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

Estos pasos se describen con más detalle en [Conexiones del indexador mediante un punto de conexión privado](search-indexer-howto-access-private.md).
Una vez que tenga un punto de conexión privado aprobado a un recurso, los indexadores establecidos como *privados* intentarán acceder a través de la conexión de punto de conexión privado.

### <a name="limits"></a>Límites

Para garantizar un rendimiento y una estabilidad óptimos del servicio de búsqueda, se imponen restricciones (por nivel de servicio de búsqueda) sobre las dimensiones siguientes:

- Los tipos de indexadores que se pueden establecer como *privados*.
- El número de recursos de vínculo privado compartidos que se pueden crear.
- El número de tipos de recursos distintos para los que se pueden crear recursos de vínculo privado compartidos.

Estos límites se documentan en los [límites de servicio](search-limits-quotas-capacity.md).

## <a name="next-steps"></a>Pasos siguientes

- [Conexiones del indexador mediante firewalls de IP](search-indexer-howto-access-ip-restricted.md)
- [Conexiones del indexador mediante la excepción del servicio de confianza](search-indexer-howto-access-trusted-service-exception.md)
- [Conexiones del indexador a un punto de conexión privado](search-indexer-howto-access-private.md)