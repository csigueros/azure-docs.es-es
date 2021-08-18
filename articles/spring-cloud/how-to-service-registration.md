---
title: Automatización del registro y la detección de servicios
description: Aprenda a automatizar la detección y el registro de servicios mediante el registro de servicios de Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 97f0a5326422613a96b530c70cf316b6db1b6b35
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014746"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Detección y registro de los servicios de Spring Cloud

La característica de detección de servicios es un requisito clave para una arquitectura basada en microservicios.  La configuración manual de cada cliente lleva tiempo e introduce la posibilidad de que se produzcan errores humanos.  El registro de servicios de Azure Spring Cloud resuelve este problema.  Una vez configurado, un servidor de registro de servicios controlará el registro y la detección de servicios de los microservicios de la aplicación. El servidor de registro de servicios mantiene un registro de los microservicios implementados, habilita el equilibrio de carga del lado cliente y desacopla los proveedores de servicios de los clientes sin depender del DNS.

::: zone pivot="programming-language-csharp"
Para obtener información sobre cómo configurar el registro de servicio para una aplicación de Steeltoe, consulte [Preparación de una aplicación de Spring de Java para la implementación en Azure Spring Cloud](how-to-prepare-app-deployment.md).
::: zone-end
::: zone pivot="programming-language-java"

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registro de la aplicación mediante el registro de servicios de Spring Cloud

Para que la aplicación pueda administrar el registro y la detección de servicios mediante el registro de servicios de Spring Cloud, deben incluirse varias dependencias en el archivo *pom.xml* de la aplicación.
Incluya las dependencias de *spring-cloud-starter-netflix-eureka-client* y *spring-cloud-starter-azure-spring-cloud-client* en el archivo *pom.xml*.

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            <version>3.0.2</version>
        </dependency>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
            <version>2.1.0</version>
        </dependency>
        <dependency>
            <groupId>com.sun.jersey</groupId>
            <artifactId>jersey-client</artifactId>
            <version>1.19.4</version>
        </dependency>
        <dependency>
            <groupId>com.sun.jersey.contribs</groupId>
            <artifactId>jersey-apache-client4</artifactId>
            <version>1.19.4</version>
        </dependency>
```

## <a name="update-the-top-level-class"></a>Actualización de la clase de nivel superior

Por último, se agrega una anotación a la clase de nivel superior de la aplicación

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

    @SpringBootApplication
    @EnableEurekaClient
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

El punto de conexión del servidor de registro de servicios de Spring Cloud se insertará como variable de entorno en la aplicación.  Los microservicios podrán ahora registrarse en el servidor de registro de servicios y detectarán otros microservicios dependientes.

Tenga en cuenta que los cambios pueden tardar unos minutos en propagarse del servidor a todos los microservicios.
::: zone-end
