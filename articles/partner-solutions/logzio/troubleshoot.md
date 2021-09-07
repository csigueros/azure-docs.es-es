---
title: 'Solución de problemas de Logz.io: Soluciones de asociados de Azure'
description: Obtenga información sobre cómo solucionar problemas de integración de Azure en Logz.io.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 08/17/2021
ms.author: tomfitz
ms.openlocfilehash: 5b1d1eb1a59e40286103d63508c98bcc3ba37b8c
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428474"
---
# <a name="troubleshoot-logzio-integration-with-azure"></a>Solución de problemas de la integración de Logz.io en Azure

En este artículo se describe cómo solucionar problemas de integración de Azure en Logz.io.

## <a name="owner-role-needed-to-create-resource"></a>Rol de propietario necesario para crear recursos

Para configurar Logz.io, es preciso tener asignado el [rol de Propietario](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) en la suscripción de Azure. Antes de comenzar esta integración, [compruebe el acceso](../../role-based-access-control/check-access.md).

## <a name="single-sign-on-errors"></a>Errores en el inicio de sesión único

Pueden producirse errores durante la configuración del inicio de sesión único. Para más información sobre cómo configurar el inicio de sesión único (SSO), consulte [Configuración del inicio de sesión único en Logz.io](setup-sso.md).

### <a name="unable-to-save-single-sign-on-settings"></a>Incapacidad de guardar la configuración de inicio de sesión único

Este error significa que hay otra aplicación empresarial que usa el identificador del Lenguaje de marcado de aserción de seguridad (SAML) de Logz.io. Para averiguar qué aplicación, seleccione **Editar** en la sección **Configuración básica de SAML**.

Para resolver este problema, deshabilite la aplicación empresarial que usa SAML o use otra aplicación empresarial para configurar el inicio de sesión único de SAML con Logz.io. Asegúrese de que la aplicación tenga la configuración necesaria.

### <a name="application-not-shown-in-single-sign-on"></a>Aplicación que no aparece en el inicio de sesión único

Pruebe a buscar por el _Id. de aplicación_. Si no se muestra ningún resultado, compruebe la configuración de SAML de la aplicación. La cuadrícula solo muestra las aplicaciones existentes con la configuración de SAML correcta. El **Identificador** y la **URL de repuesta** deben ser exactamente iguales a los que se muestran en la figura siguiente.

Los cuadros de texto en blanco permiten agregar nuevos valores para **Identificador** y **URL de respuesta**.

Use los patrones siguientes para agregar nuevos valores:

- **Identificador**: `urn:auth0:logzio:<Application ID>`
- **URL de respuesta**: `https://logzio.auth0.com/login/callback?connection=<Application ID>`

:::image type="content" source="./media/troubleshoot/basic-saml-config.png" alt-text="Opciones de configuración básica de SAML.":::

## <a name="logs-not-being-sent-to-logzio"></a>Registros que no se envían a Logz.io

Solo los recursos enumerados en las [categorías de registro de recursos de Azure Monitor](../../azure-monitor/essentials/resource-logs-categories.md) enviarán registros a Logz.io.

Para comprobar si un recurso envía registros a Logz.io:

1. Vaya a [Configuración de diagnóstico de Azure](../../azure-monitor/essentials/diagnostic-settings.md) para el recurso específico.
1. Compruebe que haya una configuración de diagnóstico de Logz.io.

:::image type="content" source="./media/troubleshoot/diagnostics.png" alt-text="Configuración de diagnóstico.":::

## <a name="limit-reached-in-monitored-resources"></a>Límite alcanzado en los recursos supervisados

El diagnóstico de Azure Monitor admite un máximo de cinco configuraciones de diagnóstico en un único recurso o suscripción. Cuando alcance ese límite, el recurso mostrará **Se alcanzó el límite** en **Recursos supervisados**. No se puede agregar supervisión con Logz.io.

:::image type="content" source="./media/troubleshoot/limit-monitored-resources.png" alt-text="Se alcanzó el límite para los recursos supervisados.":::

## <a name="vm-extension-installation-failed"></a>Error en la instalación de la extensión de VM

Una máquina virtual (VM) solo se puede supervisar mediante una única cuenta de Logz.io (principal o secundaria). Si intenta instalar el agente en una VM que ya está supervisada por otra cuenta, verá el siguiente error:

:::image type="content" source="./media/troubleshoot/vm-agent-fail.png" alt-text="Error de instalación del agente de máquina virtual.":::

## <a name="purchase-errors"></a>Errores de compra

Los errores de compra se producen porque una tarjeta de crédito válida no está conectada a la suscripción de Azure. O bien, un método de pago no está asociado a la suscripción.

Para resolver un error de compra:

- Use una suscripción de Azure diferente.
- Agregue o actualice la tarjeta de crédito o el método de pago de la suscripción. Para más información, consulte [Adición o actualización de una tarjeta de crédito para Azure](../../cost-management-billing/manage/change-credit-card.md).

Puede ver la salida del error desde la página de implementación del recurso; para ello, seleccione **Detalles de la operación**.

```json
{
  "status": "Failed",
  "error": {
    "code": "BadRequest",
    "message": "{\"message\":\"Purchase has failed because we couldn't find a valid credit card nor
               a payment method associated with your Azure subscription. Please use a different
               Azure subscription or add\\\\update current credit card or payment method for this
               subscription and retry.\",\"code\":\"BadRequest\"}"
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [administrar](manage.md) la integración de Logz.io.
- Para conocer más sobre el inicio de sesión único, consulte [Configuración del inicio de sesión único en Logz.io](setup-sso.md).
