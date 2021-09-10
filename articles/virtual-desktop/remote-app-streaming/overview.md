---
title: ¿Qué es el streaming de aplicaciones remotas de Azure Virtual Desktop? - Azure
description: Información general del streaming de aplicaciones remotas de Azure Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: e1ff4cba43c1e57c39b5e47e2485a262f66bbb5f
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123032987"
---
# <a name="what-is-azure-virtual-desktop-remote-app-streaming"></a>¿Qué es el streaming de aplicaciones remotas de Azure Virtual Desktop?

Azure Virtual Desktop es un servicio de virtualización de aplicaciones y escritorio que se ejecuta en la nube y le permite acceder al escritorio remoto en cualquier momento y en cualquier lugar. Sin embargo, ¿sabía que también puede usar Azure Virtual Desktop como plataforma como servicio (PaaS) para proporcionar las aplicaciones de su organización como software como servicio (SaaS) a sus clientes? Con el streaming de aplicaciones remotas de Azure Virtual Desktop, ahora puede usar Azure Virtual Desktop para entregar aplicaciones a los clientes a través de una red segura a través de máquinas virtuales.

Si no está familiarizado con Azure Virtual Desktop (o no está familiarizado con la virtualización de aplicaciones en general), hemos recopilado aquí algunos recursos que pueden ayudarle a hacer que la implementación esté en funcionamiento.

## <a name="requirements"></a>Requisitos

Antes de empezar, se recomienda echar un vistazo a la [información general sobre Azure Virtual Desktop](../overview.md) para obtener una lista más detallada de los requisitos del sistema para ejecutar Azure Virtual Desktop. Mientras está allí, puede examinar el resto de la documentación de Azure Virtual Desktop si desea una búsqueda más centrada en TI en el servicio, ya que la mayoría de los artículos también se aplican al streaming de aplicaciones remotas para Azure Virtual Desktop. Una vez que comprenda los aspectos básicos, puede usar la documentación de streaming de aplicaciones remotas de forma más eficaz.

Para configurar una implementación de Azure Virtual Desktop para las aplicaciones personalizadas que están disponibles para clientes ajenos a su organización, necesitará lo siguiente:

- La aplicación personalizada. Consulte [Cómo hospedar aplicaciones personalizadas con Azure Virtual Desktop](custom-apps.md) para obtener información sobre los tipos de aplicaciones que admite Azure Virtual Desktop y cómo puede ofrecerlas a los clientes.

- Sus credenciales de unión un dominio. Si aún no tiene un sistema de administración de identidades compatible con Azure Virtual Desktop, deberá configurar la administración de identidades para el grupo de hosts. Para más información, consulte [Configuración de identidades administradas](identities.md).

- Suscripción a Azure. Si aún no tiene una suscripción, asegúrese de [crear una cuenta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="get-started"></a>Introducción

Ahora que está listo, echemos un vistazo a cómo puede configurar la implementación de Azure Virtual Desktop. Tiene dos opciones para configurarse correctamente. Puede configurar la implementación de forma manual o automática. En las dos secciones siguientes se describen las diferencias entre estos dos métodos.

### <a name="set-up-azure-virtual-desktop-manually"></a>Configuración manual de Azure Virtual Desktop

Puede configurar la implementación manualmente siguiendo estos tutoriales:

1. [Creación de un grupo de hosts con Azure Portal](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

2. [Administración de grupos de aplicaciones](../manage-app-groups.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

3. [Creación de un grupo de hosts para validar las actualizaciones del servicio](../create-validation-host-pool.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

4. [Configuración de alertas de servicio](../set-up-service-alerts.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

### <a name="set-up-azure-virtual-desktop-automatically"></a>Configuración automática de Azure Virtual Desktop

Si prefiere un proceso automático, puede usar la característica de introducción para configurar la implementación automáticamente. Para más información, consulte los siguientes artículos:

- [Implementación de Azure Virtual Desktop con la característica de introducción](../getting-started-feature.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json) (al seguir estas instrucciones, asegúrese de seguir las instrucciones de [Para suscripciones con Azure AD DS o AD DS](../getting-started-feature.md#for-subscriptions-with-azure-ad-ds-or-ad-ds). Este método permite administrar mejor las identidades y la compatibilidad de las aplicaciones, y permite ajustar los costos de infraestructura relacionados con la identidad. El método para las suscripciones que aún no tienen Azure AD DS o AD DS no le ofrece estas ventajas).
- [Solución de problemas de la característica de introducción](../troubleshoot-getting-started.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

## <a name="customize-and-manage-azure-virtual-desktop"></a>Personalización y administración de Azure Virtual Desktop

Una vez que haya configurado Azure Virtual Desktop, tiene muchas opciones para personalizar la implementación para satisfacer las necesidades de su organización o clientes. Estos artículos pueden ayudarle a empezar a trabajar:

- [Hospedaje de aplicaciones personalizadas con Azure Virtual Desktop](custom-apps.md)
- [Inscripción de la suscripción en los precios de acceso por usuario](per-user-access-pricing.md)
- [Uso de Azure Active Directory](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [Uso de máquinas virtuales Windows 10 con Intune](/mem/intune/fundamentals/windows-10-virtual-machines)
- [Implementación de una aplicación mediante la asociación de aplicaciones MSIX](msix-app-attach.md)
- [Uso de Azure Monitor para Azure Virtual Desktop para supervisar implementaciones](../azure-monitor.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Configuración de un plan de continuidad empresarial y recuperación ante desastres](../disaster-recovery.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Escalado de hosts de sesión con Azure Automation](../set-up-scaling-script.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Configuración de Impresión universal](/universal-print/fundamentals/universal-print-getting-started)
- [Configuración de la característica Iniciar VM al establecer la conexión](../start-virtual-machine-connect.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

## <a name="get-to-know-your-azure-virtual-desktop-deployment"></a>Conozca la implementación de Azure Virtual Desktop

Lea los artículos siguientes para comprender los conceptos esenciales para crear y administrar implementaciones de Azure Virtual Desktop:

- [Descripción de las licencias y los precios de acceso por usuario](licensing.md)
- [Directrices de seguridad para aplicaciones entre organizaciones](security.md)
- [Procedimientos recomendados de seguridad de Azure Virtual Desktop](../security-guide.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Glosario de Azure Monitor para Azure Virtual Desktop](../azure-monitor-glossary.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Azure Virtual Desktop para la empresa](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
- [Cálculo de los costos de implementación totales](total-costs.md)
- [Cálculo de los costos de streaming de aplicaciones por usuario](streaming-costs.md)
- [Recomendaciones de arquitectura](architecture-recs.md)
- [Preguntas frecuentes sobre el inicio de máquina virtual al conectar](../start-virtual-machine-connect-faq.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

## <a name="next-steps"></a>Pasos siguientes

Si está listo para empezar a configurar la implementación manualmente, diríjase al siguiente tutorial.

> [!div class="nextstepaction"]
> [Creación de un grupo de hosts con Azure Portal](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
