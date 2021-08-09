---
title: Auditoría y administración del cumplimiento de directivas
titleSuffix: Azure Machine Learning
description: Conozca Azure Policy y aprenda a utilizar directivas integradas en Azure Machine Learning para asegurarse de que las áreas de trabajo se ajustan a sus requisitos.
author: aashishb
ms.author: aashishb
ms.date: 05/10/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: 02225a3be02612b9baa0a66aff3d3dcd5ef1bb87
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110067481"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Auditoría y administración de Azure Machine Learning mediante Azure Policy

[Azure Policy](../governance/policy/index.yml) es una herramienta de gobernanza que le permite tener la seguridad de que los recursos de Azure son compatibles con las directivas. Con Azure Machine Learning, puede asignar las siguientes directivas:

| Directiva | Descripción |
| ----- | ----- |
| **Clave administrada por el cliente** | audite o exija si las áreas de trabajo deben usar una clave administrada por el cliente. |
| **Vínculo privado** | Audite o imponga si las áreas de trabajo deben usar un punto de conexión privado para comunicarse con una red virtual. |
| **Punto de conexión privado** | Configure la subred de Azure Virtual Network donde se debe crear el punto de conexión privado. |
| **Zona DNS privada** | Configure la zona DNS privada que se usará para el vínculo privado. |
| **Identidad administrada asignada por el usuario** | Audite o exija que las áreas de trabajo usen una identidad administrada asignada por el usuario. |
| **Deshabilitación de la autenticación local** | Audite o exija que los recursos de proceso de Azure Machine Learning tengan los métodos de autenticación local deshabilitados. |
| **Modificación o deshabilitación de la autenticación local** | Configure los recursos de proceso para deshabilitar los métodos de autenticación local. |

Se pueden establecer directivas en ámbitos diferentes, por ejemplo, en el nivel de suscripción o de grupo de recursos. Para más información, consulte la [documentación de Azure Policy](../governance/policy/overview.md).

## <a name="conditional-access-policies"></a>Directivas de acceso condicional

Para controlar quién puede acceder al área de trabajo de Azure Machine Learning, use el [acceso condicional](../active-directory/conditional-access/overview.md) de Azure Active Directory.

## <a name="built-in-policies"></a>Directivas integradas

Azure Machine Learning proporciona un conjunto de directivas que puede usar en escenarios comunes con esta solución. Estas definiciones de directiva se pueden asignar a la suscripción existente o utilizar como base para crear sus propias definiciones personalizadas. Para ver una lista completa de las directivas integradas de Azure Machine Learning, consulte [Directivas integradas de Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

Para ver las definiciones de directivas integradas relacionadas con Azure Machine Learning, siga estos pasos:

1. Vaya a __Azure Policy__ en [Azure Portal](https://portal.azure.com).
1. Seleccione __Definiciones__.
1. En __Tipo__, seleccione _Integrada_ y, en __Categoría__, seleccione __Machine Learning__.

Desde aquí, puede seleccionar definiciones de directivas para verlas. Al ver una definición, puede usar el vínculo __Asignar__ para asignar la directiva a un ámbito específico y configurar los parámetros de la directiva. Para más información, consulte [Asignación de una directiva: Portal](../governance/policy/assign-policy-portal.md).

También puede asignar directivas mediante [Azure PowerShell](../governance/policy/assign-policy-powershell.md), la [CLI de Azure](../governance/policy/assign-policy-azurecli.md) y [plantillas](../governance/policy/assign-policy-template.md).

## <a name="workspace-encryption-with-customer-managed-key"></a>Cifrado del área de trabajo con una clave administrada por el cliente

Controla si un área de trabajo debe cifrarse con una clave administrada por el cliente, o bien usar una clave administrada por Microsoft para cifrar las métricas y los metadatos. Para más información sobre el uso de claves administradas por el cliente, consulte la sección [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) del artículo sobre cifrado de datos.

Para configurar esta directiva, establezca el parámetro de efecto en __audit__ o __deny__. Si se establece en __audit__, se puede crear un área de trabajo sin una clave administrada por el cliente y se creará un evento de advertencia en el registro de actividad.

Si la directiva se establece en __deny__, no podrá crear un área de trabajo a menos que especifique una clave administrada por el cliente. Si se intenta crear un área de trabajo sin una clave administrada por el cliente, se producirá un error similar a `Resource 'clustername' was disallowed by policy` y se creará un error en el registro de actividad. Como parte de este error, también se devuelve el identificador de la directiva.

## <a name="workspace-should-use-private-link"></a>El área de trabajo debe usar un vínculo privado

Controla si un área de trabajo debe usar Azure Private Link para comunicarse con Azure Virtual Network. Para más información sobre el uso del vínculo privado, consulte [Configuración del vínculo privado para un área de trabajo](how-to-configure-private-link.md).

Para configurar esta directiva, establezca el parámetro de efecto en __audit__ o __deny__. Si se establece en __audit__, puede crear un área de trabajo sin usar el vínculo privado y se creará un evento de advertencia en el registro de actividad.

Si la directiva se establece en __deny__, no puede crear un área de trabajo a menos que especifique un vínculo privado. Al intentar crear un área de trabajo sin un vínculo privado se produce un error. El error también se registra en el registro de actividad. Como parte de este error, también se devuelve el identificador de la directiva.

## <a name="workspace-should-use-private-endpoint"></a>El área de trabajo debe usar un punto de conexión privado

Configura un área de trabajo para crear un punto de conexión privado en la subred especificada de Azure Virtual Network.

Para configurar esta directiva, establezca el parámetro de efecto en __DeployIfNotExists__. Establezca __privateEndpointSubnetID__ en el id. de Azure Resource Manager de la subred.
## <a name="workspace-should-use-private-dns-zones"></a>El área de trabajo debe usar zonas DNS privadas

Configura un área de trabajo para que use una zona DNS privada e invalida la resolución de DNS predeterminada para un punto de conexión privado.

Para configurar esta directiva, establezca el parámetro de efecto en __DeployIfNotExists__. Establezca __privateDnsZoneId__ en el identificador de Azure Resource Manager de la zona DNS privada que se va a usar. 

## <a name="workspace-should-use-user-assigned-managed-identity"></a>El área de trabajo debe usar una identidad administrada asignada por el usuario

Permite controlar si se crea un área de trabajo mediante una identidad administrada asignada por el sistema (valor predeterminado) o una identidad administrada asignada por el usuario. La identidad administrada para el área de trabajo se usa para tener acceso a recursos asociados como Azure Storage, Azure Container Registry, Azure Key Vault y Azure Application Insights. Para más información, consulte [Uso de identidades administradas con Azure Machine Learning](how-to-use-managed-identities.md).

Para configurar esta directiva, establezca el parámetro de efecto en __audit__, __deny__ o __disabled__. Si se establece en __audit__, puede crear un área de trabajo sin tener que especificar una identidad administrada asignada por el usuario. En cambio, se utiliza una identidad asignada por el sistema y se crea un evento de advertencia en el registro de actividad.

Si la directiva se establece en __deny__, no podrá crear un área de trabajo a menos que proporcione una identidad asignada por el usuario durante el proceso de creación. Si intenta crear un área de trabajo sin proporcionar una identidad asignada por el usuario obtendrá un error como resultado. Este error también se registra en el registro de actividad. Como parte de este error, también se devuelve el identificador de la directiva.

## <a name="disable-local-authentication"></a>Deshabilitación de la autenticación local

Controla si una instancia o un clúster de proceso de Azure Machine Learning debe deshabilitar la autenticación local (SSH).

Para configurar esta directiva, establezca el parámetro de efecto en __audit__, __deny__ o __disabled__. Si lo establece en __audit__, podrá crear un proceso con SSH habilitado y un evento de advertencia en el registro de actividad.

Si la directiva se establece en __deny__, no podrá crear un proceso, salvo que se deshabilite SSH. Al intentar crear un proceso con SSH habilitado, se produce un error. El error también se registra en el registro de actividad. Como parte de este error, también se devuelve el identificador de la directiva.


## <a name="modifydisable-local-authentication"></a>Modificación o deshabilitación de la autenticación local

Modifica cualquier clúster de proceso o solicitud de creación de instancia de Azure Machine Learning para deshabilitar la autenticación local (SSH).

Para configurar esta directiva, establezca el parámetro de efecto en __Modificar__ o __Deshabilitado__. Si establece __Modificar__, cualquier creación de un clúster de proceso o de una instancia dentro del ámbito en el que se aplica la directiva tendrá deshabilitada automáticamente la autenticación local.

## <a name="next-steps"></a>Pasos siguientes

* [Documentación de Azure Policy](../governance/policy/overview.md)
* [Directivas integradas de Azure Machine Learning](policy-reference.md)
* [Uso de directivas de seguridad con Azure Security Center](../security-center/tutorial-security-policy.md)