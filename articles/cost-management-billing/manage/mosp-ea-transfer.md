---
title: Transferencia de una suscripción de Azure a un Contrato Enterprise
description: Este artículo le ayuda a comprender los pasos para transferir un Contrato de cliente de Microsoft o una suscripción de MOSP a un Contrato Enterprise.
author: bandersmsft
ms.reviewer: jatracey
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/12/2021
ms.author: banders
ms.custom: ''
ms.openlocfilehash: 4350a36c63e8ea6be4284baeaba3a60d1de9099b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007154"
---
# <a name="transfer-an-azure-subscription-to-an-enterprise-agreement-ea"></a>Transferencia de una suscripción de Azure a un Contrato Enterprise (EA)

Este artículo le ayuda a comprender los pasos para transferir un Contrato de cliente de Microsoft o una suscripción de MOSP (pago por uso) a un Contrato Enterprise. La transferencia no tiene tiempo de inactividad, pero hay que seguir muchos pasos para habilitar la transferencia.

> [!NOTE]
> El proceso de transferencia no cambia la información de Azure AD Directory a la que están vinculadas las suscripciones. Si desea realizar un cambio de Azure AD Directory, consulte [Transferencia de una suscripción de Azure a otro directorio de Azure AD](../../role-based-access-control/transfer-subscription.md).

## <a name="prerequisites"></a>Requisitos previos

Las secciones siguientes le ayudan a identificar el administrador de la cuenta de suscripción y el acceso necesario al EA de destino, así como a establecer el nivel de autenticación de EA.

### <a name="identify-the-subscription-account-admin"></a>Identificación del administrador de la cuenta de suscripción

Identifique al administrador de la cuenta de suscripción iniciando sesión en Azure Portal con una cuenta que tenga al menos el rol de lector de RBAC de Azure para la suscripción. Vaya a **Suscripciones** > seleccione la suscripción > **Propiedades**. Se muestra el **administrador de la cuenta** de suscripción. Tome nota de la información de usuario.

:::image type="content" source="./media/mosp-ea-transfer/subscription-account-admin.png" alt-text="Imagen que muestra las propiedades de la suscripción donde puede ver el administrador de la cuenta" lightbox="./media/mosp-ea-transfer/subscription-account-admin.png" :::

> [!NOTE]
> Debe iniciar sesión en el portal de Contrato Enterprise (EA) más adelante con la misma cuenta de usuario administrador de cuenta, como se describe en la información anterior. Si no tiene acceso a la cuenta, debe obtenerlo antes de continuar. Para más información, consulte [Transferencia de la propiedad de facturación de una suscripción de Azure a otra cuenta](billing-subscription-transfer.md).

### <a name="access-to-the-destination-ea"></a>Acceso al EA de destino

Debe tener acceso al EA de destino para crear un propietario de la cuenta de EA. Se requiere un departamento nuevo o existente. El departamento es donde se coloca la suscripción cuando se transfiere la suscripción. El propietario de la cuenta de EA posee la suscripción que se coloca en el departamento.

Debe tener uno de los siguientes roles para crear un propietario de la cuenta de EA. Para más información sobre los roles de EA, consulte [Administración de roles de Contrato Enterprise de Azure](understand-ea-roles.md).

- Administrador de empresa
- Administrador de departamentos

### <a name="set-ea-authentication-level"></a>Configuración del nivel de autenticación de EA

Los EA tienen un conjunto de nivel de autenticación que determina qué tipos de usuarios se pueden agregar como propietarios de cuentas de EA para la inscripción. Hay cuatro niveles de autenticación disponibles, como se describe en [Tipos de nivel de autenticación](ea-portal-troubleshoot.md#authentication-level-types).

Asegúrese de que el nivel de autenticación establecido para EA le permite crear un nuevo propietario de la cuenta de EA mediante el administrador de la cuenta de suscripción que anotó anteriormente. Por ejemplo:

- Si el administrador de la cuenta de suscripción tiene un dominio de dirección de correo electrónico de `@outlook.com`, el EA debe tener su nivel de autenticación establecido en **Solo cuenta de Microsoft** o **Cuenta mixta**.
- Si el administrador de la cuenta de suscripción tiene un dominio de dirección de correo electrónico de `@<YourAzureADTenantPrimaryDomain.com>`, el EA debe tener su nivel de autenticación establecido en **Cuenta profesional o educativa** o **Cuenta profesional o educativa entre inquilinos**. La capacidad de crear un nuevo propietario de la cuenta de EA depende de si el dominio predeterminado del EA es el mismo que el dominio de dirección de correo electrónico del administrador de la cuenta de suscripción.

> [!NOTE]
> Cuando se establece correctamente, cambiar el nivel de autenticación no afecta al proceso de transferencia. Para más información, consulte [Tipos de nivel de autenticación](ea-portal-troubleshoot.md#authentication-level-types).

## <a name="transfer-the-subscription-to-the-ea"></a>Transferencia de la suscripción al EA

Después de revisar y completar los [requisitos previos](#prerequisites) necesarios, está listo para iniciar el proceso de transferencia de la suscripción al EA.

### <a name="create-an-ea-account-owner"></a>Creación de un propietario de la cuenta de EA

Como se describe en la sección de [requisitos previos](#access-to-the-destination-ea), debe crear un propietario de la cuenta de EA como parte de un departamento de EA existente o nuevo.

1. Inicie sesión en el portal de EA en https://ea.azure.com como administrador de departamento o de empresa.
1. Para crear un nuevo departamento, consulte [Creación de un departamento de empresa de Azure](ea-portal-administration.md#create-an-azure-enterprise-department).
1. Para crear el propietario de la cuenta de EA, consulte [Adición de una cuenta](ea-portal-administration.md#add-an-account). Cuando se le pida el **correo electrónico del propietario de la cuenta**, escriba la dirección de correo electrónico del **administrador de la cuenta** de suscripción que identificó anteriormente.
    > [!NOTE]
    > Si el nivel de autenticación de EA se establece incorrectamente, se produce un error en la creación del propietario de la cuenta de EA y se muestra un error en el portal de EA.

Una vez creado el propietario de la cuenta de EA, el administrador de la cuenta de suscripción, que ahora también es propietario de la cuenta de EA, recibe un correo electrónico desde el portal de EA de Azure. Notifica al usuario que ahora es propietario de la cuenta de EA. Si el usuario no tiene acceso a un buzón de correo electrónico asociado a la cuenta especificada, no es necesario preocuparse. El correo electrónico es solo una notificación. La información del correo electrónico no es necesaria para continuar. Sin embargo, se recomienda un buzón de correo electrónico para futuras notificaciones sobre la suscripción.

### <a name="complete-the-subscription-transfer"></a>Conclusión de la transferencia de la suscripción

Ahora que el administrador de la cuenta de suscripción también es propietario de una cuenta de EA, puede crear suscripciones en el EA.

> [!IMPORTANT]
> Antes de continuar, debe comprender lo que sucede cuando un nuevo propietario de la cuenta de EA inicia sesión en el portal de EA por primera vez. Lea y comprenda la siguiente información antes de iniciar sesión como administrador de la cuenta de suscripción en el portal de EA.

La primera vez que un nuevo propietario de la cuenta de EA inicia sesión en el portal de EA, verá la advertencia siguiente:

```
WARNING

You are about to associate your account (email address) to the following enrollment:

Enrollment Name: <EnrollmentName>
Enrollment Number: <EnrollmentNumber>

All Enrollment Administrators can gain access to all of your subscriptions if you proceed.
Additionally, all Azure subscriptions for which you are the account owner will be converted to your Enterprise Agreement.
This includes subscriptions which include a monthly credit (e.g. Visual Studio, MPN, BizSpart, etc.) meaning you will lose the monthly credit by proceeding.
All subscriptions based on a Visual Studio subscriber offer (monthly credit for Visual Studio subscribers or Pay-As-You-Go Dev/Test) will be converted to use the Enterprise Dev/Test usage rates and be billed against this enrollment from today onwards.
If you wish to retain the monthly credits currently associated with any of your subscriptions, please cancel.
Please see additional details.

Cancel  Continue
```

La advertencia indica lo siguiente, como se documenta en [Administración del portal de EA de Azure](ea-portal-administration.md#enterprise-devtest-offer):

***Cuando se agrega un usuario como propietario de una cuenta a través del portal de EA de Azure, las suscripciones de Azure asociadas al propietario de la cuenta que se basan en la oferta de desarrollo/pruebas de MOSP (Pago por uso) o en las ofertas de crédito mensuales para suscriptores de Visual Studio se convertirán en la oferta de desarrollo/pruebas de EA. Las suscripciones basadas en otros tipos de ofertas, como MOSP (Pago por uso), asociadas al propietario de la cuenta se convertirán a la oferta de suscripción de EA estándar.***

Si el usuario entiende las consecuencias de la advertencia, seleccione **Continuar** y las suscripciones asociadas a su cuenta se transfieren al EA.

## <a name="more-help"></a>Más ayuda

Con la información anterior, debe haber transferido la suscripción al EA. Si necesita más ayuda, cree una [solicitud de soporte técnico de facturación](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-step"></a>Paso siguiente

- [Introducción a Azure Enterprise Portal](ea-portal-get-started.md)
