---
title: Creación de una entidad de servicio de Azure Active Directory desde la CLI de Azure
titleSuffix: An Azure Communication Services quickstart
description: En este inicio rápido, se creará una aplicación y una entidad de servicio para realizar la autenticación con Azure Communication Services.
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 06/30/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: 5b25c0c5d140302d6ea8abbc3eeafaf47fb3b64b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473168"
---
# <a name="quickstart-authenticate-using-azure-active-directory-azure-cli"></a>Inicio rápido: Autenticación con Azure Active Directory (CLI de Azure)

El SDK de identidad de Azure proporciona compatibilidad con la autenticación de token de Azure Active Directory (Azure AD) para paquetes de SDK de Azure. Las versiones más recientes del SDK de Azure Communication Services para .NET, Java, Python y JavaScript se integran con la biblioteca de identidades de Azure para proporcionar un medio sencillo y seguro de obtener un token de OAuth 2.0 para la autorización de las solicitudes de Azure Communication Services.

Una ventaja del SDK de Azure Identity es que permite usar el mismo código para autenticarse en varios servicios tanto si la aplicación se ejecuta en el entorno de desarrollo como en Azure. 

El SDK de identidad de Azure puede realizar la autenticación con diversos métodos. En desarrollo, se usará una entidad de servicio asociada a una aplicación registrada con las credenciales almacenadas en las variables de entorno, lo cual resulta conveniente para desarrollo y pruebas.

## <a name="prerequisites"></a>Prerrequisitos

 - CLI de Azure. [Guía de instalación](/cli/azure/install-azure-cli)
 - Una cuenta de Azure con una suscripción activa. [Cree su cuenta de forma gratuita](https://azure.microsoft.com/free).

## <a name="setting-up"></a>Instalación

Al usar Active Directory para otros recursos de Azure, debe usar identidades administradas. Para aprender a habilitar las identidades administradas para los recursos de Azure, consulte uno de estos artículos:

- [Azure Portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI de Azure](../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Plantilla de Azure Resource Manager](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [SDK de Azure Resource Manager](../../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Servicios de aplicaciones](../../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>Autenticación de una aplicación registrada en el entorno de desarrollo

Si el entorno de desarrollo no admite el inicio de sesión único ni mediante un explorador web, puede usar una aplicación registrada para autenticarse desde el entorno de desarrollo.

### <a name="creating-an-azure-active-directory-registered-application"></a>Creación de una aplicación registrada de Azure Active Directory

Para crear una aplicación registrada mediante la CLI de Azure, debe haber iniciado sesión en la cuenta de Azure en la que desea que se realicen las operaciones. Para ello, puede usar el comando `az login` y escribir sus credenciales en el explorador. Una vez que haya iniciado sesión en la cuenta de Azure desde la CLI, podemos llamar al comando `az ad sp create-for-rbac` para crear la aplicación registrada y la entidad de servicio.

En los ejemplos siguientes se usa la CLI de Azure para crear una nueva aplicación registrada

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

El comando `az ad sp create-for-rbac` devuelve una lista de propiedades de entidad de servicio en formato JSON. Copie estos valores para poder usarlos con el fin de crear las variables de entorno necesarias en el paso siguiente.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> La propagación de las asignaciones de roles de Azure pueden tardar unos minutos.

#### <a name="set-environment-variables"></a>Establecimiento de variables de entorno

El SDK de Azure Identity lee valores de tres variables del entorno de ejecución para autenticar la entidad de servicio. En la tabla siguiente se describe el valor que se debe establecer para cada variable de entorno.

| Variable de entorno  | Value                                    |
| --------------------- | ---------------------------------------- |
| `AZURE_CLIENT_ID`     | Valor `appId` del JSON generado    |
| `AZURE_TENANT_ID`     | Valor `tenant` del JSON generado   |
| `AZURE_CLIENT_SECRET` | Valor `password` del JSON generado |

> [!IMPORTANT]
> Después de establecer las variables de entorno, cierre y vuelva a abrir la ventana de la consola. Si usa Visual Studio u otro entorno de desarrollo, es posible que tenga que reiniciarlo para que registre las nuevas variables de entorno.

Una vez que se hayan establecido estas variables, debería poder usar el objeto DefaultAzureCredential en el código para autenticarse en el cliente de servicio de su elección.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Información sobre la autenticación](../../concepts/authentication.md)

Puede que también le interese:

- [Más información sobre la biblioteca de identidades de Azure](/dotnet/api/overview/azure/identity-readme)
