---
title: Solución de problemas al intentar ver la factura en Azure Portal
description: Aprenda a resolver un problema al intentar ver la factura en Azure Portal.
services: cost-management-billing
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: banders
ms.openlocfilehash: aac769894a85db94c68bc4c73971809775ea4da4
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112971343"
---
# <a name="troubleshoot-issues-while-trying-to-view-invoice-in-the-azure-portal"></a>Solución de problemas al intentar ver la factura en Azure Portal

Puede tener problemas al intentar ver la factura en Azure Portal. En esta breve guía trataremos algunos problemas comunes.
 
## <a name="common-issues-and-solutions"></a>Problemas comunes y soluciones

#### <a name="you-see-the-message-we-cant-display-the-invoices-for-your-subscription-this-typically-happens-when-you-sign-in-with-an-email-which-doesnt-have-access-to-view-invoices-check-youve-signed-in-with-the-correct-email-address-if-you-are-still-seeing-the-error-see-why-you-might-not-see-an-invoice"></a><a name="subnotfound"></a>Aparece el mensaje "No se pueden mostrar las facturas de su suscripción. Esto suele ocurrir cuando inicia sesión con un correo electrónico que no tiene acceso para ver las facturas. Compruebe que ha iniciado sesión con la dirección de correo electrónico correcta. Si sigue viendo el error, consulte Por qué no puede ver una factura".

Esto sucede cuando la identidad que usó para iniciar sesión no tiene acceso a la suscripción.

Para resolver este problema, compruebe los siguientes puntos: 

**Compruebe que ha iniciado sesión con la dirección de correo electrónico correcta:**

Solo el correo electrónico que tiene el rol de administrador de cuenta para la suscripción puede ver su factura. Compruebe que ha iniciado sesión con la dirección de correo electrónico correcta. Esta dirección se muestra en el correo electrónico que recibió cuando se generó la factura.  

  ![Captura de pantalla que muestra el correo electrónico de la factura](./media/troubleshoot-cant-find-invoice/invoice-email.png)

**Compruebe que ha iniciado sesión con la cuenta correcta:**

Algunos clientes tienen dos cuentas con la misma dirección de correo electrónico: una cuenta profesional o educativa y una cuenta personal. Por lo general, solo una de ellas tiene permisos para ver facturas. Es posible que tenga dos cuentas con la dirección de correo electrónico. Si inicia sesión con la identidad que no tiene permiso, no verá la factura. Para identificar si tiene varias cuentas y usa una cuenta diferente, siga estos pasos:

1. Inicie sesión en [Azure Portal ](https://portal.azure.com) en una ventana InPrivate/incógnito.
1. Si tiene varias cuentas con el mismo correo electrónico, se le pedirá que seleccione **Cuenta profesional o educativa** o bien **Cuenta personal**. Seleccione una de las cuentas y siga las [instrucciones que se indican aquí para ver la factura](../understand/download-azure-invoice.md#download-your-mosp-azure-subscription-invoice).  

    ![Captura de pantalla que muestra la selección de cuenta](./media/troubleshoot-cant-find-invoice/two-accounts.png)

1. Si sigue sin poder ver la factura en Azure Portal, pruebe con otra cuenta.

**Compruebe que ha iniciado sesión en el inquilino de Azure Active Directory (AAD) correcto:**

Su cuenta de facturación y su suscripción están asociadas a un inquilino de AAD. Si ha iniciado sesión en un inquilino incorrecto, no verá la factura de su suscripción. Pruebe los pasos siguientes para cambiar de inquilino en Azure Portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione la dirección de correo electrónico en la parte superior derecha de la página.
1. Seleccione Cambiar directorio.  

    ![Captura de pantalla que muestra la selección para cambiar de directorio](./media/troubleshoot-cant-find-invoice/select-switch-tenant.png)

1. Seleccione un inquilino en la sección Todos los directorios. Si no ve la sección Todos los directorios, significa que no tiene acceso a varios inquilinos.  

    ![Captura de pantalla que muestra la selección de otro directorio](./media/troubleshoot-cant-find-invoice/select-another-tenant.png)

#### <a name="you-couldnt-find-the-invoice-that-you-see-on-your-credit-card-statement"></a><a name="cantsearchinvoice">No ha podido encontrar la factura que aparece en el extracto de la tarjeta de crédito</a>

Aparece un cargo en su tarjeta de crédito con la descripción **Microsoft Gxxxxxxxxx**. Puede encontrar todas las demás facturas en el portal, pero no Gxxxxxxxxx. Esto sucede cuando la factura pertenece a otra suscripción u otro perfil de facturación. Siga estos pasos para ver la factura.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque el número de factura en la barra de búsqueda de Azure Portal.
1. Seleccione **View your invoice** (Ver su factura).  

    ![Captura de pantalla en la que se muestra la búsqueda de una factura](./media/troubleshoot-cant-find-invoice/search-invoice.png)

## <a name="contact-us-for-help"></a>Póngase en contacto con nosotros para obtener ayuda

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Pasos siguientes

- [Visualización y descarga de la factura de Azure](../understand/download-azure-invoice.md)
- [Visualización y descarga de los datos de uso y los cargos de Azure](../understand/download-azure-daily-usage.md)
- [Error No se encontraron suscripciones durante el inicio de sesión en Azure Portal](no-subscriptions-found.md)