---
title: Acceso a Config Server y Service Registry
titleSuffix: Azure Spring Cloud
description: Cómo acceder a los puntos de conexión de Service Registry y Config Server con el control de acceso basado en roles de Azure Active Directory.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: devx-track-java, subject-rbac-steps
ms.openlocfilehash: ce5905b43d37faf3b1a6965ae04efb9f8f73c2ff
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835885"
---
# <a name="access-config-server-and-service-registry"></a>Acceso a Config Server y Service Registry

En este artículo se explica cómo acceder a Spring Cloud Config Server y Spring Cloud Service Registry administrado por Azure Spring Cloud mediante el control de acceso basado en roles (RBAC) de Azure Active Directory (Azure AD).

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Asignación de un rol a un usuario o grupo de Azure AD, una MSI o una entidad de servicio

Asigne el rol al [usuario | grupo | entidad de servicio | identidad administrada] en el ámbito de [grupo de administración | suscripción | grupo de recursos | recurso].

| Nombre de rol                                       | Descripción                                                                  |
|-------------------------------------------------|------------------------------------------------------------------------------|
| Lectura en Config Server de Azure Spring Cloud         | Permite el acceso de lectura a Config Server en Azure Spring Cloud.                       |
| Colaborador en Config Server de Azure Spring Cloud    | Permite el acceso de lectura, escritura y eliminación en Config Server en Azure Spring Cloud.    |
| Lectura en Service Registry de Azure Spring Cloud      | Permite el acceso de lectura en Service Registry de Azure Spring Cloud.                    |
| Colaborador en Service Registry de Azure Spring Cloud | Permite el acceso de lectura, escritura y eliminación en Service Registry de Azure Spring Cloud. |

Para acceder a los pasos detallados, consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="access-config-server-and-service-registry-endpoints"></a>Acceso a los puntos de conexión de Config Server y Service Registry

Una vez asignado el rol, el usuario asignado puede acceder a los puntos de conexión de Config Server y Service Registry de Spring Cloud mediante estos procedimientos:

1. Obtener un token de acceso. Una vez asignado el rol a un usuario de Azure AD, este podrá usar los siguientes comandos para iniciar sesión en la CLI de Azure con un usuario, una entidad de servicio o una identidad administrada para obtener un token de acceso. Para obtener más información, consulte [Autenticación en la CLI de Azure](/cli/azure/authenticate-azure-cli).

    ```azurecli
    az login
    az account get-access-token
    ```

1. Cree el extremo. Se admiten los puntos de conexión predeterminados de Config Server y Service Registry de Spring Cloud administrados por Azure Spring Cloud.

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/{path}'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/{path}'*

    >[!NOTE]
    > Si usa Azure China, reemplace `*.azuremicroservices.io`por `*.microservices.azure.cn`. Para más información, consulte la sección [Comprobación de puntos de conexión en Azure](/azure/china/resources-developer-guide#check-endpoints-in-azure) en la [guía para desarrolladores de Azure China](/azure/china/resources-developer-guide).

1. Acceda al punto de conexión creado con el token de acceso. Coloque el token de acceso en un encabezado para proporcionar autorización: `--header 'Authorization: Bearer {TOKEN_FROM_PREVIOUS_STEP}`.

    Por ejemplo:

    a. Acceda a un punto de conexión como *'https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/health '* para ver el estado de mantenimiento de Config Server.

    b. Acceda a un punto de conexión como *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/eureka/apps '* para ver las aplicaciones registradas en Service Registry de Spring Cloud (Eureka aquí).

    Si la respuesta es *401 no autorizado*, compruebe si el rol se ha asignado correctamente.  El rol tardará varios minutos en entrar en vigor o en comprobar que el token de acceso no haya expirado.

Para más información sobre el punto de conexión del accionador, consulte [Puntos de conexión listos para producción](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints).

Para los puntos de conexión de Eureka, consulte [Operaciones de REST de Eureka](https://github.com/Netflix/eureka/wiki/Eureka-REST-operations).

Para los puntos de conexión del servidor de configuración y la información detallada de la ruta de acceso, consulte [ResourceController.java](https://github.com/spring-cloud/spring-cloud-config/blob/main/spring-cloud-config-server/src/main/java/org/springframework/cloud/config/server/resource/ResourceController.java) y [EncryptionController.java](https://github.com/spring-cloud/spring-cloud-config/blob/main/spring-cloud-config-server/src/main/java/org/springframework/cloud/config/server/encryption/EncryptionController.java).

## <a name="register-spring-boot-apps-to-spring-cloud-config-server-and-service-registry-managed-by-azure-spring-cloud"></a>Registro de aplicaciones de Spring Boot en Config Server Service Registry de Spring Cloud administrados por Azure Spring Cloud

Una vez asignado el rol, podrá registrar aplicaciones de Spring Boot en Config Server y Service Registry de Spring Cloud administrados por Azure Spring Cloud mediante la autenticación por tokens de Azure AD. Tanto Config Server como Service Registry admiten la [plantilla de REST personalizada](https://cloud.spring.io/spring-cloud-config/reference/html/#custom-rest-template) para insertar el token de portador para la autenticación.

Para más información, consulte los ejemplos de [Aceso a Config Server administrado por Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-config-server-client) y [Acceso a Service Registry administrado por Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-eureka-client). En las secciones siguientes se explican algunos detalles importantes de estos ejemplos.

**En *AccessTokenManager.java*:**

`AccessTokenManager` es responsable de obtener un token de acceso de Azure AD. Configure la información de inicio de sesión de la entidad de servicio en el archivo *application.properties* e inicialice `ApplicationTokenCredentials` para obtener el token. Puede encontrar este archivo en ambos ejemplos.

```java
prop.load(in);
tokenClientId = prop.getProperty("access.token.clientId");
String tenantId = prop.getProperty("access.token.tenantId");
String secret = prop.getProperty("access.token.secret");
String clientId = prop.getProperty("access.token.clientId");
credentials = new ApplicationTokenCredentials(
    clientId, tenantId, secret, AzureEnvironment.AZURE);
```

**En *CustomConfigServiceBootstrapConfiguration.java*:**

`CustomConfigServiceBootstrapConfiguration` implementa la plantilla de REST personalizada para Config Server e inserta el token de Azure AD como encabezados `Authorization`. Este archivo se encuentra en el [ejemplo de Config Server](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-config-server-client).

```java
public class RequestResponseHandlerInterceptor implements ClientHttpRequestInterceptor {

    @Override
    public ClientHttpResponse intercept(HttpRequest request, byte[] body, ClientHttpRequestExecution execution) throws IOException {
        String accessToken = AccessTokenManager.getToken();
        request.getHeaders().remove(AUTHORIZATION);
        request.getHeaders().add(AUTHORIZATION, "Bearer " + accessToken);

        ClientHttpResponse response = execution.execute(request, body);
        return response;
    }

}
```

**En *CustomRestTemplateTransportClientFactories.java*:**

Las dos clases anteriores son para la implementación de la plantilla de REST personalizada para Service Registry de Spring Cloud. La parte `intercept` es la misma que se mostró anteriormente para Config Server. Asegúrese de agregar a `factory.mappingJacksonHttpMessageConverter()` los convertidores de mensajes. Puede encontrar este archivo en el [ejemplo de Service Registry de Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-eureka-client).

```java
private RestTemplate customRestTemplate() {
    /*
     * Inject your custom rest template
     */
    RestTemplate restTemplate = new RestTemplate();
    restTemplate.getInterceptors()
        .add(new RequestResponseHandlerInterceptor());
    RestTemplateTransportClientFactory factory = new RestTemplateTransportClientFactory();

    restTemplate.getMessageConverters().add(0, factory.mappingJacksonHttpMessageConverter());

    return restTemplate;
}
```

Si ejecuta aplicaciones en un clúster de Kubernetes, se recomienda usar una dirección IP para registrar Service Registry de Spring Cloud para el acceso.

```properties
eureka.instance.prefer-ip-address=true
```

## <a name="next-steps"></a>Pasos siguientes

* [Inicio de sesión con la CLI de Azure](/cli/azure/authenticate-azure-cli)
* [Puntos de conexión listos para producción](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)
* [Creación de roles y permisos](how-to-permissions.md)
