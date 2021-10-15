---
title: Modelo de facturación para Azure Active Directory B2C
description: Obtenga información sobre el modelo de facturación de usuarios activos mensuales (MAU) de Azure AD B2C, cómo vincular un inquilino de Azure AD B2C a una suscripción de Azure y cómo seleccionar el plan de tarifa Premium adecuado.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 09/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: be29232e78ff3cbfa2aec9f880e42774ad0b2376
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570094"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Modelo de facturación para Azure Active Directory B2C

Los precios de Azure Active Directory B2C (Azure AD B2C) se basan en los usuarios activos mensuales (MAU), que es el recuento de usuarios únicos con actividad de autenticación dentro de un mes natural. Este modelo de facturación se aplica tanto a los inquilinos de Azure AD B2C como a la [colaboración de usuarios invitados (B2B) de Azure AD](../active-directory/external-identities/external-identities-pricing.md). La facturación de MAU le ayuda a reducir los costos al ofrecer un nivel gratis y precios flexibles y predecibles. En este artículo obtendrá información sobre la facturación MAU, la vinculación de los inquilinos de Azure AD B2C a una suscripción y el cambio de plan de tarifa.

## <a name="mau-overview"></a>Información general sobre MAU

Un usuario activo mensual (MAU) es un usuario único que realiza una autenticación en un mes determinado. Un usuario que se autentica varias veces en un mes determinado se cuenta como un MAU. Los clientes no se cobran por las autenticaciones posteriores de MAU durante el mes ni por los usuarios inactivos. Las autenticaciones pueden incluir:

- Un inicio de sesión activo e interactivo por el usuario, por ejemplo mediante el [registro o inicio de sesión](add-sign-up-and-sign-in-policy.md), [autoservicio de restablecimiento de contraseña](add-password-reset-policy.md), [edición de perfiles](add-profile-editing-policy.md) o cualquier tipo de [flujo de usuario](user-flow-overview.md) o [directiva personalizada](custom-policy-overview.md).
- Inicio de sesión pasivo no interactivo, como el [inicio de sesión único (SSO)](session-behavior.md), o cualquier tipo de adquisición de tokens, como el flujo de código de autorización, la actualización de tokens o las [credenciales de contraseña del propietario del recurso (ROPC)](add-ropc-policy.md).

Si decide proporcionar mayores niveles de garantía mediante Multi-Factor Authentication (MFA) para voz y SMS, se le seguirá cobrando una tarifa plana mundial por cada intento de MFA de ese mes, independientemente de si el inicio de sesión se ha realizado correctamente o no. 

> [!IMPORTANT]
> Este artículo no contiene detalles de precios. Para conocer la información más reciente sobre la facturación del uso y los precios, consulte [Precios de Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Consulte también [Azure AD B2C: Disponibilidad de región y residencia de datos](data-residency.md) para obtener más información sobre dónde está disponible el servicio Azure AD B2C y dónde se almacenan los datos de usuario.

## <a name="what-do-i-need-to-do"></a>¿Qué tengo que hacer?

Para aprovechar las ventajas de la facturación MAU, el inquilino de Azure AD B2C debe estar vinculado a una suscripción de Azure. Es posible que también necesite cambiar el inquilino de Azure AD B2C a otro plan de tarifa si quiere usar las características de Azure AD B2C Premium P2, como el acceso condicional basado en riesgo.

|Si el inquilino es:  |Necesita:  |
|---------|---------|
| Un inquilino de Azure AD B2C que ya se factura por MAU     | No haga nada. Cuando los usuarios se autentiquen en el inquilino de Azure AD B2C, se le facturará automáticamente mediante el modelo de facturación basado en MAU.        |
| Un inquilino de Azure AD B2C no vinculado aún a una suscripción     |  [Vincule el inquilino de Azure AD B2C a una suscripción](#link-an-azure-ad-b2c-tenant-to-a-subscription) para activar la facturación MAU.     |
| Un inquilino de Azure AD B2C vinculado a una suscripción antes del 1 de noviembre de 2019    | [Cambie a la facturación MAU (recomendado)](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants) o permanezca en el modelo de facturación por autenticación.     |
| Un inquilino de Azure AD B2C y usted quieren usar las características prémium (como el acceso condicional basado en riesgo).    | [Cambie a un plan de tarifa de Azure AD](#change-your-azure-ad-pricing-tier) que admita las características que quiera usar.        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>Acerca del modelo de facturación de usuarios activos mensuales (MAU)

La facturación MAU entró en vigor para los inquilinos de Azure AD B2C el **1 de noviembre de 2019**. Los inquilinos de Azure AD B2C creados y vinculados a una suscripción en esa fecha, o con posterioridad, se han facturado por MAU. Si tiene un inquilino de Azure AD B2C que no se ha vinculado a una suscripción, deberá hacerlo ahora. Si tiene un inquilino de Azure AD B2C que estaba vinculado a una suscripción antes del 1 de noviembre de 2019, se recomienda que actualice al modelo de facturación de usuarios activos mensuales (MAU), o puede permanecer en el modelo de facturación por autenticación.
  
El inquilino de Azure AD B2C también debe estar vinculado al plan de tarifa de Azure adecuado en función de las características que quiera usar. Para las características Premium, se requieren los [precios Premium P1 o P2](https://azure.microsoft.com/pricing/details/active-directory-b2c/) de Azure AD B2C. Es posible que tenga que actualizar el plan de tarifa a medida que use nuevas características. Por ejemplo, en el caso de las directivas de acceso condicional basadas en riesgo, deberá seleccionar el plan de tarifa Azure AD B2C Premium P2 para su inquilino.
> [!NOTE]
>  Los primeros 50 000 MAU al mes son gratuitos para las características Premium P1 y Premium P2. Para determinar el número total de MAU, combinamos los MAU de todos los inquilinos (tanto de Azure AD como de Azure AD B2C) que están vinculados a la misma suscripción.
## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Vincular un inquilino de Azure AD B2C a una suscripción

Los cargos por el uso de Azure Active Directory B2C (o Azure AD B2C) se facturan a una suscripción de Azure. Debe vincular explícitamente un inquilino de Azure AD B2C a una suscripción de Azure mediante la creación de un *recurso* de Azure AD B2C en la suscripción de Azure de destino. Se pueden crear varios recursos de Azure AD B2C en una sola suscripción de Azure, junto con otros recursos de Azure, como máquinas virtuales, cuentas de almacenamiento y Logic Apps. Para ver todos los recursos de la suscripción, vaya al inquilino de Azure Active Directory (Azure AD) al que está asociada la suscripción.

Se puede usar una suscripción vinculada a un inquilino de Azure AD B2C para la facturación del uso de Azure AD B2C u otros recursos de Azure, incluidos recursos adicionales de Azure AD B2C. No se puede usar para agregar otros servicios basados en licencias de Azure o licencias de Office 365 en el inquilino de Azure AD B2C.

### <a name="prerequisites"></a>Requisitos previos

* [Suscripción de Azure](https://azure.microsoft.com/free/)
* [Inquilino de Azure AD B2C](tutorial-create-tenant.md) que quiere vincular a una suscripción
  * Debe ser un administrador de inquilinos.
  * El inquilino no debe estar vinculado aún a una suscripción.

### <a name="create-the-link"></a>Crear el vínculo

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que tiene la suscripción de Azure AD, no el que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD en la lista **Nombre de directorio** y, después, seleccione **Cambiar**.
1. Seleccione **Crear un recurso**, escriba `Active Directory B2C` en el campo **Buscar en Marketplace** y, a continuación, seleccione **Azure Active Directory B2C**.
1. Seleccione **Crear**.
1. Seleccione **Vincular un inquilino de Azure AD B2C existente a mi suscripción de Azure**.
1. Seleccione **Inquilino de Azure AD B2C** en la lista desplegable. Solo se muestran los inquilinos para los que es administrador global y que aún no están vinculados a una suscripción. El campo **Nombre del recurso de Azure AD B2C** se rellena con el nombre de dominio del inquilino de Azure AD B2C que seleccione.
1. Seleccione una **Suscripción** activa de Azure de la que sea administrador.
1. En **Grupo de recursos**, seleccione **Crear nuevo** y, luego, especifique la **Ubicación del grupo de recursos**. Aquí la configuración del grupo de recursos no tiene ningún efecto en la ubicación, el rendimiento o el estado de facturación del inquilino de Azure AD B2C.
1. Seleccione **Crear**.

    ![Página de creación de recursos de Azure AD B2C en Azure Portal](./media/billing/portal-01-create-b2c-resource-page.png)

Después de completar estos pasos para un inquilino de Azure AD B2C, la suscripción de Azure se factura según los detalles del Contrato Enterprise o de Azure Direct.

## <a name="change-your-azure-ad-pricing-tier"></a>Cambio del plan de tarifa de Azure AD

El inquilino debe vincularse al plan de tarifa de Azure adecuado en función de las características que quiera usar con el inquilino de Azure AD B2C. Las características Premium requieren Azure AD B2C Premium P1 o P2, como se describe en [Precios de Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/). En algunos casos, deberá actualizar el plan de tarifa a medida que use nuevas características. Por ejemplo, si quiere usar Identity Protection, directivas de acceso condicional basadas en riesgo y cualquier funcionalidad Premium P2 futura con Azure AD B2C, deberá seleccionar el plan de tarifa Azure AD B2C Premium P2 para su inquilino.

Para cambiar el plan de tarifa, siga estos pasos.

1. Inicie sesión en Azure Portal.

1. Para seleccionar el directorio que contiene la suscripción de Azure a la que está vinculado el inquilino de Azure B2C, no el propio inquilino de Azure AD B2C, seleccione el icono **Directories + subscriptions** (Directorios y suscripciones) de la barra de herramientas del portal.

1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD en la lista **Nombre de directorio** y, después, seleccione **Cambiar**.

1. En el cuadro de búsqueda de la parte superior del portal, escriba el nombre del inquilino de Azure AD B2C. A continuación, seleccione el inquilino en los resultados de la búsqueda en **Recursos**.

1. En la página **Información general** del recurso, en **Plan de tarifa**, seleccione **cambiar**.

   ![Cambiar el plan de tarifa](media/billing/change-pricing-tier.png)
 
1. Seleccione el plan de tarifa que incluya las características que quiera habilitar.

   ![Selección del plan de tarifa](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>Cambio a la facturación MAU (inquilinos de Azure AD B2C anteriores a noviembre de 2019)

Si vinculó el inquilino de Azure AD B2C a una suscripción antes del **1 de noviembre de 2019**, se usa el modelo de facturación por autenticación anterior. Se recomienda actualizar al modelo de facturación de usuarios activos mensuales (MAU). Las opciones de facturación se configuran en el recurso de Azure AD B2C.

Este cambio al modo de facturación de usuarios activos mensuales (MAU) es **irreversible**. Después de convertir un recurso de Azure AD B2C al modelo de facturación basado en MAU, no se puede revertir al modelo de facturación por autenticación.

A continuación, se muestra cómo hacer el cambio a la facturación MAU para un recurso de Azure AD B2C existente:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como propietario de la suscripción con acceso administrativo para el recurso de Azure AD B2C.
1. Para seleccionar el directorio de Azure AD B2C que quiere actualizar a la facturación MAU, seleccione el icono **Directories + subscriptions** (Directorios y suscripciones) en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En el menú de la izquierda, seleccione **Azure AD B2C**. O bien, seleccione **Todos los servicios** y busque y seleccione **Azure AD B2C**.
1. En la página **Información general** del inquilino de Azure AD B2C, seleccione el vínculo en **Nombre de recurso**. Se le dirigirá al recurso de Azure AD B2C en el inquilino de Azure AD.<br/>

    ![Vínculo de recurso de Azure AD B2C resaltado en Azure Portal](./media/billing/portal-mau-02-b2c-resource-link.png)

1. En la página **Información general** del recurso de Azure AD B2C, en **Unidades facturables**, seleccione el vínculo **Per Authentication (Change to MAU)** (Por autenticación [cambiar a MAU]).<br/>

    ![Vínculo para cambiar a MAU resaltado en Azure Portal](./media/billing/portal-mau-03-change-to-mau-link.png)

1. Seleccione **Confirmar** para completar la actualización a la facturación MAU.<br/>

    ![Cuadro de diálogo de confirmación de facturación basada en MAU en Azure Portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)


### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Qué esperar cuando se realiza la transición a la facturación MAU desde la facturación por autenticación

La medición basada en MAU se habilita en cuanto usted, el propietario de la suscripción o el recurso, confirma el cambio. La factura mensual reflejará las unidades de autenticación facturadas hasta el cambio y las nuevas unidades de MAU a partir del cambio.

Los usuarios no se cuentan dos veces durante el mes de la transición. A los usuarios activos únicos que se autentican antes del cambio se les cobra una tarifa por autenticación en un mes natural. Esos mismos usuarios no se incluyen en el cálculo de MAU durante el resto del ciclo de facturación de la suscripción. Por ejemplo:

* El inquilino de Contoso B2C tiene 1000 usuarios. 250 usuarios están activos en un mes determinado. El administrador de la suscripción cambia de la facturación por autenticación a usuarios activos mensuales (MAU) el décimo día del mes.
* La facturación del 1 al 10 usa el modelo por autenticación.
  * Si 100 usuarios inician sesión durante este período (del 1 al 10), esos usuarios se etiquetan como *mes pagado*.
* La facturación a partir del décimo día (el tiempo efectivo de transición) se factura con la tarifa MAU.
  * Si otros 150 usuarios inician sesión durante este período (del 10 al 30), solo se facturan los 150 adicionales.
  * La actividad continua de los primeros 100 usuarios no afecta a la facturación durante el resto del mes natural.

Durante el período de facturación de la transición, es probable que el propietario de la suscripción vea entradas para ambos métodos (por autenticación y por MAU) en el extracto de facturación de la suscripción de Azure:

* Una entrada para el uso hasta la fecha y hora del cambio que refleja la facturación por autenticación.
* Una entrada para el uso después del cambio que refleja los usuarios activos mensuales (MAU).

Para conocer la información más reciente sobre la facturación del uso y los precios de Azure AD B2C, consulte [Precios de Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Administración de los recursos del inquilino de Azure AD B2C

Después de crear el recurso de Azure AD B2C en una suscripción de Azure, debería ver un nuevo recurso de tipo "Inquilino B2C" junto con los otros recursos de Azure.

Este recurso se puede utilizar para:

* Navegar hasta la suscripción para consultar la información de facturación.
* Obtener el id. de inquilino del inquilino de Azure AD B2C en formato GUID.
* Ir al inquilino de Azure AD B2C.
* Enviar una solicitud de soporte técnico.
* Mover el recurso del inquilino de Azure AD B2C a otra suscripción de Azure o grupo de recursos.

### <a name="regional-restrictions"></a>Restricciones regionales

Si ha establecido restricciones regionales para la creación de recursos de Azure en su suscripción, esta restricción puede impedir que cree el recurso de Azure AD B2C.

Para mitigar este problema, modere sus restricciones regionales.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Suscripciones a Azure Cloud Solution Providers (CSP)

Las suscripciones a Azure Cloud Solution Providers (CSP) son compatibles con Azure AD B2C. La funcionalidad está disponible mediante API o Azure Portal para Azure AD B2C y para todos los recursos de Azure. Los administradores de suscripción de CSP pueden vincular, mover y eliminar relaciones con Azure AD B2C de la misma manera que se hace con otros recursos de Azure.

La administración de Azure AD B2C mediante el control de acceso basado en rol no se ve afectada por la asociación entre el inquilino de Azure AD B2C y una suscripción a Azure CSP. El control de acceso basado en rol se logra mediante el uso de roles basados en inquilinos, no roles basados en suscripciones.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Cambio de la suscripción de facturación del inquilino de Azure AD B2C

### <a name="move-using-azure-resource-manager"></a>Traslado mediante Azure Resource Manager

Los inquilinos de Azure AD B2C se pueden mover a otra suscripción mediante Azure Resource Manager, si la de origen y la de destino existen dentro del mismo inquilino de Azure Active Directory.

Para aprender a mover recursos como el inquilino de Azure AD B2C a otra suscripción, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Antes de iniciar el traslado, no olvide leer el artículo completo para comprender las limitaciones y los requisitos de esta operación. Además de las instrucciones para mover los recursos, se incluye información crítica, como una lista de comprobación previa al traslado y cómo validar la operación de traslado.

### <a name="move-by-unlinking-and-relinking"></a>Traslado mediante desvinculación y nueva vinculación

Si las suscripciones de origen y destino están asociadas a diferentes inquilinos de Azure Active Directory, no puede realizar el traslado a través de Azure Resource Manager como se explicó anteriormente. Sin embargo, puede conseguir el mismo resultado desvinculando el inquilino de Azure AD B2C de la suscripción de origen y volviéndolo a vincular a la suscripción de destino. Este método es seguro porque el único objeto que se elimina es el *vínculo de facturación*, no el propio inquilino de Azure AD B2C. No se verá afectado ninguno de los usuarios, las aplicaciones, los flujos de usuario, etc.

1. En el propio directorio de Azure AD B2C, [invite a un usuario invitado](user-overview.md#guest-user) del inquilino de Azure AD de destino (el que está vinculado a la suscripción de Azure de destino) y asegúrese de que este usuario tenga el rol **Administrador global** en Azure AD B2C.
1. Navegue hasta el *recurso de Azure* que representa Azure AD B2C en la suscripción de Azure de origen, tal y como se explica en la sección [Administración de los recursos del inquilino de Azure AD B2C](#manage-your-azure-ad-b2c-tenant-resources). No cambie al inquilino real de Azure AD B2C.
1. Seleccione el botón **Eliminar** en la página **Información general**. Esto *no* elimina los usuarios o las aplicaciones del inquilino de Azure AD B2C relacionado. Simplemente quita el vínculo de facturación de la suscripción de origen.
1. Inicie sesión en Azure Portal con la cuenta de usuario que se agregó como administrador en Azure AD B2C en el paso 1. A continuación, vaya a la suscripción de destino de Azure, que está vinculada al inquilino de Azure Active Directory de destino. 
1. Vuelva a establecer el vínculo de facturación en la suscripción de destino siguiendo el procedimiento anterior [Crear el vínculo](#create-the-link).
1. El recurso de Azure AD B2C se ha trasladado a la suscripción de Azure de destino (vinculada a la instancia de destino de Azure Active Directory) y se le facturará a través de esta suscripción a partir de ahora.

## <a name="next-steps"></a>Pasos siguientes

Para obtener la información de precios más reciente, consulte [Precios de Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
