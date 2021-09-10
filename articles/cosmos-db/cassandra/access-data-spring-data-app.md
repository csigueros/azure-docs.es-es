---
title: Uso de Spring Data Apache Cassandra API con Azure Cosmos DB
description: Aprenda a usar Spring Data Apache Cassandra API con Azure Cosmos DB.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: how-to
ms.date: 07/17/2021
ms.openlocfilehash: 92f9045891aa56589e13d887dfea6c3157292185
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780972"
---
# <a name="how-to-use-spring-data-apache-cassandra-api-with-azure-cosmos-db"></a>Uso de Spring Data Apache Cassandra API con Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

En este artículo se explica cómo crear una aplicación de ejemplo que utiliza [Spring Data] para almacenar y recuperar información mediante [Cassandra API de Azure Cosmos DB](/azure/cosmos-db/cassandra-introduction).

## <a name="prerequisites"></a>Prerrequisitos

Los siguientes requisitos previos son necesarios para seguir los pasos descritos en este artículo:

* Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN] o registrarse para obtener una [cuenta de Azure gratuita].
* Un kit de desarrollo de Java (JDK) admitido Para más información sobre los JDK disponibles para desarrollar en Azure, consulte [Compatibilidad con Java en Azure y Azure Stack](/azure/developer/java/fundamentals/java-support-on-azure).
* [Apache Maven](http://maven.apache.org/), versión 3.0 o posterior.
* [Curl ](https://curl.haxx.se/) o una utilidad HTTP similar para probar la funcionalidad.
* Un cliente [Git](https://git-scm.com/downloads).

> [!NOTE]
> En los ejemplos mencionados a continuación se implementan extensiones personalizadas para mejorar la experiencia al usar Cassandra API de Azure Cosmos DB. Incluyen directivas personalizadas de reintento y equilibrio de carga, además de implementar la configuración de conexión recomendada. Para obtener más detalles sobre cómo se usan las directivas personalizadas, consulte los ejemplos de Java para la [versión 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample) y la [versión 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample-v4). 

## <a name="create-a-cosmos-db-cassandra-api-account"></a>Creación de una cuenta de Cassandra API de Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="configure-the-sample-application"></a>Configurar la aplicación de ejemplo

En el siguiente procedimiento se configura la aplicación de prueba.

1. Abra un shell de comandos y clone cualquiera de los ejemplos siguientes:

   Para el [controlador Java versión 3](https://github.com/datastax/java-driver/tree/3.x) y la versión de Spring correspondiente:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure-extension-v3.git
   ```
   
   Para el [controlador Java versión 4](https://github.com/datastax/java-driver/tree/4.x) y la versión de Spring correspondiente:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure-extension-v4.git
   ```     

    > [!NOTE]    
    > Aunque el uso descrito a continuación es idéntico para los ejemplos de la versión 3 y 4 anteriores, la manera en que se han implementado para incluir directivas personalizadas de reintento y equilibrio de carga es diferente. Se recomienda revisar el código para comprender cómo implementar directivas personalizadas si va a realizar cambios en una aplicación de Spring Java existente.  

1. Busque el archivo *application.properties* en el directorio *resources* del proyecto de ejemplo, o cree el archivo si todavía no existe.

1. Abra el archivo *application.properties* en un editor de texto y agréguele o configure las siguientes líneas; luego, sustituya los valores de ejemplo por los valores adecuados que se mencionaron anteriormente:

   ```yaml
   spring.data.cassandra.contact-points=<Account Name>.cassandra.cosmos.azure.com
   spring.data.cassandra.port=10350
   spring.data.cassandra.username=<Account Name>
   spring.data.cassandra.password=********
   ```

   Donde:

   | Parámetro | Descripción |
   |---|---|
   | `spring.data.cassandra.contact-points` | Especifica el **punto de contacto** que se mencionó anteriormente en este artículo. |
   | `spring.data.cassandra.port` | Especifica el **puerto** que se mencionó anteriormente en este artículo. |
   | `spring.data.cassandra.username` | Especifica el **nombre de usuario** que se mencionó anteriormente en este artículo. |
   | `spring.data.cassandra.password` | Especifica la **contraseña principal** que se mencionó anteriormente en este artículo. |

1. Guarde y cierre el archivo *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Empaquetado y prueba de la aplicación de ejemplo 

Vaya al directorio que contiene el archivo. archivo .pom para compilar y probar la aplicación.

1. Compile la aplicación de ejemplo con Maven; por ejemplo:

   ```shell
   mvn clean package
   ```

1. Inicie la aplicación de ejemplo; por ejemplo:

   ```shell
   java -jar target/spring-data-cassandra-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. Cree nuevos registros con `curl` desde un símbolo del sistema como en los ejemplos siguientes:

   ```shell
   curl -s -d "{\"name\":\"dog\",\"species\":\"canine\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d "{\"name\":\"cat\",\"species\":\"feline\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets
   ```

   La aplicación debe devolver valores similares a los siguientes:

   ```shell
   Added Pet{id=60fa8cb0-0423-11e9-9a70-39311962166b, name='dog', species='canine'}.

   Added Pet{id=72c1c9e0-0423-11e9-9a70-39311962166b, name='cat', species='feline'}.
   ```

1. Recupere todos los registros existentes con `curl` desde un símbolo del sistema como los siguientes ejemplos:

   ```shell
   curl -s http://localhost:8080/pets
   ```

   La aplicación debe devolver valores similares a los siguientes:

   ```json
   [{"id":"60fa8cb0-0423-11e9-9a70-39311962166b","name":"dog","species":"canine"},{"id":"72c1c9e0-0423-11e9-9a70-39311962166b","name":"cat","species":"feline"}]
   ```

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de Spring y Azure, vaya al centro de documentación de Azure.

> [!div class="nextstepaction"]
> [Spring en Azure](../../index.yml)

### <a name="additional-resources"></a>Recursos adicionales

Para más información sobre el uso de Azure con Java, consulte [Azure para desarrolladores de Java] y [Working with Azure DevOps and Java] (Trabajo con Azure DevOps y Java).

<!-- URL List -->

[Azure para desarrolladores de Java]: ../index.yml
[cuenta de Azure gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Trabajo con Azure DevOps y Java)
[ventajas como suscriptor de MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[COSMOSDB01]: media/access-data-spring-data-app/create-cosmos-db-01.png
[COSMOSDB02]: media/access-data-spring-data-app/create-cosmos-db-02.png
[COSMOSDB03]: media/access-data-spring-data-app/create-cosmos-db-03.png
[COSMOSDB04]: media/access-data-spring-data-app/create-cosmos-db-04.png
[COSMOSDB05]: media/access-data-spring-data-app/create-cosmos-db-05.png
[COSMOSDB05-1]: media/access-data-spring-data-app/create-cosmos-db-05-1.png
[COSMOSDB06]: media/access-data-spring-data-app/create-cosmos-db-06.png