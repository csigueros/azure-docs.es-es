---
title: Migración de la configuración de dirección IP pública de Azure a otra región de Azure en Azure Portal
description: Use una plantilla para migrar una configuración de dirección IP pública de Azure de una región de Azure a otra mediante Azure Portal.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 6deb906d55f74cc3d418473a669a81d3d2ddf5e4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130214670"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>Migración de una configuración de dirección IP pública de Azure a otra región mediante Azure Portal

Hay varios escenarios en los que puede ser conveniente migrar las configuraciones existentes de dirección IP pública de Azure de una región a otra. Por ejemplo, quiere crear una dirección IP pública con la misma configuración y SKU para las pruebas. También, puede que, como parte del planeamiento de la recuperación ante desastres, quiera migrar una configuración de dirección IP pública a otra región.

**Las direcciones IP públicas de Azure son específicas de la región y no se pueden migrar de una región a otra.** Sin embargo, puede usar una plantilla de Azure Resource Manager para exportar la configuración actual de una dirección IP pública.  Después, puede preparar el recurso en otra región exportando la dirección IP pública a una plantilla y modificando los parámetros para que coincidan con la región de destino, y luego implementar la plantilla en la nueva región.  Para más información sobre Resource Manager y las plantillas, consulte [Inicio rápido: Creación e implementación de plantillas de Azure Resource Manager mediante Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Prerrequisitos

- Asegúrese de que la dirección IP pública de Azure se encuentra en la región de Azure desde la que va a moverla.

- Las direcciones IP públicas de Azure no se pueden mover entre regiones.  Tendrá que asociar la nueva dirección IP pública a los recursos de la región de destino.

- Para exportar una configuración de IP pública e implementar una plantilla para crear una dirección IP pública en otra región, necesitará el rol de colaborador de red u otro superior.

- Identifique el diseño de red de origen y todos los recursos que está usando actualmente. Este diseño incluye, pero no se limita a, los equilibradores de carga, los grupos de seguridad de red (NSG) y las redes virtuales.

- Compruebe que su suscripción de Azure permite crear direcciones IP públicas en la región de destino que se usa. Para habilitar la cuota necesaria, póngase en contacto con el soporte técnico.

- Asegúrese de que la suscripción tiene suficientes recursos para admitir la adición de direcciones IP públicas para este proceso.  Vea [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Preparación y traslado
En los pasos siguientes se muestra cómo preparar la dirección IP pública para mover la configuración mediante una plantilla de Resource Manager y cómo mover la configuración de IP pública a la región de destino mediante Azure Portal.

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportación de la plantilla e implementación desde un script

1. Inicie sesión en [Azure Portal](https://portal.azure.com) > **Grupos de recursos**.
2. Busque el grupo de recursos que contiene la dirección IP pública de origen y haga clic en él.
3. Seleccione > **Configuración** > **Exportar plantilla**.
4. En la hoja **Exportar plantilla**, elija **Implementar**.
5. Haga clic en **PLANTILLA** > **Editar parámetros** para abrir el archivo **parameters.json** en el editor en línea.
8. Para editar el parámetro del nombre de la dirección IP pública, cambie la propiedad en **parameters** > **value** del nombre de la dirección IP pública de origen al nombre de la dirección IP pública de destino. Asegúrese de que el nombre se encuentra entre comillas:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  Haga clic en **Guardar** en el editor.

9.  Haga clic en **Plantilla** > **Editar plantilla** para abrir el archivo **template.json** en el editor en línea.

10. Para editar la región de destino a la que se va a mover la dirección IP pública, cambie la propiedad **location** en **resources**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. Para obtener los códigos de ubicación de la región, consulte [Ubicaciones de Azure](https://azure.microsoft.com/global-infrastructure/locations/).  El código de una región es el nombre de la región sin espacios, **Centro de EE. UU.**  = **centralus**.

12. También puede cambiar otros parámetros de la plantilla si así lo desea; son opcionales según sus requisitos:

    * **SKU**: puede cambiar la SKU de la dirección IP pública de la configuración de estándar a básica o viceversa modificando la propiedad **sku** > **name** en el archivo **template.json**:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Para más información sobre las diferencias entre las direcciones IP públicas de la SKU básica y estándar, consulte [Creación, modificación o eliminación de una dirección IP pública](./ip-services/virtual-network-public-ip-address.md):

    * **Método de asignación de IP pública** y **tiempo de espera de inactividad**: puede cambiar estas dos opciones en la plantilla si cambia la propiedad **publicIPAllocationMethod** de **Dynamic** a **Static** o bien de **Static** a **Dynamic**. El tiempo de espera de inactividad se puede cambiar modificando la propiedad **idleTimeoutInMinutes** con la cantidad deseada.  El valor predeterminado es **4**:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Para más información sobre los métodos de asignación y los valores de tiempo de espera de inactividad, consulte [Creación, modificación o eliminación de una dirección IP pública](./ip-services/virtual-network-public-ip-address.md).


13. Haga clic en **Guardar** en el editor en línea.

14. Haga clic en **Aspectos básicos** > **Suscripción** para elegir la suscripción donde se implementará la dirección IP pública de destino.

15. Haga clic en **Aspectos básicos** > **Grupo de recursos** para elegir el grupo de recursos donde se implementará la dirección IP pública de destino.  Puede hacer clic en **Crear nuevo** para crear un grupo de recursos para la dirección IP pública de destino.  Asegúrese de que el nombre no sea el mismo que el del grupo de recursos de origen de la dirección IP pública de origen existente.

16. Compruebe que **Aspectos básicos** > **Ubicación** está establecido en la ubicación de destino en la que quiere que se implemente la dirección IP pública.

17. En **CONFIGURACIÓN**, compruebe que el nombre coincide con el nombre que especificó en el editor de parámetros anterior.

18. Marque la casilla en **TÉRMINOS Y CONDICIONES**.

19. Haga clic en el botón **Comprar** para implementar la dirección IP pública de destino.

## <a name="discard"></a>Discard (Descartar)

Si quiere descartar la dirección IP pública de destino, elimine el grupo de recursos que la contiene.  Para ello, en el portal, seleccione el grupo de recursos en el panel y, luego, **Eliminar** en la parte superior de la página de información general.

## <a name="clean-up"></a>Limpieza

Para confirmar los cambios y completar el movimiento de la dirección IP pública de destino, elimine la dirección IP pública de origen o el grupo de recursos. Para ello, en el portal, seleccione la dirección IP pública o el grupo de recursos en el panel y seleccione **Eliminar** en la parte superior de cada página.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha movido una dirección IP pública de Azure de una región a otra y ha limpiado los recursos de origen.  Para obtener más información sobre cómo trasladar recursos entre regiones y la recuperación ante desastres en Azure, consulte:


- [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Traslado de máquinas virtuales de Azure a otra región](../site-recovery/azure-to-azure-tutorial-migrate.md)