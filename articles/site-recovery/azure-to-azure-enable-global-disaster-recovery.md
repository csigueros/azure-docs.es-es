---
title: Habilitación de la recuperación ante desastres en regiones de Azure en todo el mundo
description: En este artículo se describe la característica de recuperación ante desastres global en Azure Site Recovery.
author: JYOTHIRMAISURI
manager: evansma
ms.service: site-recovery
ms.topic: article
ms.date: 08/09/2021
ms.author: v-jysur
ms.openlocfilehash: 1179558e01ff23c66db652395e10c7d6259af09b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780684"
---
# <a name="enable-global-disaster-recovery-using-azure-site-recovery"></a>Habilitación de la recuperación ante desastres global mediante Azure Site Recovery

En este artículo se describe cómo replicar, conmutar por error y conmutar por recuperación máquinas virtuales de Azure de una zona de disponibilidad a otra dentro de la misma región de Azure o en una diferente.

>[!NOTE]
> Azure Site Recovery no mueve ni almacena datos fuera de la región de origen y de destino que elija. Puede seleccionar un almacén de Recovery Services de otra región (una tercera). El almacén de Recovery Services contiene metadatos, pero ningún dato real de los clientes.

El servicio Azure Site Recovery contribuye a la estrategia de continuidad empresarial y recuperación ante desastres al mantener sus aplicaciones empresariales al día y disponibles durante interrupciones planeadas y no planeadas. Es la opción recomendada de recuperación ante desastres para mantener sus aplicaciones en funcionamiento si hay interrupciones regionales.

## <a name="disaster-recovery-for-global-azure-regions"></a>Recuperación ante desastres para regiones globales de Azure

Azure fue el primer proveedor de nube pública de gran tamaño en lanzar una solución de recuperación ante desastres nativa en la nube de primera clase con la recuperación ante desastres de Azure a Azure. Esta oferta le permitía replicar y conmutar por error las aplicaciones de una región de Azure a otra, en el mismo continente, admitiendo también la recuperación ante desastres de zona a zona.   

Azure Site Recovery admite ahora la recuperación ante desastres global. Ya puede replicar y conmutar por error las aplicaciones desde cualquier región de Azure, entre continentes.

Muchos de los usuarios dirigen empresas globales y desean hospedar y replicar sus aplicaciones en todo el mundo aprovechando la presencia global de Azure. Aquí es donde puede recurrir a la recuperación ante desastres global mediante Azure Site Recovery. Esta nueva oferta suprime los límites continentales de la recuperación ante desastres de Azure a Azure. Gracias a nuestra eficaz red troncal global, puede replicar y conmutar por error las aplicaciones en cualquier región de Azure de su elección, en todo el mundo.  

Esta oferta completa la cartera de recuperación ante desastres de nube pública nativa de recuperación ante desastres de zona a zona, recuperación ante desastres dentro del continente y recuperación ante desastres global.

>[!NOTE]
>La elección de una región de recuperación ante desastres en un continente lejano tiene un impacto menor en el objetivo de punto de recuperación (RPO), pero no es lo suficientemente importante como para afectar a ninguno de los Acuerdos de Nivel de Servicio de Azure Site Recovery.

Esta oferta completa la cartera de recuperación ante desastres de nube pública nativa de recuperación ante desastres de zona a zona, recuperación ante desastres dentro del continente y recuperación ante desastres global.  

## <a name="before-you-begin"></a>Antes de empezar
En este artículo se da por supuesto que está preparado para la implementación de Azure Site Recovery, como se describe en [Tutorial de recuperación ante desastres de Azure en Azure](azure-to-azure-tutorial-enable-replication.md).

Asegúrese de que se cumplen los requisitos previos y de que ha creado un almacén de Recovery Services.

>[!NOTE]
> Vea la [matriz de soporte](azure-to-azure-support-matrix.md) antes de habilitar la replicación en sus máquinas virtuales.

## <a name="supported-platform-features"></a>Características admitidas de la plataforma

| **Característica** | **Declaración de compatibilidad** |
| --- | --- |
| Máquinas virtuales clásicas | No |
| VM de ARM |  Sí |
| Azure Disk Encryption v1 (pase doble, con Azure Active Directory [Azure AD]) |  Sí |
| Azure Disk Encryption v2 (pase único, sin Azure AD) |  Sí |
|     |  No |
| Discos administrados |  Sí |
| Claves administradas por el cliente |   Sí |
| Grupos de selección de ubicación de proximidad |  Sí |
| Interoperabilidad de copias de seguridad | Se admiten la copia de seguridad y restauración a nivel de archivo. No se admiten la copia de seguridad y restauración a nivel de disco y VM. |
| Adición o eliminación en caliente | Se pueden agregar discos después de habilitar la replicación de zona a zona. No se admite la eliminación de discos después de habilitar la replicación de zona a zona. |

## <a name="enable-replication"></a>Habilitación de la replicación

Siga estos pasos para replicar rápidamente los dispositivos:

1. En el portal, vaya a la máquina virtual.

2. En el panel de la parte izquierda, en **Operaciones**, seleccione **Recuperación ante desastres**.

3. En **Aspectos básicos**, seleccione la **Región de destino**.

   Todas las regiones de Azure disponibles en todo el mundo se muestran en el menú desplegable. Elija la región de destino según sus preferencias.  

   ![Configuración de la recuperación ante desastres global](./media/azure-to-azure-enable-global-disaster-recovery/enable-global-disaster-recovery.png)

   Puede realizar otros cambios de configuración según sea necesario.

4. Haga clic en **Configuración avanzada** para otros ajustes como **Suscripción de destino**, **Red virtual de destino**, **Disponibilidad de destino**, **Grupo con ubicación de proximidad de destino**.

5. Haga clic en **Revisar e iniciar replicación**.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [recuperación ante desastres de zona a zona](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).
- Obtenga más información sobre la [Arquitectura de recuperación ante desastres de Azure a Azure](azure-to-azure-architecture.md).
