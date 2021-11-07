---
title: Solución de problemas relacionados con la implementación de directivas de Azure en Key Vault
description: Solución de problemas relacionados con la implementación de Azure Policy en Key Vault
author: sebansal
ms.author: sebansal
ms.date: 08/17/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 380077339920071fa56d385e6de8f7e7a9e84321
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072548"
---
# <a name="troubleshoot-issues-with-implementing-azure-policy-on-key-vault"></a>Solución de problemas relacionados con la implementación de directivas de Azure en Key Vault

En este artículo se explica cómo solucionar los errores generales que pueden producirse al configurar [Azure Policy para Key Vault](./azure-policy.md) y se sugieren formas de resolverlos.

## <a name="about-azure-policy-for-key-vault"></a>Acerca de Azure Policy en Key Vault

[Azure Policy](../../governance/policy/index.yml) es una herramienta de gobierno que ofrece a los usuarios la capacidad de auditar y administrar su entorno de Azure a escala. Azure Policy proporciona la capacidad de colocar barreras en los recursos de Azure para asegurarse de que son compatibles con las reglas de directivas asignadas. Permite a los usuarios realizar tareas de auditoría, aplicación en tiempo real y corrección de su entorno de Azure. Los resultados de las auditorías realizadas por la directiva estarán disponibles para los usuarios en un panel de cumplimiento, donde podrán ver una exploración en profundidad de qué recursos y componentes son compatibles y cuáles no.

### <a name="logging"></a>Registro

Para supervisar cómo se realizan las evaluaciones de directivas, puede revisar los registros de Key Vault. Puede hacerlo al habilitar el registro de Azure Key Vault, que guarda la información en una cuenta de almacenamiento de Azure proporcionada por el usuario. Para obtener instrucciones paso a paso sobre cómo configurar esta opción, consulte [Habilitación del registro de Key Vault](howto-logging.md).

Al habilitar el registro, se creará automáticamente un contenedor llamado **AzurePolicyEvaluationDetails** para recopilar información de registro relacionada con las directivas en la cuenta de almacenamiento especificada. 

> [!NOTE]
> Debe regular estrictamente el acceso a los datos de supervisión, especialmente a los archivos de registro, ya que pueden contener información confidencial. Descubra cómo aplicar el [rol de supervisión integrado de Azure](../../azure-monitor/roles-permissions-security.md) y limitar el acceso.
> 
> 

Los blobs individuales se almacenan como texto, con formato de blob JSON. Echemos un vistazo a una entrada de registro de ejemplo de una directiva de clave: las [claves deben tener establecida la fecha de expiración](azure-policy.md?tabs=keys#secrets-should-have-expiration-date-set). Esta directiva evalúa todas las claves de los almacenes de claves y marca como no compatibles aquellas que no tienen establecida una fecha de expiración.

```json
{
  "ObjectName": "example",
  "ObjectType": "Key",
  "IsComplianceCheck": false,
  "EvaluationDetails": [
    {
      "AssignmentId": "<subscription ID>",
      "AssignmentDisplayName": "[Preview]: Key Vault keys should have an expiration date",
      "DefinitionId": "<definition ID>",
      "DefinitionDisplayName": "[Preview]: Key Vault keys should have an expiration date",
      "Outcome": "NonCompliant",
      "ExpressionEvaluationDetails": [
        {
          "Result": "True",
          "Expression": "type",
          "ExpressionKind": "Field",
          "ExpressionValue": "Microsoft.KeyVault.Data/vaults/keys",
          "TargetValue": "Microsoft.KeyVault.Data/vaults/keys",
          "Operator&quot;: &quot;Equals"
        },
        {
          "Result": "True",
          "Expression": "Microsoft.KeyVault.Data/vaults/keys/attributes.expiresOn",
          "ExpressionKind": "Field",
          "ExpressionValue": "******",
          "TargetValue": "False",
          "Operator&quot;: &quot;Exists"
        }
      ]
    }
  ]
}
```

En la tabla siguiente se muestran los nombres y las descripciones de los campos:

| Nombre del campo | Descripción |
| --- | --- |
| **ObjectName** |Nombre del objeto. |
| **ObjectType** |Tipo de objeto de almacén de claves, es decir, certificado, secreto o clave. |
| **IsComplianceCheck** |True si la evaluación se produjo durante la auditoría nocturna; false si se produjo durante la creación o actualización de recursos. |
| **AssignmentId** | Identificador de la asignación de directiva |
| **AssignmentDisplayName** | Nombre descriptivo de la asignación de directiva |
| **DefinitionId** | Identificador de la definición de directiva para la asignación |
| **DefinitionDisplayName** | Nombre descriptivo de la definición de directiva para la asignación |
| **Resultado** | Resultado de la evaluación de directivas |
| **ExpressionEvaluationDetails** | Detalles sobre las evaluaciones realizadas durante la evaluación de directivas |
| **ExpressionValue** | Valor real del campo especificado durante la evaluación de directivas |
| **TargetValue** | Valor esperado del campo especificado |


### <a name="frequently-asked-questions"></a>Preguntas más frecuentes

#### <a name="key-vault-recovery-blocked-by-azure-policy"></a>Recuperación de Key Vault bloqueado por Azure Policy

Una de las razones podría ser que su suscripción (o grupo de administración) tiene una directiva que bloquea la recuperación. Para corregir este problema, ajuste la directiva para que no se aplique cuando se recupera un almacén.

Si ve el tipo de error ```RequestDisallowedByPolicy``` en la recuperación debido a la directiva **integrada**, asegúrese de que usa la **versión más actualizada**. 

Si ha creado una **directiva personalizada** con su propia lógica, este es un ejemplo de parte de una directiva que se puede usar para requerir la eliminación temporal. Para recuperar un almacén eliminado temporalmente se usa la misma API que para la creación o la actualización de un almacén. Sin embargo, en lugar de especificar las propiedades del almacén, tiene una única propiedad "createMode" con el valor "recover". El almacén se restaurará con las propiedades que tenía cuando se eliminó. Las directivas que bloquean las solicitudes también bloquearán la recuperación de almacenes eliminados temporalmente, a menos que tengan propiedades específicas configuradas. Para corregir este problema, incluya una cláusula que haga que la directiva omita las solicitudes cuando "createMode" es "recover":

Verá que tiene una cláusula que hace que la directiva solo se aplique cuando "createMode" no es igual a "recover":

```

    "policyRule": { 
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.KeyVault/vaults"
          }, 
          {
            "not": {
              "field": "Microsoft.Keyvault/vaults/createMode",
              "equals": "recover"
            }
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.KeyVault/vaults/enableSoftDelete",
                "exists": "false"
              },
              {
                "field": "Microsoft.KeyVault/vaults/enableSoftDelete",
                "equals": "false"
              }
            ]
          }
        ]
      },
      "then": {
        "effect": "[parameters('effect')]"
      }
    }
```

#### <a name="latency-on-azure-policy-assignment-delete-on-key-vault"></a>Latencia en la eliminación de asignaciones de directivas de Azure en Key Vault 

Microsoft.KeyVault.Data: una asignación de directiva eliminada puede tardar hasta 24 horas en dejar de aplicarse. 

Mitigación: actualice el efecto de la asignación de directiva a "Deshabilitado".


#### <a name="secret-creation-via-arm-template-missing-out-policy-evaluation"></a>Creación de secretos a través de una plantilla de ARM que falta en la evaluación de directivas

Las directivas de plano de datos que evalúan la creación de secretos no serían aplicables a los [secretos creados a través de la plantilla de ARM](../secrets/quick-create-template.md?tabs=CLI) en el momento de la creación del secreto. Al cabo de 24 horas, se produciría la comprobación de cumplimiento automatizada y se pueden revisar los resultados de cumplimiento.


## <a name="next-steps"></a>Pasos siguientes

* Descubra cómo [solucionar errores mediante Azure Policy](../../governance/policy/troubleshoot/general.md)
* Más información sobre los [problemas conocidos de Azure Policy](https://github.com/azure/azure-policy#known-issues)
