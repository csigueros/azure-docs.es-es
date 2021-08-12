---
title: 'Inicio rápido: Creación de una aplicación de Java en Azure App Service'
description: Implemente su primera aplicación Hola mundo de Java en Azure App Service en cuestión de minutos. El complemento Aplicación web de Azure para Maven facilita la implementación de aplicaciones Java.
keywords: azure, app service, web app, windows, linux, java, maven, quickstart
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 79cf3c2764035ef2e8c4418d172ba991a72bf3ff
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2021
ms.locfileid: "113285915"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Inicio rápido: Creación de una aplicación de Java en Azure App Service

[Azure App Service](overview.md) proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático.  En este inicio rápido se muestra cómo usar la [CLI de Azure](/cli/azure/get-started-with-azure-cli) con el [complemento Aplicación web de Azure para Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) para implementar un archivo .jar o un archivo .war. 

También hay versiones IDE de este artículo. Consulte [Inicio rápido de Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) o [Inicio rápido de Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).

## <a name="1-prepare-your-environment"></a>1. Preparación del entorno

Antes de empezar, debe disponer de lo siguiente:

+ Una <abbr title="El perfil que mantiene la información de facturación en relación con el uso de Azure.">Cuenta de Azure</abbr> con una suscripción activa. <abbr title="La estructura organizativa básica en la que administra los recursos de Azure, que normalmente se asocia a un individuo o departamento dentro de una organización.">subscription</abbr>. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ La[CLI de Azure](/cli/azure/install-azure-cli).

+ [Kit para desarrolladores de Java](/azure/developer/java/fundamentals/java-support-on-azure), versión 8 u 11.

+ [Apache Maven](https://maven.apache.org), versión 3.0 o posterior.

El complemento de Maven usa las credenciales de la cuenta desde la CLI de Azure para la implementación en App Services. [Inicie sesión con la CLI de Azure](/cli/azure/authenticate-azure-cli) antes de continuar. Para más información, consulte [Autenticación con los complementos de Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

```azurecli
az login
```

<br>
<hr/>

## <a name="2-create-a-java-app"></a>2. Creación de una aplicación Java

# <a name="java-se"></a>[Java SE](#tab/javase)

Clone el proyecto de ejemplo [Primeros pasos de Spring Boot](https://github.com/spring-guides/gs-spring-boot).

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Cambie de directorio al del proyecto completado.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Ejecute el siguiente comando de Maven en el símbolo del sistema de Cloud Shell para crear una aplicación denominada `helloworld`:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

A continuación, cambie el directorio de trabajo a la carpeta del proyecto:

```bash
cd helloworld
```

---

<br>
<hr/>

## <a name="3-configure-the-maven-plugin"></a>3. Configuración del complemento Maven

Ejecute el siguiente comando de Maven para configurar la implementación. Este comando le ayudará a configurar el sistema operativo de App Service, la versión de Java y la versión de Tomcat.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Cuando se le aparezca la opción **Suscripción**, escriba el número impreso en el comienzo de línea para seleccionar el valor de `Subscription` adecuado.
1. Cuando se le solicite mediante la opción **Aplicación web**, presione Entrar para aceptar la opción `<create>` predeterminada o seleccione una aplicación existente.
1. Cuando se le solicite la opción **SO**, especifique `3` para seleccionar **Windows**.
1. Cuando se le solicite la opción **Plan de tarifa**, especifique `2` para seleccionar **B2**.
1. Use la versión de Java predeterminada, **Java 8**; para ello, presione Entrar.
1. Por último, presione Entrar en la última solicitud para confirmar las selecciones.

    La salida del resumen tendrá un aspecto similar al fragmento de código que se muestra a continuación.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Cuando se le aparezca la opción **Suscripción**, escriba el número impreso en el comienzo de línea para seleccionar el valor de `Subscription` adecuado.
1. Cuando se le solicite mediante la opción **Aplicación web**, presione Entrar para aceptar la opción `<create>` predeterminada o seleccione una aplicación existente.
1. Cuando se le solicite la opción **SO**, especifique `3` para seleccionar **Windows**.
1. Cuando se le solicite la opción **Plan de tarifa**, especifique `2` para seleccionar **B2**.
1. Use la versión de Java predeterminada, **Java 8**; para ello, presione Entrar.
1. Use el contenedor web predeterminado, **Tomcat 8.5**; para ello, presione Entrar.
1. Por último, presione Entrar en la última solicitud para confirmar las selecciones.

    La salida del resumen tendrá un aspecto similar al fragmento de código que se muestra a continuación.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Cuando se le aparezca la opción **Suscripción**, escriba el número impreso en el comienzo de línea para seleccionar el valor de `Subscription` adecuado.
1. Cuando se le solicite mediante la opción **Aplicación web**, presione Entrar para aceptar la opción `<create>` predeterminada o seleccione una aplicación existente.
1. Cuando se le solicite la opción **SO**, presione Entrar para seleccionar **Linux**.
1. Cuando se le solicite la opción **Plan de tarifa**, especifique `2` para seleccionar **B2**.
1. Use la versión de Java predeterminada, **Java 8**; para ello, presione Entrar.
1. Por último, presione Entrar en la última solicitud para confirmar las selecciones.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Cuando se le aparezca la opción **Suscripción**, escriba el número impreso en el comienzo de línea para seleccionar el valor de `Subscription` adecuado.
1. Cuando se le solicite mediante la opción **Aplicación web**, presione Entrar para aceptar la opción `<create>` predeterminada o seleccione una aplicación existente.
1. Cuando se le solicite la opción **SO**, presione Entrar para seleccionar **Linux**.
1. Cuando se le solicite la opción **Plan de tarifa**, especifique `2` para seleccionar **B2**.
1. Use la versión de Java predeterminada, **Java 8**; para ello, presione Entrar.
1. Use el contenedor web predeterminado, **Tomcat 8.5**; para ello, presione Entrar.
1. Por último, presione Entrar en la última solicitud para confirmar las selecciones.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

---

::: zone-end

Las configuraciones de App Service se pueden modificar directamente en el archivo `pom.xml` si es necesario. A continuación se enumeran algunas de las más comunes:

Propiedad | Obligatorio | Descripción | Versión
---|---|---|---
`<schemaVersion>` | false | Especifique la versión del esquema de configuración. Los valores admitidos son: `v1`, `v2`. | 1.5.2
`<subscriptionId>` | false | Especifique el identificador de suscripción. | 0.1.0+
`<resourceGroup>` | true | Azure <abbr title="Un contenedor lógico para recursos relacionados de Azure que se pueden administrar como una unidad.">resource group</abbr> para la aplicación web. | 0.1.0+
`<appName>` | true | El nombre de la aplicación web. | 0.1.0+
`<region>` | true | Especifica la región donde se hospedará la aplicación web. El valor predeterminado es **westeurope**. Todas las regiones válidas en la sección [Regiones admitidas](https://azure.microsoft.com/global-infrastructure/services/?products=app-service). | 0.1.0+
`<pricingTier>` | false | El plan de tarifa de la aplicación web. El valor predeterminado es **P1V2** para cargas de trabajo de producción, mientras que **B2** es el mínimo recomendado para desarrollo y pruebas de Java. [Más información](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | true | La configuración del entorno en tiempo de ejecución. Puede ver los detalles [aquí](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+
`<deployment>` | true | La configuración de implementación. Puede ver los detalles [aquí](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+

Preste atención a los valores de `<appName>` y `<resourceGroup>`(`helloworld-1590394316693` y `helloworld-1590394316693-rg` en la salida de ejemplo), ya que se usarán más adelante.

[Notificar un problema](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

<br>
<hr/>

## <a name="4-deploy-the-app"></a>4. Implementar la aplicación

Implemente la aplicación Java en Azure con el siguiente comando:

```bash
mvn package azure-webapp:deploy
```

Una vez finalizada la implementación, la aplicación estará lista en `http://<appName>.azurewebsites.net/`. Abra la dirección URL en su explorador web local. Verá la información siguiente:

![Aplicación de ejemplo que se ejecuta en Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**¡Enhorabuena!** Ha implementado su primera aplicación Java en App Service.

[Notificar un problema](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. Limpieza de recursos

Elimine la aplicación Java y los recursos relacionados para no incurrir en costos adicionales.

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Este comando puede tardar varios segundos en ejecutarse.

<br>
<hr/>

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Conexión a Azure DB para PostgreSQL con Java](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Configure CI/CD](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Información de precios](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Incorporación de registros y métricas](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Escalado vertical](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Recursos de Azure para desarrolladores de Java](/java/azure/)

> [!div class="nextstepaction"]
> [Configuración de la aplicación de Java](configure-language-java.md)
