---
title: Creación de autorizaciones aptas
description: Al incorporar clientes a Azure Lighthouse, puede permitir que los usuarios del inquilino de administración adopten un rol superior cuando sea necesario.
ms.date: 08/26/2021
ms.topic: how-to
ms.openlocfilehash: 1754a7d43f184e340badb7adb29de8caa3901372
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038691"
---
# <a name="create-eligible-authorizations"></a>Creación de autorizaciones aptas

Al incorporar clientes a Azure Lighthouse, puede crear autorizaciones para conceder roles integrados de Azure concretos a los usuarios del inquilino de administración. También puede crear autorizaciones aptas que usen [Azure Active Directory (Azure AD) Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) para permitir que los usuarios del inquilino de administración tengan un rol superior temporalmente. Esto le permite conceder permisos adicionales cuando sea necesario para que los usuarios solo tengan esos permisos durante un tiempo fijo.

La creación de autorizaciones aptas permite minimizar el número de asignaciones permanentes de usuarios a roles con privilegios, lo que ayuda a reducir los riesgos de seguridad relacionados con el acceso con privilegios por parte de los usuarios del inquilino.

En este tema se explica cómo funcionan las autorizaciones aptas y cómo crearlas al [incorporar un cliente a Azure Lighthouse](onboard-customer.md).

> [!IMPORTANT]
> Las autorizaciones aptas se encuentran actualmente en una versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="license-requirements"></a>Requisitos de licencia

La creación de autorizaciones aptas requiere una licencia Enterprise Mobility + Security E5 (EMS E5) o Azure AD Premium P2. Para obtener la licencia correcta para sus requisitos, consulte [Comparación de las características con disponibilidad general de las ediciones Gratis, Básico y Premium](https://azure.microsoft.com/pricing/details/active-directory/).

La licencia EMS E5 o Azure AD Premium P2 debe estar en manos del inquilino de administración, no del inquilino del cliente.

Los costos adicionales asociados con un rol apto solo se aplicarán durante el período de tiempo en el que el usuario tenga acceso al rol superior.

Para más información sobre las licencias para usuarios, consulte [License requirements to use Privileged Identity Management](../../active-directory/privileged-identity-management/subscription-requirements.md) (Requisitos de licencia para usar Privileged Identity Management).

## <a name="how-eligible-authorizations-work"></a>Funcionamiento de las autorizaciones aptas

Una autorización apta define una asignación de roles que requiere que el usuario active el rol cuando necesite realizar tareas con privilegios. Cuando active el rol apto, tendrá el acceso completo que concede ese rol durante el período de tiempo especificado.

Los usuarios del inquilino del cliente pueden revisar todas las asignaciones de roles, incluidas las de las autorizaciones aptas, antes del proceso de incorporación.

Una vez que un usuario activa correctamente un rol apto, tendrá ese rol superior en el ámbito delegado durante un período de tiempo preconfigurado, además de sus asignaciones de rol permanentes para ese ámbito.

Los administradores del inquilino de administración pueden revisar todas las actividades de Privileged Identity Management mediante la visualización del registro de auditoría en el inquilino de administración. Los clientes pueden ver estas acciones en el registro de actividad de Azure de la suscripción delegada.

## <a name="eligible-authorization-elements"></a>Elementos de autorización aptos

Puede crear una autorización apta al incorporar clientes con plantillas de Azure Resource Manager o publicar una oferta de servicios administrados para Azure Marketplace. Cada autorización apta debe incluir tres elementos: el usuario, el rol y la directiva de acceso.

### <a name="user"></a>Usuario

Para cada autorización apta, se proporciona el id. de entidad de seguridad para un usuario individual o un grupo de Azure AD en el inquilino de administración. Junto con el id. de entidad de seguridad, debe proporcionar un nombre para mostrar de su elección para cada autorización.

Si se proporciona un grupo en una autorización apta, cualquier miembro de ese grupo podrá elevar su propio acceso individual al rol según la directiva de acceso.

No puede usar autorizaciones aptas con entidades de servicio, ya que actualmente no hay ninguna manera de que una cuenta de entidad de servicio eleve su acceso y use un rol apto. Tampoco puede usar autorizaciones aptas con `delegatedRoleDefinitionIds` que un administrador de acceso de usuarios puede [asignar a identidades administradas](deploy-policy-remediation.md).

> [!NOTE]
> Para cada autorización apta, asegúrese de crear también una autorización permanente (activa) para el mismo id. de entidad de seguridad con un rol diferente, como Lector (u otro rol integrado de Azure que incluya acceso de lectura). Si no incluye una autorización permanente con acceso de lectura, el usuario no podrá elevar su rol en Azure Portal.

### <a name="role"></a>Role

Cada autorización apta debe incluir un [rol integrado de Azure](../../role-based-access-control/built-in-roles.md) que el usuario podrá usar cuando sea necesario.

El rol puede ser cualquier rol integrado de Azure que sea compatible con la administración de recursos delegados de Azure, excepto el de administrador de acceso de los usuarios.

> [!IMPORTANT]
> Si incluye varias autorizaciones aptas que usan el mismo rol, cada una de las autorizaciones aptas debe tener la misma configuración de directiva de acceso.

### <a name="access-policy"></a>Directiva de acceso

La directiva de acceso define los requisitos de autenticación multifactor, el período de tiempo que un usuario tendrá el rol activo antes de que expire y si se requieren aprobadores.

#### <a name="multifactor-authentication"></a>Autenticación multifactor

Especifique si [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) es necesario o no para que se active un rol apto.

#### <a name="maximum-duration"></a>Duración máxima

Defina el período de tiempo total durante el que el usuario tendrá el rol apto. El valor mínimo es 30 minutos y el máximo es 8 horas.

#### <a name="approvers"></a>Aprobadores

El elemento de aprobadores es opcional. Si lo incluye, puede especificar hasta diez usuarios o grupos de usuarios en el inquilino de administración que pueden aprobar o denegar solicitudes de un usuario para activar el rol apto.

No puede usar una cuenta de entidad de servicio como aprobador. Además, los aprobadores no pueden aprobar su propio acceso. Si un aprobador también se incluye como usuario en una autorización apta, otro aprobador tendrá que concederle acceso para que eleve su rol.

Si no incluye ningún aprobador, el usuario podrá activar el rol apto cuando quiera.

## <a name="create-eligible-authorizations-using-managed-services-offers"></a>Creación de autorizaciones aptas mediante ofertas de Servicios administrados

Para incorporar el cliente a Azure Lighthouse, puede publicar las ofertas de servicios administrados para Azure Marketplace. Al [crear las ofertas en Centro de partners](publish-managed-services-offers.md), ahora puede especificar si el **Tipo de acceso** para cada [Autorización](../../marketplace/create-managed-service-offer-plans.md#authorizations) debe ser **Activo** o **Apto**.

Al seleccionar **Apto**, el usuario de la autorización podrá activar el rol según la directiva de acceso que configure. Debe establecer una duración máxima entre 30 minutos y 8 horas y especificar si va a requerir la autenticación multifactor de Azure. También puede agregar hasta 10 aprobadores si decide usarlos, proporcionando un nombre para mostrar y un id. de entidad de seguridad para cada uno.

Asegúrese de revisar los detalles en la sección [Elementos de autorización aptos](#eligible-authorization-elements) al configurar las autorizaciones aptas en Centro de partners.

## <a name="create-eligible-authorizations-using-azure-resource-manager-templates"></a>Creación de autorizaciones aptas con plantillas de Azure Resource Manager

Para incorporar el cliente a Azure Lighthouse, use una [plantilla de Azure Resource Manager junto con un archivo de parámetros correspondiente](onboard-customer.md#create-an-azure-resource-manager-template) que modifique. La plantilla que elija dependerá de si se incorpora una suscripción completa, un grupo de recursos o varios grupos de recursos de una suscripción.

Para incluir autorizaciones aptas al incorporar un cliente, use una de las plantillas de la [sección delegated-resource-management-eligible-authorizations de nuestro repositorio de ejemplos](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations). Se proporcionan plantillas con y sin aprobadores incluidos, para que pueda usar la que mejor se adapte a su escenario.

|Para incorporar esto (con autorizaciones aptas)  |Use esta plantilla de Azure Resource Manager  |Y modifique este archivo de parámetros |
|---------|---------|---------|
|Suscripción   |[subscription.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.json)  |[subscription.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.parameters.json)    |
|Suscripción (con aprobadores)  |[subscription-managing-tenant-approvers.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription-managing-tenant-approvers.json)  |[subscription-managing-tenant-approvers.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription-managing-tenant-approvers.parameters.json)    |
|Resource group   |[rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.json)  |[rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.parameters.json)    |
|Grupo de recursos (con aprobadores)  |[rg-managing-tenant-approvers.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg-managing-tenant-approvers.json)  |[rg-managing-tenant-approvers.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg-managing-tenant-approvers.parameters.json)    |
|Varios grupos de recursos de una suscripción   |[multiple-rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.json)  |[multiple-rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.parameters.json)    |
|Varios grupos de recursos de una suscripción (con aprobadores)  |[multiple-rg-managing-tenant-approvers.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg-managing-tenant-approvers.json)  |[multiple-rg-managing-tenant-approvers.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg-managing-tenant-approvers.parameters.json)    |

La plantilla **subscription-managing-tenant-approvers.json**, que se puede usar para incorporar una suscripción con autorizaciones aptas (incluidos los aprobadores), se muestra a continuación.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "type": "string",
            "metadata": {
                "description": "Specify a unique name for your offer"
            }
        },
        "mspOfferDescription": {
            "type": "string",
            "metadata": {
                "description": "Name of the Managed Service Provider offering"
            }
        },
        "managedByTenantId": {
            "type": "string",
            "metadata": {
                "description": "Specify the tenant id of the Managed Service Provider"
            }
        },
        "authorizations": {
            "type": "array",
            "metadata": {
                "description": "Specify an array of objects, containing tuples of Azure Active Directory principalId, a Azure roleDefinitionId, and an optional principalIdDisplayName. The roleDefinition specified is granted to the principalId in the provider's Active Directory and the principalIdDisplayName is visible to customers."
            }
        },
        "eligibleAuthorizations": {
            "type": "array",
            "metadata": {
                "description": "Provide the authorizations that will have just-in-time role assignments on customer environments with support for approvals from the managing tenant"
            }
        }
    },
        "variables": {
            "mspRegistrationName": "[guid(parameters('mspOfferName'))]",
            "mspAssignmentName": "[guid(parameters('mspOfferName'))]"
        },
        "resources": [
            {
                "type": "Microsoft.ManagedServices/registrationDefinitions",
                "apiVersion": "2020-02-01-preview",
                "name": "[variables('mspRegistrationName')]",
                "properties": {
                    "registrationDefinitionName": "[parameters('mspOfferName')]",
                    "description": "[parameters('mspOfferDescription')]",
                    "managedByTenantId": "[parameters('managedByTenantId')]",
                    "authorizations": "[parameters('authorizations')]",
                    "eligibleAuthorizations": "[parameters('eligibleAuthorizations')]"
                }
            },
            {
                "type": "Microsoft.ManagedServices/registrationAssignments",
                "apiVersion": "2020-02-01-preview",
                "name": "[variables('mspAssignmentName')]",
                "dependsOn": [
                    "[resourceId('Microsoft.ManagedServices/registrationDefinitions/', variables('mspRegistrationName'))]"
                ],
                "properties": {
                    "registrationDefinitionId": "[resourceId('Microsoft.ManagedServices/registrationDefinitions/', variables('mspRegistrationName'))]"
                }
            }
        ],
        "outputs": {
            "mspOfferName": {
                "type": "string",
                "value": "[concat('Managed by', ' ', parameters('mspOfferName'))]"
            },
            "authorizations": {
                "type": "array",
                "value": "[parameters('authorizations')]"
            },
            "eligibleAuthorizations": {
                "type": "array",
                "value": "[parameters('eligibleAuthorizations')]"
            }
        }
    }
```

### <a name="define-eligible-authorizations-in-your-parameters-file"></a>Definición de autorizaciones aptas en el archivo de parámetros

La [plantilla de ejemplo subscription-managing-tenant-approvers.Parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription-managing-tenant-approvers.parameters.json) se puede usar para definir autorizaciones, incluidas las autorizaciones aptas, al incorporar una suscripción.

Cada una de las autorizaciones aptas debe definirse en el parámetro `eligibleAuthorizations`. En este ejemplo se incluye una autorización apta.

Esta plantilla también incluye el elemento `managedbyTenantApprovers`, que agrega un objeto `principalId` que será necesario para aprobar todos los intentos de activar los roles aptos que se definen en el elemento `eligibleAuthorizations`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Relecloud Managed Services"
        },
        "mspOfferDescription": {
            "value": "Relecloud Managed Services"
        },
        "managedByTenantId": {
            "value": "<insert the managing tenant id>"
        },
        "authorizations": {
            "value": [
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
                    "principalIdDisplayName": "PIM group"
                }
            ]
        }, 
        "eligibleAuthorizations":{
            "value": [
                {
                        "justInTimeAccessPolicy": {
                            "multiFactorAuthProvider": "Azure",
                            "maximumActivationDuration": "PT8H",
                            "managedByTenantApprovers": [ 
                                { 
                                    "principalId": "00000000-0000-0000-0000-000000000000", 
                                    "principalIdDisplayName": "PIM-Approvers" 
                                } 
                            ]
                        },
                        "principalId": "00000000-0000-0000-0000-000000000000", 
                        "principalIdDisplayName": "Tier 2 Support",
                        "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"

                }
            ]
        }
    }
}
```

Cada entrada dentro del parámetro `eligibleAuthorizations` contiene [tres elementos](#eligible-authorization-elements) que definen una autorización apta: `principalId`, `roleDefinitionId` y `justInTimeAccessPolicy`.

`principalId` especifica el id. del usuario o grupo de Azure AD al que se aplicará esta autorización apta.

`roleDefinitionId` contiene el id. de definición de rol para un [rol integrado de Azure](../../role-based-access-control/built-in-roles.md) que el usuario podrá usar cuando sea necesario. Si incluye varias autorizaciones aptas que usan el mismo `roleDefinitionId`, cada una de ellas debe tener una configuración idéntica para `justInTimeAccessPolicy`.

`justInTimeAccessPolicy` especifica tres elementos:

- `multiFactorAuthProvider` se puede establecer en **Azure**, que requerirá la autenticación mediante Azure AD Multi-Factor Autenticación, o en **Ninguno** si no se requiere la autenticación multifactor.
- `maximumActivationDuration` establece el período de tiempo total durante el que el usuario tendrá el rol apto. Este valor debe usar el formato de duración ISO 8601. El valor mínimo es PT30M (30 minutos) y el máximo, PT8H (8 horas). Para simplificar, se recomienda usar valores solo en incrementos de media hora (por ejemplo, PT6H para 6 horas o PT6H30M para 6,5 horas).
- `managedByTenantApprovers` es opcional. Si lo incluye, debe contener una o varias combinaciones de un elemento principalId y un elemento principalIdDisplayName que serán necesarios para aprobar cualquier activación del rol apto.

Para más información sobre estos elementos, consulte la sección [Elementos de autorización aptos](#eligible-authorization-elements) anterior.

## <a name="elevation-process-for-users"></a>Proceso de elevación para usuarios

Después de incorporar un cliente a Azure Lighthouse, todos los roles aptos que incluya estarán disponibles para el usuario especificado (o para los usuarios de cualquier grupo especificado).

Cada usuario puede elevar su acceso en cualquier momento. Para hacerlo, debe visitar la página **Mis clientes** de Azure Portal, seleccionar una delegación y, a continuación, seleccionar **Administrar roles elegibles**. Después, pueden seguir los [pasos para activar el rol](../../active-directory/privileged-identity-management/pim-resource-roles-activate-your-roles.md) en Azure AD Privileged Identity Management.

:::image type="content" source="../media/manage-eligible-roles.png" alt-text="Captura de pantalla que muestra el botón Administrar roles elegibles en Azure Portal.":::

Si se han especificado aprobadores, el usuario no tendrá acceso al rol hasta que un aprobador designado conceda la aprobación. Todos los aprobadores recibirán una notificación cuando se solicite la aprobación y el usuario no podrá usar el rol apto hasta que se le conceda la aprobación. También se notificará a los aprobadores cuando suceda esto. Para más información sobre el proceso de aprobación, consulte [Aprobación o denegación de solicitudes de roles de recursos de Azure en Privileged Identity Management](../../active-directory/privileged-identity-management/pim-resource-roles-approval-workflow.md).

Una vez activado el rol apto, el usuario tendrá ese rol durante todo el tiempo especificado en la autorización apta. Después de ese período de tiempo, ya no podrá usar ese rol, a menos que repita el proceso de elevación para volver a utilizarlo.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [incorporar clientes a Azure Lighthouse mediante plantillas de ARM](onboard-customer.md).
- Obtenga información sobre cómo [incorporar clientes mediante ofertas de servicios administrados](publish-managed-services-offers.md).
- Obtenga más información acerca de [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md).
- Obtenga más información acerca de [inquilinos, usuarios y roles en Azure Lighthouse](../concepts/tenants-users-roles.md).
