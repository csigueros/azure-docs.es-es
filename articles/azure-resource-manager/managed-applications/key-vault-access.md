---
title: Uso de Key Vault al implementar la aplicación administrada
description: Muestra cómo usar los secretos de acceso de Azure Key Vault al implementar Managed Applications
author: tfitzmac
ms.custom: subject-rbac-steps
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: tomfitz
ms.openlocfilehash: d5a6dc1de2ee574a69b8dab746a24bcf82b44d64
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253168"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Acceso al secreto de Key Vault al implementar Azure Managed Applications

Cuando tiene que pasar un valor seguro (por ejemplo, una contraseña) como un parámetro durante la implementación, puede recuperar el valor de [Azure Key Vault](../../key-vault/general/overview.md). Para acceder a Key Vault cuando se implementa Managed Applications, debe conceder acceso a la entidad de servicio del **proveedor de recursos de dispositivos**. El servicio Managed Applications utiliza esta identidad para ejecutar operaciones. Para recuperar correctamente un valor de un almacén de claves durante la implementación, es necesario que la entidad de servicio pueda obtener acceso al almacén de claves.

En este artículo se describe cómo configurar Key Vault para que funcione con Managed Applications.

## <a name="enable-template-deployment"></a>Habilitar la implementación de plantillas

1. En el portal, seleccione su instancia de Key Vault.

1. Seleccione **Directivas de acceso**.   

   ![Seleccionar directivas de acceso](./media/key-vault-access/select-access-policies.png)

1. Seleccione **Haga clic para mostrar las directivas de acceso avanzado**.

   ![Mostrar directivas de acceso avanzado](./media/key-vault-access/advanced.png)

1. Seleccione **Habilitar el acceso a Azure Resource Manager para la implementación de plantillas**. Después, seleccione **Guardar**.

   ![Habilitar la implementación de plantillas](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Agregar servicio como colaborador

Asigne el rol de **Colaborador** al usuario del **proveedor de recursos del dispositivo** en el ámbito del almacén de claves.

Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="reference-key-vault-secret"></a>Referencia a secreto de Key Vault

Para pasar un secreto desde Key Vault a una plantilla de la aplicación administrada, debe usar una [plantilla vinculada o anidada](../templates/linked-templates.md) y hacer referencia a Key Vault en los parámetros de la plantilla vinculada o anidada. Proporcione el identificador de recurso de Key Vault y el nombre del secreto.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

Ha configurado Key Vault para que esté accesible durante la implementación de una aplicación administrada.

* Para obtener información acerca de cómo pasar un valor de Key Vault como parámetro de plantilla, consulte [Uso de Azure Key Vault para pasar el valor de parámetro seguro durante la implementación](../templates/key-vault-parameter.md).
* Para ver ejemplos de aplicaciones administradas, consulte [Sample projects for Azure managed applications](sample-projects.md) (Ejemplos de proyectos para aplicaciones administradas de Azure).
* Para aprender a crear un archivo de definición de interfaz de usuario para una aplicación administrada, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
