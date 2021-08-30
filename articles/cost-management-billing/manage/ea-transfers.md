---
title: Transferencias de Azure Enterprise
description: Describe las transferencias del Contrato Enterprise de Azure
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: cc8d65ec4b714bbb98036c5ed3deabd4b75d3c98
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751983"
---
# <a name="azure-enterprise-transfers"></a>Transferencias de Azure Enterprise

En este artículo se proporciona información general de las transferencias empresariales.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Transferencia de una cuenta empresarial a una nueva inscripción

Una transferencia de cuenta mueve un propietario de la cuenta de una inscripción a otra. Todas las suscripciones relacionadas en el propietario de la cuenta se moverán a la inscripción de destino. Use una transferencia de cuentas cuando tenga varias inscripciones activas y solo desee mover los propietarios de la cuenta seleccionada.

Esta sección tiene solo carácter informativo, ya que el administrador de la empresa no puede realizar la acción. Para transferir una cuenta de empresa a una nueva inscripción, se necesita una solicitud de soporte técnico.

Tenga en cuenta los puntos siguientes cuando transfiera una cuenta de empresa a una nueva inscripción:

- Solo se transfieren las cuentas especificadas en la solicitud. Si se eligen todas las cuentas, se transferirán todas ellas.
- La inscripción de origen mantiene su estado como activo o extendido. Puede seguir usando la inscripción hasta que expire.

### <a name="prerequisites"></a>Requisitos previos

Al solicitar una transferencia de cuentas, proporcione la siguiente información:

- El número de la inscripción de destino, el nombre de cuenta y el correo electrónico del propietario de la cuenta que se va a transferir.
- Para la inscripción de origen, el número de inscripción y la cuenta que se va a transferir

Otros puntos que hay que tener en cuenta antes de transferir una cuenta:

- Es necesaria la aprobación de un administrador del Contrato Enterprise para la inscripción de origen y destino.
- Si la transferencia de una cuenta no cumple los requisitos, considere la posibilidad de realizar una transferencia de inscripción.
- La transferencia de la cuenta transfiere todos los servicios y suscripciones relacionados con las cuentas específicas.
- Una vez completada la transferencia, la cuenta transferida aparece como inactiva en la inscripción de origen y como activa en la inscripción de destino.
- La cuenta muestra la fecha de finalización correspondiente a la fecha de transferencia efectiva en la inscripción de origen y la fecha de inicio en la inscripción de destino.
- Cualquier uso que se haya realizado de la cuenta antes de la fecha de transferencia efectiva permanecerá bajo la inscripción de origen.

## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Transferencia de la inscripción empresarial a una nueva

Una transferencia de inscripción se tiene en cuenta cuando:

- Ha finalizado el plazo de prepago de la inscripción actual.
- Una inscripción está en estado expirado o extendido y se negocia un nuevo contrato.
- Tiene varias inscripciones y desea combinar todas las cuentas y la facturación en una sola inscripción.

Esta sección tiene solo carácter informativo, ya que el administrador de la empresa no puede realizar la acción. Se necesita una solicitud de soporte técnico para transferir una inscripción empresarial a una nueva, a menos que la inscripción sea apta para la [transferencia de inscripción automática](#auto-enrollment-transfer).

Cuando se solicita la transferencia de una inscripción empresarial completa a una inscripción, se producen las siguientes acciones:

- El uso transferido puede tardar hasta 72 horas en reflejarse en la nueva inscripción.
- Si se habilitaron los cargos de DA o AO en la inscripción transferida, deben habilitarse en la nueva inscripción.
- Si usa informes de API o Power BI, genere una nueva clave de API en la nueva inscripción.
- Todos los servicios, suscripciones, cuentas, departamentos y la estructura de inscripción de Azure completa, incluidos todos los administradores de departamento del Contrato Enterprise, se transfieren a una nueva inscripción de destino.
- El estado de inscripción se establece en _Transferido_. La inscripción transferida solo está disponible con fines de informe de historial de uso.
- No se pueden agregar roles o suscripciones a una inscripción transferida. El estado Transferido evita más uso en la inscripción.
- Se pierde cualquier saldo de prepago de Azure restante en el contrato, incluidos los términos futuros.
-    Si la inscripción desde la que va a realizar la transferencia tiene compras de instancias reservadas, el precio de compra permanecerá en la inscripción de origen; sin embargo, todas las ventajas de las instancias reservadas se transferirán para su uso en la nueva inscripción.
-    La cuota de compra única de Marketplace y las tarifas fijas mensuales que ya se hayan realizado en la inscripción antigua no se transfieren a la nueva inscripción. Se transferirán los cargos de Marketplace basados en el consumo.

### <a name="effective-transfer-date"></a>Fecha de transferencia efectiva

La fecha de transferencia efectiva puede ser la fecha de inicio de la suscripción de destino o una fecha posterior.

El uso de la inscripción de origen se cobra en el prepago de Azure o como uso por encima del límite. El uso que se produce después de la fecha de transferencia efectiva se transfiere y se carga a la nueva inscripción.

### <a name="prerequisites"></a>Requisitos previos

Al solicitar una transferencia de inscripciones, proporcione la siguiente información:

- Para la inscripción de origen, el número de inscripción.
- Para la inscripción de destino, el número de inscripción al que se va a transferir.
- Para la fecha de vigencia de la transferencia de inscripción, puede ser la fecha de inicio de la inscripción de destino o después de ella. La fecha elegida no puede afectar al uso de ninguna factura por uso por encima del límite ya emitida.

Otros puntos que hay que tener en cuenta antes de una transferencia de inscripciones:

- Es necesaria la aprobación de un administrador del Contrato Enterprise para la inscripción de origen y destino.
- Si una transferencia de inscripción no cumple sus requisitos, considere la posibilidad de transferir una cuenta.
- El estado de inscripción de origen se actualizará al estado transferido y solo estará disponible para fines de informes de uso históricos.
- No hay ningún tiempo de inactividad durante la transferencia de la inscripción.
- El uso puede tardar entre 24 y 48 horas en reflejarse en la inscripción de destino.
- La configuración de la vista de costos para administradores de departamento o propietarios de la cuenta no se lleva a cabo.
  - Si se ha habilitado previamente, la configuración debe estar habilitada para la inscripción de destino.
- Las claves de API usadas en la inscripción de origen se deben volver a generar para la inscripción de destino.
- Si las inscripciones de origen y destino se encuentran en instancias de nube diferentes, se producirá un error en la transferencia. Soporte técnico de Azure puede transferir solo dentro de la misma instancia de nube.
- Para reservas (instancias reservadas):
  - La inscripción o transferencia de cuentas entre distintas monedas afecta a las compras de reserva mensuales.
  - Siempre que el suyo sea un cambio de moneda durante o después de una transferencia de inscripción, las reservas pagadas mensualmente se cancelan para la inscripción de origen. Esto es intencionado y solo afecta a las compras de reserva mensuales.
  - Es posible que tenga que volver a comprar las reservas mensuales canceladas de la inscripción de origen mediante la nueva inscripción en la moneda local o nueva.


### <a name="auto-enrollment-transfer"></a>Transferencia de inscripción automática

Podría ver que una inscripción tiene el estado **Transferido** aunque no haya enviado una incidencia de soporte técnico para solicitar una transferencia de inscripción. El estado **Transferido** se produce como resultado del proceso de transferencia de inscripción automática. Para que el proceso de transferencia de inscripción automática se produzca durante la fase de renovación, hay algunos elementos que se deben incluir en el nuevo contrato:

- Número de inscripción anterior (debe existir en Enterprise Portal)
- La fecha de expiración del número de inscripción anterior es un día anterior a la fecha de inicio efectiva del nuevo contrato.
- El nuevo contrato tiene una orden de pago por adelantado de Azure facturada que tiene una fecha actual o pasada.
- La nueva inscripción se ha creado en Enterprise Portal.

Si no faltan datos de uso en Enterprise Portal entre la inscripción anterior y la nueva inscripción, no tiene que crear una incidencia de soporte técnico de transferencia.

### <a name="azure-prepayment"></a>Prepago de Azure

El prepago de Azure no se transfiere entre inscripciones. Los saldos de prepago de Azure están contractualmente asociados a la inscripción en la que se solicitaron. El prepago no se transfiere como parte del proceso de transferencia de la cuenta o inscripción.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>No hay servicios afectados para las transferencias de cuentas e inscripciones.

No hay ningún tiempo de inactividad durante la transferencia de la cuenta o inscripción. Se puede completar el mismo día de la solicitud si se proporciona toda la información necesaria.

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Transferencia de una suscripción de Enterprise a una suscripción de pago por uso

Para transferir una suscripción de Enterprise a una suscripción individual con tarifas de pago por uso, debe crear una nueva solicitud de soporte técnico en Azure Enterprise Portal. Para crear una solicitud de soporte técnico, seleccione **+ Nueva solicitud de soporte técnico** en el área **Ayuda y soporte técnico**.

## <a name="change-azure-subscription-or-account-ownership"></a>Cambio de la propiedad de la cuenta o de la suscripción de Azure

El portal del Contrato Enterprise de Azure puede transferir suscripciones de un propietario de cuenta a otro. Para más información, consulte [Cambio de la propiedad de la cuenta o de la suscripción de Azure](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

## <a name="subscription-transfer-effects"></a>Efectos de la transferencia de la suscripción

Si se transfiere una suscripción de Azure a una cuenta que se encuentra en el mismo inquilino de Azure Active Directory, todos los usuarios, grupos y entidades de servicio que tenían [control de acceso basado en rol de Azure (Azure RBAC)](../../role-based-access-control/overview.md) para administrar recursos mantendrán su acceso.

Para ver los usuarios con acceso RBAC a la suscripción:

1. En Azure Portal, abra **Suscripciones**.
2. Seleccione la suscripción que desee ver y, después, seleccione **Control de acceso (IAM)** .
3. Seleccione **Asignaciones de roles**. La página de asignación de roles enumera todos los usuarios que tienen acceso RBAC a la suscripción.

Si transfiere la suscripción a una cuenta en otro inquilino de Azure AD, todos los usuarios, grupos y entidades de servicio que tenían [RBAC](../../role-based-access-control/overview.md) para administrar recursos _perderán_ el acceso. Aunque el acceso con RBAC no está presente, el acceso a la suscripción puede estar disponible mediante mecanismos de seguridad, entre los que se incluyen:

- Certificados de administración que conceden al usuario derechos administrativos a los recursos de la suscripción. Para obtener más información, consulte [Crear y cargar un certificado de administración para Azure](../../cloud-services/cloud-services-certs-create.md).
- Claves de acceso para servicios como Almacenamiento. Para más información, vea [Introducción a las cuentas de Azure Storage](../../storage/common/storage-account-overview.md).
- Credenciales de acceso remoto para servicios como Azure Virtual Machines.

Si el destinatario necesita restringir el acceso a sus recursos de Azure, debería plantearse la posibilidad de actualizar todos los secretos asociados al servicio. La mayoría de los recursos se pueden actualizar mediante el uso de los siguientes pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En el menú Concentrador, seleccione **Todos los recursos**.
3. Seleccione el recurso.
4. En la página de recursos, seleccione **Settings** (Configuración) para ver y actualizar los secretos existentes.

## <a name="next-steps"></a>Pasos siguientes

- Si necesita ayuda para solucionar problemas con el portal del Contrato Enterprise de Azure, consulte [Solución de problemas de acceso al portal del Contrato Enterprise de Azure](ea-portal-troubleshoot.md).