---
title: Vincule a un identificador de asociado para habilitar el crédito que ha obtenido un asociado en los recursos delegados
description: Asocie el identificador de asociado para recibir créditos obtenidos por el asociado (PEC) en los recursos de cliente que administra a través de Azure Lighthouse.
ms.date: 02/12/2021
ms.topic: how-to
ms.openlocfilehash: e21fc641f421580e6f00de8036f88632cc860060
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/14/2021
ms.locfileid: "112075626"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>Vincule a un identificador de asociado para habilitar el crédito que ha obtenido un asociado en los recursos delegados 

Si es miembro de [Microsoft Partner Network](https://partner.microsoft.com/), puede vincular su identificador de asociado con las credenciales utilizadas para administrar los recursos de cliente delegados, lo que permite a Microsoft identificar y reconocer asociados que impulsan el éxito de los clientes de Azure. Este vínculo también permite a los asociados de [CSP (proveedor de soluciones en la nube)](/partner-center/csp-overview) recibir [crédito obtenido por asociados de los servicios administrados (PEC)](/partner-center/partner-earned-credit) para los clientes que han [firmado el contrato de cliente de Microsoft (MCA)](/partner-center/confirm-customer-agreement) y se encuentran [ en el plan de Azure](/partner-center/azure-plan-get-started).

Para obtener el reconocimiento de las actividades de Azure Lighthouse, deberá [vincular el id. de MPN](../../cost-management-billing/manage/link-partner-id.md) con al menos una cuenta de usuario del inquilino de administración y asegurarse de que la cuenta vinculada tiene acceso a cada una de las suscripciones incorporadas.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Asociación del identificador de asociado al incorporar nuevos clientes

Use el proceso siguiente para vincular su identificador de asociado (y permitir al asociado ganar crédito, si corresponde). Para completar estos pasos, necesitará conocer su [identificador de asociado de MPN](/partner-center/partner-center-account-setup#locate-your-mpn-id). Asegúrese de utilizar el **identificador de MPN asociado** que se muestra en su perfil de asociado.

Para simplificar, se recomienda crear una cuenta de entidad de servicio en el inquilino, vincularla al **Id. de MPN asociado** y, después, concederle acceso a todos los clientes que incorpore con un [rol integrado de Azure que sea apto para el PEC](/partner-center/azure-roles-perms-pec).

1. [Cree una cuenta de usuario de entidad de servicio](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) en el inquilino de administración. En este ejemplo, utilizaremos el nombre *Cuenta de Automation del proveedor* para esta cuenta de entidad de servicio.
1. Con esa cuenta de entidad de servicio, [vincúlela a su identificador de MPN asociado](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) en el inquilino de administración. Solo tiene que hacer esto una vez.
1. Al incorporar un cliente [mediante plantillas de ARM](onboard-customer.md) u [ofertas de servicio administrado](publish-managed-services-offers.md), asegúrese de incluir una autorización que incluya la Cuenta de automatización del proveedor como usuario con un [rol integrado de Azure que sea apto para el PEC](/partner-center/azure-roles-perms-pec).

Siguiendo estos pasos, todos los inquilinos de cliente que administre se asociarán con su identificador de asociado. No es necesario que la cuenta "Provider Automation Account" autentique o realice ninguna acción en el inquilino del cliente.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Diagrama que muestra el proceso de vinculación de identificador de asociado con Azure Lighthouse.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Adición del identificador de asociado a los clientes incorporados anteriormente

Si ya ha incorporado un cliente, es posible que no desee realizar otra implementación para agregar la entidad de servicio de la cuenta "Provider Automation Account". En su lugar, puede vincular el **identificador de MPN asociado** a una cuenta de usuario que ya tenga acceso para trabajar en el inquilino de ese cliente. Asegúrese de que se ha concedido a la cuenta un [rol integrado de Azure que sea apto para el PEC](/partner-center/azure-roles-perms-pec).

Una vez que la cuenta se ha [vinculado a su identificador de MPN asociado](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) en el inquilino de administración, podrá realizar un seguimiento del reconocimiento del impacto en ese cliente.

## <a name="confirm-partner-earned-credit"></a>Confirmación de crédito obtenido por el asociado

Puede [ver los detalles del PEC en Azure Portal](/partner-center/partner-earned-credit-explanation#azure-cost-management) y confirmar qué costos han recibido la ventaja del PEC. Recuerde que PEC solo se aplica a los clientes de CSP que han firmado el MCA y que están en el plan de Azure.

Si ha seguido los pasos anteriores y no ve la asociación esperada, abra una solicitud de soporte técnico en Azure Portal.

También puede usar el [SDK del Centro de partners](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) y filtrar por `rateOfPartnerEarnedCredit` para automatizar la comprobación de PEC en una suscripción.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Microsoft Partner Network](/partner-center/mpn-overview).
- Aprenda [cómo se calcula y se paga el PEC](/partner-center/partner-earned-credit-explanation).
- [Incorpore clientes](onboard-customer.md) a Azure Lighthouse.