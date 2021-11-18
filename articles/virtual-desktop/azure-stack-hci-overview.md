---
title: 'Azure Virtual Desktop para Azure Stack HCI (versión preliminar): Azure'
description: Una breve introducción a Azure Virtual Desktop para Azure Stack HCI (versión preliminar).
author: Heidilohr
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: helohr
manager: femila
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5ba286a6023927d2c4d4821fb631a0df0b326b6c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704048"
---
# <a name="azure-virtual-desktop-for-azure-stack-hci-preview"></a>Azure Virtual Desktop para Azure Stack HCI (versión preliminar)

> [!IMPORTANT]
> Azure Virtual Desktop para Azure Stack HCI está actualmente en versión preliminar.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

Azure Virtual Desktop para Azure Stack HCI (versión preliminar) permite implementar hosts de sesión de Azure Virtual Desktop en la infraestructura local de Azure Stack HCI. También puede usar Azure Virtual Desktop para Azure Stack HCI a fin de administrar los hosts de sesión desde Azure Portal. Si ya tiene una implementación de Infraestructura del escritorio virtual (VDI) local existente, Azure Virtual Desktop para Azure Stack HCI puede mejorar la experiencia del administrador y el usuario final. Si ya usa Azure Virtual Desktop en la nube, puede ampliar la implementación a la infraestructura local para satisfacer mejor las necesidades de rendimiento o localidad de datos.

Azure Virtual Desktop para Azure Stack HCI está actualmente en versión preliminar pública. Azure Stack HCI no admite actualmente determinadas características importantes de Azure Virtual Desktop. Debido a estas limitaciones, aún no se recomienda usar esta característica para cargas de trabajo de producción.

## <a name="key-benefits"></a>Ventajas principales

Se ha establecido qué es Azure Virtual Desktop para Azure Stack HCI. La pregunta sigue siendo: ¿qué puede hacer por usted?

Con Azure Virtual Desktop para Azure Stack HCI puede:

- Mejorar el rendimiento de los usuarios de Azure Virtual Desktop en áreas con conectividad deficiente a la nube pública de Azure al proporcionarles hosts de sesión más cerca de su ubicación.

- Cumplir los requisitos de localidad de los datos manteniendo los datos de la aplicación y del usuario en el entorno local.  Para más información, vea [Ubicaciones de datos para Azure Virtual Desktop](data-locations.md).

- Mejorar el acceso a las aplicaciones y orígenes de datos locales heredados manteniendo los escritorios virtuales y las aplicaciones en la misma ubicación.

- Reducir los costos y mejorar la experiencia del usuario con escritorios virtuales de varias sesiones de Windows 10 y Windows 11 Enterprise.

- Simplificar la implementación y administración de VDI en comparación con las soluciones de VDI locales tradicionales mediante Azure Portal.

## <a name="pricing"></a>Precios

Lo siguiente afecta a cuánto cuesta ejecutar Azure Virtual Desktop para Azure Stack HCI:
 - Costos de infraestructura. Para Azure Stack HCI pagará tarifas de servicio mensuales. Obtenga más información en [Precios de Azure Stack HCI](https://azure.microsoft.com/pricing/details/azure-stack/hci/).
 
- Derechos de acceso de usuario para Azure Virtual Desktop. Las mismas licencias que conceden acceso a Azure Virtual Desktop en la nube también se aplican a Azure Virtual Desktop para Azure Stack HCI. Obtenga más información en [Precios de Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).

- La tarifa del servicio híbrido de Azure Virtual Desktop. Con esta tarifa tendrá que pagar por cada CPU virtual (vCPU) activa de los hosts de sesión de Azure Virtual Desktop que se ejecutan en Azure Stack HCI. Esta tarifa se activará una vez que finalice el período de versión preliminar.

## <a name="known-issues-and-limitations"></a>Limitaciones y problemas conocidos

Somos conscientes de los siguientes problemas que afectan a la versión preliminar pública de Azure Virtual Desktop para Azure Stack HCI:

- Los grupo de hosts de Azure Stack HCI no admiten actualmente las siguientes características de Azure Virtual Desktop:
    
    - [Azure Monitor para Azure Virtual Desktop](azure-monitor.md)
    - [Escalado del host de sesión con Azure Automation](set-up-scaling-script.md)
    - [Escalabilidad automática (versión preliminar)](autoscale-scaling-plan.md)
    - [Inicio de la máquina virtual al conectar](start-virtual-machine-connect.md)
    - [Redireccionamiento multimedia (versión preliminar)](multimedia-redirection.md)
    - [Precios de acceso por usuario](./remote-app-streaming/licensing.md)

- La pestaña Azure Virtual Desktop de Azure Portal no puede crear máquinas virtuales directamente en la infraestructura de Azure Stack HCI. En su lugar, los administradores deben crear máquinas virtuales locales por separado y, después, registrarlas con un grupo de hosts de Azure Virtual Desktop.

- Al conectarse a un escritorio virtual multisesión de Windows 10 o 11 Enterprise, los usuarios pueden ver problemas de activación, como una marca de agua de escritorio que indica "Activar Windows", aunque tengan una licencia válida.

- Actualmente Azure Virtual Desktop para Azure Stack HCI no admite grupos de hosts que contengan hosts de sesión locales y en la nube. Cada grupo de hosts de la implementación solo debe tener un tipo de grupo de hosts.

- Los hosts de sesión de Azure Stack HCI no admiten determinados servicios de Azure exclusivos para la nube.

- Como Azure Stack HCI admite tantos tipos de hardware y funcionalidades de red locales, el rendimiento y la densidad de usuarios pueden variar ampliamente entre los hosts de sesión que se ejecutan en la nube de Azure. Las [instrucciones de dimensionamiento de máquinas virtuales](/windows-server/remote/remote-desktop-services/virtual-machine-recs) de Azure Virtual Desktop son amplias, por lo que solo debe usarlas para las estimaciones de rendimiento iniciales.

Si durante la versión preliminar detecta algún problema que no se encuentre en esta lista, le animamos a que lo notifique.

## <a name="next-steps"></a>Pasos siguientes

Ahora que está familiarizado con Azure Virtual Desktop para Azure Stack HCI, aprenda a implementar esta característica en [Configuración de Azure Virtual Desktop para Azure Stack HCI (versión preliminar)](azure-stack-hci.md).
