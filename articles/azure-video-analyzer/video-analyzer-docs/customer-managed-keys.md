---
title: 'Claves administradas por el cliente: Azure Video Analyzer'
description: Puede usar una clave administrada por el cliente (es decir, Bring Your Own Key) con Azure Video Analyzer.
author: anilmur
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: d24ddd090f9acffc3dd19643d4004dbb58fc344d
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114603853"
---
# <a name="customer-managed-keys-with-azure-video-analyzer"></a>Claves administradas por el cliente con Azure Video Analyzer

Bring Your Own Key (BYOK) es una iniciativa de Azure para ayudar a los clientes a trasladar sus cargas de trabajo a la nube. Las claves administradas por el cliente permiten a este cumplir las normas del sector y mejorar el aislamiento de los inquilinos de un servicio. Conceder a los clientes el control de las claves de cifrado es una manera de minimizar el acceso y el control innecesarios y construir confianza en los servicios de Microsoft.

## <a name="keys-and-key-management"></a>Claves y administración de claves

Se crea una clave de cuenta para todas las cuentas de Video Analyzer. De forma predeterminada, esta clave de cuenta se cifra mediante una clave del sistema propiedad de Video Analyzer (es decir, una clave administrada por el sistema). En su lugar, puede usar su propia clave con Azure Video Analyzer. En ese caso, la clave de cuenta se cifra con la clave. Las directivas de acceso y los metadatos de recursos de vídeo se cifran mediante la clave de cuenta.

Video Analyzer usa una identidad administrada asignada por el usuario para leer la clave de una instancia de Key Vault de su propiedad. Debe proporcionar la identidad administrada asignada por el usuario al crear o actualizar la cuenta de Video Analyzer y asignar el [control de acceso basado en rol de Azure]../../role-based-access-control/overview.md) a la instancia de Key Vault. Video Analyzer requiere que el almacén Key Vault esté en la misma región que la cuenta y que tenga habilitada la eliminación temporal y la protección de purgas.

La clave puede ser una clave RSA 2048, 3072 o 4096, y se admiten las claves de software y HSM.

> [!NOTE]
> No se admiten las claves de EC.

Puede especificar un nombre de clave y una versión de clave, o simplemente un nombre de clave. Cuando use solo un nombre de clave, Video Analyzer usará la versión más reciente de la clave. Las nuevas versiones de las claves de cliente se detectan automáticamente, y la clave de cuenta se vuelve a cifrar.

> [!WARNING]
> Video Analyzer supervisa el acceso a la clave de cliente. Si la clave de cliente deja de estar accesible (por ejemplo, se ha eliminado la clave, se ha eliminado el almacén Key Vault o se ha quitado la concesión de acceso), Video Analyzer pasará la cuenta al estado inaccesible de la clave de cliente (con lo que se deshabilitará la cuenta). Puede eliminar la cuenta o restaurar la clave de cuenta para reanudar el acceso.

## <a name="double-encryption"></a>Doble cifrado

Video Analyzer protege la información confidencial mediante el cifrado doble según el procedimiento habitual de Azure, consulte [Cifrado doble de Azure](../../security/fundamentals/double-encryption.md). En el caso de los datos en reposo, la primera capa de cifrado utiliza una clave administrada por el cliente o una clave administrada por Microsoft, en función del valor de `encryption` de la cuenta. La segunda capa de cifrado de datos en reposo se proporciona automáticamente mediante una clave administrada de Microsoft independiente.

> [!NOTE]
> El cifrado doble se habilita automáticamente en la cuenta de Video Analyzer. Sin embargo, la clave administrada por el cliente y el cifrado doble en la cuenta de almacenamiento se deben configurar por separado. Consulte [Cifrado de almacenamiento](../../storage/common/storage-service-encryption.md).


## <a name="next-steps"></a>Pasos siguientes

Revise [Identidad administrada](managed-identity.md).
