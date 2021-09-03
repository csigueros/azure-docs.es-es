---
title: Solución de problemas de pedidos de Azure Stack Edge mediante Azure Portal
description: Describe cómo solucionar los problemas de pedidos de Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/21/2021
ms.author: alkohli
ms.openlocfilehash: 1d84d6b8bd2978f58f5fb44c0ee1d003db89b7da
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746524"
---
# <a name="troubleshoot-your-azure-stack-edge-ordering-issues"></a>Solución de problemas de pedidos de Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo solucionar los problemas de pedidos de Azure Stack Edge.

## <a name="unsupported-subscription-or-region"></a>Suscripción o región no admitidas

**Descripción del error:** En Azure Portal, si recibe el error:

*No se admite la suscripción o la región seleccionada. Elija otra suscripción o región.*

![Suscripción o región no admitidas](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**Solución propuesta:**  Asegúrese de que ha usado una suscripción admitida como [Contrato Enterprise (EA) de Microsoft](https://azure.microsoft.com/overview/sales-number/), [Proveedor de soluciones en la nube (CSP)](/partner-center/azure-plan-lp) o [Patrocinio de Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/). No se admiten las suscripciones de pago por uso. Para obtener más información, consulte [Prerrequisitos de recursos de Azure Stack Edge](azure-stack-edge-deploy-prep.md#prerequisites).

Existe la posibilidad de que Microsoft pueda permitir una actualización del tipo de suscripción en cada caso particular. Póngase en contacto con [Soporte técnico de Microsoft](https://azure.microsoft.com/support/options/) para comprender sus necesidades y ajustar estos límites adecuadamente.

## <a name="selected-subscription-type-not-supported"></a>No se admite el tipo de suscripción seleccionado

**Error:** Tiene una suscripción de EA, CSP o patrocinada y recibe el siguiente error:

*No se admite el tipo de suscripción seleccionado. Make sure that you use a supported subscription. [Más información](azure-stack-edge-deploy-prep.md#prerequisites). Si usa un tipo de suscripción compatible, asegúrese de lo siguiente:
 
- Que el proveedor `Microsoft.DataBoxEdge` está registrado cuando realice pedidos a través del portal clásico.
- Que el proveedor `Microsoft.EdgeOrder` está registrado cuando realice pedidos a través de Azure Edge Hardware Center (versión preliminar).
 
Para obtener información acerca de cómo registrarse, consulte [Registro de proveedores de recursos](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).

**Solución propuesta:** Siga estos pasos para registrar el proveedor de recursos de Azure Stack Edge:

1. En Azure Portal, vaya a **Inicio** > **Suscripciones**.

2. Seleccione la suscripción que usará para pedir el dispositivo.

3. Seleccione **Proveedores de recursos** y, después, busque **Microsoft.DataBoxEdge**.

    ![Registro del proveedor de recursos](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

Si no tiene acceso de propietario o colaborador para registrar el proveedor de recursos, verá el siguiente error: *La suscripción &lt;nombre de la suscripción&gt; no tiene permisos para registrar el proveedor de recursos: Microsoft.DataBoxEdge.*

Para obtener más información, consulte [Registro del proveedor de recursos](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).

## <a name="resource-provider-not-registered-for-subscription"></a>Proveedor de recursos no registrado para la suscripción

**Error**: en Azure Portal, selecciona una suscripción que se usará para Azure Stack Edge o Data Box Gateway y recibe uno de los errores siguientes:

*Resource provider(s): Microsoft.DataBoxEdge are not registered for subscription &lt;nombre de la suscripción&gt; and you don't have permissions to register a resource provider for subscription &lt;nombre de la suscripción&gt;* (Proveedores de recursos: Microsoft.DataBoxEdge no está registrado en <nombre de la suscripción>, y usted no tiene permisos para registrar un proveedor de recursos para la suscripción <nombre de la suscripción>).

*Resource provider(s): Microsoft.EdgeOrder are not registered for subscription &lt;nombre de la suscripción&gt; and you don't have permissions to register a resource provider for subscription &lt;nombre de la suscripción&gt;* (Proveedores de recursos: Microsoft.EdgeOrder no está registrado en la suscripción <nombre de la suscripción>, y usted no tiene permisos para registrar un proveedor de recursos para la suscripción <nombre de la suscripción>).

**Solución propuesta:** Eleve el acceso a la suscripción o busque a alguien con acceso de propietario o colaborador para registrar el proveedor de recursos.

## <a name="resource-disallowed-by-policy"></a>La directiva no permite el recurso

**Error:** En Azure Portal, intenta registrar un proveedor de recursos y recibe el siguiente error:

*Resource &lt;nombre del recurso&gt; was disallowed by policy. (Código: RequestDisallowedByPolicy). Initiative: Deny generally unwanted Resource Types. Directiva: Not allowed resource types* (La directiva no permite el recurso <nombre del recurso>. [Código: RequestDisallowedByPolicy]. Iniciativa: Denegar los tipos de recursos normalmente no deseados. Directiva: Tipos de recursos no permitidos).

**Solución propuesta:** Este error se produce debido a una directiva de Azure existente que bloquea la creación de recursos. El administrador del sistema de una organización establece las directivas de Azure para garantizar el cumplimiento al usar o crear recursos de Azure. Si alguna de estas directivas bloquea la creación de recursos de Azure Stack Edge, póngase en contacto con el administrador del sistema para editar su directiva de Azure.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre la [Solución de problemas de Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md).
