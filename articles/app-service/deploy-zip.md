---
title: Implementación de archivos en App Service
description: Aprenda a implementar distintos paquetes de aplicaciones o bibliotecas discretas, archivos estáticos o scripts de inicio en Azure App Service
ms.topic: article
ms.date: 08/13/2021
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: a73d03d97e2b5d5d743b1db3980c8e587a0f454b
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225459"
---
# <a name="deploy-files-to-app-service"></a>Implementación de archivos en App Service

En este artículo, se muestra cómo implementar el código como un paquete ZIP, WAR, JAR o EAR en [Azure App Service](overview.md). También se muestra cómo implementar archivos individuales en App Service, de forma independiente del paquete de aplicación.

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos en este artículo, [cree una aplicación de App Service](./index.yml) o use alguna aplicación que haya creado para otro tutorial.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="deploy-a-zip-package"></a>Implementación de un paquete ZIP

Al implementar un paquete ZIP, App Service desempaqueta su contenido en la ruta de acceso predeterminada de la aplicación (`D:\home\site\wwwroot` para Windows y `/home/site/wwwroot` para Linux).

Esta implementación del paquete ZIP usa el mismo servicio de Kudu que permite realizar implementaciones basadas en la integración continua. Kudu admite la siguientes funcionalidades para implementar el paquete ZIP: 

- La eliminación de archivos restantes de una implementación anterior.
- La opción de activar el proceso de compilación predeterminado, en el que se incluye la restauración del paquete.
- La personalización de la implementación, incluyendo la ejecución de scripts de implementación.  
- Registros de implementación. 
- Un límite de tamaño de paquete de 2048 MB.

Para obtener más información, consulte la [documentación de Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

> [!NOTE]
> Los archivos del paquete ZIP solo se copian si sus marcas de tiempo no coinciden con lo que ya está implementado. La generación de un archivo ZIP mediante un proceso de compilación que almacena en memoria caché los resultados puede dar lugar a implementaciones más rápidas. Consulte el tema sobre la [implementación desde un archivo ZIP o una dirección URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) para obtener más información.

# <a name="azure-cli"></a>[CLI de Azure](#tab/cli)

Implemente un paquete ZIP en la aplicación web mediante el comando [az webapp deploy](/cli/azure/webapp#az_webapp_deploy). El comando de la CLI usa la [API de publicación de Kudu](#kudu-publish-api-reference) para implementar los archivos y se puede personalizar completamente.

En el ejemplo siguiente, se inserta un paquete ZIP en el sitio. Especifique la ruta de acceso al paquete ZIP local para `--src-path`.

```azurecli-interactive
az webapp deploy --resource-group <group-name> --name <app-name> --src-path <zip-package-path>
```

Este comando reinicia la aplicación después de implementar el paquete ZIP. 

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

El ejemplo siguiente usa el parámetro `--src-url` para especificar la dirección URL de una cuenta de Azure Storage de la que el sitio debe extraer el archivo ZIP.

```azurecli-interactive
az webapp deploy --resource-group <grou-name> --name <app-name> --src-url "https://storagesample.blob.core.windows.net/sample-container/myapp.zip?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

En el ejemplo siguiente, se usa [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) para cargar el paquete ZIP. Reemplace los marcadores de posición `<group-name>`, `<app-name>` y `<zip-package-path>`.

```powershell
Publish-AzWebApp -ResourceGroupName Default-Web-WestUS -Name MyApp -ArchivePath <zip-package-path> 
```

# <a name="kudu-api"></a>[API de Kudu](#tab/api)

En el ejemplo siguiente, se usa la herramienta cURL para implementar un paquete ZIP. Reemplace los marcadores de posición `<username>`, `<zip-package-path>` y `<app-name>`. Cuando cURL se lo solicite, escriba la [contraseña de implementación](deploy-configure-credentials.md).

```bash
curl -X POST -u <username> --data-binary @"<zip-package-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=zip
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

El ejemplo siguiente usa el parámetro `packageUri` para especificar la dirección URL de una cuenta de Azure Storage de la que la aplicación web debe extraer el archivo ZIP.

```bash
curl -X POST -u <username> https://<app-name>.scm.azurewebsites.net/api/publish -d '{"packageUri": "https://storagesample.blob.core.windows.net/sample-container/myapp.zip?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3"}'
```

# <a name="kudu-ui"></a>[Interfaz de usuario de Kudu](#tab/kudu-ui)

En el explorador, vaya a `https://<app_name>.scm.azurewebsites.net/ZipDeployUI`.

Cargue el paquete ZIP que creó en [Creación de un paquete ZIP de proyecto](#create-a-project-zip-package) arrastrándolo al área del explorador de archivos en la página web.

Cuando la implementación está en curso, un icono en la esquina superior derecha muestra el progreso como un porcentaje. La página también muestra mensajes detallados para la operación debajo del área del explorador. Cuando se haya completado, el último mensaje de implementación debe indicar `Deployment successful`.

El punto de conexión anterior no funciona en este momento con App Services de Linux. Considere la posibilidad de usar en su lugar FTP o la [API de implementación de ZIP](/azure/app-service/faq-app-service-linux#continuous-integration-and-deployment).

-----

## <a name="enable-build-automation-for-zip-deploy"></a>Habilitación de la automatización de compilación para la implementación mediante ZIP

De manera predeterminada, el motor de implementación da por supuesto que un paquete ZIP está listo para ejecutarse tal cual y no ejecuta ninguna automatización de la compilación. Para habilitar la misma automatización de la compilación que en una [implementación de Git](deploy-local-git.md), establezca la configuración de la aplicación `SCM_DO_BUILD_DURING_DEPLOYMENT` ejecutando el siguiente comando en [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Para obtener más información, consulte la [documentación de Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="deploy-warjarear-packages"></a>Implementación de paquetes WAR, JAR y EAR

Puede implementar su paquete [WAR](https://wikipedia.org/wiki/WAR_(file_format)), [JAR](https://wikipedia.org/wiki/JAR_(file_format)) o [EAR](https://wikipedia.org/wiki/EAR_(file_format)) en App Service para ejecutar la aplicación web de Java mediante la CLI de Azure, PowerShell o la API de publicación de Kudu.

El proceso de implementación coloca el paquete correctamente en la unidad de archivos compartidos (consulte [Referencia de la API de publicación de Kudu](#kudu-publish-api-reference)). Por ese motivo, no se recomienda implementar paquetes WAR, JAR o EAR mediante [FTP](deploy-ftp.md) o WebDeploy.

# <a name="azure-cli"></a>[CLI de Azure](#tab/cli)

Implemente un paquete WAR en Tomcat o JBoss EAP mediante el comando [az webapp deploy](/cli/azure/webapp#az_webapp_deploy). Especifique la ruta de acceso al paquete de Java local para `--src-path`.

```azurecli-interactive
az webapp deploy --resource-group <group-name> --name <app-name> --src-path ./<package-name>.war
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

El ejemplo siguiente usa el parámetro `--src-url` para especificar la dirección URL de una cuenta de Azure Storage de la que la aplicación web debe extraer el archivo ZIP.

```azurecli-interactive
az webapp deploy --resource-group <grou-name> --name <app-name> --src-url "https://storagesample.blob.core.windows.net/sample-container/myapp.war?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3
```

El comando de la CLI usa la [API de publicación de Kudu](#kudu-publish-api-reference) para implementar el paquete y se puede personalizar completamente.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

En el ejemplo siguiente, se usa [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) para cargar el archivo .war. Reemplace los marcadores de posición `<group-name>`, `<app-name>` y `<package-path>` (solo se admiten archivos WAR y JAR en Azure PowerShell).

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <package-path>
```

# <a name="kudu-api"></a>[API de Kudu](#tab/api)

En el ejemplo siguiente, se usa la herramienta cURL para implementar un archivo .war, .jar o .ear. Reemplace los marcadores de posición `<username>`, `<file-path>`, `<app-name>` y `<package-type>` (`war`, `jar` o `ear`, según corresponda). Cuando cURL se lo solicite, escriba la [contraseña de implementación](deploy-configure-credentials.md).

```bash
curl -X POST -u <username> --data-binary @"<file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=<package-type>
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

El ejemplo siguiente usa el parámetro `packageUri` para especificar la dirección URL de una cuenta de Azure Storage de la que la aplicación web debe extraer el archivo WAR. El archivo WAR también podría ser un archivo JAR o EAR.

```bash
curl -X POST -u <username> https://<app-name>.scm.azurewebsites.net/api/publish -d '{"packageUri": "https://storagesample.blob.core.windows.net/sample-container/myapp.war?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3"}'
```

Para más información, consulte [Referencia de la API de publicación de Kudu](#kudu-publish-api-reference).

# <a name="kudu-ui"></a>[Interfaz de usuario de Kudu](#tab/kudu-ui)

La interfaz de usuario de Kudu no admite la implementación de aplicaciones JAR, WAR ni EAR. Utilice una de las otras opciones.

-----

## <a name="deploy-individual-files"></a>Implementación de archivos individuales

# <a name="azure-cli"></a>[CLI de Azure](#tab/cli)

Implemente un script de inicio, una biblioteca y un archivo estático en la aplicación web mediante el comando [az webapp deploy](/cli/azure/webapp#az_webapp_deploy) con el parámetro `--type`.

Si implementa un script de inicio de esta manera, App Service utiliza automáticamente el script para iniciar la aplicación.

El comando de la CLI usa la [API de publicación de Kudu](#kudu-publish-api-reference) para implementar los archivos y se puede personalizar completamente.

### <a name="deploy-a-startup-script"></a>Implementación de un script de inicio

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path scripts/startup.sh --type=startup
```

### <a name="deploy-a-library-file"></a>Implementación de un archivo de biblioteca

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path driver.jar --type=lib
```

### <a name="deploy-a-static-file"></a>Implementación de un archivo estático

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path config.json --type=static
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

No compatible. Consulte la CLI de Azure o la API de Kudu.

# <a name="kudu-api"></a>[API de Kudu](#tab/api)

### <a name="deploy-a-startup-script"></a>Implementación de un script de inicio

En el ejemplo siguiente, se usa la herramienta cURL para implementar un archivo de inicio para la aplicación. Reemplace los marcadores de posición `<username>`, `<startup-file-path>` y `<app-name>`. Cuando cURL se lo solicite, escriba la [contraseña de implementación](deploy-configure-credentials.md).

```bash
curl -X POST -u <username> --data-binary @"<startup-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=startup
```

### <a name="deploy-a-library-file"></a>Implementación de un archivo de biblioteca

En el ejemplo siguiente, se usa la herramienta cURL para implementar un archivo de biblioteca para la aplicación. Reemplace los marcadores de posición `<username>`, `<lib-file-path>` y `<app-name>`. Cuando cURL se lo solicite, escriba la [contraseña de implementación](deploy-configure-credentials.md).

```bash
curl -X POST -u <username> --data-binary @"<lib-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=lib&path="/home/site/deployments/tools/my-lib.jar"
```

### <a name="deploy-a-static-file"></a>Implementación de un archivo estático

En el ejemplo siguiente, se usa la herramienta cURL para implementar un archivo de configuración para la aplicación. Reemplace los marcadores de posición `<username>`, `<config-file-path>` y `<app-name>`. Cuando cURL se lo solicite, escriba la [contraseña de implementación](deploy-configure-credentials.md).

```bash
curl -X POST -u <username> --data-binary @"<config-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=static&path="/home/site/deployments/tools/my-config.json"
```

# <a name="kudu-ui"></a>[Interfaz de usuario de Kudu](#tab/kudu-ui)

La interfaz de usuario de Kudu no admite la implementación de archivos individuales. Use la CLI de Azure o la API REST de Kudu.

-----

## <a name="kudu-publish-api-reference"></a>Referencia de la API de publicación de Kudu

La API `publish` de Kudu le permite especificar los mismos parámetros del comando de la CLI como parámetros de consulta URL. Para autenticarse con la API de Kudu, puede usar la autenticación básica con las [credenciales de implementación](deploy-configure-credentials.md#userscope) de la aplicación.

En la tabla siguiente, se muestran los parámetros de consulta disponibles, sus valores permitidos y sus descripciones.

| Key | Valores permitidos | Descripción | Obligatorio | Tipo  |
|-|-|-|-|-|
| `type` | `war`\|`jar`\|`ear`\|`lib`\|`startup`\|`static`\|`zip` | Tipo de artefacto que se va a implementar; establece la ruta de acceso de destino predeterminada e informa a la aplicación web de cómo se debe controlar la implementación. <br/> - `type=zip`: implementar un paquete ZIP descomprimiendo el contenido en `/home/site/wwwroot`. El parámetro `path` es opcional. <br/> - `type=war`: implementar un paquete WAR. De manera predeterminada, el paquete WAR se implementa en `/home/site/wwwroot/app.war`. La ruta de acceso de destino se puede especificar con `path`. <br/> - `type=jar`: implementar un paquete JAR en `/home/site/wwwroot/app.jar`. El parámetro `path` no se tiene en cuenta. <br/> - `type=ear`: implementar un paquete EAR en `/home/site/wwwroot/app.ear`. El parámetro `path` no se tiene en cuenta. <br/> - `type=lib`: implementar un archivo de biblioteca JAR. De manera predeterminada, el archivo se implementa en `/home/site/libs`. La ruta de acceso de destino se puede especificar con `path`. <br/> - `type=static`: implementar un archivo estático (por ejemplo, un script). De manera predeterminada, el archivo se implementa en `/home/site/scripts`. La ruta de acceso de destino se puede especificar con `path`. <br/> - `type=startup`: implementar un script que App Service utiliza automáticamente como script de inicio para la aplicación. De manera predeterminada, el script se implementa en `D:\home\site\scripts\<name-of-source>` para Windows y en `home/site/wwwroot/startup.sh` para Linux. La ruta de acceso de destino se puede especificar con `path`. | Sí | String |
| `restart` | `true`\|`false` | De manera predeterminada, la API reinicia la aplicación después de la operación de implementación (`restart=true`). Para implementar varios artefactos, evite reinicios en todos ellos menos en la implementación final; para ello, establezca `restart=false`. | No | Boolean |
| `clean` | `true`\|`false` | Especifica si se debe limpiar (eliminar) la implementación de destino antes de implementar allí el artefacto. | No | Boolean |
| `ignorestack` | `true`\|`false` | La API de publicación usa la variable de entorno `WEBSITE_STACK` para elegir valores predeterminados seguros en función de la pila del lenguaje del sitio. Si se establece este parámetro en `false`, se deshabilitan los valores predeterminados específicos del lenguaje. | No | Boolean |
| `path` | `"<absolute-path>"` | Ruta de acceso absoluta en la que se implementará el artefacto. Por ejemplo: `"/home/site/deployments/tools/driver.jar"`, `"/home/site/scripts/helper.sh"`. | No | String |

## <a name="next-steps"></a>Pasos siguientes

Para ver escenarios de implementación más avanzados, pruebe [Implementación en Azure con Git](deploy-local-git.md). La implementación basada en Git en Azure permite el control de versiones, la restauración de paquetes, MSBuild y mucho más.

## <a name="more-resources"></a>Más recursos

* [Kudu: realizar una implementación desde un archivo .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Credenciales de implementación de Azure App Service](deploy-ftp.md)
* [Referencia de variables de entorno y configuración de la aplicación](reference-app-settings.md)
