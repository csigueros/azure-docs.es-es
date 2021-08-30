---
title: Autenticación en recursos de Azure con servidores habilitados para Arc
description: En este artículo se describe la compatibilidad de Azure Instance Metadata Service con los servidores habilitados para Arc y cómo puede autenticarse en recursos de Azure y locales mediante un secreto.
ms.topic: conceptual
ms.date: 07/16/2021
ms.openlocfilehash: 76f7174792f751322545b1d30bb51476c5339e26
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114389916"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>Autenticación en recursos de Azure con servidores habilitados para Arc

Las aplicaciones o procesos que se ejecutan directamente en servidores habilitados para Azure Arc pueden sacar partido de las identidades administradas para acceder a otros recursos de Azure que admiten la autenticación basada en Azure Active Directory. Una aplicación puede obtener un [token de acceso](../../active-directory/develop/developer-glossary.md#access-token) que represente su identidad (y que puede la asigna el sistema para servidores habilitados para Arc) y usarlo como token de "portador" para autenticarse en otro servicio.

Vea la documentación de [información general sobre las identidades administradas](../../active-directory/managed-identities-azure-resources/overview.md) para obtener una descripción detallada de las identidades administradas, así como saber distinguir las identidades asignadas por el sistema y las asignadas por el usuario.

En este artículo se muestra cómo un servidor puede usar una identidad administrada asignada por el sistema para acceder a Azure [Key Vault](../../key-vault/general/overview.md). Actuando como un arranque, Key Vault hace posible que la aplicación cliente use un secreto para acceder a recursos que no están protegidos por Azure Active Directory (AD). Por ejemplo, los certificados TLS/SSL que los servidores web de IIS usan se pueden almacenar en Azure Key Vault y puede implementar de forma segura los certificados en servidores Windows o Linux fuera de Azure.

## <a name="security-overview"></a>Introducción a la seguridad

Mientras el servidor se incorpora a los servidores habilitados para Azure Arc, se realizan varias acciones para configurar con mediante identidad administrada, similar a lo que se realiza para una máquina virtual de Azure:

- Azure Resource Manager recibe una solicitud para habilitar la identidad administrada asignada por el sistema en el servidor habilitado para Arc.

- Azure Resource Manager crea una entidad de servicio en Azure AD para la identidad del servidor. La entidad de servicio se crea en el inquilino de Azure AD que sea de confianza para la suscripción.

- Azure Resource Manager configura la identidad en el servidor mediante la actualización del punto de conexión de identidad de Azure Instance Metadata Service (IMDS) para [Windows](../../virtual-machines/windows/instance-metadata-service.md) o [Linux](../../virtual-machines/linux/instance-metadata-service.md) con el identificador de cliente y el certificado de la entidad de servicio. El punto de conexión es un punto de conexión de REST solamente accesible desde dentro del servidor mediante una dirección IP no enrutable conocida. Este servicio proporciona un subconjunto de información de metadatos sobre el servidor habilitado para Arc que ayuda a administrarlo y configurarlo.

El entorno de un servidor habilitado para identidades administradas se configurará con las siguientes variables en un servidor habilitado para Windows Arc:

- **IMDS_ENDPOINT**: la dirección IP del punto de conexión de IMDS `http://localhost:40342` para los servidores habilitados para Arc.

- **IDENTITY_ENDPOINT**: el punto de conexión de localhost correspondiente a la identidad administrada del servicio `http://localhost:40342/metadata/identity/oauth2/token`.

El código que se ejecuta en el servidor puede solicitar un token del punto de conexión de Azure Instance Metadata Service, accesible únicamente desde dentro de dicho servidor.

La variable de entorno del sistema **IDENTITY_ENDPOINT** se usa para detectar el punto de conexión de identidad mediante aplicaciones. Las aplicaciones deben intentar recuperar los valores de **IDENTITY_ENDPOINT** e **IMDS_ENDPOINT valores** y usarlos. Las aplicaciones con cualquier nivel de acceso pueden realizar solicitudes a los puntos de conexión. Las respuestas de metadatos se tratan con normalidad y se entregan a cualquier proceso de la máquina. Sin embargo, cuando se realiza una solicitud que expondría un token, requerimos que el cliente proporcione un secreto para dar fe de que puede acceder a los datos que solo están disponibles para usuarios con mayores privilegios.

## <a name="prerequisites"></a>Prerrequisitos

- Conocimientos sobre identidades administradas.
- Un servidor conectado y registrado con servidores habilitados para Arc.
- Debe ser miembro del [grupo Propietario](../../role-based-access-control/built-in-roles.md#owner)** en la suscripción o el grupo de recursos para realizar la creación de los recursos necesarios y los pasos de administración de roles.
- Una instancia de Azure Key Vault para almacenar y recuperar la credencial. y asignar el acceso de identidad de Azure Arc a KeyVault.

    - Si no tiene una instancia de Key Vault creada, consulte [Creación de un almacén de claves](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-).
    - Para configurar el acceso mediante la identidad administrada usada por el servidor, consulte [Conceder acceso para Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access) o [Conceder acceso para Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access). En el paso 5, va a escribir el nombre del servidor habilitado para Arc. Para completar esto con PowerShell, consulte [Asignación de una directiva de acceso de Key Vault mediante Azure PowerShell](../../key-vault/general/assign-access-policy-powershell.md).

## <a name="acquiring-an-access-token-using-rest-api"></a>Adquisición de un token de acceso mediante la API de REST

El método para obtener y usar una identidad administrada asignada por el sistema para autenticarse con recursos de Azure es similar a cómo se realiza con una máquina virtual de Azure.

En el caso de un servidor Windows habilitado para Arc, mediante PowerShell, se invoca la solicitud web para obtener el token del host local en el puerto específico. Especifique la solicitud mediante la dirección IP o la variable de entorno **IDENTITY_ENDPOINT**.

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

La respuesta siguiente es un ejemplo que se devuelve:

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="Una recuperación correcta del token de acceso mediante PowerShell.":::

En el caso de un servidor Linux habilitado para Arc, mediante Bash, se invoca la solicitud web para obtener el token del host local en el puerto específico. Especifique la siguiente solicitud mediante la dirección IP o la variable de entorno **IDENTITY_ENDPOINT**. Para completar ese paso, necesita un cliente SSH.

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

La respuesta siguiente es un ejemplo que se devuelve:

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="Una recuperación correcta del token de acceso mediante Bash.":::

La respuesta incluye el token de acceso que necesita para acceder a cualquier recurso de Azure. Para completar la configuración para autenticarse en Azure Key Vault, consulte [Acceso a Key Vault con Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) o [Acceso a Key Vault con Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data).

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre Azure Key Vault, consulte [Acerca de Azure Key Vault](../../key-vault/general/overview.md).

- Obtenga información sobre cómo asignar un acceso de identidad administrada a un recurso [mediante PowerShell](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) o mediante [la CLI de Azure](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).