---
title: Incorporación de un cliente a Azure Lighthouse
description: Aprenda a incorporar un cliente a Azure Lighthouse para permitir que los usuarios de su inquilino accedan a sus recursos y los administren.
ms.date: 09/30/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3dbd3eb285a0135ca1b86294c5d3a41ef88a8472
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353306"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Incorporación de un cliente a Azure Lighthouse

En este artículo se explica cómo, como proveedor de servicios, puede incorporar un cliente a Azure Lighthouse. Cuando lo haga, los recursos delegados (suscripciones o grupos de recursos) del inquilino de Azure Active Directory del cliente se pueden administrar por los usuarios de su inquilino mediante la [Administración de recursos delegados de Azure](../concepts/architecture.md).

> [!TIP]
> Aunque en este tema nos referiremos a los proveedores de servicios y clientes, las [empresas que administren varios inquilinos](../concepts/enterprise.md) pueden usar el mismo proceso para configurar Azure Lighthouse y consolidar su experiencia de administración.

Puede repetir el proceso de incorporación de varios clientes. Cuando un usuario con permisos apropiados inicia sesión en el inquilino administrador, se puede autorizar a ese usuario en los ámbitos del inquilino del cliente para realizar operaciones de administración sin tener que iniciar sesión en todos y cada uno de los inquilinos de cliente individuales.

> [!NOTE]
> Los clientes también se pueden incorporar a Azure Lighthouse cuando compran una oferta de servicios administrados (pública o privada) que [haya publicado en Azure Marketplace](publish-managed-services-offers.md). También puede usar el proceso de incorporación que se describe aquí junto con ofertas publicadas en Azure Marketplace.

El proceso de incorporación requiere que se tomen medidas desde el inquilino del proveedor de servicios y del inquilino del cliente. Todos estos pasos se describen en este artículo.

## <a name="gather-tenant-and-subscription-details"></a>Recopilación de los detalles del inquilino y la suscripción

Para incorporar el inquilino de un cliente, este debe tener una suscripción activa a Azure. Necesitará saber:

- El identificador del inquilino del proveedor de servicios (donde va a administrar los recursos del cliente). Si [crea la plantilla en Azure Portal](#create-your-template-in-the-azure-portal), este valor se proporciona automáticamente.
- El identificador del inquilino del cliente (cuyos recursos administrará el proveedor de servicios).
- Los identificadores de suscripción de cada suscripción específica del inquilino del cliente que administrará el proveedor de servicios (o que contenga los grupos de recursos que administrará el proveedor de servicios).

Si no conoce el identificador de un inquilino, puede [recuperarlo mediante Azure Portal, Azure PowerShell o la CLI de Azure](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md).

## <a name="define-roles-and-permissions"></a>Definir roles y permisos

Como proveedor de servicios, es posible que quiera realizar varias tareas para un solo cliente, lo que requiere un acceso diferente para distintos ámbitos. Puede definir tantas autorizaciones como necesite para asignar los [roles integrados de Azure](../../role-based-access-control/built-in-roles.md) adecuados. Cada autorización incluye un valor **principalId** que hace referencia a un usuario, grupo o entidad de servicio de Azure AD en el inquilino de administración.

> [!NOTE]
> A menos que se especifique explícitamente, las referencias a un "usuario" en la documentación de Azure Lighthouse pueden referirse a un usuario, grupo o entidad de servicio de Azure AD en una autorización.

Para definir las autorizaciones, tiene que conocer los valores de identificador de cada usuario, grupo de usuarios o entidad de servicio en el inquilino de administración al que quiere conceder acceso. Puede [recuperar estos identificadores mediante Azure Portal, Azure PowerShell o la CLI de Azure](../../role-based-access-control/role-assignments-template.md#get-object-ids) desde el inquilino de administración. También necesitará el identificador de definición de roles para cada [rol integrado](../../role-based-access-control/built-in-roles.md) que quiera asignar.

> [!TIP]
> Se recomienda asignar el [rol de eliminación de asignación del registro de servicios administrados](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) al incorporar un cliente, de modo que los usuarios del inquilino puedan [quitar el acceso a la delegación](remove-delegation.md) más adelante si es necesario. Si este rol no está asignado, solo un usuario puede quitar los recursos delegados del inquilino del cliente.

Se recomienda usar grupos de usuarios de Azure AD para cada rol siempre que sea posible, en lugar de usuarios individuales. Esto le ofrece la flexibilidad para agregar o quitar usuarios individuales del grupo que tiene acceso, de modo que no tenga que repetir el proceso de incorporación para realizar cambios en el usuario. También puede asignar roles a una entidad de servicio, lo que puede ser útil para escenarios de automatización.

> [!IMPORTANT]
> Para agregar permisos a un grupo de Azure AD, el **Tipo de grupo** debe establecerse en **Seguridad**. Esta opción se selecciona cuando se crea el grupo. Para obtener más información vea [Creación de un grupo básico e incorporación de miembros con Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Al definir las autorizaciones, asegúrese de seguir el principio de privilegios mínimos para que los usuarios solo tengan los permisos necesarios para completar su trabajo. Para obtener información sobre los roles admitidos y los procedimientos recomendados, vea [Inquilinos, usuarios y roles en escenarios de Azure Lighthouse](../concepts/tenants-users-roles.md).

Para realizar el seguimiento del impacto en las involucraciones de los clientes y recibir el reconocimiento correspondiente, asocie el identificador de Microsoft Partner Network (MPN) con al menos una cuenta de usuario con acceso a cada una de las suscripciones incorporadas. Tendrá que realizar esta asociación en el inquilino del proveedor de servicios. Se recomienda crear una cuenta de entidad de servicio en el inquilino que esté asociado a su identificador de MPN y, a continuación, incluya esa entidad de servicio cada vez que incorpore un cliente. Para obtener más información, consulte el artículo sobre la [vinculación de un identificador de asociado para habilitar el crédito que ha obtenido un asociado en los recursos delegados](partner-earned-credit.md).

> [!TIP]
> También puede crear *autorizaciones aptas* que permitan a los usuarios del inquilino de administración tener un rol superior temporalmente. Esta característica está actualmente en una versión preliminar pública y tiene requisitos de licencia específicos. Para obtener más información, vea [Creación de autorizaciones aptas](create-eligible-authorizations.md).

## <a name="create-an-azure-resource-manager-template"></a>Creación de una plantilla de Azure Resource Manager

Para incorporar el cliente, deberá crear una plantilla de [Azure Resource Manager](../../azure-resource-manager/index.yml) para la oferta con la información siguiente. Los valores **mspOfferName** y **mspOfferDescription** serán visibles para el cliente en la [página de proveedores de servicios](view-manage-service-providers.md) de Azure Portal una vez que la plantilla se implementa en el inquilino del cliente.

|Campo  |Definición  |
|---------|---------|
|**mspOfferName**     |Nombre que describe esta definición. Este valor se muestra al cliente como el título de la oferta y debe ser un valor único.        |
|**mspOfferDescription**     |Breve descripción de la oferta (por ejemplo, "oferta de administración de VM de Contoso"). Este campo es opcional, pero se recomienda para que los clientes comprendan claramente su oferta.   |
|**managedByTenantId**     |El identificador de inquilino.          |
|**authorizations**     |Los valores de **principalId** para los usuarios/grupos/SPN del inquilino, cada uno de ellos con un valor de **principalIdDisplayName** para ayudar a su cliente a entender el propósito de la autorización y asignarla a un valor de **roleDefinitionId** integrado para especificar el nivel de acceso.      |

Puede crear esta plantilla en Azure Portal o modificando manualmente las plantillas proporcionadas en nuestro [repositorio de ejemplos](https://github.com/Azure/Azure-Lighthouse-samples/). 

> [!IMPORTANT]
> El proceso que se describe aquí requiere una implementación independiente para cada suscripción que se está incorporando, incluso si va a incorporar suscripciones en el mismo inquilino de cliente. También se necesitan implementaciones independientes si se incorporan varios grupos de recursos en distintas suscripciones del mismo inquilino del cliente. Sin embargo, la incorporación de varios grupos de recursos dentro de una sola suscripción puede realizarse en una implementación.
>
> También se necesitan implementaciones independientes para varias ofertas que se aplican a la misma suscripción (o grupos de recursos dentro de una suscripción). Cada oferta aplicada debe usar un valor de **mspOfferName** diferente.

### <a name="create-your-template-in-the-azure-portal"></a>Creación de la plantilla en Azure Portal

Para crear la plantilla en Azure Portal, vaya a **Mis clientes** y seleccione **Crear plantilla ARM** en la página de información general.

En la página **Crear oferta de plantilla ARM**, proporcione su **nombre** y una **descripción** opcional. Estos valores se usarán en **mspOfferName** y **mspOfferDescription** en la plantilla. El valor **managedByTenantId** se proporcionará automáticamente, en función del inquilino de Azure AD en el que ha iniciado sesión.

A continuación, seleccione **Suscripción** o **Grupo de recursos**, en función del ámbito del cliente que desee incorporar. Si selecciona **Grupo de recursos**, tendrá que proporcionar el nombre del grupo de recursos para incorporarlo. Puede seleccionar el icono **+** para agregar grupos de recursos adicionales según sea necesario. (Todos los grupos de recursos deben estar en la misma suscripción de cliente).

Por último, para crear las autorizaciones, seleccione **+ Agregar autorización**. Para cada una de las autorizaciones, proporcione los detalles siguientes:

1. Seleccione **Tipo de entidad de seguridad** según el tipo de cuenta que desee incluir en la autorización. Este puede ser **Usuario**, **Grupo** o **Entidad de servicio**. En este ejemplo, elegiremos **Usuario**.
1. Seleccione el enlace **+ Seleccionar usuario** para abrir el panel de selección. Puede usar el campo de búsqueda para buscar el usuario que desea agregar. Una vez que lo haya hecho, haga clic en **Seleccionar**. El campo **Id. de la entidad de seguridad** del usuario se rellenará automáticamente.
1. Revise el campo **Nombre para mostrar** (rellenado según el usuario que seleccionó) y haga los cambios que desee.
1. Seleccione el **rol** que va a asignar al usuario.
1. En tipo de **Acceso**, seleccione **Permanente** o **Elegible**. Si selecciona **Elegible**, deberá especificar opciones para la duración máxima, la autenticación multifactor y si se requiere o no aprobación. Para más información, consulte [Creación de autorizaciones aptas](create-eligible-authorizations.md). La característica de autorizaciones aptas se encuentra actualmente en versión preliminar pública y no se puede usar con entidades de servicio.
1. Seleccione **Agregar** para crear la autorización.

:::image type="content" source="../media/add-authorization.png" alt-text="Captura de pantalla de la sección Agregar autorización de Azure Portal.":::

Después de seleccionar **Agregar**, volverá a la pantalla **Crear oferta de plantilla ARM**. Puede seleccionar **+ Agregar autorización** de nuevo para agregar tantas autorizaciones como sea necesario.

Cuando haya agregado todas las autorizaciones, seleccione **Ver plantilla**. En esta pantalla, verá un archivo .json que corresponde a los valores especificados. Seleccione **Descargar** para guardar una copia de este archivo .json. Esta plantilla se puede [implementar en el inquilino del cliente](#deploy-the-azure-resource-manager-template). También puede editarla manualmente si necesita realizar cambios. Tenga en cuenta que el archivo no se almacena en Azure Portal.

### <a name="create-your-template-manually"></a>Creación manual de la plantilla

Puede crear la plantilla a partir de una plantilla de Azure Resource Manager (proporcionada en el [repositorio de ejemplos](https://github.com/Azure/Azure-Lighthouse-samples/)) y un archivo de parámetros correspondiente que se modifica para que coincida con la configuración y para definir las autorizaciones. Si lo prefiere, puede incluir toda la información directamente en la plantilla, en lugar de usar un archivo de parámetros independiente.

La plantilla que elija dependerá de si se incorpora una suscripción completa, un grupo de recursos o varios grupos de recursos dentro de una suscripción. También proporcionamos una plantilla que se puede usar para los clientes que han adquirido una oferta de servicios administrados que ha publicado en Azure Marketplace, si prefiere incorporar sus suscripciones de esta manera.

|Para incorporar esto  |Use esta plantilla de Azure Resource Manager  |Y modifique este archivo de parámetros |
|---------|---------|---------|
|Suscripción   |[subscription.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/subscription/subscription.json)  |[subscription.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/subscription/subscription.parameters.json)    |
|Resource group   |[rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/rg/rg.json)  |[rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/rg/rg.parameters.json)    |
|Varios grupos de recursos de una suscripción   |[multi-rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/rg/multi-rg.json)  |[multiple-rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/rg/multiple-rg.parameters.json)    |
|Suscripción (al usar una oferta publicada en Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

Si desea incluir [autorizaciones elegibles](create-eligible-authorizations.md#create-eligible-authorizations-using-azure-resource-manager-templates) (actualmente en versión preliminar pública), seleccione la plantilla correspondiente en la sección [delegated-resource-management-eligible-authorizations](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations) de nuestro repositorio de ejemplos.

> [!TIP]
> Aunque no se puede incorporar un grupo de administración completo en una implementación, es posible implementar una directiva para [incorporar cada suscripción en un grupo de administración](onboard-management-group.md). Tendrá acceso a todas las suscripciones del grupo de administración, aunque tendrá que trabajar en ellas como si fueran suscripciones individuales (en lugar de realizar acciones en el recurso del grupo de administración directamente).

En el ejemplo siguiente se muestra un archivo **subscription.parameters.json** modificado que se puede usar para incorporar una suscripción. Los archivos de parámetros del grupo de recursos (situados en la carpeta [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management/rg)) son similares, pero también incluyen un parámetro **rgName** para identificar los grupos de recursos específicos que se incorporarán.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "00000000-0000-0000-0000-000000000000"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

La última autorización del ejemplo anterior agrega un valor de **principalId** con el rol de administrador de acceso de usuario (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Al asignar este rol, debe incluir la propiedad **delegatedRoleDefinitionIds** y uno o más roles integrados de Azure compatibles. El usuario creado en esta autorización podrá asignar estos roles a las [identidades administradas](../../active-directory/managed-identities-azure-resources/overview.md) en el inquilino del cliente, algo necesario para [implementar directivas que pueden corregirse](deploy-policy-remediation.md).  El usuario también puede crear incidentes de soporte técnico. No se aplicará a este **principalId** ningún otro permiso asociado normalmente al rol Administrador de acceso de usuario.

## <a name="deploy-the-azure-resource-manager-template"></a>Implementación de la plantilla de Azure Resource Manager

Una vez que haya creado la plantilla, un usuario del inquilino del cliente debe implementarla en su inquilino. Se necesita una implementación independiente para cada suscripción que quiera incorporar (o para cada suscripción que contenga grupos de recursos que quiera incorporar).

Al incorporar una suscripción (o uno o varios grupos de recursos dentro de una suscripción) mediante el proceso que se describe aquí, el proveedor de recursos **Microsoft.ManagedServices** se registrará para esa suscripción.

> [!IMPORTANT]
> Esta implementación debe realizarse desde una cuenta que no sea de invitado en el inquilino del cliente, que tenga el rol con el permiso `Microsoft.Authorization/roleAssignments/write`, como [Propietario](../../role-based-access-control/built-in-roles.md#owner), para la suscripción que se va a incorporar (o que contenga los grupos de recursos que se están incorporando). Para buscar usuarios que puedan delegar la suscripción, un usuario del inquilino del cliente puede seleccionar la suscripción en Azure Portal, abrir **Control de acceso (IAM)** y [ver todos los usuarios con el rol Propietario](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).
>
> Si la suscripción se creó con el [programa Proveedor de soluciones en la nube (CSP)](../concepts/cloud-solution-provider.md), cualquier usuario que tenga el rol de [agente de administración](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) en el inquilino del proveedor de servicios puede realizar la implementación.

La implementación puede realizarse mediante PowerShell, la CLI de Azure o Azure Portal, como se muestra a continuación.

### <a name="powershell"></a>PowerShell

Para implementar únicamente una plantilla:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -Verbose
```

Para implementar una plantilla con un archivo de parámetros independiente:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

Para implementar únicamente una plantilla:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --verbose
```

Para implementar una plantilla con un archivo de parámetros independiente:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --parameters <parameters/parameterFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --parameters <parameterFile> \
                         --verbose
```

### <a name="azure-portal"></a>Azure portal

Con esta opción, puede modificar la plantilla directamente en Azure Portal y, a continuación, implementarla. Esto lo debe hacer un usuario en el inquilino del cliente.

1. En nuestro [repositorio de GitHub](https://github.com/Azure/Azure-Lighthouse-samples/), seleccione el botón **Deploy to Azure** (Implementar en Azure) que se muestra junto a la plantilla que quiera usar. La plantilla se abrirá en Azure Portal.
1. Escriba los valores de **Msp Offer Name** (nombre de oferta de MSP), **Msp Offer Description** (descripción de la oferta de MSP), **Managed by Tenant Id** (administrado por id. de inquilino) y **Authorizations** (autorizaciones). Si lo prefiere, puede seleccionar **Editar parámetros** para especificar valores de `mspOfferName`, `mspOfferDescription`, `managedbyTenantId` y `authorizations` directamente en el archivo de parámetros. Asegúrese de actualizar estos valores, en lugar de usar los valores predeterminados de la plantilla.
1. Seleccione **Revisar y crear** y, luego, **Crear**.

Transcurridos unos minutos, se mostrará una notificación indicando que la implementación se ha completado.

## <a name="confirm-successful-onboarding"></a>Confirmar la incorporación correcta

Cuando una suscripción de cliente se incorpora correctamente a Azure Lighthouse, los usuarios del inquilino del proveedor de servicios pueden ver la suscripción y sus recursos (si se les ha concedido acceso a ellos mediante el proceso anterior, ya sea individualmente o como miembro de un grupo de Azure AD con los permisos adecuados). Para confirmarlo, compruebe que la suscripción aparece de una de las siguientes maneras.  

### <a name="azure-portal"></a>Azure Portal

En el inquilino del proveedor de servicios:

1. Vaya a la página [Mis clientes](view-manage-customers.md).
2. Seleccione **Clientes**.
3. Confirme que puede ver las suscripciones con el nombre de la oferta que proporcionó en la plantilla de Resource Manager.

> [!IMPORTANT]
> Para ver la suscripción delegada en [Mis clientes](view-manage-customers.md). Se debe haber concedido a los usuarios del inquilino del proveedor de servicios el rol [Lector](../../role-based-access-control/built-in-roles.md#reader) (u otro rol integrado que incluya acceso de lectura) al incorporar la suscripción.

En el inquilino del cliente:

1. Vaya a la [página Proveedores de servicios](view-manage-service-providers.md).
2. Seleccione **Ofertas del proveedor de servicios**.
3. Confirme que puede ver las suscripciones con el nombre de la oferta que proporcionó en la plantilla de Resource Manager.

> [!NOTE]
> Una vez completada la implementación, las actualizaciones pueden tardar hasta 15 minutos en verse reflejadas en Azure Portal. Puede ver las actualizaciones antes si actualiza el token de Azure Resource Manager actualizando el explorador, iniciando y cerrando la sesión o solicitando un token nuevo.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext

# Confirm successful onboarding for Azure Lighthouse

Get-AzManagedServicesDefinition
Get-AzManagedServicesAssignment
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list

# Confirm successful onboarding for Azure Lighthouse

az managedservices definition list
az managedservices assignment list
```

Si necesita realizar cambios una vez que el cliente se ha incorporado, puede [actualizar la delegación](update-delegation.md). También puede [retirar el acceso a la delegación](remove-delegation.md) completamente.

## <a name="troubleshooting"></a>Solución de problemas

Si no puede incorporar correctamente el cliente, o si los usuarios tienen problemas para acceder a los recursos delegados, compruebe las siguientes sugerencias y requisitos e inténtelo de nuevo.

- A los usuarios que necesiten ver los recursos del cliente en Azure Portal se les debe haber asignado el rol [Lector](../../role-based-access-control/built-in-roles.md#reader) (o cualquier otro rol integrado que incluya acceso de lectura) durante el proceso de incorporación.
- El valor `managedbyTenantId` no debe ser el mismo que el identificador de inquilino de la suscripción que se va a incorporar.
- No puede tener varias asignaciones en el mismo ámbito con el mismo `mspOfferName`.
- El proveedor de recursos **Microsoft.ManagedServices** se debe registrar para la suscripción delegada. Esto se debería realizar automáticamente durante la implementación, pero si no es así, puede [registrarlo manualmente](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).
- Las autorizaciones no deben incluir ningún usuario con el rol integrado [Propietario](../../role-based-access-control/built-in-roles.md#owner) ni ningún rol integrado con [DataActions](../../role-based-access-control/role-definitions.md#dataactions).
- Los grupos se deben crear con [**Tipo de grupo**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types) establecido en **Security** y no en **Microsoft 365**.
- Si se concedió acceso a un grupo, compruebe que el usuario sea miembro de ese grupo. Si no, puede [agregarlo al grupo mediante Azure AD](../../active-directory/fundamentals/active-directory-groups-members-azure-portal.md), sin tener que realizar otra implementación. Tenga en cuenta que los [propietarios de grupos](../../active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners.md) no son necesariamente miembros de los grupos que administran y es posible que deban agregarse para tener acceso.
- Puede experimentarse un retraso adicional antes de que se habilite el acceso para [grupos anidados](../..//active-directory/fundamentals/active-directory-groups-membership-azure-portal.md).
- Los [roles integrados de Azure](../../role-based-access-control/built-in-roles.md) que se incluyen en las autorizaciones no deben incluir ningún rol en desuso. Si un rol integrado de Azure cae en desuso, los usuarios que se hayan incorporado con ese rol perderán el acceso, y usted no podrá incorporar delegaciones adicionales. Para solucionarlo, actualice la plantilla para usar solo roles integrados admitidos y, a continuación, realice una nueva implementación.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre las [experiencias de administración entre inquilinos](../concepts/cross-tenant-management-experience.md).
- Puede [ver y administrar clientes](view-manage-customers.md) desde **Mis clientes**, en Azure Portal.
- Aprenda a [actualizar](update-delegation.md) o [eliminar](remove-delegation.md) una delegación.
