---
title: Uso de Azure Portal para solucionar problemas de errores de activación relacionados con Azure Stack Edge Pro con GPU | Microsoft Docs
description: Describe cómo solucionar los problemas relacionados con el almacén de claves y la activación de Azure Stack Edge Pro con GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 1f6729fc0a723a21f66380a397a222bef23cba9e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750313"
---
# <a name="troubleshoot-activation-or-secret-deletion-issues-on-azure-key-vault-for-your-azure-stack-edge-pro-gpu-device"></a>Solución de problemas de activación o eliminación de secretos en Azure Key Vault para el dispositivo Azure Stack Edge Pro con GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo solucionar los problemas de activación de un dispositivo Azure Stack Edge Pro con GPU. 


## <a name="activation-errors"></a>Errores de activación

En la tabla siguiente se resumen los errores relacionados con la activación del dispositivo y la solución recomendada correspondiente.

| Mensaje de error| Resolución recomendada |
|------------------------------------------------------|--------------------------------------|
| Si la instancia de Azure Key Vault que se usa para la activación se elimina antes de que el dispositivo se active con la clave de activación, recibirá este error. <br> ![Error del almacén de claves 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | Si se ha eliminado el almacén de claves, puede restaurarlo si está en duración de la protección de purga. Siga los pasos descritos en [Recuperación de un almacén de claves](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault). <br>Si ha transcurrido la duración de la protección de purga, deberá crear un nuevo almacén de claves a través de la hoja [Recuperación de un almacén de claves](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault). |
| Si la instancia de Azure Key Vault se elimina después de activar el dispositivo y, a continuación, intenta realizar cualquier operación que implique cifrado (por ejemplo, **Agregar usuario**, **Agregar recurso compartido** o **Configurar proceso**, recibirá este error. <br> ![Error del almacén de claves 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | Si se ha eliminado el almacén de claves, puede restaurarlo si está en duración de la protección de purga. Siga los pasos descritos en [Recuperación de un almacén de claves](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault). <br>Si ha transcurrido la duración de la protección de purga, deberá crear un nuevo almacén de claves tal y como se describe en [Recuperación de un almacén de claves](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault). |
| Si se produce un problema en la generación de la clave de activación debido a un error, recibirá este error. En la notificación se incluyen más detalles. <br> ![Error del almacén de claves 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | Asegúrese de que los puertos y las direcciones URL especificados en [Acceso a Azure Key Vault desde detrás de un firewall](../key-vault/general/access-behind-firewall.md) estén abiertos en el firewall para poder acceder al almacén de claves. Espere unos minutos y vuelva a intentar la operación. Si el persiste el problema, póngase en contacto con el Soporte técnico de Microsoft. |
| Si el usuario tiene permisos de solo lectura, no puede generar una clave de activación y se presenta este error. <br> ![Error del almacén de claves 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | Esto puede deberse a que no tiene el acceso correcto o a que `Microsoft.KeyVault` no está registrado.<li>Asegúrese de que tiene acceso de propietario o colaborador en el nivel de grupo de recursos que se usa para el recurso de Azure Stack Edge.</li><li>Asegúrese de que el proveedor de recursos `Microsoft.KeyVault` está registrado. Para registrar un proveedor de recursos, vaya a la suscripción usada para el recurso de Azure Stack Edge. Vaya a **Proveedores de recursos**, busque *Microsoft.KeyVault* y seleccione **Registrar**. Para obtener más información, consulte [Registro del proveedor de recursos](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).</li> |


## <a name="unregistered-resource-provider-errors"></a>Errores de proveedor de recursos sin registrar

| Mensaje de error| Resolución recomendada |
|------------------------------------------------------|--------------------------------------|
| Si el proveedor de recursos de Key Vault no está registrado, verá un error al crear un almacén de claves durante la generación de la clave de activación. <br> <!--![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | No se generará la clave de activación. <br>Asegúrese de que el proveedor de recursos `Microsoft.KeyVault` está registrado. Para registrar un proveedor de recursos, vaya a la suscripción usada para el recurso de Azure Stack Edge. Vaya a **Proveedores de recursos**, busque *Microsoft.KeyVault* y seleccione **Registrar**. <br>Para obtener más información, consulte [Registro del proveedor de recursos](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).</li> |
| Si el proveedor de recursos de Storage no está registrado, verá un error al crear una cuenta de almacenamiento para los registros de auditoría. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Este error no es un error de bloqueo y se generará la clave de activación. <br>El proveedor de recursos de Storage se registra automáticamente, pero si no es así, siga los pasos descritos en [Registro de un proveedor de recursos](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers) para registrar `Microsoft.Storage` en su suscripción.  |

## <a name="key-vault-or-secret-deletion-errors"></a>Errores de eliminación de secretos o almacén de claves

| Mensaje de error| Resolución recomendada |
|------------------------------------------------------|--------------------------------------|
| Si se ha eliminado la clave de integridad del canal en la instancia de Azure Key Vault e intenta realizar cualquier operación que implique cifrado (por ejemplo, **Agregar usuario**, **Agregar recurso compartido** o **Configurar proceso**), recibirá este error.  |Si se ha eliminado la clave de integridad de canal en el almacén de claves, pero esta sigue estando dentro de la duración de la purga, siga los pasos descritos en [Deshacer eliminación de la clave de Key Vault](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval). <br>Si ha transcurrido la duración de la protección de purga y se ha realizado una copia de seguridad de la clave, puede restaurar desde la copia de seguridad. De lo contrario, no podrá recuperar la clave. Póngase en contacto con el servicio de soporte técnico de Microsoft para conocer los pasos siguientes. |
<!--|Quítelo después de comprobar con Noopur : el cliente no necesita hacer nada. Si la clave de integridad del canal en Azure Key Vault se eliminó antes de la activación del dispositivo y la clave de activación se vuelve a generar, recibirá este error. <br> ![Error del almacén de claves 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png) | Se volverá a crear la clave de integridad del canal y se actualizarán los metadatos.
|-->

## <a name="audit-logging-errors"></a>Errores de registro de auditoría

| Mensaje de error| Resolución recomendada |
|------------------------------------------------------|--------------------------------------|
| Si se produce un error en la creación de la configuración de diagnóstico para el almacén de claves, verá este error. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | No se trata de un error de bloqueo y se generará la clave de activación. <br> Puede [crear una configuración de diagnóstico para almacenar los registros de auditoría](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs) manualmente. |
| Si se produce un error en la creación de la cuenta de almacenamiento, por ejemplo, porque ya existe una cuenta para el nombre especificado, verá este error. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Puede crear manualmente una cuenta de almacenamiento y vincularla a la configuración de diagnóstico en el almacén de claves. A continuación, esta cuenta se usa para almacenar los registros de auditoría. <br> Para obtener más información, consulte [Creación de una cuenta de almacenamiento para sus registros](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs).  |
|Si se elimina la identidad administrada asignada por el sistema para el recurso de Azure Stack Edge, verá este error. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Verá una alerta en la hoja Seguridad del recurso Azure Stack Edge. Seleccione esta alerta para [crear una nueva identidad administrada a través de la hoja Recuperación del almacén de claves](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault).  |
| Si la identidad administrada no tiene acceso al almacén de claves, verá este error. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Verá una alerta en la hoja Seguridad del recurso Azure Stack Edge. Seleccione esta alerta para [conceder acceso de identidad administrada al almacén de claves a través de la hoja Recuperación del almacén de claves](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault).  |

## <a name="resource-move-errors"></a>Errores de movimiento de recursos

| Mensaje de error| Resolución recomendada |
|------------------------------------------------------|--------------------------------------|
| Si el recurso del almacén de claves se mueve entre grupos de recursos o suscripciones, verá este error. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | El traslado de recursos del almacén de claves se trata de la misma manera que la eliminación del almacén de claves. Puede restaurarlo si está en duración de la protección de purga. Si ha transcurrido la duración de la protección de purga, deberá crear un nuevo almacén de claves. Para obtener más información sobre cualquiera de los casos anteriores, consulte [Recuperación de un almacén de claves](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault).    |
| Si la suscripción que usa se mueve entre inquilinos, verá este error. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Vuelva a configurar la identidad administrada y cree un nuevo almacén de claves. También puede mover el recurso del almacén de claves, en cuyo caso solo será necesario volver a configurar la identidad administrada. En cada uno de los casos anteriores, consulte [Recuperación de un almacén de claves](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault).   |
| Si el recurso de la cuenta de almacenamiento que se usa para los registros de auditoría se mueve entre grupos de recursos o suscripciones, no verá un error.  | Puede [crear una nueva cuenta de almacenamiento y configurarla para almacenar los registros de auditoría](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs). |

## <a name="other-errors"></a>Otros errores

| Mensaje de error| Resolución recomendada |
|------------------------------------------------------|--------------------------------------|
| Si las restricciones de red están configuradas para el almacén de claves, verá este error. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | El servicio no puede diferenciar entre la eliminación del almacén de claves o el escenario de almacén de claves no accesible debido a las restricciones de red. En cada caso, se le dirigirá a la hoja [Recuperación de un almacén de claves](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault).     |


## <a name="next-steps"></a>Pasos siguientes

- [Instalación de Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-deploy-install.md).
