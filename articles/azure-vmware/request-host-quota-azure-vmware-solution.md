---
title: Solicitud de la cuota del host para Azure VMware Solution
description: Aprenda a solicitar la capacidad o la cuota del host para Azure VMware Solution. También puede solicitar más hosts en una nube privada de Azure VMware Solution existente.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 05/13/2021
ms.openlocfilehash: ceb32d7e09e6c595a6cddf844c713093253b8994
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111421659"
---
# <a name="request-host-quota-for-azure-vmware-solution"></a>Solicitud de la cuota del host para Azure VMware Solution

En este procedimiento, aprenderá a solicitar la capacidad o la cuota del host para [Azure VMware Solution](introduction.md). Deberá enviar una incidencia de soporte técnico para que se asignen los hosts. Si tiene una nube privada de Azure VMware Solution y quiere asignar más hosts, seguirá el mismo proceso.

>[!IMPORTANT]
>Puede que la asignación de los hosts tarde algunos días en función del número solicitado.  De modo que, solicite lo que necesite para el aprovisionamiento y así no tener que solicitar un aumento de cuota tan a menudo.

## <a name="eligibility-criteria"></a>Criterios de elegibilidad

Necesitará una cuenta de Azure en una suscripción a Azure. La suscripción a Azure debe cumplir uno de los siguientes criterios:

- Una suscripción bajo un [Contrato Enterprise (EA) de Azure](../cost-management-billing/manage/ea-portal-agreements.md) con Microsoft.
- Una suscripción administrada de Proveedor de soluciones en la nube (CSP) bajo un contrato de ofertas existente de Azure de CSP o un plan de Azure.

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
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Área de clientes del Centro de partners de Microsoft" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. Seleccione el cliente y, a continuación, seleccione **Agregar productos**.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Centro de partners de Microsoft" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. Seleccione **Plan de Azure** y, después, seleccione **Agregar al carro**. 
   
   1. Revise y termine la configuración general de la suscripción al plan de Azure del cliente. Para más información, consulte la [documentación del Centro de partners de Microsoft](/partner-center/azure-plan-manage).

1. Después de configurar el plan de Azure y una vez que tiene los [permisos de Azure RBAC](/partner-center/azure-plan-manage) necesarios para la suscripción, solicitará la cuota de la suscripción al plan de Azure. 

   1. Acceda a Azure Portal desde el [Centro de partners de Microsoft](https://partner.microsoft.com) mediante el procedimiento de **administrador con derechos delegados** (AOBO).
   
   1. Seleccione **CSP** para acceder al área **Clientes**.
   
   1. Expanda los detalles del cliente y seleccione **Portal de administración de Microsoft Azure**.
   
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
      - ¿Está pensado para hospedar varios clientes?
   
      >[!NOTE]
      >Azure VMware Solution requiere un mínimo de tres hosts y recomienda redundancia de N+1 hosts. 
   
   1. Seleccione **Revisar + crear** para enviar la solicitud.


## <a name="next-steps"></a>Pasos siguientes

Para poder implementar Azure VMware Solution, primero debe [registrar el proveedor de recursos](deploy-azure-vmware-solution.md#step-1-register-the-microsoftavs-resource-provider) con su suscripción para habilitar el servicio.   

