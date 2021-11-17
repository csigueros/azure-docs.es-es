---
title: Solución de errores comunes de implementación de Azure
description: Describe los errores comunes de los recursos de Azure implementados con plantillas de Azure Resource Manager plantillas (plantillas de ARM) o archivos de Bicep.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 988d2e85475ed1c94580cc550630df82fceddb7a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470250"
---
# <a name="troubleshoot-common-azure-deployment-errors"></a>Solución de errores comunes de implementación de Azure

En este artículo, se describen algunos errores comunes de implementación de Azure y se proporciona información sobre cómo resolverlos. Los recursos de Azure se pueden implementar Azure Resource Manager plantillas (plantillas de ARM) o archivos Bicep. Si no encuentra el código del error de implementación, consulte [Búsqueda de códigos de error](find-error-code.md).

Si no aparece el código de error, envíe una incidencia de GitHub. En el lado derecho de la página, seleccione **Comentarios**. En la parte inferior de la página, en **Comentarios**, seleccione **Esta página**.

## <a name="error-codes"></a>Códigos de error

| Código de error | Mitigación | Más información |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Siga las restricciones de nomenclatura para las cuentas de almacenamiento. | [Resolución del nombre de la cuenta de almacenamiento](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Consulte las propiedades disponibles para la cuenta de almacenamiento. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | El clúster o la región no tienen recursos disponibles o no admiten el tamaño de máquina virtual solicitado. Vuelva a realizar la solicitud más adelante o solicite otro tamaño de máquina virtual. | [Problemas de aprovisionamiento y asignación de Linux](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux) <br><br> [Problemas de aprovisionamiento y asignación de Windows](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) <br><br> [Solución de problemas de asignación](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | Espere a que la operación simultánea finalice. | |
| AuthorizationFailed | La cuenta o entidad de servicio no dispone de acceso suficiente para completar la implementación. Compruebe el rol al que la cuenta pertenece y su acceso para el ámbito de implementación.<br><br>Puede recibir este error cuando un proveedor de recursos necesario no está registrado. | [Control de acceso basado en roles de Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)<br><br>[Resolución de registros](error-register-resource-provider.md) |
| BadRequest | Envió valores de implementación que no coinciden con los que Resource Manager esperaba. Compruebe el mensaje de estado interno para obtener ayuda para solucionar el problema. | [Referencia de plantilla](/azure/templates/) <br><br> [Ubicaciones admitidas](../templates/resource-location.md) |
| Conflicto | Se solicita una operación no permitida con el estado actual del recurso. Por ejemplo, solo se permite el cambio de tamaño del disco al crear una VM o al desasignar la VM. | |
| DeploymentActiveAndUneditable | Espere a que la implementación simultánea de este grupo de recursos finalice. | |
| DeploymentFailedCleanUp | Al implementar en modo completo, se eliminará cualquier recurso que no esté en la plantilla. Obtendrá este error si no tiene los permisos adecuados para eliminar todos los recursos que no están en la plantilla. Para evitar el error, cambie el modo de implementación a incremental. | [Modos de implementación de Azure Resource Manager](../templates/deployment-modes.md) |
| DeploymentNameInvalidCharacters | El nombre de implementación solo puede contener letras, dígitos, guiones `(-)`, puntos `(.)` o caracteres de subrayado `(_)`. | |
| DeploymentNameLengthLimitExceeded | Los nombres de implementación se limitan a 64 caracteres.  | |
| DeploymentFailed | El error DeploymentFailed es un error general que no proporciona la información necesaria para resolverlo. Mire en los detalles del error si hay un código de error que proporcione más información. | [Búsqueda de códigos de error](find-error-code.md) |
| DeploymentQuotaExceeded | Si se alcanza el límite de 800 implementaciones por grupo de recursos, elimine las implementaciones que ya no necesite del historial. | [Resolución de error cuando el recuento de implementaciones es superior a 800](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | Simplifique la plantilla para reducir el tamaño. | [Resolución de errores de tamaño de plantilla](error-job-size-exceeded.md) |
| DnsRecordInUse | El nombre del registro de DNS debe ser único. Escribe otro nombre. | |
| ImageNotFound | Compruebe la configuración de la imagen de máquina virtual. |  |
| InternalServerError | Causado por un problema temporal. Vuelva a intentar la implementación. | |
| InUseSubnetCannotBeDeleted | Este error puede producirse al intentar actualizar un recurso, si el proceso de solicitud elimina y crea el recurso. Asegúrese de especificar todos los valores sin cambios. | [Actualización de recursos](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Obtenga el token de acceso para el inquilino adecuado. Solo puede obtener el token del inquilino al que pertenece su cuenta. | |
| InvalidContentLink | Probablemente ha tratado de agregar un vínculo a una plantilla anidada que no está disponible. Compruebe el URI proporcionado para la plantilla anidada. Si la plantilla se encuentra en una cuenta de almacenamiento, asegúrese de que puede accederse al URI. Debe pasar un token de SAS. Actualmente, no se puede agregar un vínculo a una plantilla que se encuentre en una cuenta de almacenamiento detrás de un [firewall de Azure Storage](../../storage/common/storage-network-security.md). De todos modos, tiene la posibilidad de mover la plantilla a otro repositorio, como GitHub. | [Plantillas vinculadas](../templates/linked-templates.md) |
| InvalidDeploymentLocation | Al realizar la implementación en el nivel de suscripción, ha proporcionado una ubicación diferente para un nombre de implementación usado previamente. | [Implementaciones de nivel de suscripción](../templates/deploy-to-subscription.md) |
| InvalidParameter | Uno de los valores proporcionados para un recurso no coincide con el valor esperado. Este error puede deberse a muchas condiciones diferentes. Por ejemplo, una contraseña puede ser insuficiente o un nombre de blob puede ser incorrecto. El mensaje de error debe indicar qué valor debe corregirse. | |
| InvalidRequestContent | Los valores de implementación incluyen valores que no se reconocen o valores requeridos que faltan. Confirme los valores para el tipo de recurso. | [Referencia de plantilla](/azure/templates/) |
| InvalidRequestFormat | Habilite el registro de depuración cuando se ejecute la implementación y compruebe el contenido de la solicitud. | [Registro de depuración](enable-debug-logging.md) |
| InvalidResourceLocation | Proporcione un nombre único para la cuenta de almacenamiento. | [Resolución del nombre de la cuenta de almacenamiento](error-storage-account-name.md) |
| InvalidResourceNamespace | Compruebe el espacio de nombres del recurso especificado en la propiedad **type**. | [Referencia de plantilla](/azure/templates/) |
| InvalidResourceReference | El recurso aún no existe o se hace referencia a él de forma incorrecta. Compruebe si tiene que agregar una dependencia. Compruebe que el uso de la función **reference** incluye los parámetros necesarios para su escenario. | [Resolución de dependencias](error-not-found.md) |
| InvalidResourceType | Compruebe el tipo de recurso especificado en la propiedad **type**. | [Referencia de plantilla](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registre la suscripción con el proveedor de recursos. | [Resolución de registros](error-register-resource-provider.md) |
| InvalidTemplateDeployment <br> InvalidTemplate | Compruebe la sintaxis de la plantilla en busca de errores. | [Resolución de plantilla no válida](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Quite las dependencias innecesarias. | [Resolver dependencias circulares](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | Simplifique la plantilla para reducir el tamaño. | [Resolución de errores de tamaño de plantilla](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | Compruebe si la cuenta pertenece al mismo inquilino que el grupo de recursos en que está realizando la implementación. | |
| LinkedInvalidPropertyId | No se ha resuelto el identificador de un recurso. Compruebe que ha proporcionado todos los valores necesarios para el identificador de recurso. Por ejemplo, el identificador de suscripción, el nombre del grupo de recursos, el tipo de recurso, el nombre del recurso primario (si es necesario) y el nombre del recurso. | |
| LocationRequired | Proporcione una ubicación para el recurso. | [Establecimiento de la ubicación](../templates/resource-location.md) |
| MismatchingResourceSegments | Asegúrese de que el recurso anidado tiene el número correcto de segmentos de nombre y tipo. | [Resolver los segmentos de recursos](error-invalid-template.md#incorrect-segment-lengths) |
| MissingRegistrationForLocation | Compruebe el estado de registro del proveedor de recursos y las ubicaciones admitidas. | [Resolución de registros](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Registre la suscripción con el proveedor de recursos. | [Resolución de registros](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Compruebe el estado de registro del proveedor de recursos. | [Resolución de registros](error-register-resource-provider.md) |
| NotFound | Puede que esté intentando implementar un recurso dependiente en paralelo con un recurso principal. Compruebe si tiene que agregar una dependencia. | [Resolución de dependencias](error-not-found.md) |
| OperationNotAllowed | La implementación trata de realizar una operación que excede la cuota de la suscripción, del grupo de recursos o de la región. Si es posible, revise la implementación para respetar las cuotas. De lo contrario, considere la posibilidad de solicitar un cambio de las cuotas. | [Resolución de cuotas](error-resource-quota.md) |
| ParentResourceNotFound | Asegúrese de que existe un recurso principal antes de crear los recursos secundarios. | [Resolución del recurso principal](error-parent-resource.md) |
| PasswordTooLong | Puede que haya seleccionado una contraseña con demasiados caracteres, o que haya convertido el valor de contraseña en una cadena segura antes de pasarlo como parámetro. Si la plantilla incluye un parámetro de **cadena segura**, no es necesario convertir el valor en una cadena segura. Proporcione el valor de contraseña como texto. |  |
| PrivateIPAddressInReservedRange | La dirección IP especificada incluye un intervalo de direcciones requerido por Azure. Cambie la dirección IP para evitar el intervalo reservado. | [Direcciones IP](../../virtual-network/ip-services/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | La dirección IP especificada está fuera del intervalo de subred. Cambie la dirección IP para que esté dentro del intervalo de subred. | [Direcciones IP](../../virtual-network/ip-services/public-ip-addresses.md) |
| PropertyChangeNotAllowed | Algunas propiedades no se pueden cambiar en un recurso implementado. Al actualizar un recurso, limite los cambios a las propiedades permitidas. | [Actualización de recursos](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | La suscripción incluye una directiva de recursos que impide una acción que intenta realizar durante la implementación. Busque la directiva que bloquea la acción. Si es posible, cambie la implementación para cumplir con las limitaciones de la directiva. | [Resolución de directivas](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Proporcione un nombre de recurso que no incluya un nombre reservado. | [Nombres de recurso reservados](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Espere a que la eliminación finalice. | |
| ResourceGroupNotFound | Compruebe el nombre del grupo de recursos de destino para la implementación. El grupo de recursos de destino ya debe existir en la suscripción. Compruebe el contexto de la suscripción. | [CLI de Azure ](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | La implementación hace referencia a un recurso que no se puede resolver. Compruebe que el uso de la función **reference** incluye los parámetros necesarios para su escenario. | [Resolución de referencias](error-not-found.md) |
| ResourceQuotaExceeded | La implementación trata de crear recursos que exceden la cuota de la suscripción, del grupo de recursos o de la región. Si es posible, revise la infraestructura para respetar las cuotas. De lo contrario, considere la posibilidad de solicitar un cambio de las cuotas. | [Resolución de cuotas](error-resource-quota.md) |
| SkuNotAvailable | Seleccione la SKU (como el tamaño de la máquina virtual) que está disponible para la ubicación seleccionada. | [Resolución de SKU](error-sku-not-available.md) |
| StorageAccountAlreadyExists <br> StorageAccountAlreadyTaken | Proporcione un nombre único para la cuenta de almacenamiento. | [Resolución del nombre de la cuenta de almacenamiento](error-storage-account-name.md)  |
| StorageAccountNotFound | Compruebe la suscripción, el grupo de recursos y el nombre de la cuenta de almacenamiento que intenta utilizar. | |
| SubnetsNotInSameVnet | Una máquina virtual solo puede tener una red virtual. Al implementar varias NIC, asegúrese de que pertenecen a la misma red virtual. | [Varias NIC](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | No se puede acceder a una suscripción especificada para la implementación. Podría deberse a que el identificador de la suscripción sea incorrecto, a que el usuario que implemente la plantilla carezca de los permisos adecuados para implementar en la suscripción o a que el identificador de la suscripción tenga un formato incorrecto. Al usar implementaciones anidadas para [implementar entre ámbitos](../templates/deploy-to-resource-group.md), proporcione el GUID de la suscripción. | |
| SubscriptionNotRegistered | Al implementar un recurso, se debe registrar el proveedor de recursos para la suscripción. Cuando se usa una plantilla de Azure Resource Manager para la implementación, el proveedor de recursos se registra automáticamente en la suscripción. A veces, el registro automático no se completa a tiempo. Para evitar este error intermitente, registre el proveedor de recursos antes de la implementación. | [Resolución de registros](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Quite las dependencias innecesarias. | [Resolver dependencias circulares](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Reduzca el número de grupos de recursos de una sola implementación. | [Implementaciones entre ámbitos](../templates/deploy-to-resource-group.md) |

## <a name="next-steps"></a>Pasos siguientes

- [Búsqueda de códigos de error](find-error-code.md)
- [Habilitación del registro de depuración](enable-debug-logging.md)
- [Creación de una plantilla de solución de problemas](create-troubleshooting-template.md)
