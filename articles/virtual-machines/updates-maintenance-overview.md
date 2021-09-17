---
title: Información general de actualizaciones y mantenimiento
description: Más información sobre las actualizaciones y las opciones de mantenimiento disponibles con máquinas virtuales en Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/10/2021
ms.reviewer: cynthn
ms.openlocfilehash: 5e2d200e7133074ffc1dc2732cdedb692c1ea30c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698334"
---
# <a name="updates-and-maintenance-overview"></a>Información general de actualizaciones y mantenimiento

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

En este artículo se proporciona información general de las distintas opciones de actualización y mantenimiento para las máquinas virtuales (VM) de Azure.

## <a name="automatic-os-image-upgrade"></a>Actualización automática de las imágenes del sistema operativo

La habilitación de las [actualizaciones automáticas de imágenes del sistema operativo](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) en el conjunto de escalado facilita la administración de actualizaciones al actualizarse de forma segura y automática el disco del sistema operativo de todas las instancias del conjunto de escalado.

La [actualización automática del sistema operativo](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) tiene las siguientes características:

- Una vez configurada, la imagen del sistema operativo más reciente publicada por los editores de la imagen se aplica automáticamente al conjunto de escalado sin la intervención del usuario.
- Actualiza lotes de instancias de forma gradual cada vez que el editor publica una nueva imagen.
- Se integra con sondeos de estado de la aplicación y la [extensión Estado de la aplicación](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context).
- Funciona con todos los tamaños de máquina virtual y puede usarse con imágenes de Windows y Linux.
- Puede rechazar las actualizaciones automáticas en cualquier momento (las actualizaciones de sistema operativo también se pueden iniciar manualmente).
- El disco del sistema operativo de una máquina virtual se reemplaza por el nuevo disco de sistema operativo creado con la versión más reciente de la imagen. Las extensiones configuradas y los scripts de datos personalizados se ejecutan, mientras se conservan los discos de datos persistentes.
- Se admite la [secuenciación de extensiones](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context).
- La actualización automática de la imagen del sistema operativo se puede habilitar en un conjunto de escalado de cualquier tamaño.


## <a name="automatic-vm-guest-patching-preview"></a>Aplicación de revisiones automáticas a los invitados de máquinas virtuales (versión preliminar)

La habilitación de la [aplicación de revisiones automática](automatic-vm-guest-patching.md) a invitados de máquinas virtuales de Azure facilita la administración de las actualizaciones mediante la aplicación segura y automática de revisiones a máquinas virtuales para mantener el cumplimiento de la seguridad.

La [aplicación de revisiones automáticas](automatic-vm-guest-patching.md) a invitados de máquinas virtuales tiene las siguientes características:
- Las revisiones clasificadas de tipo *Critico* o de *Seguridad* se descargan y se aplican automáticamente en la máquina virtual.
- Las revisiones se aplican durante las horas valle de la zona horaria de la máquina virtual.
- Azure administra la orquestación de las revisiones y estas se aplican siguiendo el [principio de orden de disponibilidad](automatic-vm-guest-patching.md#availability-first-updates).
- Se supervisa el mantenimiento de la máquina virtual, determinado a través de las señales de mantenimiento de la plataforma, para detectar errores en la aplicación de revisiones.
- Funciona con todos los tamaños de máquina virtual.


## <a name="automatic-extension-upgrade"></a>Actualización automática de extensiones

La [actualización automática de extensiones](automatic-extension-upgrade.md) está disponible para las máquinas virtuales de Azure y Azure Virtual Machine Scale Sets. Cuando la actualización automática de extensiones está habilitada en una máquina virtual o en un conjunto de escalado, la extensión se actualiza automáticamente cada vez que el editor de la extensión publica una nueva versión de esta.

 La actualización automática de extensiones tiene las siguientes características:
- Es compatible con máquinas virtuales de Azure y Azure Virtual Machine Scale Sets.
- Las actualizaciones se aplican según un modelo de implementación de orden de disponibilidad.
- En el caso de un conjunto de escalado de máquinas virtuales, no se actualizará más del 20 % de las máquinas virtuales del conjunto de escalado en un único lote. El tamaño mínimo de lote es una única máquina virtual.
- Funciona con todos los tamaños de máquina virtual y con extensiones de Windows y Linux.
- Puede optar a las actualizaciones automáticas en cualquier momento.
- La actualización automática de extensiones se puede habilitar en una instancia de Virtual Machine Scale Sets de cualquier tamaño.
- Cada extensión admitida se inscribe individualmente, y puede elegir qué extensiones se van a actualizar automáticamente.
- Se admite en todas las regiones de la nube pública.

## <a name="hotpatch"></a>Revisiones en caliente

La aplicación de [revisiones en caliente](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context) es una nueva manera de instalar actualizaciones en nuevas máquinas virtuales (VM) de Windows Server Azure Edition que no es necesario reiniciar después de la instalación. La revisión en caliente para máquinas virtuales de Windows Server Azure Edition presenta las siguientes ventajas:

- Impacto reducido sobre la carga de trabajo con menos reinicios
- Implementación más rápida de actualizaciones ya que los paquetes son más pequeños, se instalan más rápido y tienen una orquestación de revisiones más sencilla con Azure Update Manager
- Mejor protección, ya que los paquetes de actualización de Hotpatch se limitan a las actualizaciones de seguridad de Windows que se instalan más rápido sin necesidad de reiniciar.


## <a name="azure-update-management"></a>Azure Update Management

Puede usar [Update Management en Azure Automation](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context) para administrar las actualizaciones del sistema operativo de las máquinas virtuales Windows y Linux en Azure, en entornos locales y en otros entornos en la nube. Puede evaluar rápidamente el estado de las actualizaciones disponibles en todas las máquinas agente y administrar el proceso de instalación de las actualizaciones necesarias para los servidores.

## <a name="maintenance-control"></a>Control de mantenimiento

Administre las actualizaciones de la plataforma, que no requieren un reinicio, mediante el [control de mantenimiento](maintenance-control.md). Azure actualiza con frecuencia su infraestructura para mejorar la confiabilidad, el rendimiento y la seguridad o para poner en marcha nuevas características. La mayoría de las actualizaciones son transparentes para los usuarios. Algunas cargas de trabajo especialmente delicadas, como los juegos, el streaming multimedia o las transacciones financieras, no pueden tolerar ni siquiera unos segundos de bloqueo o desconexión por mantenimiento de una máquina virtual. El control de mantenimiento ofrece la opción de poner en espera las actualizaciones de la plataforma y aplicarlas en un período sucesivo de 35 días.

El control de mantenimiento le permite decidir cuándo se aplican las actualizaciones a las máquinas virtuales aisladas y los hosts dedicados de Azure.

Con el [control de mantenimiento](maintenance-control.md), puede:
- Aplicar actualizaciones por lotes en un único paquete de actualización.
- Esperar hasta 35 días para aplicar las actualizaciones.
- Para automatizar las actualizaciones de plataforma configure una programación de mantenimiento o use [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- Mantener el trabajo de las configuraciones entre suscripciones y grupos de recursos.


## <a name="scheduled-events"></a>Eventos programados

Scheduled Events es un servicio de Azure Metadata Service que proporciona el tiempo de aplicación para prepararse para el mantenimiento de la máquina virtual. Da información sobre los eventos de mantenimiento próximos (por ejemplo, un reinicio) para que la aplicación pueda prepararlos y así limitar las interrupciones. Está disponible para todos los tipos de máquina virtual de Azure, incluidos IaaS y PaaS, tanto en Windows como en Linux.

Para más información sobre Scheduled Events, consulte [Scheduled Events para máquinas virtuales Windows](./windows/scheduled-events.md) y [Scheduled Events para máquinas virtuales Linux](./linux/scheduled-events.md).

## <a name="next-steps"></a>Pasos siguientes

Consulte la documentación sobre [disponibilidad y escalado](availability.md) para ver más formas de aumentar el tiempo de actividad de las aplicaciones y los servicios.
