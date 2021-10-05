---
title: Solicitud de la cuota del host para Azure VMware Solution
description: Aprenda a solicitar la capacidad o la cuota del host para Azure VMware Solution. También puede solicitar más hosts en una nube privada de Azure VMware Solution existente.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 09/27/2021
ms.openlocfilehash: 2b3e2291726f37f3c802d1db24429381e7a2b7d1
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083246"
---
# <a name="request-host-quota-for-azure-vmware-solution"></a>Solicitud de la cuota del host para Azure VMware Solution

En este procedimiento, aprenderá a solicitar la capacidad o la cuota del host para [Azure VMware Solution](introduction.md). Va a enviar una incidencia de soporte técnico para que los hosts se asignen, ya sea para una nueva implementación o para una existente. 

Si tiene una nube privada de Azure VMware Solution y quiere asignar más hosts, seguirá el mismo proceso.

>[!IMPORTANT]
>Se puede tardar un máximo de cinco días laborables en asignar los hosts, en función del número solicitado.  De modo que, solicite lo que necesite para el aprovisionamiento y así no tener que solicitar un aumento de cuota tan a menudo.

## <a name="eligibility-criteria"></a>Criterios de elegibilidad

Necesitará una cuenta de Azure en una suscripción de Azure que cumpla uno de los criterios siguientes:

- Una suscripción bajo un [Contrato Enterprise (EA) de Azure](../cost-management-billing/manage/ea-portal-agreements.md) con Microsoft.
- Una suscripción administrada de Proveedor de soluciones en la nube (CSP) bajo un contrato de ofertas existente de Azure de CSP o un plan de Azure.
- Un [Contrato de cliente de Microsoft](../cost-management-billing/understand/mca-overview.md) con Microsoft.

## <a name="request-host-quota-for-ea-customers"></a>Solicitud de cuota de host para los clientes de EA

1. En Azure Portal, en **Ayuda y soporte técnico**, cree una **[solicitud de soporte técnico](https://rc.portal.azure.com/#create/Microsoft.Support)** y proporcione la siguiente información:
   - **Tipo de problema**: Requisitos previos técnicos
   - **Subscription** (Suscripción): Seleccione su suscripción.
   - **Servicio:** Todos los servicios > Azure VMware Solution
   - **Recurso:** Pregunta general 
   - **Resumen:** Capacidad necesitada
   - **Tipo de problema**: problemas de administración de la capacidad.
   - **Subtipo de problema:** solicitud de cliente de capacidad o cuota de host adicional.

1. En el campo **Descripción** de la incidencia de soporte técnico, en la pestaña **Detalles**, proporcione la siguiente información:

   - POC o producción 
   - Nombre de región
   - Número de hosts
   - Cualquier otro detalle

   >[!NOTE]
   >Azure VMware Solution requiere un mínimo de tres hosts y recomienda redundancia de N+1 hosts. 

1. Seleccione **Revisar + crear** para enviar la solicitud.


## <a name="request-host-quota-for-csp-customers"></a>Solicitud de cuota de host para los clientes de CSP 

Los CSP deben usar el [Centro de partners de Microsoft](https://partner.microsoft.com) para habilitar Azure VMware Solution para sus clientes. En este artículo se usa el [plan de Azure de CSP](/partner-center/azure-plan-lp) como ejemplo para ilustrar el procedimiento de compra para asociados.

Acceda a Azure Portal desde el Centro de partners mediante el procedimiento de **administrador con derechos delegados** (AOBO).

>[!IMPORTANT] 
>El servicio de Azure VMware Solution no proporciona el multiinquilino necesario. Los asociados de hospedaje que requieren esta función no se admiten. 

1. Configure el plan de Azure del CSP:

   1. En el **Centro de partners**, seleccione **CSP** para acceder al área **Clientes**.
   
      :::image type="content" source="media/pre-deployment/csp-customers-screen.png" alt-text="Captura de pantalla que muestra el área de clientes del Centro de partners de Microsoft." lightbox="media/pre-deployment/csp-customers-screen.png":::
   
   1. Seleccione el cliente y, a continuación, seleccione **Agregar productos**.
   
      :::image type="content" source="media/pre-deployment/csp-partner-center.png" alt-text="Captura de pantalla que muestra el plan de Azure seleccionado en el Centro de partners de Microsoft." lightbox="media/pre-deployment/csp-partner-center.png":::
   
   1. Seleccione **Plan de Azure** y, después, seleccione **Agregar al carro**. 
   
   1. Revise y termine la configuración general de la suscripción al plan de Azure del cliente. Para más información, consulte la [documentación del Centro de partners de Microsoft](/partner-center/azure-plan-manage).

1. Después de configurar el plan de Azure y una vez que tiene los [permisos de Azure RBAC](/partner-center/azure-plan-manage) necesarios para la suscripción, solicitará la cuota de la suscripción al plan de Azure. 

   1. Acceda a Azure Portal desde el [Centro de partners de Microsoft](https://partner.microsoft.com) mediante el procedimiento de **administrador con derechos delegados** (AOBO).
   
   1. Seleccione **CSP** para acceder al área **Clientes**.
   
   1. Expanda los detalles del cliente y seleccione **Portal de administración de Microsoft Azure**.
   
   1. En Azure Portal, en **Ayuda y soporte técnico**, cree una **[solicitud de soporte técnico](https://rc.portal.azure.com/#create/Microsoft.Support)** y especifique la siguiente información:
      - **Tipo de problema**: Requisitos previos técnicos
      - **Subscription** (Suscripción): Seleccione su suscripción.
      - **Servicio:** Todos los servicios > Azure VMware Solution
      - **Recurso:** Pregunta general 
      - **Resumen:** Capacidad necesitada
      - **Tipo de problema**: problemas de administración de la capacidad.
      - **Subtipo de problema:** solicitud de cliente de capacidad o cuota de host adicional.
   
   1. En el campo **Descripción** de la incidencia de soporte técnico, en la pestaña **Detalles**, proporcione la siguiente información:
   
      - POC o producción 
      - Nombre de región
      - Número de hosts
      - Cualquier otro detalle
      - ¿Está pensado para hospedar varios clientes?
   
      >[!NOTE]
      >Azure VMware Solution requiere un mínimo de tres hosts y recomienda redundancia de N+1 hosts. 
   
   1. Seleccione **Revisar + crear** para enviar la solicitud.


## <a name="next-steps"></a>Pasos siguientes

Para implementar Azure VMware Solution, antes es preciso [registrar el proveedor de recursos](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider) en una suscripción para habilitar el servicio.   
