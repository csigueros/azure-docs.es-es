---
title: Permisos y seguridad de Azure Chaos Studio
description: Comprenda el funcionamiento de los permisos en Azure Chaos Studio y cómo puede proteger los recursos de la inserción de errores accidental.
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: fa547771b125b17d05e6a615f01cecc899cba7e0
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372215"
---
# <a name="permissions-and-security-in-azure-chaos-studio"></a>Permisos y seguridad en Azure Chaos Studio

Azure Chaos Studio permite mejorar la resistencia del servicio mediante la inserción sistemática de errores en los recursos de Azure. La inserción de errores es una forma eficaz de mejorar la resistencia del servicio, pero también puede resultar peligrosa. Causar errores en la aplicación puede tener un mayor impacto de lo previsto originalmente y dar a actores malintencionados la oportunidad de infiltrarse. Chaos Studio cuenta con modelo de permisos sólido que impide que los errores se ejecuten de forma involuntaria o por un actor malintencionado. En este artículo, aprenderá a proteger los recursos destinados a la inserción de errores mediante Chaos Studio.

## <a name="how-can-i-restrict-the-ability-to-inject-faults-with-chaos-studio"></a>¿Cómo puedo restringir la capacidad de insertar errores con Chaos Studio?

Chaos Studio tiene tres niveles de seguridad que le ayudan a controlar cómo y cuándo se puede producir la inserción de errores en un recurso.

En primer lugar, un experimento de caos es un recurso de Azure implementado en una región, un grupo de recursos y una suscripción. Los usuarios deben disponer de los permisos de Azure Resource Manager adecuados para crear, actualizar, iniciar, cancelar, eliminar o consultar un experimento. Cada permiso es una operación de ARM que se puede asignar granularmente a una identidad o asignarse como parte de un rol con permisos comodín. Por ejemplo, el rol Colaborador de Azure tiene el permiso */write en el ámbito asignado, que incluirá el permiso Microsoft.Chaos/experiments/write. Al intentar controlar la capacidad de insertar errores en un recurso, la operación más importante para restringir es Microsoft.Chaos/experiments/start/action, ya que inicia un experimento de caos que insertará errores.

En segundo lugar, un experimento de caos tiene una [identidad administrada asignada por el sistema](../active-directory/managed-identities-azure-resources/overview.md) que ejecuta errores en un recurso. Al crear un experimento, la identidad administrada asignada por el sistema se crea en el inquilino de Azure Active Directory sin permisos. Antes de ejecutar el experimento de caos, debe conceder a su identidad los [permisos adecuados](chaos-studio-fault-providers.md) para todos los recursos de destino. Si la identidad del experimento no tiene el permiso adecuado para un recurso, no podrá ejecutar un error en ese recurso.

Por último, cada recurso debe incorporarse a Chaos Studio como [destino con las funcionalidades correspondientes habilitadas](chaos-studio-targets-capabilities.md). Si un destino o la funcionalidad del error que se ejecuta no existe, se produce un error en el experimento sin afectar al recurso.

## <a name="agent-authentication"></a>Autenticación del agente

Si desea ejecutar errores basados en agente, debe instalar el agente de Chaos Studio en la máquina virtual o el conjunto de escalado de máquinas virtuales. El agente usa una [identidad administrada asignada por el usuario](../active-directory/managed-identities-azure-resources/overview.md) para autenticarse en Chaos Studio y un *perfil de agente* para establecer una relación con un recurso de VM específico. Si desea incorporar una máquina virtual o un conjunto de escalado de máquinas virtuales para errores basados en agente, primero debe crear un destino de agente. El destino del agente debe tener una referencia a la identidad administrada asignada por el usuario que se usará para la autenticación. El destino del agente contiene un *id. de perfil de agente*, que se proporciona como configuración al instalar el agente. Los perfiles de agente son únicos para cada destino, mientras que los destinos son únicos para recurso.

## <a name="arm-operations-and-roles"></a>Operaciones y roles de ARM

Chaos Studio permite realizar las operaciones siguientes:

| Operación | Descripción |
| -- | -- |
| Microsoft.Chaos/targets/[Read,Write,Delete] | Obtenga, cree, actualice o elimine un destino. |
| Microsoft.Chaos/targets/capabilities/[Read,Write,Delete] | Obtenga, cree, actualice o elimine una capacidad. |
| Microsoft.Chaos/locations/targetTypes/Read | Obtenga todos los tipos de destino. |
| Microsoft.Chaos/locations/targetTypes/capabilityTypes/Read | Obtenga todos los tipos de capacidad. |
| Microsoft.Chaos/experiments/[Read,Write,Delete] | Obtenga, cree, actualice o elimine un experimento de caos. |
| Microsoft.Chaos/experiments/start/action | Inicie un experimento de caos. |
| Microsoft.Chaos/experiments/cancel/action | Detenga un experimento de caos. |
| Microsoft.Chaos/experiments/statuses/Read | Obtenga el estado de una ejecución de un experimento de caos. |
| Microsoft.Chaos/experiments/executionDetails/Read | Obtenga los detalles (estado y errores de cada acción) de una ejecución de un experimento de caos. |

Para asignar estos permisos de forma granular, puede [crear un rol personalizado](../role-based-access-control/custom-roles.md).

## <a name="network-security"></a>Seguridad de las redes

Todas las interacciones del usuario con Chaos Studio tienen lugar en Azure Resource Manager. Si un usuario inicia un experimento, este puede interactuar con puntos de conexión distintos a Resource Manager en función del error.
* Errores directos del servicio: la mayoría de los errores directos del servicio se ejecutan a través de Azure Resource Manager. Los recursos de destino no requieren ningún punto de conexión de red permitido.
* Errores de Chaos Mesh de AKS directos del servicio: los errores directos del servicio de Azure Kubernetes Service que usan Chaos Mesh requieren que el clúster de AKS tenga un servidor de API de Kubernetes expuesto públicamente. [Puede aprender a limitar el acceso de red de AKS a un conjunto de intervalos IP aquí.](../aks/api-server-authorized-ip-ranges.md)
* Errores basados en agente: los errores basados en agente requieren que el agente pueda acceder al servicio del agente de Chaos Studio. Una máquina virtual o un conjunto de escalado de máquinas virtuales debe tener acceso de salida a http://agentcommunicationservice-frontdoor-canary.trafficmanager.net para que el agente se conecte correctamente.

Azure Chaos Studio no admite etiquetas de servicio ni Private Link.

## <a name="data-encryption"></a>Cifrado de datos

Chaos Studio cifra todos los datos de manera predeterminada. Chaos Studio solo acepta entradas de las propiedades del sistema, como los id. de objeto de identidad administrada; los nombres de experimento, paso o rama; y los parámetros de error (por ejemplo, el intervalo de puertos de red para bloquear en un error de desconexión de red). Estas propiedades no deben usarse para almacenar datos confidenciales, como información de pago o contraseñas. Para obtener más información sobre cómo Chaos Studio protege los datos, consulte el artículo [Protección de datos de cliente de Azure](../security/fundamentals/protection-customer-data.md).

## <a name="next-steps"></a>Pasos siguientes
Ahora que sabe cómo proteger el experimento de caos, puede:
- [Crear y ejecutar tu primer experimento](chaos-studio-tutorial-service-direct-portal.md)
- [Crear y ejecutar su primer experimento de Azure Kubernetes Service](chaos-studio-tutorial-aks-portal.md)
