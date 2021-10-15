---
title: Integración de Azure Key Vault con la activación de dispositivos y recursos de Azure Stack Edge
description: Se describe cómo Azure Key Vault está asociado con la administración de secretos durante la activación de dispositivos de Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 67cddd1839e666d4908706a1bbdaccbbd3565704
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351434"
---
# <a name="manage-azure-stack-edge-secrets-using-azure-key-vault"></a>Administración de secretos de Azure Stack Edge mediante Azure Key Vault 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Key Vault se integra con los recursos de Azure Stack Edge para la administración de secretos. En este artículo se proporcionan detalles sobre cómo se crea una instancia de Azure Key Vault para recursos de Azure Stack Edge durante la activación de dispositivos y, a continuación, se usa para la administración de secretos. 


## <a name="about-key-vault-and-azure-stack-edge"></a>Acerca de Key Vault y Azure Stack Edge

El servicio en la nube Azure Key Vault se puede utilizar para almacenar de forma segura y controlar el acceso a los tokens, las contraseñas, los certificados, las claves de API y otros secretos. Key Vault también facilita la creación y control de las claves de cifrado utilizadas para cifrar los datos. 

Para el servicio Azure Stack Edge, la integración con Key Vault proporciona las siguientes ventajas:

- Almacena secretos de cliente. Uno de los secretos usados para el servicio Azure Stack Edge, es la clave de integridad del canal (CIK). Esta clave le permite cifrar los secretos y se almacena de forma segura en el almacén de claves. Los secretos de dispositivo, como la clave de recuperación de BitLocker y la contraseña de usuario del controlador de administración de placa base (BMC), también se almacenan en el almacén de claves. 

    Para más información, consulte [Almacenamiento seguro de secretos y claves](../key-vault/general/overview.md#securely-store-secrets-and-keys).
- Pasa secretos de cliente cifrados al dispositivo.
- Muestra los secretos del dispositivo para facilitar el acceso si el dispositivo está fuera de servicio.


## <a name="generate-activation-key-and-create-key-vault"></a>Generación de una clave de activación y creación de un almacén de claves

Se crea un almacén de claves para el recurso de Azure Stack Edge durante el proceso de generación de claves de activación. El almacén de claves se crea en el mismo grupo de recursos donde está el recurso de Azure Stack Edge. Se requiere permiso de colaborador en el almacén de claves. 

### <a name="prerequisites-for-key-vault"></a>Requisitos previos para un almacén de claves

Antes de la creación del almacén de claves durante la activación, deben cumplirse los siguientes requisitos previos:

- Registre el proveedor de recursos *Microsoft.KeyVault* antes de crear el recurso de Azure Stack Edge. El proveedor de recursos se registra automáticamente si tiene acceso de propietario o colaborador a la suscripción. El almacén de claves se crea en la misma suscripción y grupo de recursos que el recurso de Azure Stack Edge. 

- Cuando se crea un recurso de Azure Stack Edge, también se crea una identidad administrada asignada por el sistema que persiste durante la vigencia del recurso y se comunica con el proveedor de recursos en la nube. 

    Cuando se habilita la instancia de identidad administrada, Azure crea una identidad de confianza para el recurso de Azure Stack Edge.

### <a name="key-vault-creation"></a>Creación de Key Vault

Después de crear el recurso, debe activarlo con el dispositivo. Para ello, generará una clave de activación a desde Azure Portal. 

Al generar una clave de activación, se producen los siguientes eventos: 

![Flujo de generación de claves de activación](media/azure-stack-edge-gpu-activation-key-vault/activation-key-generation-flow-1.png)

- Solicite una clave de activación en Azure Portal. A continuación, la solicitud se envía al proveedor de recursos del almacén de claves. 
- Se crea un almacén de claves de nivel estándar con directiva de acceso que está bloqueado de forma predeterminada. 
    - Este almacén de claves usa el nombre predeterminado o un nombre personalizado de entre 3 y 24 caracteres que especificó. No se puede usar un almacén de claves que ya esté en uso. 
    - Los detalles del almacén de claves se almacenan en el servicio. Este almacén de claves se usa para la administración de secretos y persiste durante el tiempo que exista el recurso de Azure Stack Edge. 

        ![Almacén de claves creado durante la generación de claves de activación](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)
- Para evitar la eliminación accidental, se habilita un bloqueo de recursos en el almacén de claves. También se habilita una eliminación temporal en el almacén de claves que permite restaurar el almacén de claves en un plazo de 90 días si hay una eliminación accidental. Para más información, consulte el artículo [Información general sobre la eliminación temporal de Azure Key Vault](../key-vault/general/soft-delete-overview.md).
- Ahora se habilita una identidad administrada asignada por el sistema que se creó al crear el recurso de Azure Stack Edge.
- Se genera una clave de integridad de canal (CIK) y se coloca en el almacén de claves. Los detalles de CIK se muestran en el servicio.
- También se crea una cuenta de almacenamiento con redundancia de zona (ZRS) en el mismo ámbito que el recurso Azure Stack Edge y se coloca un bloqueo en la cuenta. 
    - Esta cuenta se usa para almacenar los registros de auditoría. 
    - La creación de la cuenta de almacenamiento es un proceso de larga duración y tarda unos minutos.
    - La cuenta de almacenamiento se etiqueta con el nombre del almacén de claves.
- Se agrega una configuración de diagnóstico al almacén de claves y se habilita el registro. 
- La identidad administrada se agrega a la directiva de acceso del almacén de claves para permitir el acceso al almacén de claves, ya que el dispositivo usa el almacén de claves para almacenar y recuperar secretos. 
- El almacén de claves autentica la solicitud con identidad administrada para generar la clave de activación. La clave de activación se devuelve a Azure Portal. A continuación, puede copiar esta clave y usarla en la interfaz de usuario local para activar el dispositivo.

> [!NOTE]
> - Si existe un recurso de Azure Stack Edge antes de que se integre Azure Key Vault con el recurso de Azure Stack Edge, no se verá afectado. Puede seguir usando el recurso de Azure Stack Edge existente.
> -  La creación del almacén de claves y la cuenta de almacenamiento se suma al costo total de los recursos. Para obtener más información sobre las transacciones permitidas y sus correspondientes cargos, consulte [Precios de Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) y [Precios de cuentas de almacenamiento](https://azure.microsoft.com/pricing/details/storage/blobs/).

Si tiene problemas relacionados con el almacén de claves y la activación de dispositivos, consulte [Solución de problemas de activación de dispositivos](azure-stack-edge-gpu-troubleshoot-activation.md).


## <a name="view-key-vault-properties"></a>Ver las propiedades del almacén de claves

Una vez generada la clave de activación y creado el almacén de claves, es posible que desee acceder al almacén de claves para ver los secretos, las directivas de acceso, los diagnósticos y la información. En el siguiente procedimiento se describe cada una de estas operaciones.

### <a name="view-secrets"></a>Ver secretos

Una vez generada la clave de activación y creado el almacén de claves, es posible que desee acceder al almacén de claves. 

Para acceder al almacén de claves y ver los secretos, siga estos pasos:

1. En la instancia de Azure Portal del recurso de Azure Stack Edge, vaya a **Seguridad**. 
1. En el panel derecho, en **Seguridad**, puede ver los **Secretos**. 
1. También puede ir al almacén de claves asociado con el recurso de Azure Stack Edge. Seleccione **Nombre de almacén de claves**. 

    ![Vaya al almacén de claves del dispositivo](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. Para ver los secretos almacenados en el almacén de claves, vaya a **Secretos**. Las contraseñas de usuario de clave de integridad de canal, clave de recuperación de BitLocker y controlador de administración de placa base (BMC) se almacenan en el almacén de claves. Si el dispositivo está fuera de servicio, el portal proporciona un acceso sencillo a la clave de recuperación de BitLocker y a la contraseña de usuario de BMC.
    
    ![Visualización de secretos de dispositivo en el almacén de claves](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-secrets-1.png)

### <a name="view-managed-identity-access-policies"></a>Visualización de directivas de acceso de identidad administrada

Para acceder a las directivas de acceso para el almacén de claves y la identidad administrada, siga estos pasos:

1. En la instancia de Azure Portal del recurso de Azure Stack Edge, vaya a **Seguridad**. 
1. Seleccione el vínculo correspondiente al **nombre del almacén de claves** para ir al almacén de claves asociado al recurso de Azure Stack Edge. 

    ![Vaya al almacén de claves del dispositivo](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. Para ver las directivas de acceso asociadas al almacén de claves, vaya a **Directivas de acceso**. Puede ver que se ha concedido acceso a la identidad administrada. Seleccione **Permisos de secretos**. Puede ver que el acceso a la identidad administrada está restringido solo a la **Obtención** y el **Establecimiento** del secreto. 
    
    ![Visualizar directivas de acceso para el almacén de claves](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-access-policies-1.png)

    
### <a name="view-audit-logs"></a>Visualización de registros de auditoría

Para acceder al almacén de claves y ver la configuración de diagnóstico y los registros de auditoría, siga estos pasos:

1. En la instancia de Azure Portal del recurso de Azure Stack Edge, vaya a **Seguridad**. 
1. Seleccione el vínculo correspondiente al **nombre del almacén de claves** para ir al almacén de claves asociado al recurso de Azure Stack Edge. 

    ![Vaya al almacén de claves del dispositivo](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. Para ver la configuración de diagnóstico asociada al almacén de claves, vaya a **Configuración de diagnóstico**. Esta configuración le permite supervisar cómo y cuándo se accede a los almacenes de claves y quién lo hace. Puede ver que se ha creado una configuración de diagnóstico. Los registros fluyen a la cuenta de almacenamiento que también se creó. Los eventos de auditoría también se crean en el almacén de claves.
    
    ![Visualización de la configuración de diagnóstico del almacén de claves](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-diagnostics-settings-1.png)

Si ha configurado un destino de almacenamiento diferente para los registros en el almacén de claves, puede ver los registros directamente en esa cuenta de almacenamiento.

### <a name="view-insights"></a>Ver información 

Para acceder a la información del almacén de claves, incluidas las operaciones realizadas en el almacén de claves, siga estos pasos:

1. En la instancia de Azure Portal del recurso de Azure Stack Edge, vaya a **Seguridad**. 
1. Seleccione el vínculo correspondiente a **Diagnóstico del almacén de claves**. 

    ![Vaya al almacén de claves del dispositivo](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. La hoja **Insights** ofrece una descripción general de las operaciones realizadas en el almacén de claves.

    ![Visualización de información para el almacén de claves](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-insights-1.png) 

### <a name="view-managed-identity-status"></a>Visualización del estado de la identidad administrada

Para ver el estado de la identidad administrada asignada por el sistema asociada al recurso de Azure Stack Edge, siga estos pasos:

1. En la instancia de Azure Portal del recurso de Azure Stack Edge, vaya a **Seguridad**. 
1. En el panel derecho, vaya a **Identidad administrada asignada por el sistema** para ver si la identidad administrada asignada por el sistema está habilitada o deshabilitada.

    ![Vaya al almacén de claves del dispositivo](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

### <a name="view-key-vault-locks"></a>Ver bloqueos de almacén de claves

Para acceder al almacén de claves y ver los bloqueos, siga estos pasos:

1. En la instancia de Azure Portal del recurso de Azure Stack Edge, vaya a **Seguridad**. 
1. Seleccione el vínculo correspondiente al **nombre del almacén de claves** para ir al almacén de claves asociado al recurso de Azure Stack Edge. 

    ![Vaya al almacén de claves del dispositivo](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)
1. Para ver los bloqueos del almacén de claves, vaya a **Bloqueos**. Para evitar la eliminación accidental, se habilita un bloqueo de recursos en el almacén de claves. 
    
    ![Visualización de bloqueos en el almacén de claves](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-locks-1.png)


## <a name="regenerate-activation-key"></a>Regenerar clave de activación

En determinados casos, es posible que tenga que regenerar la clave de activación. Al regenerar una clave de activación, se producen los siguientes eventos:

1. Solicita regenerar una clave de activación en Azure Portal. 
1. La clave de activación se devuelve a Azure Portal. A continuación, puede copiar esta clave y usarla. 

No se tiene acceso al almacén de claves al regenerar la clave de activación. 

## <a name="recover-device-secrets"></a>Recuperación de secretos de dispositivo

Si el CIK se elimina accidentalmente o los secretos (por ejemplo, la contraseña de usuario de BMC) se han quedado obsoletos en el almacén de claves, tendría que insertar secretos desde el dispositivo para actualizar los secretos del almacén de claves. 

Siga estos pasos para sincronizar secretos de dispositivo:  

1. En Azure Portal, vaya al recurso de Azure Stack Edge y luego a **Seguridad**.
1. En el panel derecho, en la barra de comandos superior, seleccione **Sincronizar secretos de dispositivo**.
1. Los secretos del dispositivo se insertan en el almacén de claves para restaurar o actualizar los secretos del almacén de claves. Verá una notificación cuando se completa la sincronización.

    ![Sincronización de secretos de dispositivo en el almacén de claves](media/azure-stack-edge-gpu-activation-key-vault/sync-device-secrets-1.png)

## <a name="delete-key-vault"></a>Eliminar el almacén de claves

Hay dos maneras de eliminar el almacén de claves asociado al recurso de Azure Stack Edge clave:

- Elimine el recurso de Azure Stack Edge y elija eliminar el almacén de claves asociado al mismo tiempo.
- Eliminó accidentalmente el almacén de claves directamente.

Cuando se elimina el recurso de Azure Stack Edge, el almacén de claves también se elimina con el recurso. Se le pedirá confirmación. Si va a almacenar otras claves en este almacén de claves y no piensa eliminar este almacén de claves, puede optar por no dar su consentimiento. Solo se elimina el recurso de Azure Stack Edge, lo que deja intacto el almacén de claves. 

Siga estos pasos para eliminar el recurso de Azure Stack Edge y el almacén de claves asociado:

1. En Azure Portal, vaya al recurso de Azure Stack Edge y, a continuación, a **Información general**.
1. En el panel derecho, seleccione **Eliminar**. Esta acción eliminará el recurso de Azure Stack Edge.

   ![Eliminación del recurso de Azure Stack Edge y el almacén de claves asociado](media/azure-stack-edge-gpu-activation-key-vault/delete-azure-stack-edge-resource-1.png)  

1. Verá una hoja de confirmación. Escriba el nombre del recurso de Azure Stack Edge. Para confirmar la eliminación del almacén de claves asociado, escriba **Sí**.

    ![Confirmación de la eliminación del recurso de Azure Stack Edge y el almacén de claves asociado](media/azure-stack-edge-gpu-activation-key-vault/confirm-delete-azure-stack-edge-resource-1.png)   
1. Seleccione **Eliminar**.

El recurso de Azure Stack Edge y el almacén de claves se eliminan.

El almacén de claves se puede eliminar accidentalmente cuando el recurso de Azure Stack Edge está en uso. Si esto sucede, se genera una alerta crítica en la página de **Seguridad** para el recurso de Azure Stack Edge. Puede ir a esta página para recuperar el almacén de claves. 


## <a name="recover-key-vault"></a>Recuperación del almacén de claves

Puede recuperar el almacén de claves asociado al recurso de Azure Stack Edge si se elimina accidentalmente o se purga. Si este almacén de claves se usó para almacenar otras claves, deberá recuperar esas claves restaurando el almacén de claves.

- Después de la eliminación, tiene un periodo de 90 días para restaurar el almacén de claves que se eliminó.
- Si ya ha transcurrido el período de protección de purga de 90 días, no puede restaurar el almacén de claves. En su lugar, deberá crear un nuevo almacén de claves.

Siga estos pasos para recuperar el almacén de claves, dentro del periodo de 90 días posteriores a la eliminación:

- En Azure Portal, vaya a la página de **Seguridad** del recurso de Azure Stack Edge. Verá una notificación que confirma que se eliminó el almacén de claves asociado al recurso. Puede seleccionar la notificación o seleccionar **Reconfigurar** con el nombre del almacén de claves en **Preferencias de seguridad** para recuperar el almacén de claves.

    ![Ir a la página de Seguridad](media/azure-stack-edge-gpu-activation-key-vault/security-page-1.png) 

- En la hoja de **Recuperación de almacén de claves**, seleccione **Configuración**. Las siguientes operaciones se realizan como parte de la recuperación:  

    ![Pasos de la recuperación](media/azure-stack-edge-gpu-activation-key-vault/recover-key-vault-2.png) 

    - Se recupera un almacén de claves con el mismo nombre y se coloca un bloqueo en el recurso del almacén de claves. 
    
        > [!NOTE]
        > Si se elimina el almacén de claves y no ha transcurrido el período de protección de purga de 90 días, en ese período de tiempo, el nombre del almacén de claves no se puede usar para crear un nuevo almacén de claves.
    - Se ha creado la cuenta de almacenamiento para almacenar los registros de auditoría. 
    - Se ha concedido acceso al almacén de claves a la identidad administrada asignada por el sistema.
    - Los secretos del dispositivo se insertan en el almacén de claves. 
    
    Seleccione **Configurar**. 
 
    ![Recuperación de la hoja del almacén de claves](media/azure-stack-edge-gpu-activation-key-vault/recover-key-vault-1.png)  

    El almacén de claves se recupera y, una vez completada la recuperación, se muestra una notificación a ese efecto.

Si se elimina el almacén de claves y ha transcurrido el período de protección de purga de 90 días, tendrá la opción de crear un nuevo almacén de claves mediante el [procedimiento Recuperar clave](#recover-key-vault) descrito anteriormente. En este caso, proporcionará un nuevo nombre para el almacén de claves. Se crea una nueva cuenta de almacenamiento, se concede a la identidad administrada acceso a este almacén de claves y los secretos de dispositivo se insertan en este almacén de claves.
  
<!--To recover the key vault using the follow these steps to [Recover your key vault](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates).-->

## <a name="recover-managed-identity-access"></a>Recuperación del acceso a identidades administradas

Si se elimina la directiva de acceso a la identidad administrada asignada por el sistema, se genera una alerta cuando el dispositivo no puede volver a sincronizar los secretos del almacén de claves. Si la identidad administrada no tiene acceso al almacén de claves, se genera de nuevo una alerta de dispositivo. Seleccione la alerta en cada caso para abrir la **Recuperación de la hoja de almacén de claves** y reconfigúrela. Este proceso debe restaurar el acceso de la identidad administrada. 

![Concesión de acceso de identidad administrada al flujo del almacén de claves](media/azure-stack-edge-gpu-activation-key-vault/activation-key-generation-flow-2.png)


## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [Generación de la clave de activación](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).
- [Solucione los errores del almacén de claves](azure-stack-edge-gpu-troubleshoot-activation.md) en el dispositivo de Azure Stack Edge.
