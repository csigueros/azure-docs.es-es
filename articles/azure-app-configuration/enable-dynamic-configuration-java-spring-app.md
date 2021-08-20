---
title: Uso de la configuración dinámica en una aplicación de Spring Boot
titleSuffix: Azure App Configuration
description: Aprenda a actualizar dinámicamente los datos de configuración para aplicaciones de Spring Boot
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 22214c6113d182363ccd86d9e79dac971eb0e432
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114465823"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Tutorial: Uso de la configuración dinámica en una aplicación de Java Spring

App Configuration tiene dos bibliotecas para Spring. `azure-spring-cloud-appconfiguration-config` requiere Spring Boot y tiene una dependencia de `spring-cloud-context`. `azure-spring-cloud-appconfiguration-config-web` requiere Spring Web junto con Spring Boot. Ambas bibliotecas admiten el desencadenamiento manual para comprobar los valores de configuración actualizados. `azure-spring-cloud-appconfiguration-config-web` también agrega compatibilidad con la comprobación automática de la actualización de la configuración.

La actualización permite actualizar los valores de configuración sin tener que reiniciar la aplicación, aunque hará que se vuelvan a crear todas los beans de `@RefreshScope`. La biblioteca de cliente almacena en caché un identificador hash de las configuraciones cargadas actualmente para evitar demasiadas llamadas al almacén de configuración. La operación de actualización no actualiza el valor hasta que haya expirado el valor almacenado en caché, incluso cuando el valor cambia en el almacén de configuración. El tiempo de expiración predeterminado de cada solicitud es de 30 segundos. Si es necesario, se puede invalidar.

La actualización automatizada de `azure-spring-cloud-appconfiguration-config-web` se desencadena en función de la actividad, específicamente de `ServletRequestHandledEvent` de Spring Web. Si no se desencadena un evento `ServletRequestHandledEvent`, la actualización automatizada de `azure-spring-cloud-appconfiguration-config-web` no desencadenará una actualización aunque haya expirado la fecha de expiración de la caché.

## <a name="use-manual-refresh"></a>Uso de la actualización manual

App Configuration expone `AppConfigurationRefresh` que se puede usar para comprobar si la memoria caché ha expirado y, si ha expirado, desencadenar una actualización.

```java
import com.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`refreshConfigurations()` de `AppConfigurationRefresh` devuelve un valor de `Future` que es true si se ha desencadenado una actualización y false en caso contrario. False significa que el tiempo de expiración de la caché no ha expirado, no se ha producido ningún cambio u otro subproceso está comprobando actualmente una actualización.

## <a name="use-automated-refresh"></a>Uso de la actualización automatizada

Para usar la actualización automatizada, comience con una aplicación de Spring Boot que usa App Configuration, como la aplicación que se crea siguiendo las indicaciones del [inicio rápido de Spring Boot con App Configuration](quickstart-java-spring-app.md).

Después, abra el archivo *pom.xml* en un editor de texto y agregue `<dependency>` para `azure-spring-cloud-appconfiguration-config-web` mediante el código siguiente.

**Spring Boot**

```xml
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
    <version>2.0.0</version>
</dependency>
```

> [!NOTE]
> Si necesita admitir dependencias anteriores, vea nuestra [biblioteca anterior](https://github.com/Azure/azure-sdk-for-java/blob/spring-cloud-starter-azure-appconfiguration-config_1.2.9/sdk/appconfiguration/spring-cloud-starter-azure-appconfiguration-config/README.md).

1. Actualización de `bootstrap.properties` para habilitar las métricas

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled=true
    spring.cloud.azure.appconfiguration.stores[0].monitoring.triggers[0].key=sentinel
    ```

1. Abra **Azure Portal** y vaya al recurso de App Configuration asociado a la aplicación. Seleccione **Explorador de configuración** en **Operaciones** y, después, **+ Crear** > **Par clave-valor** para crear un par clave-valor y agregar los parámetros siguientes:

    | Clave | Value |
    |---|---|
    | centinela | 1 |

    Deje **Etiqueta** y **Tipo de contenido** en blanco, por ahora.

1. Seleccione **Aplicar**.

1. Compile la aplicación de Spring Boot con Maven y ejecútela.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Abra una ventana del explorador y vaya a la dirección URL: `http://localhost:8080`.  Verá el mensaje asociado con la clave.

    También puede usar *curl* para probar la aplicación, por ejemplo:

    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Para probar la configuración dinámica, abra el portal de Azure App Configuration asociado a la aplicación. Seleccione **Explorador de configuración** y actualice el valor de la clave que se muestra, por ejemplo:

    | Clave | Value |
    |---|---|
    | /application/config.message | Hello: actualizado |

1. Actualice a un nuevo valor la clave de Sentinel que ha creado antes. Este cambio hará que la aplicación actualice todas las claves de configuración una vez que haya transcurrido el intervalo de actualización.

    | Clave | Value |
    |---|---|
    | centinela | 2 |

1. Actualice la página del explorador para ver el nuevo mensaje que se muestra.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha habilitado la aplicación de Spring Boot para actualizar dinámicamente la configuración a partir de App Configuration. Para obtener información sobre cómo usar una identidad administrada de Azure para simplificar el acceso a App Configuration, vaya al siguiente tutorial.

> [!div class="nextstepaction"]
> [Integración de identidades administradas](./howto-integrate-azure-managed-service-identity.md)
