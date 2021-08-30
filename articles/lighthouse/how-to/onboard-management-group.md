---
title: Incorporación de todas las suscripciones de un grupo de administración
description: Puede implementar una directiva de Azure Policy para delegar todas las suscripciones de un grupo de administración en un suscriptor administrador de Azure Lighthouse.
ms.date: 08/13/2021
ms.topic: how-to
ms.openlocfilehash: 07f2bb850120b1f27f090aec9d0d3f97d9643d05
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206798"
---
# <a name="onboard-all-subscriptions-in-a-management-group"></a>Incorporación de todas las suscripciones de un grupo de administración

[Azure Lighthouse](../overview.md) permite delegar suscripciones o grupos de recursos, pero no [grupos de administración](../../governance/management-groups/overview.md). Sin embargo, puede implementar una [directiva de Azure Policy](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) para delegar todas las suscripciones de un grupo de administración en un suscriptor de administración de Azure Lighthouse.

La directiva usa el efecto [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) para comprobar si cada suscripción del grupo de administración se ha delegado en el inquilino de administración especificado. Si alguna suscripción aún no está delegada, la directiva crea la asignación de Azure Lighthouse en función de los valores que proporcione en los parámetros. A continuación, tendrá acceso a todas las suscripciones del grupo de administración, exactamente igual que si cada una de ellas se hubiera incorporado manualmente.

Al usar esta directiva, tenga presente lo siguiente:

- Cada suscripción del grupo de administración tendrá el mismo conjunto de autorizaciones. Para variar los usuarios y roles a los que se concede acceso, tendrá que incorporar una suscripción manualmente.
- Aunque se incorporarán todas las suscripciones del grupo de administración, no puede realizar acciones con el recurso del grupo de administración a través de Azure Lighthouse. Tendrá que seleccionar las suscripciones en las que trabajar, tal y como lo haría si se incorporaran por separado.

A menos que se especifique a continuación, todos estos pasos debe realizarlos un usuario en el inquilino del cliente con los permisos correspondientes.

> [!TIP]
> Aunque en este tema hacemos referencia a los proveedores de servicios y clientes, las [empresas que administran varios inquilinos](../concepts/enterprise.md) pueden usar los mismos procesos.

## <a name="register-the-resource-provider-across-subscriptions"></a>Registro del proveedor de recursos en varias suscripciones

Normalmente, el proveedor de recursos **Microsoft.ManagedServices** se registra para una suscripción como parte del proceso de incorporación. Si se usa la directiva para incorporar las suscripciones de un grupo de administración, el proveedor de recursos debe registrarse de antemano. Esta operación la puede realizar un usuario con los roles "Colaborador" o "Propietario" en el inquilino del cliente (o cualquier otro que tenga permisos para realizar la operación  `/register/action`  para el proveedor de recursos). Para más información, vea [Tipos y proveedores de recursos de Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

Puede usar una [aplicación lógica de Azure para registrar automáticamente el proveedor de recursos en varias suscripciones](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/register-managed-services-rp-customer). Esta aplicación lógica se puede implementar en el inquilino de un cliente con permisos limitados que le permitan registrar el proveedor de recursos en cada suscripción de un grupo de administración.

También proporcionamos una [aplicación lógica de Azure que se puede implementar en el inquilino del proveedor de servicios](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/register-managed-services-rp-partner). Esta aplicación lógica puede asignar el proveedor de recursos en las distintas suscripciones en varios inquilinos si [se concede un consentimiento de administrador para todo el inquilino](../../active-directory/manage-apps/grant-admin-consent.md) a la aplicación lógica. La concesión del consentimiento del administrador para todo el inquilino requiere que inicie sesión como un usuario que esté autorizado para dar su consentimiento en nombre de la organización. Tenga presente que, aunque use esta opción para registrar el proveedor en varios inquilinos, la directiva debe implementarse por separado para cada grupo de administración.

## <a name="create-your-parameters-file"></a>Creación de un archivo de parámetros

Para asignar la directica, implemente el archivo [deployLighthouseIfNotExistManagementGroup.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-delegate-management-groups/deployLighthouseIfNotExistManagementGroup.json) desde nuestro repositorio de ejemplos, junto con un archivo de parámetros [deployLighthouseIfNotExistsManagementGroup.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-delegate-management-groups/deployLighthouseIfNotExistsManagementGroup.parameters.json) que edite con sus detalles específicos de inquilino y asignación. Estos dos archivos contienen los mismos detalles que se usarían para [incorporar una suscripción individual](onboard-customer.md).

En el siguiente ejemplo se muestra un archivo de parámetros que delegará las suscripciones al inquilino de Servicios administrados de Relecloud, con acceso concedido a dos identificadores principales: uno para asistencia de nivel 1 y otro para una cuenta de automatización, que puede [asignar la propiedad delegateRoleDefinitionIds a identidades administradas en el inquilino del cliente](deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": { 
        "managedByName": { 
            "value": "Relecloud Managed Services" 
        }, 
        "managedByDescription": { 
            "value": "Relecloud provides managed services to its customers" 
        }, 
        "managedByTenantId": { 
            "value": "00000000-0000-0000-0000-000000000000" 
        }, 
        "managedByAuthorizations": { 
            "value": [ 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "principalIdDisplayName": "Tier 1 Support", 
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c" 
                }, 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "principalIdDisplayName": "Automation Account - Full access", 
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9", 
                    "delegatedRoleDefinitionIds": [ 
                        "b24988ac-6180-42a0-ab88-20f7382dd24c", 
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293", 
                        "91c1777a-f3dc-4fae-b103-61d183457e46" 
                    ] 
                }                 
            ] 
        } 
    } 
} 
```

## <a name="assign-the-policy-to-a-management-group"></a>Asignación de la directiva a un grupo de administración  

Una vez que haya editado la directiva para crear las asignaciones, puede asignarla en el nivel de grupo de administración. Para obtener más información sobre cómo asignar una directiva y ver los resultados del estado de cumplimiento, consulte  [Inicio rápido: Creación de una asignación de directiva](../../governance/policy/assign-policy-portal.md).

En el siguiente script de PowerShell se muestra cómo agregar la definición de directiva en el grupo de administración especificado, usando el archivo de plantilla y parámetros que ha creado. Debe crear la tarea de asignación y corrección para las suscripciones existentes.

```azurepowershell-interactive
New-AzManagementGroupDeployment -Name <DeploymentName> -Location <location> -ManagementGroupId <ManagementGroupName> -TemplateFile <path to file> -TemplateParameterFile <path to parameter file> -verbose
```

## <a name="confirm-successful-onboarding"></a>Confirmar la incorporación correcta

Puede confirmar que las suscripciones se han incorporado correctamente de varias maneras. Para obtener más información, consulte [Confirmación de la incorporación correcta](onboard-customer.md#confirm-successful-onboarding).

Si mantiene la aplicación lógica y la directiva activas para el grupo de administración, también se incorporarán las nuevas suscripciones que se agreguen al grupo.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre cómo [incorporar clientes a Azure Lighthouse](onboard-customer.md).
- Más información acerca de [Azure Policy](../../governance/policy/index.yml).
- Obtenga información sobre [Azure Logic Apps](../../logic-apps/logic-apps-overview.md).
