---
title: 'Solución de problemas de Apache Kafka para Confluent Cloud: soluciones de partners de Azure'
description: En este artículo se proporciona información sobre la solución de problemas y las preguntas más frecuentes (P+F) para Confluent Cloud en Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/18/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 784fed6efb83e02940c6b73fb4c3aab792c4cf99
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112518239"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Solución de problemas de Apache Kafka para soluciones de Confluent Cloud

Este documento contiene información sobre la solución de problemas de las soluciones que usan Apache Kafka para Confluent Cloud.

Si no encuentra una respuesta o no puede resolver un problema, [cree una solicitud a través de Azure Portal](get-support.md) o póngase en contacto con el [soporte técnico de Confluent](https://support.confluent.io).

## <a name="cant-find-offer-in-the-marketplace"></a>No se encuentra la oferta en Marketplace

Para encontrar la oferta en Azure Marketplace, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), haga clic en **Crear un recurso**.
1. Busque _Apache Kafka en Confluent Cloud_.
1. Seleccione el icono de la aplicación.

Si no aparece la oferta, póngase en contacto con el [soporte técnico de Confluent](https://support.confluent.io). El identificador de inquilino de Azure Active Directory debe estar en la lista de inquilinos permitidos. Para obtener información sobre cómo buscar el identificador de inquilino, consulte [Búsqueda del identificador de inquilino de Azure Active Directory](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md).

## <a name="purchase-errors"></a>Errores de compra

* Los errores de compra se producen porque una tarjeta de crédito válida no está conectada a la suscripción de Azure o un método de pago no está asociado a la suscripción.

  Use una suscripción de Azure diferente. O bien, agregue o actualice la tarjeta de crédito o el método de pago de la suscripción. Para obtener más información, consulte cómo [actualizar el método de pago y la tarjeta de crédito](../../cost-management-billing/manage/change-credit-card.md).

* Una suscripción de Contrato Enterprise (EA) no permite realizar compras en Marketplace.

  Use otra suscripción. O bien, compruebe si su suscripción de EA está habilitada para la compra en Marketplace. Para obtener más información, consulte la sección [Habilitación de compras en Azure Marketplace](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). Si esas opciones no solucionan el problema, póngase en contacto con el [soporte técnico de Confluent](https://support.confluent.io).

## <a name="conflict-error"></a>Error de conflicto

Si previamente se ha registrado en Confluent Cloud, debe usar una nueva dirección de correo electrónico para crear otro recurso de organización de Confluent Cloud. Cuando se usa una dirección de correo electrónico previamente registrada, se obtiene un error de **conflicto**. Vuelva a registrarse, pero esta vez con una nueva dirección de correo electrónico.

## <a name="deploymentfailed-error"></a>Error de implementación

Si obtiene un error **DeploymentFailed**, compruebe el estado de la suscripción de Azure. Asegúrese de que no está suspendida y de que no tiene ningún problema de facturación.

## <a name="resource-isnt-displayed"></a>El recurso no se muestra

Si las hojas **Información general** o **Eliminar** de Confluent Cloud no se muestran en el portal, intente actualizar la página. Este error puede ser un problema intermitente con el portal. Si eso no funciona, póngase en contacto con el [soporte técnico de Confluent](https://support.confluent.io).

Si el recurso de Confluent Cloud no se encuentra en la lista Azure **Todos los recursos**, póngase en contacto con el [soporte técnico de Confluent](https://support.confluent.io).

## <a name="resource-creation-takes-long-time"></a>La creación de recursos tarda mucho tiempo

Si el proceso de implementación tarda más de tres horas en completarse, póngase en contacto con el soporte técnico.

Si se produce un error en la implementación y el recurso de Confluent Cloud tiene un estado de `Failed`, elimine el recurso. Después de la eliminación, vuelva a intentar crear el recurso.

## <a name="offer-plan-doesnt-load"></a>El plan de oferta no se carga

Este error puede ser un problema intermitente con Azure Portal. Intente implementar la oferta de nuevo.

## <a name="unable-to-delete"></a>No se pudo eliminar

Si no puede eliminar recursos de Confluent, compruebe que tiene permisos para eliminar el recurso. Debe tener permiso para realizar acciones de Microsoft.Confluent/*/Delete. Para más información sobre la visualización de permisos, consulte [Enumeración de asignaciones de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-list-portal.md).

Si tiene los permisos correctos pero todavía no puede eliminar el recurso, póngase en contacto con el [soporte técnico de Confluent](https://support.confluent.io). Esta condición puede estar relacionada con la directiva de retención de Confluent. El soporte técnico de Confluent puede eliminar la organización y la dirección de correo electrónico.

## <a name="unable-to-use-single-sign-on"></a>No se puede usar el inicio de sesión único

Si el inicio de sesión único no funciona en el portal de SaaS de Confluent Cloud, compruebe que está usando el correo electrónico de Azure Active Directory correcto. También debe haber dado su consentimiento para permitir el acceso al portal de software como servicio (SaaS) de Confluent Cloud. Para obtener más información, consulte la sección [Guía sobre el inicio de sesión único](manage.md#single-sign-on).

Si el problema persiste, póngase en contacto con el [soporte técnico de Confluent](https://support.confluent.io).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información acerca de la [administración de la instancia](manage.md) de Apache Kafka para Confluent Cloud.
