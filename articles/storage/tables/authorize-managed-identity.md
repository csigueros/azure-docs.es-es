---
title: Autorización del acceso a datos de tabla con una identidad administrada (versión preliminar)
titleSuffix: Azure Storage
description: Use identidades administradas para recursos de Azure para autorizar el acceso a datos de tabla desde aplicaciones que se ejecutan en máquinas virtuales de Azure, aplicaciones de funciones y otras.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/11/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d6410115f9ba12f0beb5dc1408a076753182797
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859604"
---
# <a name="authorize-access-to-table-data-with-managed-identities-for-azure-resources-preview"></a>Autorización del acceso a datos de tabla con identidades administradas para recursos de Azure (versión preliminar)

Azure Table Storage admite la autenticación de Azure Active Directory (Azure AD) con [identidades administradas para recursos de Azure](../../active-directory/managed-identities-azure-resources/overview.md). Las identidades administradas para recursos de Azure pueden autorizar el acceso a datos de tabla con credenciales de Azure AD desde aplicaciones que se ejecutan en máquinas virtuales (VM) de Azure, aplicaciones de funciones, conjuntos de escalado de máquinas virtuales y otros servicios. Si usa identidades administradas para recursos de Azure junto con autenticación de Azure AD, puede evitar el almacenamiento de credenciales con las aplicaciones que se ejecutan en la nube.

En este artículo se muestra cómo autorizar el acceso a datos de tabla desde una máquina virtual de Azure mediante identidades administradas para recursos de Azure.

> [!IMPORTANT]
> La autorización con Azure AD para tablas se encuentra actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="enable-managed-identities-on-a-vm"></a>Habilitación de identidades administradas en una máquina virtual

Para poder usar identidades administradas para recursos de Azure a fin de autorizar el acceso a tablas desde la máquina virtual, primero debe habilitar las identidades administradas para recursos de Azure en la máquina virtual. Para aprender a habilitar las identidades administradas para los recursos de Azure, consulte uno de estos artículos:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI de Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Plantilla de Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas cliente de Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Para más información sobre las identidades administradas, consulte [Identidades administradas para recursos de Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="assign-an-rbac-role-to-a-managed-identity"></a>Asignación de un rol RBAC a una identidad administrada

Cuando una entidad de seguridad de Azure AD intenta acceder a los datos de una cuenta de Azure Storage, esa entidad de seguridad debe tener permisos para el recurso de datos. Si la entidad de seguridad es una identidad administrada en Azure o una cuenta de usuario de Azure AD que ejecuta código en el entorno de desarrollo, se le debe asignar un rol de Azure que conceda acceso a los datos en Azure Storage. Para información sobre la asignación de permisos para el acceso a los datos mediante Azure RBAC, consulte [Asignación de un rol de Azure para acceder a datos de blobs](assign-azure-role-data-access.md).

> [!NOTE]
> Al crear una cuenta de Azure Storage, no se le asignan automáticamente permisos para tener acceso a datos a través de Azure AD. Tiene que asignarse a sí mismo de forma explícita un rol de Azure para Azure Storage. Puede asignarlo a nivel de su suscripción, grupo de recursos, cuenta de almacenamiento o tabla.

## <a name="use-a-managed-identity-to-create-a-table-in-net"></a>Uso de una identidad administrada para crear una tabla en .NET

La biblioteca cliente de Azure Identity simplifica el proceso de obtener un token de acceso de OAuth 2.0 para la autorización con Azure Active Directory (Azure AD) a través del [SDK de Azure](https://github.com/Azure/azure-sdk). Al usar la biblioteca cliente de Azure Identity para obtener un token de acceso, puede emplear el mismo código para adquirir el token tanto si la aplicación se ejecuta en el entorno de desarrollo como en Azure. Para más información, consulte [Uso de la biblioteca de Azure Identity para obtener un token de acceso para la autorización](../common/identity-library-acquire-token.md).

Para obtener un token que el código pueda usar para autorizar solicitudes a Azure Storage, cree una instancia de la clase [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). A continuación, puede usar el token para crear un objeto de cliente de servicio que esté autorizado para realizar operaciones de datos en Azure Storage. Para más información sobre el uso de la clase **DefaultAzureCredential** para autorizar a una identidad administrada el acceso a Azure Storage, consulte [Biblioteca cliente de Azure Identity para .NET](/dotnet/api/overview/azure/identity-readme).

En el ejemplo de código siguiente se muestra cómo obtener la credencial de token autenticada y usarla para crear un objeto de cliente de servicio y, luego, usar el cliente de servicio para crear una tabla:

```csharp
public static void CreateTable(string accountName, string tableName)
{
    // Construct the table endpoint from the arguments.
    string tableEndpoint = string.Format("https://{0}.table.core.windows.net/",
                                                accountName);

    // Get a token credential and create a service client object for the table.
    TableClient tableClient = new TableClient(new Uri(tableEndpoint), 
                                                tableName, 
                                                new DefaultAzureCredential());

    try
    {
        // Create the table.
        tableClient.Create();

    }
    catch (RequestFailedException e)
    {
        Console.WriteLine("Exception: {0}", e.Message);
    }
}
```

> [!NOTE]
> Para autorizar solicitudes para datos de tabla con Azure AD, debe usar HTTPS para esas solicitudes.

## <a name="next-steps"></a>Pasos siguientes

- [Asignación de un rol de Azure para acceder a datos de tabla](assign-azure-role-data-access.md)
- [Autorización del acceso a tablas mediante Azure Active Directory (versión preliminar)](authorize-access-azure-active-directory.md)