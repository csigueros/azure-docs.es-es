---
title: 'Preguntas y solución de problemas relacionados con la administración de clientes potenciales: Centro de Partners de Microsoft'
description: Lea acerca de los errores y las preguntas comunes al configurar clientes potenciales del marketplace comercial en el Centro de partners.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 5cec26344baed0a547570b386edc88a75ebbd052
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112294272"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>Preguntas y solución de problemas relacionados con la configuración de clientes potenciales

En este artículo se da respuesta a algunas preguntas comunes sobre la administración de clientes potenciales en las ofertas del marketplace comercial. También se tratan los errores que pueden surgir al configurar clientes potenciales en el sistema de administración de relaciones con clientes (CRM) en el Centro de partners.

## <a name="common-questions-about-lead-management"></a>Preguntas habituales sobre la administración de clientes potenciales

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>¿Dónde puedo obtener ayuda para la configuración de mi destino de clientes potenciales?

Consulte [Clientes potenciales de las ofertas del marketplace comercial](partner-center-portal/commercial-marketplace-get-customer-leads.md) para información general sobre cómo conectar su sistema CRM a las ofertas del marketplace comercial. Si tiene un error, consulte la guía de solución de problemas a continuación. Si necesita ayuda adicional, envíe una incidencia de soporte técnico mediante la sección de [Ayuda y soporte técnico del Centro de partners](https://go.microsoft.com/fwlink/?linkid=2165533). A continuación, seleccione **Creación de ofertas** > **El tipo de oferta** > **Configuración de administración de clientes potenciales**.

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>¿Tengo que configurar un destino de clientes potenciales para poder publicar una oferta en el marketplace comercial?

La respuesta depende del tipo de oferta que publique. El software como servicio (SaaS) y Dynamics 365 Customer Engagement usan **Contacto** para mostrar todas las ofertas de Dynamics 365 Finance y Operations, todas las ofertas de Dynamics 365 Business Central y todas las ofertas de servicio de consultoría. Como resultado, requieren una conexión a un destino de cliente potencial. Si el tipo de oferta no aparece en la lista, no se requiere ninguna conexión a un destino de cliente potencial. Es recomendable configurar un destino de clientes potenciales para no perder oportunidades de negocio.

#### <a name="how-can-i-find-the-test-lead"></a>¿Cómo puedo encontrar al cliente potencial de prueba?

Busque `"MSFT_TEST"` en el destino del cliente potencial. A continuación se incluye un cliente potencial de muestra de Microsoft. Tenga en cuenta que el formato del cliente potencial varía en función del destino del cliente potencial.

```
{
    "userDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Tengo una oferta activa. ¿Por qué no veo ningún cliente potencial?

Asegúrese de que la conexión con el destino de clientes potenciales sea válida. Le enviaremos un cliente potencial de muestra después de seleccionar **Publicar** en la oferta en el Centro de partners. Si ve el cliente potencial de prueba, la conexión es válida. También puede probar la conexión de clientes potenciales intentando obtener la vista previa de la oferta durante el paso de vista previa. Seleccione **Obténgalo ahora**, **Contacto** o **Prueba gratuita** en la lista del marketplace comercial.

Además, asegúrese de buscar los datos correctos. Consulte [Descripción de los datos de clientes potenciales](partner-center-portal/commercial-marketplace-get-customer-leads.md) para ver una explicación de los datos de clientes potenciales que se envían al destino de clientes potenciales.

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>He configurado Azure Blob Storage como destino de mis clientes potenciales. ¿Por qué no veo el cliente potencial?

Azure Blob Storage ya no se admite como destino de clientes potenciales, por lo que le faltan los clientes potenciales que haya generado su oferta. Cambie a cualquiera de las otras [opciones de destino de clientes potenciales](partner-center-portal/commercial-marketplace-get-customer-leads.md). 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>He recibido un correo electrónico del marketplace comercial. ¿Por qué no encuentro al cliente potencial en mi CRM?

Es posible que el dominio de correo electrónico del usuario final sea .edu. Por motivos de privacidad, no se pasa información personal procedente del dominio .edu. Envíe una incidencia de soporte técnico mediante la [Ayuda y soporte técnico del Centro de partners](https://go.microsoft.com/fwlink/?linkid=2165533).

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>He configurado una tabla de Azure como destino de mi cliente potencial. ¿Cómo puedo ver los clientes potenciales?

Puede tener acceso a los datos de clientes potenciales almacenados en la tabla de Azure desde Azure Portal. También puede descargar e instalar el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para ver los datos de las tablas de la cuenta de Azure Storage.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>He configurado una tabla de Azure como destino de mi cliente potencial. ¿Puedo recibir una notificación cuando se envíe un nuevo cliente potencial del marketplace comercial?

Sí. Siga las instrucciones de [Configuración de la administración de clientes potenciales mediante una tabla de Azure](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) para configurar un flujo de Microsoft que envía un correo electrónico si se agrega un cliente potencial a la tabla de Azure.

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>He configurado Salesforce como destino de clientes potenciales. ¿Por qué no los encuentro?

Compruebe si el formulario de web a cliente potencial es un campo obligatorio en función de la lista desplegable. En caso afirmativo, cámbielo por un campo de texto no obligatorio.

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Hubo un problema con el destino de clientes potenciales y perdí algunos clientes potenciales. ¿Pueden enviármelos en un correo electrónico?

Debido a las directivas de información personal, no podemos compartir la información de clientes potenciales mediante correo electrónico no protegido.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>He configurado una tabla de Azure como destino de mi cliente potencial. ¿Cuánto costará?

Hay pocos datos de generación de clientes potenciales. Es menor que 1 GB para casi todos los editores. El costo depende del número de clientes potenciales recibidos. Por ejemplo, si se reciben 1000 clientes potenciales en un mes, el costo es de unos 50 céntimos. Para obtener más información sobre los precios de almacenamiento, consulte [Información general sobre los precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Si no se responde a su pregunta, póngase en contacto con el Soporte técnico de Microsoft desde la [Ayuda y soporte técnico del Centro de Partners](https://aka.ms/marketplacepublishersupport). A continuación, seleccione **Creación de ofertas** > **El tipo de oferta** > **Configuración de administración de clientes potenciales**.

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Recibo notificaciones por correo electrónico cuando se reciben nuevos clientes potenciales. ¿Cómo puedo configurar a otra persona para que reciba estos mensajes de correo?

Acceda a la oferta en el Centro de partners y vaya a la página **Configuración de la oferta** > **Administración de clientes potenciales** > **Editar**. Actualice las direcciones de correo electrónico en el campo **Correo electrónico de contacto**.

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> Solución de errores de configuración de clientes potenciales

**No se pudo guardar el cliente potencial en Dynamics CRM. Compruebe la configuración de la cuenta de Dynamics CRM. LastCRMError: no se puede iniciar sesión en Dynamics CRM; LastCRMException:** 

> Si ha seleccionado la autenticación de Microsoft 365, compruebe si la cuenta de usuario y la contraseña son válidas. Si ha seleccionado Azure Active Directory, compruebe si el identificador del inquilino, el identificador de la aplicación y la clave secreta de la aplicación coinciden con lo que ha configurado en Azure Active Directory. Siga las instrucciones que encontrará [aquí](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Si el nombre de usuario y la contraseña de la cuenta son válidos, asegúrese de que tiene acceso a Dynamics 365 y una licencia asignada (es decir, los pasos 11 al 15 si usa Azure Active Directory o la configuración de seguridad si usa un usuario de Office). 

**No se pudo guardar el cliente potencial en Dynamics CRM. El usuario no tiene permisos de creación para el atributo leadsourcecode en la entidad de cliente potencial.** 

> La aplicación o el usuario no tienen roles de seguridad para el sistema de escritura de clientes potenciales de Microsoft Marketplace. Siga los pasos 11 a 15 que se indican [aquí](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) si usa Azure Active Directory, o la configuración de seguridad si usa un usuario de Office.

**No se pudo guardar el cliente potencial en Dynamics CRM mediante AAD. Excepción: No se ha podido encontrar el inquilino. Esta instancia puede ocurrir si no hay suscripciones activas para el inquilino.**  

> El identificador de directorio proporcionado en la sección de administración de clientes potenciales no es un directorio válido. Obtenga el identificador de directorio con las instrucciones que aparecen en el paso 2 (en Azure Active Directory) de [este vínculo](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**No se pudo guardar el cliente potencial en Dynamics CRM. LastCRMError: error de SecLib::RetrievePrivilegeForUser: no hay ningún rol asignado al usuario.**  

> Resolución: asigne el rol de seguridad al sistema de escritura de clientes potenciales de Microsoft Marketplace. Siga las instrucciones que se proporcionan [aquí](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) en la configuración de seguridad.

**No se pudo guardar el cliente potencial en Dynamics CRM mediante AAD. Excepción: No se encontró la aplicación con el identificador en el directorio.** 

> El identificador de aplicación proporcionado en la sección de administración de clientes potenciales no es un directorio válido. Obtenga el identificador de directorio basándose en las instrucciones que aparecen en el paso 8 (en Azure Active Directory, a partir de [aquí](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**No se pudo guardar el cliente potencial en Dynamics CRM mediante AAD. Excepción: El identificador del inquilino solicitado no es válido y el formato de dominio externo no es válido** 

> El identificador de directorio proporcionado en la sección de administración de clientes potenciales no es un directorio válido. Obtenga el identificador de directorio basándose en las instrucciones que aparecen en el paso 2 (en Azure Active Directory, a partir de [aquí](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**No se pudo guardar el cliente potencial en Dynamics CRM mediante AAD. Excepción: Error al validar las credenciales: se ha proporcionado un secreto de cliente no válido.** 

> Resolución: Inicie sesión en Azure Portal y compruebe que la clave de aplicación coincida con lo que hay en Centro de partners. Obtenga la contraseña basándose en las instrucciones que aparecen en el paso 10 (en Azure Active Directory, a partir de [aquí](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**No se pudo guardar el cliente potencial en Dynamics CRM. LastCRMError: Se agotó el tiempo de espera del canal de solicitud mientras se esperaba una respuesta después de 00:02:00. Aumente el valor de tiempo de espera que se pasa a la llamada a la solicitud o aumente el valor de SendTimeout en el enlace. El tiempo asignado a esta operación puede haber sido una fracción de un tiempo de espera mayor.**  

> Resolución: Inicie sesión en Centro de partners, compruebe en Configuración de la oferta >> Clientes potenciales >> Dirección URL que la instancia de Dynamic CRM sea válida.
