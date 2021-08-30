---
title: Enlace de Azure Cache for Redis a una aplicación de Azure Spring Cloud
description: Aprenda a enlazar Azure Cache for Redis con una aplicación de Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 31cb033bad3a6b356b447754670fd88bf0ee4663
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861336"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Enlace de Azure Cache for Redis a una aplicación de Azure Spring Cloud

**Este artículo se aplica a:** ✔️ Java

En lugar de configurar manualmente las aplicaciones de Spring Boot, puede enlazar automáticamente servicios de Azure seleccionados a las aplicaciones mediante Azure Spring Cloud. En este artículo se muestra cómo enlazar su aplicación a Azure Cache for Redis.

## <a name="prerequisites"></a>Requisitos previos

* Una instancia de Azure Spring Cloud implementada
* Una instancia de servicio de Azure Cache for Redis
* Extensión de Azure Spring Cloud para la CLI de Azure

Si no tiene una instancia implementada de Azure Spring Cloud, siga los pasos descritos en el [inicio rápido sobre la implementación de una aplicación de Azure Spring Cloud](./quickstart.md).

## <a name="prepare-your-java-project"></a>Preparación del proyecto de Java

1. Agregue la siguiente dependencia al archivo pom.xml del proyecto:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```

1. Quite las propiedades `spring.redis.*` del archivo `application.properties`.

1. Actualice la implementación actual mediante `az spring-cloud app update` o cree una mediante `az spring-cloud app deployment create`.

## <a name="bind-your-app-to-the-azure-cache-for-redis"></a>Enlace de una aplicación con Azure Cache for Redis

#### <a name="service-binding"></a>[enlace del servicio](#tab/Service-Binding)
1. Vaya a la página del servicio Azure Spring Cloud en Azure Portal. Vaya a **Application Dashboard** (Panel de la aplicación) y seleccione la aplicación para enlazar a Azure Cache for Redis. Esta aplicación es la misma que actualizó o implementó en el paso anterior.

1. Seleccione **Service binding** (Enlace de servicio) y seleccione **Create service binding** (Crear enlace de servicio). Rellene el formulario y asegúrese de seleccionar el valor de **Binding type** (Tipo de enlace) de **Azure Cache for Redis**, el servidor de Azure Cache for Redis y la opción de clave **Primary** (Principal).

1. Reinicie la aplicación. El enlace debería funcionar ahora.

1. Para asegurarse de que el enlace de servicios es correcto, seleccione el nombre del enlace y compruebe sus detalles. El campo `property` debería ser similar a este:

    ```properties
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)

El siguiente script de Terraform muestra cómo configurar una aplicación de Azure Spring Cloud con Azure Cache for Redis.

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

resource "azurerm_redis_cache" "redis" {
  name                = "redis-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  capacity            = 0
  family              = "C"
  sku_name            = "Standard"
  enable_non_ssl_port = false
  minimum_tls_version = "1.2"
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
    "spring.redis.host"     = azurerm_redis_cache.redis.hostname
    "spring.redis.password" = azurerm_redis_cache.redis.primary_access_key
    "spring.redis.port"     = "6380"
    "spring.redis.ssl"      = "true"
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido a enlazar una aplicación de Azure Spring Cloud a Azure Cache for Redis. Para más información sobre el enlace de servicios a una aplicación, consulte [Enlace a una instancia de Azure Database for MySQL](./how-to-bind-mysql.md).
