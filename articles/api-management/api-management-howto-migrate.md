---
title: Migración de Azure API Management entre regiones
description: Aprenda a migrar una instancia de API Management de una región a otra.
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 08/20/2021
ms.author: danlep
ms.custom: subject-moving-resources
ms.openlocfilehash: 1007241c09e22d1fb35f6cc12733c04946af88f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591793"
---
# <a name="how-to-move-azure-api-management-across-regions"></a>Migración de Azure API Management entre regiones

En este artículo se explica cómo migrar una instancia de API Management a otra región de Azure. Una instancia se puede migrar a otra región por muchas razones. Por ejemplo:

* Buscar la instancia más cercana a los consumidores de API
* Implementar características disponibles solo en regiones determinadas
* Cumplir requisitos internos de gobernanza y directivas

Para migrar instancias de API Management de una región de Azure a otra, use las operaciones de [copia de seguridad y restauración](api-management-howto-disaster-recovery-backup-restore.md) del servicio. Puede usar un nombre de instancia de API Management diferente o el existente. 

> [!NOTE]
> API Management además admite la [implementación en varias regiones](api-management-howto-deploy-multi-region.md), que distribuye un único servicio de Azure API Management entre varias regiones de Azure. La implementación en varias regiones ayuda a reducir la latencia de las solicitudes que perciben los consumidores de API distribuidos geográficamente, y mejora la disponibilidad del servicio en caso de que una región se quede sin conexión.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="considerations"></a>Consideraciones

* Elija el mismo plan de tarifa de API Management en las regiones de origen y de destino. 
* La copia de seguridad y la restauración no funcionan al migrar entre distintos tipos de nube. En ese caso, exporte el recurso [como una plantilla](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Después, adapte la plantilla exportada a la región de Azure de destino y vuelva a crear el recurso. 

## <a name="prerequisites"></a>Requisitos previos

* Revise los requisitos y las limitaciones de las operaciones de [copia de seguridad y restauración](api-management-howto-disaster-recovery-backup-restore.md) de API Management. 
* Vea [De lo que no se hace una copia de seguridad](api-management-howto-disaster-recovery-backup-restore.md#what-is-not-backed-up). Registre la configuración y los datos que va a tener que volver a crear manualmente después de migrar la instancia.
* Cree una [cuenta de almacenamiento](../storage/common/storage-account-create.md?tabs=azure-portal) en la región de origen. La va a usar para hacer una copia de seguridad de la instancia de origen. 

## <a name="prepare-and-move"></a>Preparación y traslado

### <a name="option-1-use-a-different-api-management-instance-name"></a>Opción 1: Usar un nombre de instancia de API Management diferente

1. En la región de destino, cree una nueva instancia de API Management con el mismo plan de tarifa que la instancia de API Management de origen. Use otro nombre para la nueva instancia.
1. [Realice una copia de seguridad](api-management-howto-disaster-recovery-backup-restore.md#-back-up-an-api-management-service) de la instancia de API Management existente en la cuenta de almacenamiento. 
1. [Restaure](api-management-howto-disaster-recovery-backup-restore.md#-restore-an-api-management-service) la copia de seguridad de la instancia de origen en la nueva instancia de API Management.
1. Si tiene un dominio personalizado que apunta a la instancia de API Management de la región de origen, actualice el CNAME del dominio personalizado para que apunte a la nueva instancia de API Management. 

### <a name="option-2-use-the-same-api-management-instance-name"></a>Opción 2: Usar el mismo nombre para la instancia de API Management

> [!WARNING]
> Esta opción elimina la instancia de API Management original y genera tiempo de inactividad durante la migración. Asegúrese de que tiene una copia de seguridad válida antes de eliminar la instancia de origen.

1. [Realice una copia de seguridad](api-management-howto-disaster-recovery-backup-restore.md#-back-up-an-api-management-service) de la instancia de API Management existente en la cuenta de almacenamiento. 
1. Elimine la instancia de API Management en la región de origen. 
1. Cree una nueva instancia de API Management en la región de destino con el mismo nombre que el de la región de origen.
1. [Restaure](api-management-howto-disaster-recovery-backup-restore.md#-restore-an-api-management-service) la copia de seguridad de la instancia de origen en la nueva instancia de API Management de la región de destino.  

## <a name="verify"></a>Comprobación

1. Asegúrese de que la operación de restauración se completa correctamente antes de acceder a la instancia de API Management en la región de destino.
1. Configure las opciones que no se migren automáticamente durante la operación de restauración. Ejemplos: configuración de red virtual, identidades administradas, contenido del portal para desarrolladores, certificados de dominio personalizados y entidades de certificación personalizadas.
1. Acceda a los puntos de conexión de API Management en la región de destino. Por ejemplo, pruebe las API o acceda al portal para desarrolladores.

## <a name="clean-up-source-resources"></a>Limpieza de los recursos de origen

Si ha migrado la instancia de API Management mediante la opción 1, después de restaurar y configurar correctamente la instancia de destino, puede eliminar la instancia de origen.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre la característica de copia de seguridad y restauración, consulte [Implementación de la recuperación ante desastres](api-management-howto-disaster-recovery-backup-restore.md).
* Para obtener más información sobre la migración de recursos de Azure, vea la [guía para la migración entre regiones de Azure](https://github.com/Azure/Azure-Migration-Guidance).
* [Optimice y ahorre en el gasto en la nube](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
