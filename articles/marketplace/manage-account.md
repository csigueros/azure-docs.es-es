---
title: Administración de una cuenta de Marketplace comercial en el Centro de partners
description: Administre una cuenta de Marketplace comercial en el Centro de partners.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 09/27/2021
ms.openlocfilehash: f6730ad58491fb24dbc025eaa44fbc7d7bdade58
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359823"
---
# <a name="manage-a-commercial-marketplace-account-in-partner-center"></a>Administración de una cuenta de Marketplace comercial en el Centro de partners

**Roles adecuados**

- Propietario
- Manager

Después de [crear una cuenta del Centro de partners](./create-account.md), puede usar el [panel de marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) para administrar su cuenta y las ofertas.

## <a name="access-your-account-settings"></a>Acceso a la configuración de cuenta

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Vista de áreas de trabajo](#tab/workspaces-view)

Si aún no lo ha hecho, usted (o el administrador de la organización) debe acceder a la [configuración de la cuenta](https://partner.microsoft.com/dashboard/account/v3/organization/legalinfo#mpn) correspondiente a la cuenta del Centro de partners.

1. Inicie sesión en el [panel del marketplace comercial](https://partner.microsoft.com/dashboard/home) en el Centro de partners con la cuenta a la que quiere acceder. Si tiene varias cuentas y ha iniciado sesión con otra, puede [cambiar de cuenta](switch-accounts.md).

1. Seleccione **Configuración** (icono de engranaje) en la esquina superior derecha y, luego, **Configuración de cuenta**.

    [ ![Captura de pantalla del menú de configuración de cuenta en el Centro de partners.](./media/manage-accounts/settings-account-workspaces.png) ](./media/manage-accounts/settings-account-workspaces.png#lightbox)

1. En **Configuración de cuenta**, seleccione **Legal** y, a continuación, la pestaña **Desarrollador** para ver los detalles relacionados con su cuenta de marketplace comercial.

    [ ![Captura de pantalla de la pestaña Desarrollador de la página legal en Configuración de la cuenta.](./media/manage-accounts/developer-tab-workspaces.png) ](./media/manage-accounts/developer-tab-workspaces.png#lightbox)

#### <a name="current-view"></a>[Vista actual](#tab/current-view)

Si aún no lo ha hecho, usted (o el administrador de la organización) debe acceder a la [configuración de la cuenta](https://go.microsoft.com/fwlink/?linkid=2165291) correspondiente a la cuenta del Centro de partners.

1. Inicie sesión en el [panel del marketplace comercial](https://go.microsoft.com/fwlink/?linkid=2165290) en el Centro de partners con la cuenta a la que quiere acceder. Si tiene varias cuentas y ha iniciado sesión con otra, puede [cambiar de cuenta](switch-accounts.md).
1. En la parte superior derecha, seleccione **Configuración** (icono del engranaje) y, después, **Configuración del modelo de inquilino**.

   :::image type="content" source="media/manage-accounts/settings-account.png" alt-text="Captura de pantalla que muestra la opción Configuración de cuenta en el Centro de partners.":::

1. En **Configuración de cuenta**, seleccione **Legal** y, a continuación, la pestaña **Desarrollador** para ver los detalles relacionados con su cuenta de marketplace comercial.

   :::image type="content" source="media/manage-accounts/developer-tab.png" alt-text="Captura de pantalla que muestra la pestaña Desarrollador." lightbox="media/manage-accounts/developer-tab.png":::

---

### <a name="account-settings-page"></a>Página de configuración de cuenta

Al seleccionar **Configuración** y expandir **Configuración de la cuenta**, la vista predeterminada es **Información legal**. Esta página puede tener hasta tres pestañas, en función de los programas en los que se haya inscrito: _Partner_, _Revendedor_ y _Desarrollador_.

La pestaña **Partner**, para los partners inscritos en MPN, incluye:

- Toda la información empresarial legal, como el nombre legal registrado y la dirección de la empresa.
- El contacto principal.
- Las ubicaciones empresariales.

La pestaña **Revendedor**, para partners que realizan negocios de CSP incluye:

- La información de contacto principal.
- El perfil de soporte técnico para clientes.
- La información del programa.

La pestaña **Desarrollador**, para los partners inscritos en cualquier programa para desarrolladores, tiene la siguiente información:

- **Detalles de la cuenta**: tipo de cuenta y estado de la misma
- **Identificadores del editor**: identificador del vendedor, del usuario y del editor, inquilinos de Azure AD y más.
- **Información de contacto:** nombre para mostrar del editor, contacto del vendedor (nombre, correo electrónico, teléfono y dirección) y aprobador de la empresa (nombre, correo electrónico, teléfono).

### <a name="account-settings---developer-tab"></a>Configuración de la cuenta - pestaña Desarrollador

En la siguiente información se describen los datos de la pestaña Desarrollador.

#### <a name="account-details"></a>Detalles de la cuenta

En la sección _Detalles de la cuenta_ de la pestaña _Desarrollador_, puede ver información básica, como el **Tipo de cuenta** (personal o empresarial) y el **Estado de comprobación** de la cuenta. Durante el proceso de comprobación de la cuenta, esta configuración mostrará todos los pasos que sean necesarios, incluidas la comprobación de correo electrónico, la comprobación de empleo y la comprobación de negocio.

#### <a name="publisher-ids"></a>Identificadores del editor

En la sección de identificadores del editor, puede ver los valores de **Id. de Symantec** (si corresponde), **Id. de vendedor**, **Id. de usuario** e **Id. de MPN**, así como los **inquilinos de Azure AD**. Microsoft asigna estos valores para identificar de manera exclusiva su cuenta de desarrollador y no se pueden modificar.

Si no tiene un identificador de Symantec, puede seleccionar el vínculo para solicitar uno.

### <a name="contact-info"></a>Información de contacto

En la sección _Información de contacto_, puede ver el **Nombre para mostrar del publicador**, **Información de contacto del vendedor** (nombre de contacto, correo electrónico, número de teléfono y dirección del vendedor de la empresa) y el **Aprobador de la empresa** (nombre, correo electrónico y número de teléfono de la persona con autoridad para aprobar las decisiones de la empresa).

También puede seleccionar el vínculo **Actualizar** para cambiar la información de contacto, como el nombre para mostrar del publicador y la dirección de correo electrónico.

### <a name="account-settings-identifiers"></a>Configuración de la cuenta - identificadores

En **Configuración de la cuenta** > **Perfil de organización**, seleccione **Identificadores** para consultar la siguiente información:

- **Id. de MPN:** cualquier identificador de MPN asociado a su cuenta.
- **CSP:** identificadores de MPN asociados al programa CSP para esta cuenta.
- **Editor :** identificadores de vendedor asociados a su cuenta.
- **GUID de seguimiento**: todos los GUID de seguimiento asociados a su cuenta.

#### <a name="tracking-guids"></a>GUID de seguimiento

Los identificadores únicos globales (GUID) son números de referencia exclusivos (con 32 dígitos hexadecimales) que se pueden utilizar para realizar el seguimiento del uso de Azure.

Para crear identificadores únicos globales para el seguimiento, debe usar un generador de GUID. El equipo de Azure Storage ha creado un [formulario de generación de GUID](https://aka.ms/StoragePartners) que enviará por correo electrónico un GUID en el formato correcto. Este GUID se puede reutilizar en los distintos sistemas de seguimiento.

Se recomienda crear un GUID único para todos los canales de distribución y oferta de cada producto. Puede optar por usar un solo GUID para los múltiples canales de distribución del producto si no quiere que los informes se dividan.

Si implementa un producto mediante una plantilla y está disponible tanto en Azure Marketplace como en GitHub, puede crear y registrar dos GUID distintos:

- Producto A en Azure Marketplace
- Producto A en GitHub

La creación de informes se realiza en función del valor de asociado comercial (id. de Partner de Microsoft) y de los GUID. También puede realizar un seguimiento de los GUID a un nivel más pormenorizado para cada plan de la oferta.

Para más información, consulte [Tracking Azure customer usage with GUIDs FAQ](azure-partner-customer-usage-attribution.md#faq) (Preguntas más frecuentes sobre el seguimiento del uso por parte de los clientes de Azure mediante los GUID).

### <a name="agreements"></a>Contratos

La página **Contratos** le permite ver una lista de los contratos de publicación que ha autorizado. Estos contratos se enumeran según el nombre y número de versión, incluida la fecha en que se aceptó y el nombre del usuario que aceptó el contrato.

Para acceder a la página Contratos:

1. Inicie sesión en el [Centro de partners](https://partner.microsoft.com/dashboard/home).
1. En la parte superior derecha, seleccione **Configuración** > **Configuración de la cuenta**.
1. En **Configuración de la cuenta**, seleccione **Contratos**.

La sección **Se requieren acciones** puede aparecer en la parte superior de esta página si hay actualizaciones del contrato que requieren su atención. Para aceptar un contrato actualizado, lea primero la versión del contrato vinculado y seleccione **Aceptar el contrato**.

## <a name="set-up-a-payout-profile"></a>Configuración de un perfil de pago

Para tener una oferta comercializable en Azure Marketplace, se debe enviar y validar un perfil de impuestos y de pago en el Centro de partners.  Antes de crear un perfil de pago, es necesario enviar un perfil de impuestos. El envío de un perfil de impuestos puede tardar hasta 48 horas en validarse.

Un perfil de pago es la cuenta bancaria a la que se envían los ingresos de sus ventas. Esta cuenta bancaria debe estar en el mismo país o región en el que se ha registrado su cuenta del Centro de partners. Para más información sobre un perfil de pago, consulte [Configuración de la cuenta de pago y los formularios de impuestos](/partner-center/set-up-your-payout-account).

### <a name="to-set-up-your-payout-profile"></a>Para configurar el perfil de pago:

#### <a name="workspaces-view"></a>[Vista de áreas de trabajo](#tab/workspaces-view)

1. Inicie sesión en el [panel del marketplace comercial](https://partner.microsoft.com/dashboard/home) en el Centro de partners con la cuenta a la que quiere acceder.

1. En la parte superior derecha, seleccione **Configuración** (icono del engranaje) y, después, **Configuración del modelo de inquilino**.

    [ ![Captura de pantalla del menú de configuración de cuenta en el Centro de partners.](./media/manage-accounts/settings-account-workspaces.png) ](./media/manage-accounts/settings-account-workspaces.png#lightbox)

1. En el menú izquierdo, en **Pago e impuestos**, seleccione **Asignación de perfiles fiscales y de pago**.

    [ ![Se muestra la página Asignación de perfiles fiscales y de pago.](./media/manage-accounts/payout-and-tax-profile-assignment.png) ](./media/manage-accounts/payout-and-tax-profile-assignment.png#lightbox)

    > [!NOTE]
    > Si no ve la sección **Pago e impuestos** en el menú izquierdo, póngase en contacto con el administrador global o el administrador de la cuenta para obtener permisos.

1. Para más información sobre la configuración de un perfil de pago, consulte [Configuración de la cuenta de pago y los formularios de impuestos](/partner-center/set-up-your-payout-account).

#### <a name="current-view"></a>[Vista actual](#tab/current-view)

1. Vaya a la página de [información general del marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) en el Centro de partners.
1. En la sección **Perfil**, junto a **Payout Profile** (perfil de pago), seleccione **Actualizar**.

    > [!NOTE]
    > Si no ve la sección **Pago e impuestos** en el menú izquierdo, póngase en contacto con el administrador global o el administrador de la cuenta para obtener permisos.

1. Para más información sobre la configuración del perfil de pago, consulte [Configuración de la cuenta de pago y los formularios de impuestos](/partner-center/set-up-your-payout-account).

---

> [!IMPORTANT]
> El hecho de cambiar la cuenta de pago puede dar lugar a retrasos en los pagos de hasta un ciclo completo. Este retraso se produce porque es necesario comprobar el cambio de la cuenta, al igual que hicimos cuando configuró por primera vez la cuenta de pago. Aunque se le seguirá pagando el importe completo después de que se haya comprobado la cuenta, los pagos pendientes del ciclo actual se agregarán al siguiente.  

## <a name="tax-profile"></a>Perfil fiscal

Revise el estado actual de su perfil fiscal y confirme que aparece el **Tipo de entidad** y la **Información de certificado fiscal** correctos. Seleccione **Editar** para actualizar o rellenar los formularios necesarios.

Para establecer el estado fiscal, debe especificar el país o región de residencia y ciudadanía, y rellenar los formularios fiscales adecuados asociados a su país o región.

Independientemente de su país o región de residencia o ciudadanía, debe rellenar los formularios fiscales de los Estados Unidos para vender ofertas mediante Microsoft. Los asociados que cumplen determinados requisitos de residencia de los Estados Unidos deben rellenar un formulario IRS W-9. El resto de asociados de fuera de Estados Unidos deben rellenar un formulario de IRS W-8. Puede rellenar estos formularios en línea a medida que completa su perfil fiscal.

No es necesario tener el número de identificación fiscal individual (ITIN) de Estados Unidos para recibir pagos de Microsoft o para reclamar beneficios del tratado fiscal.

Puede completar y enviar los formularios fiscales electrónicamente desde el Centro de partners. En la mayoría de los casos, no es necesario imprimir y enviar por correo ningún formulario.

Los distintos países y regiones tienen requisitos fiscales diferentes. El importe exacto que debe pagar en impuestos dependerá de los países y regiones en los que desea vender sus ofertas. Microsoft remite las ventas y el impuesto sobre el consumo en su nombre en algunos países y regiones. Estos países y regiones se identificarán durante el proceso de publicación de la oferta. En otros países y regiones, según donde esté registrado, puede que tenga que remitir las ventas y los impuestos sobre el consumo directamente ante la autoridad fiscal local. Además, los ingresos por ventas que recibe pueden estar sujetos a impuestos como ingresos. Le recomendamos encarecidamente que se ponga en contacto con la autoridad pertinente del país o región que mejor le pueda ayudar a determinar la información fiscal adecuada para sus transacciones de ventas de Microsoft.

Para más información sobre un perfil fiscal, consulte [Creación y administración de perfiles fiscales y pagos de incentivos en Centro de partners](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) y [Configuración de la cuenta de pago y los formularios de impuestos](/partner-center/set-up-your-payout-account).

### <a name="withholding-rates"></a>Tasas de retención

La información que envíe en las declaraciones de impuestos determinará la tasa de retención fiscal adecuada. La tasa de retención se aplica solo a las ventas que se realizan en Estados Unidos. Las ventas realizadas en ubicaciones fuera de Estados Unidos no están sujetas a retenciones. Las tasas de retención varían, pero para la mayoría de los desarrolladores que están registrados fuera de Estados Unidos, la tasa predeterminada es del 30 %. Podrá reducir esta tasa si su país o región ha firmado con Estados Unidos un tratado tributario por ingresos.

### <a name="tax-treaty-benefits"></a>Beneficios por tratado tributario

Si reside fuera de Estados Unidos, puede aprovechar los beneficios por tratados tributarios. Estos beneficios varían según el país o la región, y puede que le permitan reducir el importe de los impuestos que retiene Microsoft. Puede reclamar los beneficios por tratado tributario rellenando la Parte II del formulario W-8BEN. Es recomendable que se ponga en contacto con los recursos adecuados de su país o región para determinar si estos beneficios se le pueden aplicar.

[Más información sobre detalles fiscales para desarrolladores de aplicaciones y juegos de Windows y editores de Azure Marketplace](/windows/uwp/publish/tax-details-for-paid-apps).

### <a name="payout-hold-status"></a>Estado de retención de pagos

De forma predeterminada, Microsoft envía los pagos mensualmente. No obstante, puede optar por poner sus pagos en espera, lo que impediría el envío de pagos a su cuenta. Si decide retener los pagos, se siguen registrando los ingresos que genera y se le proporcionan los detalles en el **Resumen de pagos**. Pero no se le envía ningún pago a la cuenta hasta que quite la retención.

**Para retener los pagos**:

1. Vaya a **Configuración de la cuenta**. 
1. En el panel de navegación izquierdo, expanda **Pago e impuestos** y seleccione **Perfiles fiscales y de pago**.
1. Seleccione el programa para el que quiere retener los pagos y, a continuación, active la casilla **Hold my Payment** (retener mi pago).

Puede cambiar el estado de retención de pagos en cualquier momento, pero tenga en cuenta que su decisión afectará a su próximo pago mensual. Por ejemplo, si quiere retener los pagos de abril, asegúrese de establecer el estado de retención de pagos en **Activado** antes del final de marzo.

Después de establecer el estado de la retención de pagos en **Activado**, todos los pagos se pondrán en espera hasta que desplace el control deslizante de nuevo a **Desactivado**. Una vez hecho esto, se le incluye en el siguiente ciclo de pago mensual (siempre que se hayan cumplido los umbrales de pago aplicables). Por ejemplo, si ha retenido los pagos pero quiere que se genere un pago en junio, asegúrese de alternar el estado de retención de pagos a **Desactivado** antes de finales de mayo.

> [!NOTE]
> La selección del **Estado de retención de pagos** se aplica a **todas** las fuentes de ingresos que se pagan a través del Centro de partners de Microsoft, incluidos Azure Marketplace, Microsoft AppSource, Microsoft Store, publicidad y otros). No puede seleccionar estados de retención de pagos diferentes para cada fuente de ingresos.

## <a name="devices"></a>Dispositivos

La configuración de administración de dispositivos solo se aplica a la publicación de la Plataforma universal de Windows (UWP). [Más información](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

## <a name="create-a-billing-profile"></a>Creación de un perfil de facturación

Si va a publicar una oferta de [Dynamics 365 for Customer Engagement y Power Apps](./partner-center-portal/create-new-customer-engagement-offer.md) o [Dynamics 365 for Operations](./partner-center-portal/create-new-operations-offer.md) tendrá que rellenar el *perfil de facturación*.

La dirección de facturación se rellena previamente a partir de la entidad legal y puede actualizarla más adelante. Los campos IMPUESTO y CIF son obligatorios para algunos países y opcionales para otros. No se puede editar el nombre del país o región y el nombre de la empresa.

1. En la parte superior derecha, seleccione **Configuración** (icono del engranaje) y, después, **Configuración del modelo de inquilino**.
1. A continuación, en el panel de navegación izquierdo, expanda **Perfil de organización** y seleccione **Perfil de facturación.**

## <a name="multi-user-account-management"></a>Administración de cuentas multiusuario

El Centro de partners usa [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) para la administración y el acceso a la cuenta multiusuario. La instancia de Azure AD de la organización se asocia automáticamente con la cuenta del Centro de partners como parte del proceso de inscripción.

## <a name="next-steps"></a>Pasos siguientes

- [Adición y administración de usuarios](add-manage-users.md)
