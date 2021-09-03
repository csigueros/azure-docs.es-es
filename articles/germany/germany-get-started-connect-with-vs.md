---
title: Conexión a Azure Alemania mediante Visual Studio | Microsoft Docs
description: Información acerca de la administración de suscripciones en Azure Alemania mediante Visual Studio
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: f5749ed9d2080a92a33993d8131595359110b75e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029145"
---
# <a name="connect-to-azure-germany-by-using-visual-studio"></a>Conexión a Azure Alemania mediante Visual Studio

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Los desarrolladores usan Visual Studio para administrar fácilmente sus suscripciones de Azure mientras se implementan soluciones. Actualmente, no se puede configurar una conexión a Azure Alemania en la interfaz de usuario de Visual Studio.

## <a name="visual-studio-2017-and-visual-studio-2019"></a>Visual Studio 2017 y Visual Studio 2019

Visual Studio requiere un archivo de configuración para conectarse a Azure Alemania. Si dicho archivo está en su lugar, Visual Studio se conecta a Azure Alemania, en lugar de a Azure global.

### <a name="create-a-configuration-file-for-azure-germany"></a>Creación de un archivo de configuración para Azure Alemania

Cree un archivo llamado *AadProvider.Configuration.json* con el siguiente contenido:

```json
{
  "AuthenticationQueryParameters":null,
  "AsmEndPoint":"https://management.microsoftazure.de/",
  "Authority":"https://login.microsoftonline.de/",
  "AzureResourceManagementEndpoint":"https://management.microsoftazure.de/",
  "AzureResourceManagementAudienceEndpoints":["https://management.core.cloudapi.de/"],
  "ClientIdentifier":"872cd9fa-d31f-45e0-9eab-6e460a02d1f1",
  "EnvironmentName":"BlackForest",
  "GraphEndpoint":"https://graph.cloudapi.de",
  "MsaHomeTenantId":"f577cd82-810c-43f9-a1f6-0cc532871050",
  "NativeClientRedirect":"urn:ietf:wg:oauth:2.0:oob",
  "PortalEndpoint":"https://portal.core.cloudapi.de/",
  "ResourceEndpoint":"https://management.microsoftazure.de/",
  "ValidateAuthority":true,
  "VisualStudioOnlineEndpoint":"https://app.vssps.visualstudio.com/",
  "VisualStudioOnlineAudience":"499b84ac-1321-427f-aa17-267ca6975798"
}
```

### <a name="update-visual-studio-for-azure-germany"></a>Actualización de Visual Studio para Azure Alemania

1. Cierre Visual Studio.
1. Coloque *AadProvider.Configuration.json* en *%localappdata%\\.IdentityService\AadConfigurations*. Cree esta carpeta, en caso de que no la encuentre.
1. Inicie Visual Studio y empiece a usar su cuenta de Azure Germany.

> [!NOTE]
> Con el archivo de configuración solo se puede acceder a Azure Alemania. Seguirá viendo las suscripciones que configuró anteriormente, pero no funcionan porque Visual Studio está ahora conectado a Azure Alemania, en lugar de a Azure global. Para conectarse a Azure global, quite el archivo.
>

### <a name="revert-a-visual-studio-connection-to-azure-germany"></a>Reversión de una conexión de Visual Studio a Azure Alemania

Para que Visual Studio pueda conectarse a Azure global, es preciso quitar el archivo de configuración que permite la conexión a Azure Alemania.

1. Cierre Visual Studio.
1. Elimine o cambie el nombre de la carpeta *%localappdata%\.IdentityService\AadConfigurations*.
1. Inicie Visual Studio y empiece a usar su cuenta de Azure global.

> [!NOTE]
> Después de revertir esta configuración, ya no se podrá acceder a las suscripciones de Azure Alemania.
>

## <a name="visual-studio-2015"></a>Visual Studio 2015

Visual Studio 2015 requiere un cambio en el Registro para conectarse a Azure Alemania. Después de establecer esta clave del Registro, Visual Studio se conecta a Azure Alemania, en lugar de a Azure global.

### <a name="update-visual-studio-2015-for-azure-germany"></a>Actualización de Visual Studio 2015 para Azure Alemania

Para que Visual Studio se pueda conectar a Azure Alemania, es preciso actualizar el Registro.

1. Cierre Visual Studio.
1. Cree un archivo de texto llamado *VisualStudioForAzureGermany.reg*.
1. Copie y pegue el siguiente texto en *VisualStudioForAzureGermany.reg*:

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
"AadInstance"="https://login.microsoftonline.de/"
"adaluri"="https://management.microsoftazure.de"
"AzureRMEndpoint"="https://management.microsoftazure.de"
"AzureRMAudienceEndpoint"="https://management.core.cloudapi.de"
"EnableAzureRMIdentity"="true"
"GraphUrl"="graph.cloudapi.de"
"AadApplicationTenant"="f577cd82-810c-43f9-a1f6-0cc532871050"
```

1. Guarde y luego ejecute el archivo haciendo doble clic en él. Se le pide que combine el archivo en el Registro.
1. Inicie Visual Studio y empiece a usar [Cloud Explorer](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer) con su cuenta de Azure Alemania.

> [!NOTE]
> Cuando esta clave del Registro está establecida, solo se puede acceder a las suscripciones de Azure Alemania. Seguirá viendo las suscripciones que configuró anteriormente, pero no funcionan porque Visual Studio está ahora conectado a Azure Alemania, en lugar de a Azure global. Para conectarse a Azure global, revierta los cambios.
>

### <a name="revert-a-visual-studio-2015-connection-to-azure-germany"></a>Reversión de una conexión de Visual Studio 2015 a Azure Alemania

Para que Visual Studio pueda conectarse a Azure global, es preciso quitar los valores del Registro que permiten la conexión a Azure Alemania.

1. Cierre Visual Studio.
1. Cree un archivo de texto llamado *VisualStudioForAzureGermany_Remove.reg*.
1. Copie y pegue el siguiente texto en *VisualStudioForAzureGermany_Remove.reg*:

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
"AadInstance"=-
"adaluri"=-
"AzureRMEndpoint"=-
"AzureRMAudienceEndpoint"=-
"EnableAzureRMIdentity"=-
"GraphUrl"=-
```

1. Guarde y luego ejecute el archivo haciendo doble clic en él. Se le pide que combine el archivo en el Registro.
1. Inicie Visual Studio.

> [!NOTE]
> Después de revertir esta clave del Registro, aparecerán las suscripciones de Azure Alemania, pero no podrá acceder a ellas. Quítelas de forma segura.
>

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de cómo conectarse a Azure Alemania, consulte los siguientes recursos:

* [Conexión a Azure Alemania mediante PowerShell](./germany-get-started-connect-with-ps.md)
* [Conexión a Azure Alemania mediante la CLI de Azure](./germany-get-started-connect-with-cli.md)
* [Conexión a Azure Alemania mediante Azure Portal](./germany-get-started-connect-with-portal.md)