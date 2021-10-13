---
title: Información general sobre el control del mantenimiento de máquinas virtuales de Azure mediante Azure Portal
description: Obtenga información sobre cómo controlar cuándo se aplica mantenimiento a las máquinas virtuales de Azure mediante el control de mantenimiento.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 47d99e87e788c833e5793a1e4e3b7d517d90909b
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535001"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Administración de las actualizaciones de la plataforma con el control de mantenimiento 

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Administre las actualizaciones de la plataforma, que no requieren un reinicio, mediante el control de mantenimiento. Azure actualiza con frecuencia su infraestructura para mejorar la confiabilidad, el rendimiento y la seguridad o para poner en marcha nuevas características. La mayoría de las actualizaciones son transparentes para los usuarios. Algunas cargas de trabajo especialmente delicadas, como los juegos, el streaming multimedia o las transacciones financieras, no pueden tolerar ni siquiera unos segundos de bloqueo o desconexión por mantenimiento de una máquina virtual. El control de mantenimiento ofrece la opción de poner en espera las actualizaciones de la plataforma y aplicarlas en un período sucesivo de 35 días. 

El control de mantenimiento le permite decidir cuándo se aplican las actualizaciones a las máquinas virtuales aisladas y los hosts dedicados de Azure.

Con el control de mantenimiento, puede:
- Aplicar actualizaciones por lotes en un único paquete de actualización.
- Esperar hasta 35 días para aplicar las actualizaciones. 
- Para automatizar las actualizaciones de plataforma configure una programación de mantenimiento.
- Mantener el trabajo de las configuraciones entre suscripciones y grupos de recursos. 

## <a name="limitations"></a>Limitaciones

- Las máquinas virtuales deben estar en un [host dedicado](./dedicated-hosts.md) o bien crearse con un [tamaño de máquina virtual aislada](isolation.md).
- La duración de la ventana de mantenimiento puede variar mes a mes y, a veces, puede tardar hasta dos horas en aplicar las actualizaciones pendientes una vez que la inicia el usuario.  
- Después de 35 días, se aplicará automáticamente una actualización.
- Las actividades de mantenimiento de nivel de bastidor aún no forman parte de este control de mantenimiento.
- El usuario debe tener acceso de **colaborador del recurso**.

## <a name="management-options"></a>Opciones de administración

Puede crear y administrar configuraciones de mantenimiento con cualquiera de estas opciones:

- [CLI de Azure](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure Portal](maintenance-control-portal.md)

En el artículo sobre la [programación de actualizaciones de mantenimiento con control de mantenimiento y Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler) encontrará un ejemplo de Azure Functions.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte [Mantenimiento y actualizaciones](maintenance-and-updates.md).
