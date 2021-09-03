---
title: Enlace de Azure Cosmos DB con la aplicación Azure Spring Cloud
description: Aprenda a enlazar Azure Cosmos DB con la aplicación Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 387d526002411395e8bebc0fa59925bfa383e598
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861333"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Enlace de una base de datos de Azure Cosmos DB a una aplicación de Azure Spring Cloud

**Este artículo se aplica a:** ✔️ Java

En lugar de configurar manualmente las aplicaciones de Spring Boot, puede enlazar automáticamente servicios de Azure seleccionados a las aplicaciones mediante Azure Spring Cloud. En este artículo se muestra cómo enlazar la aplicación a una base de datos de Azure Cosmos DB.

Requisitos previos:

* Una instancia de Azure Spring Cloud implementada. Para comenzar, siga nuestro [inicio rápido sobre la implementación mediante la CLI de Azure](./quickstart.md).
* Una cuenta de Azure Cosmos DB con un nivel mínimo de permisos de colaborador.

## <a name="prepare-your-java-project"></a>Preparación del proyecto de Java

1. Agregue una de las siguientes dependencias al archivo pom.xml de la aplicación de Azure Spring Cloud. Elija la dependencia que sea adecuada para el tipo de API.

    * Tipo de API: Core (SQL)

      ```xml
      <dependency>
          <groupId>com.azure.spring</groupId>
          <artifactId>azure-spring-boot-starter-cosmos</artifactId>
          <version>3.6.0</version>
      </dependency>
      ```

    * Tipo de API: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * Tipo de API: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * Tipo de API: tabla de Azure

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Actualice la aplicación actual mediante la ejecución de `az spring-cloud app deploy` o cree una implementación para este cambio mediante la ejecución de `az spring-cloud app deployment create`.

## <a name="bind-your-app-to-the-azure-cosmos-db"></a>Enlace de la aplicación a Azure Cosmos DB

#### <a name="service-binding"></a>[enlace del servicio](#tab/Service-Binding)
Azure Cosmos DB tiene cinco tipos de API diferentes que admiten el enlace. En el procedimiento siguiente se muestra cómo usarlas:

1. Crea una base de datos de Azure Cosmos DB. Consulte el inicio rápido sobre la [creación de una base de datos](../cosmos-db/create-cosmosdb-resources-portal.md) para obtener ayuda.

1. Registre el nombre de la base de datos. En este procedimiento, el nombre de la base de datos es **testdb**.

1. Vaya a la página del servicio Azure Spring Cloud en Azure Portal. Vaya a **Application Dashboard** (Panel de la aplicación) y seleccione la aplicación que va a enlazar a Azure Cosmos DB. Esta aplicación es la misma que actualizó o implementó en el paso anterior.

1. Seleccione **Service binding** (Enlace de servicio) y seleccione **Create service binding** (Crear enlace de servicio). Para rellenar el formulario, seleccione:
   * El valor de **Binding type** (Tipo de enlace) de **Azure Cosmos DB**.
   * El tipo de API.
   * El nombre de la base de datos.
   * La cuenta de Azure Cosmos DB.

    > [!NOTE]
    > Si usa Cassandra, utilice un espacio de claves para el nombre de la base de datos.

1. Seleccione **Restart** (Reiniciar) en la página de la aplicación para reiniciar la aplicación.

1. Para tener la seguridad de que el servicio esté enlazado correctamente, seleccione el nombre del enlace y compruebe sus detalles. El campo `property` debe ser parecido a este ejemplo:

    ```properties
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)
El siguiente script de Terraform muestra cómo configurar una aplicación Azure Spring Cloud con la API de MongoDB de Azure Cosmos DB.

```terraform
provider "azurerm" {
  features {}
}

variable "application_name" {
  type        = string
  description = "The name of your application"
  default     = "demo-abc"
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_cosmosdb_account" "cosmosdb" {
  name                = "cosmosacct-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  offer_type          = "Standard"
  kind                = "MongoDB"

  consistency_policy {
    consistency_level = "Session"
  }

  geo_location {
    failover_priority = 0
    location          = azurerm_resource_group.example.location
  }
}

resource "azurerm_cosmosdb_mongo_database" "cosmosdb" {
  name                = "cosmos-${var.application_name}-001"
  resource_group_name = azurerm_cosmosdb_account.cosmosdb.resource_group_name
  account_name        = azurerm_cosmosdb_account.cosmosdb.name
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "${var.application_name}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}

resource "azurerm_spring_cloud_app" "example" {
  name                = "${var.application_name}-app"
  resource_group_name = azurerm_resource_group.example.name
  service_name        = azurerm_spring_cloud_service.example.name
  is_public           = true
  https_only          = true
}

resource "azurerm_spring_cloud_java_deployment" "example" {
  name                = "default"
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  cpu                 = 2
  memory_in_gb        = 4
  instance_count      = 2
  jvm_options         = "-XX:+PrintGC"
  runtime_version     = "Java_11"

  environment_variables = {
    "azure.cosmosdb.uri" : azurerm_cosmosdb_account.cosmosdb.connection_strings[0]
    "azure.cosmosdb.database" : azurerm_cosmosdb_mongo_database.cosmosdb.name
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido a enlazar una aplicación de Azure Spring Cloud a una base de datos de Azure Cosmos DB. Para más información sobre el enlace de servicios a una aplicación, consulte [Enlace a una caché de Azure Cache for Redis](./how-to-bind-redis.md).
