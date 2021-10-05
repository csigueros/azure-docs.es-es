---
title: Configuración de la aceleración automática de inicio de sesión mediante la detección del dominio de inicio
titleSuffix: Azure AD
description: Aprenda a configurar la directiva de detección del dominio de inicio para la autenticación de Azure Active Directory para los usuarios federados, incluidas sugerencias de dominio y aceleración automática.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/13/2021
ms.author: davidmu
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.reviewer: hirsin
ms.openlocfilehash: 56a4c06759841e5e011deb5fde50f1e9fe2cbea7
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061834"
---
# <a name="configure-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Configuración del comportamiento de inicio de sesión de una aplicación mediante una directiva de detección del dominio de inicio

En este artículo se proporciona una introducción a la configuración del comportamiento de autenticación de Azure Active Directory (Azure AD) para los usuarios federados mediante la directiva Detección de dominio principal.  Se trata el uso de la aceleración automática para omitir la pantalla de entrada de nombre de usuario y reenviar automáticamente a los usuarios a los puntos de conexión de inicio de sesión federados.  Microsoft ya no recomienda configurar la aceleración automática, ya que puede impedir el uso de métodos de autenticación más seguros como First Identity Online (FIDO) y dificulta la colaboración.

## <a name="prerequisites"></a>Requisitos previos

Para configurar la directiva de HRD para una aplicación en Azure AD, necesita:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.
- La versión preliminar más reciente de los cmdlets de PowerShell de Azure AD.

## <a name="set-up-an-hrd-policy-on-an-application"></a>Establecimiento de una directiva de HRD en una aplicación

Usaremos los cmdlets de PowerShell de Azure AD para desplazarse por algunos escenarios, incluidos:

- La configuración de la directiva HRD para la aceleración automática de una aplicación en un inquilino con un dominio federado único.

- La configuración de la directiva HRD para la aceleración automática de una aplicación en uno de varios dominios que se verifican en el inquilino.

- La configuración de la directiva HRD para habilitar una aplicación heredada para que haga la autenticación de nombre de usuario y contraseña directamente a Azure AD para un usuario federado.

- Enumerar las aplicaciones para las que se configura una directiva.

En los ejemplos siguientes, podrá crear, actualizar, vincular y eliminar directivas en las entidades de servicio de aplicación en Azure AD.

1. Antes de empezar, ejecute el comando Connect para iniciar sesión con la cuenta de administrador de Azure AD:

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. Ejecute el siguiente comando para ver todas las directivas de la organización:

    ```powershell
    Get-AzureADPolicy
    ```

Si no se devuelve nada, significa que no tiene directivas creadas en el inquilino.

En este ejemplo, puede crear una directiva que, cuando se asigne a una aplicación:

- Acelere automáticamente a los usuarios hacia una pantalla de inicio de sesión de AD FS cuando estos inicien sesión en una aplicación y cuando haya un solo dominio en su inquilino.
- Acelere automáticamente a los usuarios hacia una pantalla de inicio de sesión de AD FS si hay más de un dominio federado en su inquilino.
- Permita el inicio de sesión no interactivo de nombre de usuario y contraseña directamente a Azure AD a los usuarios federados para las aplicaciones a las que está asignada la directiva.

## <a name="create-an-hrd-policy"></a>Creación de una directiva de HRD

La directiva siguiente acelera automáticamente a los usuarios hacia una pantalla de inicio de sesión de AD FS cuando estos inician sesión en una aplicación y cuando hay un solo dominio en su inquilino.

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

La siguiente directiva acelera automáticamente a los usuarios hacia una pantalla de inicio de sesión de AD FS cuando hay más de un dominio federado en su inquilino. Si tiene más de un dominio federado que autentica a los usuarios para las aplicaciones, es necesario especificar el dominio para la aceleración automática.

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Para crear una directiva a fin de habilitar la autenticación de nombre de usuario y contraseña para los usuarios federados directamente con Azure AD para aplicaciones específicas, ejecute el siguiente comando:

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

Para ver la nueva directiva y obtener el valor de **ObjectID**, ejecute el siguiente comando:

```powershell
Get-AzureADPolicy
```

Para aplicar la directiva de HRD después de crearla, puede asignarla a varias entidades de servicio de aplicación.

## <a name="locate-the-service-principal-to-which-to-assign-the-policy"></a>Búsqueda de la entidad de servicio a la cual se asignará la directiva

Para ello, necesita el valor de **ObjectID** de las entidades de servicio a las que quiere asignar la directiva. Hay varias maneras de buscar el valor de **ObjectID** de las entidades de servicio.

Puede usar el [portal de Azure](/graph/api/resources/serviceprincipal) o bien puede consultar [Microsoft Graph](https://portal.azure.com). También puede ir a la [herramienta Probador de Graph](https://developer.microsoft.com/graph/graph-explorer) e iniciar sesión en su cuenta de Azure AD para ver todas las entidades de servicio de su organización.

Dado que usa PowerShell, puede usar el cmdlet siguiente para enumerar las entidades de servicio y sus identificadores.

```powershell
Get-AzureADServicePrincipal
```

## <a name="assign-the-policy-to-your-service-principal"></a>Asignación de la directiva a su entidad de servicio

Una vez tenga el valor de **ObjectID** de la entidad de servicio de la aplicación para el que quiere configurar la aceleración automática, ejecute el siguiente comando. Este comando asocia la directiva HRD que creó en el paso 1 con la entidad de servicio que encuentra en el paso 2.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Puede repetir este comando para cada entidad de servicio a la que quiera agregar la directiva.

En caso de que una aplicación ya tenga una directiva HomeRealmDiscovery asignada, no podrá agregar un segunda.  En ese caso, cambie la definición de la directiva de detección del dominio de inicio que está asignada a la aplicación para agregar parámetros adicionales.

### <a name="check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Comprobación de a qué entidades de servicio de aplicación está asignada la directiva de HRD

Para comprobar qué aplicaciones tienen configurada la directiva de aceleración automática, use el cmdlet **Get-AzureADPolicyAppliedObject**. Pásele el valor de **ObjectID** de la directiva que quiera comprobar.

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>

Try the application to check that the new policy is working.

### List the applications for which HRD policy is configured

1. List all policies that were created in your organization

```powershell
Get-AzureADPolicy
```

Apunte el valor de **ObjectID** de la directiva de la cual quiere enumerar las asignaciones.

2. Enumeración de las entidades de servicio a las que se asignó la directiva

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="remove-an-hrd-policy-from-an-application"></a>Supresión de una directiva de HRD de una aplicación

1. Obtención del valor de ObjectID

Use el ejemplo anterior para obtener el valor de **ObjectID** de la directiva y el de la entidad de servicio de aplicación de la que quiere eliminarlo.

2. Supresión de la asignación de directiva de la entidad de servicio de aplicación

```powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

3. Comprobación de que se eliminó correctamente; para ello, enumere las entidades de servicio a las que se asignó la directiva

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre cómo funciona la detección de dominios de inicio en Azure AD, consulte [Detección del dominio de inicio de una aplicación en Azure AD](home-realm-discovery-policy.md).