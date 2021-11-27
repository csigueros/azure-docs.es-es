---
title: Administración de Azure Portal para Contratos Enterprise directos
description: En este artículo, se explican las tareas comunes que realiza un administrador de empresa directo en Azure Portal.
author: bandersmsft
ms.author: banders
ms.date: 11/16/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: sapnakeshari
ms.openlocfilehash: 0cd07afa3b5bb63d017443ee4bc9de5e21f652ff
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718571"
---
# <a name="azure-portal-administration-for-direct-enterprise-agreements"></a>Administración de Azure Portal para Contratos Enterprise directos

En este artículo, se explican las tareas comunes que realiza un administrador de Contrato Enterprise (EA) directo en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes). Un Contrato Enterprise directo se firma entre Microsoft y un cliente de Contrato Enterprise.

Por el contrario, un Contrato Enterprise indirecto es aquel en el que un cliente firma un contrato con un asociado de Microsoft. Los Contratos Enterprise indirectos se administran mediante [Azure Enterprise Portal](https://ea.azure.com/). Para más información sobre la administración de Contratos Enterprise indirectos, consulte [Administración del portal del Contrato Enterprise de Azure](ea-portal-administration.md).

## <a name="manage-your-enrollment"></a>Administración de la inscripción

Para administrar el servicio, el administrador de empresa inicial abre  [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)  e inicia sesión con la dirección del correo electrónico de invitación.

Si se le ha configurado como administrador de empresa, vaya a Azure Portal e inicie sesión con la dirección de correo electrónico y la contraseña de su cuenta profesional, educativa o de Microsoft.

Si tiene más de una cuenta de facturación, seleccione una cuenta de facturación en el menú de ámbitos de facturación. Puede ver las propiedades y la directiva de la cuenta de facturación en el menú de la izquierda.

Consulte el vídeo de [administración de inscripción de EA](https://www.youtube.com/watch?v=NUlRrJFF1_U). Forma parte de la serie de vídeos [Experiencia de facturación del cliente de Enterprise directa en Azure Portal](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm).

>[!VIDEO https://www.youtube.com/embed/NUlRrJFF1_U]

## <a name="select-a-billing-scope"></a>Selección de un ámbito de facturación

Los Contratos Enterprise y los clientes que acceden a los contratos pueden tener varias inscripciones. Un usuario puede tener acceso a varios ámbitos de inscripción (ámbitos de cuenta de facturación). Toda la información y la actividad de Azure Portal están en el contexto de un ámbito de cuenta de facturación. Es importante que el administrador de empresa seleccione primero un ámbito de facturación y, a continuación, realice las tareas administrativas.

### <a name="to-select-a-billing-scope"></a>Para seleccionar un ámbito de facturación

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Busque **Cost Management + Billing** y selecciónelo.  
    :::image type="content" source="./media/direct-ea-administration/search-cost-management.png" alt-text="Captura de pantalla que muestra la búsqueda de Cost Management + Billing." lightbox="./media/direct-ea-administration/search-cost-management.png" :::
1. Seleccione **Ámbitos de facturación** en el menú de navegación y luego seleccione la cuenta de facturación con la que quiere trabajar.  
    :::image type="content" source="./media/direct-ea-administration/select-billing-scope.png" alt-text="Captura de pantalla que muestra la selección de una cuenta de facturación." lightbox="./media/direct-ea-administration/select-billing-scope.png" :::

## <a name="view-enrollment-details"></a>Visualización de los detalles de la inscripción

Un administrador de empresa de Azure (administrador de EA) puede ver y administrar las propiedades y la directiva de la inscripción para asegurarse de que las opciones de la inscripción estén configuradas correctamente.

### <a name="to-view-enrollment-properties"></a>Para ver las propiedades de la inscripción

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación.
1. En el menú de navegación, en **Configuración**, seleccione **Propiedades**.
1. Consulte la información de la cuenta de facturación.  
    :::image type="content" source="./media/direct-ea-administration/billing-account-properties.png" alt-text="Captura de pantalla que muestra las propiedades de la cuenta de facturación." lightbox="./media/direct-ea-administration/billing-account-properties.png" :::

### <a name="view-and-manage-enrollment-policies"></a>Visualización y administración de directivas de inscripción

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación.
1. En el menú de navegación seleccione **Directivas**.
1. Para habilitar o deshabilitar las directivas, seleccione **Activar** o **Desactivar**.  
    :::image type="content" source="./media/direct-ea-administration/enrollment-policies.png" alt-text="Captura de pantalla que muestra las directivas de inscripción de E A." lightbox="./media/direct-ea-administration/enrollment-policies.png" :::

[Consulte los precios de los diferentes roles de usuario](understand-ea-roles.md#see-pricing-for-different-user-roles) para más información sobre el administrador del departamento (DA) y el propietario de la cuenta (AO).

## <a name="add-another-enterprise-administrator"></a>Adición de otro administrador de empresa

Los administradores de EA existentes son los únicos que pueden crear otros administradores de empresa. Use algunas de las siguientes opciones según su situación.

### <a name="if-youre-already-an-enterprise-administrator"></a>Si ya es un administrador de empresa

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación.
1. En el menú izquierdo, seleccione **Control de acceso (IAM)**.
1. En el menú superior, seleccione **+ Agregar** y, a continuación, seleccione **Administrador de empresa**.  
    :::image type="content" source="./media/direct-ea-administration/add-enterprise-admin-navigate.png" alt-text="Captura de pantalla que muestra como ir a la opción del Administrador de empresa." lightbox="./media/direct-ea-administration/add-enterprise-admin-navigate.png" :::
1. Complete el formulario Agregar asignación de roles y, a continuación, seleccione **Agregar**.

Asegúrese de tener a mano la dirección de correo electrónico del usuario y el método de autenticación preferido, como cuenta profesional, educativa o de Microsoft.

Un administrador de EA puede administrar el acceso de los administradores de empresa existentes seleccionando el símbolo de puntos suspensivos (**...**) situado a la derecha de cada usuario. Puede **editar** y **eliminar** usuarios existentes.

### <a name="if-youre-not-an-enterprise-administrator"></a>Si no es administrador de empresa

Si no es administrador de EA, póngase en contacto con el administrador de EA para solicitarle que le agregue a una inscripción. El administrador de EA usa los pasos anteriores para agregarle como administrador de empresa. Una vez que se haya agregado a una inscripción, recibirá un correo electrónico de activación.

### <a name="if-your-enterprise-administrator-cant-help-you"></a>Si el administrador de empresa no puede ayudarle

Si el administrador de su empresa no puede ayudarle, cree una  [solicitud de soporte técnico de Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Proporcione la siguiente información:

- Número de inscripción
- Dirección de correo electrónico que se debe agregar y tipo de autenticación (cuenta profesional, educativa o de Microsoft)
- Aprobación por correo electrónico por parte de un administrador de empresa existente

Si el administrador de empresa existente no está disponible, póngase en contacto con su partner o asesor de software para solicitarle que cambie los detalles del contacto a través de la herramienta Centro de servicios de licencias por volumen (VLSC).

## <a name="create-an-azure-enterprise-department"></a>Creación de un departamento empresarial de Azure

Los administradores de EA y los administradores de departamento usan los departamentos para organizar e informar sobre los servicios empresariales de Azure y el uso por departamento y centro de costos. El administrador de empresa puede:

- Agregar o quitar departamentos.
- Asociar una cuenta a un departamento.
- Crear administradores de departamento.
- Permitir a los administradores de departamento ver el precio y los costos.

Un administrador de departamento puede agregar cuentas a sus departamentos. Puede quitar cuentas de sus departamentos, pero no de la inscripción.

Consulte el vídeo de [administración de departamentos de EA](https://www.youtube.com/watch?v=NUlRrJFF1_U). Forma parte de la serie de vídeos [Experiencia de facturación del cliente de Enterprise directa en Azure Portal](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm).

>[!VIDEO https://www.youtube.com/embed/cxAtOSSE6UI]

### <a name="to-create-a-department"></a>Para crear un departamento

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación.
1. En el menú de navegación de la izquierda, seleccione **Departamentos**.
1. Seleccione **+Agregar**.  
    :::image type="content" source="./media/direct-ea-administration/add-department.png" alt-text="Captura de pantalla que muestra la navegación para agregar un departamento." lightbox="./media/direct-ea-administration/add-department.png" :::
1. Especifique la información en el formulario Agregar nuevo departamento. El nombre del departamento es el único campo obligatorio. Debe tener tres caracteres como mínimo.
1. Cuando haya terminado, seleccione **Guardar**.

## <a name="add-a-department-administrator"></a>Adición de un administrador de departamento

Una vez creado un departamento, el administrador de EA puede agregar administradores de departamento y asociar cada uno de ellos a un departamento. Los administradores de departamento pueden realizar las siguientes acciones para sus departamentos:

- Crear otros administradores de departamento
- Ver y editar propiedades de departamento, como el nombre o el centro de costos
- Adición de cuentas
- Quitar cuentas
- Descargar detalles de uso
- Ver el uso y los cargos mensuales <sup>1</sup>

 <sup>1</sup> Un administrador de EA debe conceder los permisos.

### <a name="to-add-a-department-administrator"></a>Para agregar un administrador de departamento

Como administrador de empresa:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación.
1. En el menú de navegación, seleccione **Departamentos**.
1. Seleccione el departamento para el que quiere agregar un administrador.
1. En la vista de departamento, seleccione **Control de acceso (IAM)**.
1. Seleccione **+ Agregar** y, a continuación, seleccione **Administrador de departamento**.
1. Escriba la dirección de correo electrónico y otra información necesaria.
1. Para que el acceso sea de solo lectura, establezca la opción **Solo lectura** en **Sí** y seleccione **Agregar**.  
    :::image type="content" source="./media/direct-ea-administration/add-department-admin.png" alt-text="Captura de pantalla que muestra como ir a la opción del Administrador de departamento." lightbox="./media/direct-ea-administration/add-department-admin.png" :::

### <a name="to-set-read-only-access"></a>Para establecer acceso de solo lectura

Los administradores de EA pueden conceder acceso de solo lectura a los administradores de departamento. Cuando cree un administrador de departamento, establezca la opción de solo lectura en **Sí**.

Para editar un administrador de departamento existente:

1. Seleccione el símbolo de puntos suspensivos (**...**) situado a la derecha de una fila y selecciónelo.
1. Establezca la opción de solo lectura en   **Sí** y, a continuación, seleccione  **Aplicar**.

Los administradores de empresa obtienen automáticamente permisos de administrador de departamento.

## <a name="add-an-account-and-account-owner"></a>Adición de una cuenta y un propietario de cuenta

La estructura de las cuentas y suscripciones afecta al modo en que se administran y cómo aparecen en las facturas e informes. Entre los ejemplos de estructura típica de una organización se incluye las divisiones empresariales, los equipos funcionales y las ubicaciones geográficas.

Después de agregar una nueva cuenta a la inscripción, se envía al propietario de la cuenta un correo electrónico de propiedad de la cuenta que se usa para confirmar la propiedad.

Consulte el vídeo de [administración de cuentas de EA](https://www.youtube.com/watch?v=VKWAEx6qfPc). Forma parte de la serie de vídeos [Experiencia de facturación del cliente de Enterprise directa en Azure Portal](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm).

>[!VIDEO https://www.youtube.com/embed/VKWAEx6qfPc]

### <a name="to-add-an-account-and-account-owner"></a>Para agregar una cuenta y un propietario de cuenta

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación.
1. En el menú de la izquierda, seleccione **Cuentas**.
1. Seleccione **+Agregar**.
1. En la página Agregar una cuenta, escriba un nombre descriptivo para identificar la cuenta, que se usará para los informes.
1. Escriba la dirección de **correo electrónico del propietario de la cuenta** que se va a asociar a la nueva cuenta.
1. Seleccione un departamento o déjelo sin asignar.
1. Cuando haya terminado, seleccione **Agregar**.

### <a name="to-confirm-account-ownership"></a>Para confirmar la propiedad de la cuenta

Una vez que el propietario de la cuenta reciba el correo electrónico de propiedad de la cuenta, debe confirmar su propiedad.

1. Inicie sesión en la cuenta de correo electrónico asociada a la cuenta profesional, educativa o de Microsoft que se estableció como propietario de la cuenta.
1. Abra la notificación de correo electrónico titulada _Invitación para activar su cuenta en el servicio Microsoft Azure_.
1. Seleccione el vínculo **Activar cuenta** en la invitación.
1. Inicie sesión en Azure Portal.
1. En la página Activar cuenta, seleccione **Sí, deseo continuar** para confirmar la propiedad de la cuenta.  
    :::image type="content" source="./media/direct-ea-administration/activate-account.png" alt-text="Captura de pantalla que muestra la página Activar cuenta." lightbox="./media/direct-ea-administration/activate-account.png" :::

Una vez confirmada la propiedad de la cuenta, puede crear suscripciones y comprar recursos con las suscripciones.

## <a name="change-azure-subscription-or-account-ownership"></a>Cambio de la propiedad de la cuenta o de la suscripción de Azure

Los administradores de EA pueden usar Azure Portal para transferir la propiedad de la cuenta de todas o las suscripciones seleccionadas de una inscripción. Cuando se completa la transferencia de una suscripción o de la propiedad de una cuenta, Microsoft actualiza el propietario de la cuenta.

Antes de iniciar la transferencia de propiedad, familiarícese con las siguientes directivas del control de acceso basado en rol de Azure (RBAC de Azure):

- Al realizar transferencias de propiedad de suscripción o cuenta entre dos identificadores de organización dentro del mismo inquilino, se conservan los siguientes elementos:
    - Directivas de RBAC de Azure
    - Administrador del servicios existente
    - Roles de coadministrador
- Las transferencias de suscripciones o de propiedad de cuentas entre inquilinos provocan la pérdida no solo de las directivas de Azure RBAC, sino también de las asignaciones de roles.
- Las directivas y los roles de administrador no se transfieren entre los distintos directorios. Los administradores de servicios se actualizan al propietario de la cuenta de destino.
- Para evitar la pérdida de las directivas de RBAC de Azure y las asignaciones de roles al transferir la suscripción de un inquilino a otro, asegúrese de que la opción **Move the subscriptions to the recipient’s Azure AD tenant** (Mover las suscripciones al inquilino de Azure AD del destinatario) no esté seleccionada. Esta selección conserva los servicios, los roles de Azure y las directivas del inquilino de Azure AD actual y solo transfiere la propiedad de facturación de la cuenta.

Antes de cambiar el propietario de una cuenta:

1. Vea la pestaña **Account** (Cuenta) e identifique la cuenta de origen. La cuenta de origen debe estar activa.
1. Identifique la cuenta de destino y asegúrese de que esté activa.

Para transferir la propiedad de la cuenta para todas las suscripciones:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación.
1. En el menú de la izquierda, seleccione **Cuentas**.
1. Seleccione una **cuenta** y, a continuación, seleccione el símbolo de puntos suspensivos (**…**) situado a la derecha de la fila.
1. Seleccione **Transfer subscriptions** (Transferir suscripciones).  
    :::image type="content" source="./media/direct-ea-administration/transfer-subscriptions-01.png" alt-text="Captura de pantalla que muestra dónde transferir suscripciones." lightbox="./media/direct-ea-administration/transfer-subscriptions-01.png" :::
1. En la página Transfer subscriptions (Transferir suscripciones), seleccione la cuenta de destino a la que se transferirá y, a continuación, seleccione **Next** (Siguiente).
1. Si desea transferir la propiedad de la cuenta entre inquilinos de Azure AD, seleccione la confirmación **Yes, I would also like to move the subscriptions to the new account's Azure AD tenant** (Sí, también quiero mover las suscripciones al inquilino de Azure AD de la nueva cuenta).
1. Confirme la transferencia y seleccione  **Submit** (Enviar).  
    :::image type="content" source="./media/direct-ea-administration/transfer-account-confirmation.png" alt-text="Captura de pantalla que muestra la confirmación de la transferencia de la suscripción." lightbox="./media/direct-ea-administration/transfer-account-confirmation.png" :::

Para transferir la propiedad de la cuenta para una sola suscripción:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación.
1. En el menú de la izquierda, seleccione **Suscripciones de Azure**.
1. En la página Suscripciones de Azure, seleccione el símbolo de puntos suspensivos (**...**) situado a la derecha de cada suscripción.
1. Seleccione **Transferir suscripción**.
1. En la página Transferir suscripción, seleccione la cuenta de destino para transferir la suscripción y, a continuación,seleccione   **Siguiente**.
1. Si desea transferir la propiedad de la suscripción entre inquilinos de Azure AD, seleccione la opción **Yes, I would like to also move the subscriptions to the to the new account's Azure AD tenant** (Sí, también quiero mover las suscripciones al inquilino de Azure AD de la nueva cuenta).
1. Confirme la transferencia y seleccione **Enviar**.

## <a name="associate-an-account-to-a-department"></a>Asociar una cuenta a un departamento

Los administradores de EA pueden asociar las cuentas existentes a los departamentos de la inscripción.

### <a name="to-associate-an-account-to-a-department"></a>Para asociar una cuenta a un departamento

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación.
1. En el menú de la izquierda, seleccione **Cuentas**.
1. Seleccione el símbolo de puntos suspensivos (**...**) situado a la derecha de cualquier cuenta y, a continuación, seleccione **Editar**.
1. En la página Editar cuenta, seleccione un **Departamento** de la lista.
1. Seleccione  **Guardar**.

## <a name="associate-an-account-with-a-pay-as-you-go-subscription"></a>Asociación de una cuenta a una suscripción de pago por uso

Si ya tiene una cuenta educativa, profesional o de Microsoft existente para una suscripción de pago por uso de Azure, puede asociarla a la inscripción del Contrato Enterprise.

Para asociar una cuenta, debe tener el rol Administrador de EA o Administrador de departamento.

### <a name="to-associate-an-existing-account"></a>Para asociar una cuenta existente

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación.
1. En el menú de la izquierda, seleccione **Cuentas**.
1. Seleccione **+Agregar**.
1. En la página Agregar una cuenta, escriba el nombre que desea usar para identificar la cuenta con fines informativos.
1. Seleccione la cuenta de **Microsoft** o la **cuenta profesional o educativa** asociada a la cuenta de Azure existente.
1. Escriba la dirección de correo electrónico del propietario de la cuenta y confírmela.
1. Seleccione **Agregar**.

La cuenta puede tardar hasta ocho horas en aparecer en Azure Portal.

### <a name="to-confirm-account-ownership"></a>Para confirmar la propiedad de la cuenta

1. Inicie sesión en la cuenta de correo electrónico asociada a la cuenta profesional, educativa o de Microsoft que ha asociado en el procedimiento anterior.
1. Abra la notificación de correo electrónico titulada _Invitación para activar su cuenta en el servicio Microsoft Azure_.
1. Seleccione el vínculo **Activar cuenta** en la invitación.
1. Inicie sesión en Azure Portal.
1. En la página Activar cuenta, seleccione **Sí, deseo continuar** para confirmar la propiedad de la cuenta.  
    :::image type="content" source="./media/direct-ea-administration/activate-account.png" alt-text="Captura de pantalla que muestra la página Activar cuenta." lightbox="./media/direct-ea-administration/activate-account.png" :::

## <a name="enable-azure-marketplace-purchases"></a>Habilitación de las compras en Azure Marketplace

Aunque la mayoría de las _suscripciones_ de pago por uso se pueden asociar a un Contrato Enterprise, los _servicios_ de Azure Marketplace comprados previamente no. Para obtener una vista única de todas las suscripciones y cargos, se recomienda habilitar las compras de Azure Marketplace.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación.
1. En el menú izquierdo, seleccione **Directivas**.
1. En **Azure Marketplace**, establezca la directiva en **Activada**.  
   :::image type="content" source="./media/direct-ea-administration/azure-marketplace.png" alt-text="Captura de pantalla que muestra la configuración de la directiva de Azure Marketplace." lightbox="./media/direct-ea-administration/azure-marketplace.png" :::

El propietario de la cuenta ahora puede volver a comprar cualquier servicio de Azure Marketplace que se hubiera adquirido anteriormente en la suscripción de pago por uso.

La configuración se aplica a todos los propietarios de cuenta de la inscripción. Les permite realizar compras en Azure Marketplace.

Después de que se activen las suscripciones de la inscripción de EA de Azure, cancele los servicios de Azure Marketplace que se crearon con la suscripción de pago por uso. Este paso es crítico en caso de que expire el instrumento de pago de la suscripción de pago por uso.

## <a name="msdn-subscription-transfer"></a>Transferencia de suscripciones de MSDN

Cuando se transfiere una suscripción de MSDN a una inscripción de EA, se convierte automáticamente en una suscripción de desarrollo y pruebas de MSDN. Después de la conversión, la suscripción pierde cualquier crédito monetario existente. Por lo tanto, se recomienda usar todo el crédito antes de transferirla al Contrato Enterprise.

## <a name="azure-in-open-subscription-transfer"></a>Transferencia de suscripciones de Azure bajo licencia Open

Al transferir una suscripción de Azure bajo licencia Open a un Contrato Enterprise, se pierden los créditos sin usar de Azure bajo licencia Open. Se recomienda usar todo el crédito de la suscripción de Azure bajo licencia Open antes de transferirla al Contrato Enterprise.

## <a name="subscription-transfers-with-support-plans"></a>Transferencias de suscripciones con planes de soporte técnico

Si el Contrato Enterprise no tiene un plan de soporte técnico e intenta transferir una suscripción existente del Contrato de soporte técnico en línea de Microsoft (MOSA) que tenga un plan de soporte técnico, la suscripción no se transferirá automáticamente. Tendrá que volver a comprar un plan de soporte técnico para la inscripción de EA durante el período de gracia, que se extiende hasta finales del mes siguiente.

## <a name="manage-department-and-account-spending-with-budgets"></a>Administración de los gastos de departamentos y cuentas con presupuestos

Los clientes de Contrato Enterprise pueden establecer presupuestos para cada departamento y cuenta de una inscripción. Los presupuestos en Cost Management le ayudan a planear y dirigir la presentación de cuentas de la organización. Le ayudan a informar a otros usuarios sobre sus gastos a fin de administrar de manera proactiva los costos y supervisar cómo avanza el gasto a lo largo del tiempo. Puede configurar alertas en función del costo real o el costo previsto para asegurarse de que el gasto se encuentre dentro de los límites de gasto de la organización. Cuando se superan los umbrales presupuestarios que ha creado, solo se desencadenan las notificaciones. Ninguno de los recursos se ve afectado y no se detiene el consumo. Puede usar los presupuestos para comparar y realizar un seguimiento de gastos para analizar los costos. Para más información sobre cómo crear presupuestos, consulte [Tutorial: Creación y administración de presupuestos de Azure](../costs/tutorial-acm-create-budgets.md).

## <a name="enterprise-agreement-user-roles"></a>Roles de usuario del Contrato Enterprise

Azure Portal le ayuda a administrar los costos y el uso del Contrato Enterprise de Azure. Hay tres roles principales del Contrato Enterprise:

- Administrador de Contrato Enterprise (EA)
- Administrador de departamentos
- Propietario de cuenta

Cada rol tiene un nivel de acceso y autoridad diferente. Para más información sobre los roles de usuario, consulte  [Roles de los usuarios de empresa](understand-ea-roles.md#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Adición de una cuenta del Contrato Enterprise de Azure

Una cuenta del Contrato Enterprise de Azure es una unidad organizativa en Azure Portal. En Azure Portal, se conoce como _cuenta_. Se utiliza para administrar las suscripciones y los informes. Para acceder a los servicios de Azure y usarlos, debe crear una cuenta o tener una ya creada. Para más información sobre las cuentas, consulte [Adición de una cuenta y un propietario de cuenta](#add-an-account-and-account-owner).

## <a name="enable-the-enterprise-devtest-offer"></a>Habilitación de la oferta Desarrollo/pruebas - Enterprise

Como administrador de EA, puede permitir a los propietarios de cuentas de la organización crear suscripciones basadas en la oferta Desarrollo/pruebas - Enterprise. Para ello, seleccione la opción **Desarrollo/pruebas** en las propiedades de la cuenta. Una vez que haya activado la opción Desarrollo/pruebas, comuníqueselo al propietario de la cuenta para que pueda crear las suscripciones de Desarrollo/pruebas del Contrato Enterprise necesarias para sus equipos de suscriptores de desarrollo y pruebas. La oferta permite a los suscriptores activos de Visual Studio ejecutar cargas de trabajo de desarrollo y pruebas en Azure a las tarifas especiales de desarrollo y pruebas. Proporciona acceso a toda la galería de imágenes de desarrollo y pruebas incluido Windows 8.1 y Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Para configurar la oferta Desarrollo/pruebas - Enterprise

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación.
1. En el menú de la izquierda, seleccione **Cuentas**.
1. Seleccione la cuenta en la que desea habilitar el acceso a Desarrollo/pruebas.
1. En la página de la cuenta de la inscripción, seleccione **Editar**.
1. En la página Editar cuenta, seleccione **Desarrollo/pruebas** y, a continuación, seleccione **Guardar**.

Cuando se agrega un usuario como propietario de la cuenta, las suscripciones de Azure asociadas con el usuario que se basan en la oferta Desarrollo/pruebas de pago por uso, o en las ofertas de crédito mensuales para suscriptores de Visual Studio, se convertirán en la oferta Desarrollo/pruebas - Enterprise. Las suscripciones basadas en otros tipos de oferta, como Pago por uso, asociadas al propietario de la cuenta se convertirán en ofertas de Microsoft Azure Enterprise.

Actualmente, la oferta Desarrollo/pruebas no es aplicable a los clientes de Azure Gov.

## <a name="create-a-subscription"></a>una suscripción

Los propietarios de cuentas pueden ver y administrar suscripciones. Las suscripciones se pueden usar para dar acceso a los equipos de la organización a proyectos y entornos de desarrollo. Por ejemplo: 

- Prueba
- Producción
- Desarrollo
- Ensayo

La creación de distintas suscripciones para cada entorno de aplicación ayuda a proteger cada entorno.

- También se puede asignar una cuenta de administrador de servicios diferente a cada suscripción.
- Se pueden asociar las suscripciones a cualquier número de servicios.
- El propietario de la cuenta crea suscripciones y asigna una cuenta de administrador de servicios a cada una de las suscripciones de su cuenta.

Consulte el vídeo de [administración de suscripciones de EA](https://www.youtube.com/watch?v=KFfcg2eqPo8). Forma parte de la serie de vídeos [Experiencia de facturación del cliente de Enterprise directa en Azure Portal](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm).

>[!VIDEO https://www.youtube.com/embed/KFfcg2eqPo8]

## <a name="add-a-subscription"></a>Agregar una suscripción

Los propietarios de cuenta crean suscripciones dentro de su cuenta de inscripción. La primera vez que agrega una suscripción a la cuenta, se le pide que acepte el Contrato Microsoft Online Subscription (MOSA) y un plan de tarifas. Aunque ni MOSA ni el plan de tarifas se aplican a los clientes con Contrato Enterprise, son necesarios para crear la suscripción. La inscripción del Contrato Enterprise de Microsoft Azure sustituye a los elementos anteriores y la relación contractual no cambia. Cuando se le solicite, seleccione la opción que indica que acepta los términos.

_Microsoft Azure Enterprise_  es el nombre predeterminado cuando se crea una suscripción. Puede cambiar el nombre para diferenciarla de las demás suscripciones de la inscripción y para asegurarse de reconocerla en los informes de empresa.

### <a name="to-add-a-subscription"></a>Para agregar una suscripción

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación para la inscripción.
1. En el menú de la izquierda, seleccione **Cuentas**.
1. En la lista de cuentas de la inscripción, seleccione aquella en la que quiere crear una suscripción.
1. En la página de la cuenta de la inscripción, seleccione **+ Nueva suscripción**.  
    :::image type="content" source="./media/direct-ea-administration/new-subscription.png" alt-text="Captura de pantalla que muestra la opción de nueva suscripción." lightbox="./media/direct-ea-administration/new-subscription.png" :::
1. En la página Crear suscripción, escriba un nombre para la suscripción y, a continuación, seleccione **Crear**.

El nombre de la suscripción aparece en los informes. Se trata del nombre del proyecto asociado a la suscripción en el portal de desarrollo.

Las nuevas suscripciones pueden tardar hasta 24 horas en aparecer en la lista de suscripciones. Después de crear una suscripción, puede:

- Editar los detalles de la suscripción
- Administrar los servicios de la suscripción

También puede crear suscripciones si va a la página Suscripciones de Azure y selecciona **+ Agregar**.

## <a name="cancel-a-subscription"></a>Cancelación de una suscripción

Solo los propietarios de cuentas pueden cancelar sus propias suscripciones.

Para eliminar una suscripción en la que es el propietario de la cuenta:

1. Inicie sesión en Azure Portal con las credenciales asociadas a la cuenta.
1. En el menú de navegación, seleccione **Suscripciones**.
1. Seleccione una suscripción.
1. En la página de detalles de la suscripción, en la esquina superior izquierda de la página, seleccione **Cancelar suscripción**.
1. Escriba el nombre de la suscripción, elija el motivo de la cancelación y, a continuación, seleccione el botón **Cancelar**.

Para más información, consulte  [¿Qué ocurre después de la cancelación de una suscripción?](cancel-azure-subscription.md#what-happens-after-subscription-cancellation).

## <a name="delete-an-enrollment-account"></a>Eliminación de una cuenta de inscripción

Solo puede eliminar una cuenta de inscripción cuando no haya suscripciones activas.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Vaya a **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Ámbitos de facturación** y, a continuación, seleccione un ámbito de cuenta de facturación.
1. En el menú de la izquierda, seleccione **Cuentas**.
1. En la lista Cuentas, busque la cuenta que desea eliminar.
1. En la fila de la cuenta que desea eliminar, seleccione los puntos suspensivos (**...**) y, a continuación, seleccione **Eliminar**.
1. En la página Eliminar cuenta, seleccione la confirmación **Sí, quiero eliminar esta cuenta** y, a continuación, seleccione **Eliminar**.

## <a name="azure-sponsorship-offer"></a>Oferta Patrocinio de Azure

La oferta Patrocinio de Azure es una cuenta de Microsoft Azure patrocinada limitada. Está disponible mediante invitación por correo electrónico solo para un número limitado de clientes seleccionados por Microsoft. Si es apto para la oferta Patrocinio de Microsoft Azure, recibirá una invitación por correo electrónico a su identificador de cuenta.

Para más información, cree una  [solicitud de soporte técnico para la activación del patrocinio](https://aka.ms/azrsponsorship).

## <a name="convert-to-work-or-school-account-authentication"></a>Conversión a la autenticación de la cuenta profesional o educativa

Los usuarios Enterprise de Azure pueden convertir desde una cuenta de Microsoft (MSA o Live ID) a una cuenta profesional o educativa. Una cuenta profesional o educativa usa el tipo de autenticación de Azure Active Directory.

### <a name="to-begin"></a>Para empezar

1. Agregue la cuenta profesional o educativa a Azure Portal en los roles necesarios.
1. Si recibe errores, es posible que la cuenta no sea válida en Azure Active Directory. Azure usa el nombre principal de usuario (UPN), que no es siempre idéntico a la dirección de correo electrónico.
1. Inicie sesión en Azure Portal con la cuenta profesional o educativa.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Para convertir suscripciones de cuentas de Microsoft a cuentas profesionales o educativas

1. Inicie sesión en Azure Portal con la cuenta de Microsoft que es propietaria de las suscripciones.
1. Use la transferencia de propiedad de la cuenta para pasar a la nueva cuenta.
1. La cuenta de Microsoft debe estar libre de suscripciones activas y se puede eliminar.
1. Las cuentas eliminadas siguen apareciendo en Azure Portal con estado inactivo por motivos del historial de facturación. Para filtrarlas de la vista, seleccione **Show only active accounts** (Mostrar solo cuentas activas).

## <a name="azure-ea-term-glossary"></a>Glosario de términos de Azure EA

**Cuenta**<br>
Una unidad organizativa. Se utiliza para administrar las suscripciones y para los informes.

**Propietario de cuenta**<br>
Persona que administra las suscripciones y los administradores de servicios en Azure. Pueden ver los datos de uso de esta cuenta y sus suscripciones asociadas.

**Suscripción de modificación**<br>
Suscripción de un año o coincidente de la enmienda de la inscripción.

**Pago por adelantado**<br>
pago por adelantado de una cantidad monetaria anual por los servicios de Azure con una tasa de prepago con descuento por el uso de este pago por adelantado.

**Administrador de departamento**<br>
Persona que administra departamentos, crea cuentas nuevas y propietarios de cuentas, ve los detalles de uso de los departamentos que administra y ve los costos cuando se le conceden permisos.

**Número de inscripción**<br>
Identificador único proporcionado por Microsoft para identificar la inscripción específica asociada a un Contrato Enterprise.

**Administrador de Enterprise**<br>
Persona que administra departamentos, propietarios de departamentos, cuentas y propietarios de cuentas en Azure. Pueden administrar los administradores de empresa y ver los datos de uso, las cantidades facturadas y los cargos no facturados en todas las cuentas y suscripciones asociadas a la inscripción de la empresa.

**Contrato Enterprise**<br>
un contrato de licencias de Microsoft para clientes con adquisición centralizada que desean estandarizar toda la organización en la tecnología de Microsoft y mantener una infraestructura de tecnología de la información en un estándar de software de Microsoft.

**Inscripción de Contrato Enterprise**<br>
Inscripción en el programa de Contrato Enterprise que proporciona productos de Microsoft en volumen a tarifas con descuento.

**Cuenta Microsoft**<br>
Servicio basado en web que permite a los sitios participantes autenticar a un usuario con un único conjunto de credenciales.

**Modificación de la inscripción de Microsoft Azure Enterprise (modificación de la inscripción)**<br>
Enmienda firmada por una empresa, que proporciona acceso a Azure como parte de la inscripción empresarial.

**Cantidad de recursos consumidos**<br>
Cantidad de un servicio individual de Azure que se usó en un mes.

**Administrador de servicios**<br>
Persona que accede y administra suscripciones y proyectos de desarrollo.

**Suscripción**<br>
Representa una suscripción de Contrato Enterprise de Azure y es un contenedor de servicios de Azure administrados por el mismo administrador de servicios.

**Cuenta profesional o educativa**<br>
Para organizaciones que han configurado Azure Active Directory con Federación en la nube y todas las cuentas están en un solo inquilino.

## <a name="enrollment-status"></a>Estado de inscripción

**Nuevo**<br>
Este estado se asigna a una inscripción que se creó hace menos de 24 horas y que se actualizará a un estado pendiente en un plazo máximo de 24 horas.

**Pendiente**<br>
El administrador de la inscripción debe iniciar sesión en Azure Portal. Una vez que inicia sesión, la inscripción cambiará a estado Activa.

**Activo**<br>
La inscripción está activa y se pueden crear cuentas y suscripciones en Azure Portal. La inscripción permanecerá activa hasta la fecha de finalización del Contrato Enterprise.

**Período extendido indefinido**<br>
Un período extendido indefinido tiene lugar después de que se haya alcanzado la fecha de finalización del Contrato Enterprise. Permite a los clientes de Azure EA que participan en el período extendido seguir usando los servicios de Azure indefinidamente al final de su Contrato Enterprise.

Antes de que la inscripción de Azure EA alcance la fecha de finalización del Contrato Enterprise, el administrador de inscripciones debe decidir entre las opciones siguientes:

- Renovar la inscripción mediante la adición de un nuevo pago por adelantado de Azure.
- Transferirla a una nueva inscripción.
- Migrar al Programa de suscripción en línea de Microsoft (MOSP).
- Confirmar la deshabilitación de todos los servicios asociados con la inscripción.

**Expired**<br>
El cliente de Azure EA no participa en el período extendido, y la inscripción de Azure EA ha alcanzado la fecha de finalización del Contrato Enterprise. La inscripción expirará, y se deshabilitarán todos los servicios asociados.

**Transferida**<br>
Las inscripciones en las que se hayan transferido a una nueva inscripción todas las cuentas y servicios asociados aparecen con estado Transferida.
 > [!NOTE]
 > Las inscripciones no se transfieren automáticamente si se genera un nuevo número de inscripción en el momento de la renovación. Debe incluir el número de inscripción anterior en la solicitud de renovación para facilitar la transferencia automática.

## <a name="next-steps"></a>Pasos siguientes

- Si tiene que crear una solicitud de soporte técnico de Azure para la inscripción de EA, consulte [Creación de una solicitud de soporte técnico de Azure para un problema de Contrato Enterprise](how-to-create-azure-support-request-ea.md).
- Lea las [preguntas frecuentes de Cost Management + Billing](../cost-management-billing-faq.yml) para más información sobre la propiedad de la suscripción de EA.