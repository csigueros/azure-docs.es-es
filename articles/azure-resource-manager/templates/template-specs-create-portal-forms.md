---
title: Creación de formularios del portal para una especificación de plantilla
description: Aprenda a crear formularios que se muestren en los formularios de Azure Portal. Uso del formulario para implementar una especificación de plantilla
ms.topic: tutorial
ms.date: 11/02/2021
ms.openlocfilehash: 3175264b8d3a5b9fd1698c8ccd1522ddb1f54b63
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478111"
---
# <a name="tutorial-create-azure-portal-forms-for-a-template-spec"></a>Tutorial: Creación de formularios de Azure Portal para una especificación de plantilla

Para ayudar a los usuarios a implementar una [especificación de plantilla](template-specs.md), puede crear un formulario que se muestre en Azure Portal. El formulario permite a los usuarios especificar valores que se pasan a la especificación de plantilla como parámetros.

Al crear la especificación de plantilla, se empaquetan el formulario y la plantilla de Azure Resource Manager conjuntamente. La implementación de la especificación de plantilla a través del portal inicia el formulario automáticamente.

:::image type="content" source="./media/template-specs-create-portal-forms/view-portal.png" alt-text="Captura de pantalla del formulario para proporcionar valores para la especificación de plantilla":::

## <a name="prerequisites"></a>Prerrequisitos

Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

En Azure PowerShell, use la [versión 6.0.0, o cualquier versión posterior](/powershell/azure/install-az-ps). En la CLI de Azure, use la [versión 2.24.0, o cualquier versión posterior](/cli/azure/install-azure-cli).

## <a name="create-template"></a>Creación de una plantilla

Para mostrar los diferentes elementos del portal que están disponibles en un formulario, usará una plantilla de Resource Manager con varios parámetros. La siguiente plantilla crea un almacén de claves, configura los permisos para el almacén de claves para un usuario y agrega un secreto.

Copie este archivo y guárdelo localmente. En este tutorial se supone que le ha asignado el nombre **keyvault.json**, pero puede darle cualquier otro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Specifies the Azure location where the key vault should be created."
      }
    },
    "enabledForDeployment": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Virtual Machines are permitted to retrieve certificates stored as secrets from the key vault."
      }
    },
    "enabledForDiskEncryption": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Disk Encryption is permitted to retrieve secrets from the vault and unwrap keys."
      }
    },
    "enabledForTemplateDeployment": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Resource Manager is permitted to retrieve secrets from the key vault."
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue": "[subscription().tenantId]",
      "metadata": {
        "description": "Specifies the Azure Active Directory tenant ID that should be used for authenticating requests to the key vault. Get it by using Get-AzSubscription cmdlet."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the secret that you want to create."
      }
    },
    "secretValue": {
      "type": "securestring",
      "metadata": {
        "description": "Specifies the value of the secret that you want to create."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "enabledForDeployment": "[parameters('enabledForDeployment')]",
        "enabledForDiskEncryption": "[parameters('enabledForDiskEncryption')]",
        "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "objectId": "[parameters('objectId')]",
            "tenantId": "[parameters('tenantId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/secrets",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "properties": {
        "value": "[parameters('secretValue')]"
      }
    }
  ]
}
```

## <a name="create-default-form"></a>Creación de un formulario predeterminado

Azure Portal proporciona un espacio aislado para crear y obtener una vista previa de los formularios. Este espacio aislado puede generar un formulario a partir de una plantilla de Resource Manager existente. Este formulario predeterminado se usará para empezar a crear un formulario para la especificación de plantilla.

1. Abra el [espacio aislado de vista de formulario](https://aka.ms/form/sandbox).

1. En **Package Type** (Tipo de paquete), seleccione **CustomTemplate**.

   :::image type="content" source="./media/template-specs-create-portal-forms/package-type.png" alt-text="Captura de pantalla de la selección de la opción de plantilla personalizada como tipo de paquete":::

1. Seleccione el icono para abrir una plantilla existente.

   :::image type="content" source="./media/template-specs-create-portal-forms/open-template.png" alt-text="Captura de pantalla del icono para abrir el archivo":::

1. Vaya a la plantilla del almacén de claves que guardó localmente. Selecciónela y, después, seleccione **OK** (Aceptar).
1. Cuando se le pregunte si desea sobrescribir los cambios actuales, seleccione **Yes** (Sí).
1. El formulario generado automáticamente se muestra en la ventana de código. Para ver que funciona sin modificaciones, seleccione **Vista previa**.

   :::image type="content" source="./media/template-specs-create-portal-forms/preview-form.png" alt-text="Captura de pantalla de la selección de vista previa":::

1. El espacio aislado muestra el formulario. Tiene campos para seleccionar una suscripción, un grupo de recursos y una región. También tiene campos para todos los parámetros de la plantilla.

   La mayoría de los campos son cuadros de texto, pero algunos de ellos son específicos para el tipo de parámetro. Cuando la plantilla incluye valores permitidos para un parámetro, el formulario generado automáticamente utiliza un elemento desplegable. El elemento desplegable se ha rellenado previamente con los valores permitidos.

   > [!WARNING]
   > No seleccione **Create** (Crear), ya que iniciará una implementación real. Tendrá la oportunidad de implementar la especificación de plantilla más adelante en este tutorial.

## <a name="customize-form"></a>Personalización del formulario

El formulario predeterminado es un buen punto de partida para conocer los formularios, pero es habitual que se desee personalizar. Se puede editar en el espacio aislado o en Visual Studio Code. La opción de vista previa solo está disponible en el espacio aislado.

1. Vamos a establecer el esquema correcto. Reemplace el texto del esquema por:

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" range="1-2" highlight="2" :::

1. Dé al formulario un **título** que describa su uso.

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" range="1-6" highlight="6" :::

1. El formulario predeterminado tenía todos los campos de la plantilla combinados en un paso denominado **Aspectos básicos**. Para ayudar a los usuarios a conocer los valores que proporcionan, divida el formulario en pasos. Cada uno de ellos paso contiene campos relacionados con una parte lógica de la solución que se va a implementar.

   Busque el paso con la etiqueta **Basics** (Aspectos básicos). Mantendrá este paso, pero agregará los pasos siguientes. Los nuevos pasos se centrarán en configurar el almacén de claves, establecer los permisos de usuario y especificar el secreto. Asegúrese de agregar una coma después del paso de aspectos básicos.

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/steps.json" highlight="15-32" :::

   > [!IMPORTANT]
   > Las propiedades del formulario distinguen mayúsculas de minúsculas. Asegúrese de usar las mayúsculas y minúsculas como se muestra en los ejemplos.

1. Seleccione **Vista previa**. Verá los pasos, pero la mayoría de ellos no tienen ningún elemento.

   :::image type="content" source="./media/template-specs-create-portal-forms/view-steps.png" alt-text="Captura de pantalla de los pasos del formulario":::

1. Ahora, mueva los elementos a los pasos adecuados. Comience por los elementos con la etiqueta **Secret Name** (Nombre del secreto) y **Secret Value** (Valor del secreto). Quite estos elementos del paso **Basics** (Aspectos básicos) y agréguelos al paso **Secret** (Secreto).

   ```json
   {
     "name": "secret",
     "label": "Secret",
     "elements": [
       {
         "name": "secretName",
         "type": "Microsoft.Common.TextBox",
         "label": "Secret Name",
         "defaultValue": "",
         "toolTip": "Specifies the name of the secret that you want to create.",
         "constraints": {
           "required": true,
           "regex": "",
           "validationMessage": ""
         },
         "visible": true
       },
       {
         "name": "secretValue",
         "type": "Microsoft.Common.PasswordBox",
         "label": {
           "password": "Secret Value",
           "confirmPassword": "Confirm password"
         },
         "toolTip": "Specifies the value of the secret that you want to create.",
         "constraints": {
           "required": true,
           "regex": "",
           "validationMessage": ""
         },
         "options": {
           "hideConfirmation": true
         },
         "visible": true
       }
     ]
   }
   ```

1. Al mover elementos, debe reparar la sección `outputs`. Actualmente, la sección de salidas hace referencia a esos elementos como si estuvieran en el paso de aspectos básicos. Corrija la sintaxis para que haga referencia a los elementos del paso `secret`.

   ```json
   "outputs": {
     "parameters": {
       ...
       "secretName": "[steps('secret').secretName]",
       "secretValue": "[steps('secret').secretValue]"
     }
   ```

1. Siga moviendo los elementos a los pasos adecuados. En lugar de pasar por cada uno de ellos, examine el formulario actualizado.

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" :::

Guarde este archivo localmente con el nombre **keyvaultform.json**.

## <a name="create-template-spec"></a>Crear una especificación de plantilla

Al crear la especificación de plantilla, indique ambos archivos.

En el caso de PowerShell, use [New-AzTemplateSpec](/powershell/module/az.resources/new-aztemplatespec) y especifique el formulario en el parámetro `-UIFormDefinitionFile`.

```azurepowershell
New-AzTemplateSpec `
  -name keyvaultspec `
  -version 1 `
  -ResourceGroupName templateSpecRG `
  -location westus2 `
  -templatefile keyvault.json `
  -UIFormDefinitionFile keyvaultform.json
```

En el caso de la CLI de Azure, use [az ts create](/cli/azure/ts#az_ts_create) y especifique el formulario en el parámetro `--ui-form-definition`.

```azurecli
az ts create \
  --name keyvaultspec \
  --version 1 \
  --resource-group templatespecRG \
  --location westus2 \
  --template-file keyvault.json \
  --ui-form-definition keyvaultform.json
```

## <a name="deploy-through-portal"></a>Implementación mediante el portal

Para probar el formulario, vaya al portal y, después, a la especificación de plantilla. Seleccione **Deploy** (Implementar).

:::image type="content" source="./media/template-specs-create-portal-forms/deploy-template-spec.png" alt-text="Captura de pantalla de la información general de especificación de plantilla con la opción de implementación":::

Verá el formulario que ha creado. Siga los pasos y especifique los valores adecuados en los distintos campos.

En el paso **Basics** (Aspectos básicos), verá un campo para **Region** (Región). Este campo se usa para la ubicación del grupo de recursos. En el paso **Key Vault** (Almacén de claves), verá un campo para **Location** (Ubicación). Este campo se usa para la ubicación del almacén de claves.

En el paso **Permissions** (Permisos), puede especificar su propio identificador de usuario para el identificador de objeto. Use el valor predeterminado (`["list"]`) para los permisos de clave y secreto. Mejorará esa opción en la sección siguiente.

Cuando haya terminado de especificar valores, seleccione **Create** (Crear) para implementar la especificación de plantilla.

## <a name="improve-the-form"></a>Mejora del formulario

En la sección anterior, agregó pasos y movió elementos, pero no cambió ninguno de los comportamientos predeterminados. En esta sección, realizará cambios que mejoran la experiencia de los usuarios de la especificación de plantilla.

Anteriormente, los dos campos de permisos eran cuadros de texto. Ahora, usará una lista desplegable. Establezca el tipo en `Microsoft.Common.DropDown`.

Actualice `keysPermissions`:

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-171" highlight="3" :::

Y `secretsPermissions`:

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="253-255" highlight="3" :::

Estos campos deben pasar una matriz a la plantilla. Una lista desplegable normal no funcionará porque solo le permite seleccionar un valor. Para seleccionar más de un valor y pasarlos como una matriz, agregue el campo `multiselect` y establézcalo en `true`.

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-173" highlight="5" :::

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="253-257" highlight="5" :::

Por último, debe especificar los valores permitidos para la lista desplegable y un valor predeterminado.

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-304" highlight="6-8,12-81,90-92,96-133" :::

Cree una versión de la especificación de plantilla.

Con PowerShell:

```azurepowershell
New-AzTemplateSpec `
  -name keyvaultspec `
  -version 2 `
  -ResourceGroupName templateSpecRG `
  -location westus2 `
  -templatefile keyvault.json `
  -UIFormDefinitionFile keyvaultform.json
```

O la CLI de Azure:

```azurecli
az ts create \
  --name keyvaultspec \
  --version 2 \
  --resource-group templatespecRG \
  --location westus2 \
  --template-file keyvault.json \
  --ui-form-definition keyvaultform.json
```

Vuelva a implementar la especificación de plantilla con el formulario del portal mejorado.

:::image type="content" source="./media/template-specs-create-portal-forms/view-portal.png" alt-text="Captura de pantalla del formulario para proporcionar valores para la especificación de plantilla":::

Observe que los campos de permiso ahora son una lista desplegable que permite varios valores.

## <a name="next-steps"></a>Pasos siguientes

Para información sobre la implementación de una especificación de plantilla como una plantilla vinculada, consulte [Tutorial: Implementación de una especificación de plantilla como una plantilla vinculada](template-specs-deploy-linked-template.md).
