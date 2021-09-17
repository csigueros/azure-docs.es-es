---
title: Administrar suscripciones
description: Las suscripciones consisten en dispositivos confirmados administrados y se pueden incorporar o retirar según sea necesario.
ms.date: 08/10/2021
ms.topic: how-to
ms.openlocfilehash: 41240bd3b05c08d8acd3484bbe32fc99b6590d49
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444425"
---
# <a name="manage-defender-for-iot-subscriptions"></a>Administración de suscripciones a Defender para IoT

## <a name="about-subscriptions"></a>Información sobre las suscripciones

La implementación de Defender para IoT se administra a través de las suscripciones de la cuenta de Azure Defender para IoT.
Puede incorporar, editar y retirar las suscripciones a Defender para IoT desde el [portal de Azure Defender para IoT](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

Para cada suscripción, se le pedirá que defina una serie de *dispositivos confirmados*. Los dispositivos confirmados son el número aproximado de dispositivos que se supervisarán en su empresa. 

### <a name="subscription-billing"></a>Facturación de suscripciones

Se le facturará según el número de dispositivos confirmados asociados a cada suscripción.

El ciclo de facturación de Azure Defender para IoT sigue un mes natural. Los cambios realizados en los dispositivos confirmados durante el mes se implementan una hora después de confirmar la actualización y se reflejan en la factura mensual. La *retirada* de suscripciones también se hace efectiva una hora después de confirmarse.

Su empresa puede tener más de una entidad de pago. Si este es el caso, puede incorporar más de una suscripción.

Antes de suscribirse, debe tener una idea de cuántos dispositivos desea que cubran sus suscripciones.

Los usuarios también pueden trabajar con una suscripción de prueba, que permite supervisar un número limitado de dispositivos durante 30 días.
Para obtener información sobre el precio de dispositivos confirmados, consulte [Precios de Azure Defender](https://azure.microsoft.com/pricing/details/azure-defender/).

## <a name="requirements"></a>Requisitos

Antes de incorporar una suscripción, compruebe lo siguiente:

- Ha configurado su cuenta de Azure.
- Tiene los permisos de usuario de Azure necesarios.

### <a name="azure-account-subscription-requirements"></a>Requisitos de suscripción de la cuenta de Azure

Para empezar a trabajar con Azure Defender para IoT, debe tener una suscripción de cuenta de Microsoft Azure.

Si no tiene una suscripción, puede registrarse para obtener una cuenta gratuita. Para obtener más información, consulte https://azure.microsoft.com/free/.

Si ya tiene acceso a una suscripción a Azure, pero no aparece en la lista al suscribirse a Defender para IoT, compruebe los detalles de la cuenta y confirme sus permisos con el propietario de la suscripción. Vea https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade.

### <a name="user-permission-requirements"></a>Requisitos de permisos de usuario

Los **propietarios** y los **colaboradores** de suscripciones de Azure pueden incorporar, actualizar y retirar suscripciones a Azure Defender para IoT.

## <a name="onboard-a-trial-subscription"></a>Incorporación de una suscripción de prueba

Si desea evaluar Defender para IoT, puede usar una suscripción de prueba. La prueba es válida durante 30 días y admite 1000 dispositivos confirmados. Con la prueba, puede implementar un sensor más de Defender para IoT en su red. Use los sensores para supervisar el tráfico, analizar datos, generar alertas, obtener información sobre riesgos y vulnerabilidades de la red, etc. La prueba también permite descargar una consola de administración local para ver información agregada generada por los sensores.

En esta sección se describe cómo crear una suscripción de prueba para un sensor.

**Para crear una suscripción de prueba:**

1. Vaya al [portal de Azure Defender para IoT](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Seleccione **Introducción**.
1. Seleccione **Onboard subscription** (Incorporar suscripción).
1. En la página "Pricing" (Precios), seleccione **Empezar con una prueba**.
1. Seleccione una suscripción en el panel "Onboard trial subscription" (Incorporar suscripción de prueba); a continuación, seleccione **Evaluar**.
1. Confirme la evaluación.
1. En caso necesario, incorpore o configure un sensor.

## <a name="onboard-a-subscription"></a>Incorporación de una suscripción

En esta sección se describe cómo incorporar una suscripción.

**Para incorporar una suscripción:**

1. Vaya al [portal de Azure Defender para IoT](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Seleccione **Introducción**.
1. Seleccione **Onboard subscription** (Incorporar suscripción).
1. En la página "Pricing" (Precios), seleccione **Suscribirse**.
1. En el panel **Onboard subscription** (Incorporar suscripción), seleccione una suscripción y el número de dispositivos confirmados en el menú desplegable.

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="Seleccione la suscripción y el número de dispositivos confirmados.":::

1. Selecciona **Subscribe** (Suscribirse).
1. Confirme su suscripción.
1. Si aún no lo ha hecho, incorpore o configure un sensor.

## <a name="update-committed-devices-in-a-subscription"></a>Actualización de dispositivos confirmados en una suscripción

Es posible que tenga que actualizar su suscripción con más o menos dispositivos confirmados. Puede que más dispositivos requieran supervisión si, por ejemplo, aumenta la cobertura de sitios existente, detecta más dispositivos de los esperados o hay cambios en la red (como agregar conmutadores).

**Para actualizar una suscripción:**
1. Vaya al [portal de Azure Defender para IoT](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Seleccione **Onboard subscription** (Incorporar suscripción).
1. Seleccione la suscripción y, a continuación, los tres puntos. (...).
1. Seleccione **Editar**.
1. Actualice los dispositivos confirmados y seleccione **Guardar**.
2. En el cuadro de diálogo de confirmación que se abre, seleccione **Confirmar**.
Los cambios en los dispositivos se harán efectivos una hora después de confirmarse. La facturación de estos cambios se reflejará a principios del mes siguiente a la confirmación del cambio.

Deberá cargar un nuevo archivo de activación en la consola de administración local. El archivo de activación refleja el nuevo número de dispositivos confirmados. Consulte [Carga de un archivo de activación](how-to-manage-the-on-premises-management-console.md#upload-an-activation-file).
## <a name="offboard-a-subscription"></a>Retirada de suscripciones

Es posible que tenga que retirar una suscripción, por ejemplo, si necesita trabajar con una nueva entidad de pago. La retirada de suscripciones también se hace efectiva una hora después de confirmarse.
La próxima factura mensual reflejará este cambio.

Quite todos los sensores asociados a la suscripción antes de retirarla. Para obtener más información sobre cómo eliminar un sensor, consulte [Eliminación de un sensor](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor).

**Para retirar una suscripción:**

1. Vaya al [portal de Azure Defender para IoT](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Seleccione la suscripción y, a continuación, los tres puntos. (...).

1. Seleccione **Retirar suscripción**.

1. En el menú emergente de confirmación, active la casilla para confirmar que ha eliminado todos los sensores asociados con la suscripción.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Active la casilla y seleccione Retirar para retirar el sensor.":::

1. Seleccione **Retirar**.

## <a name="apply-a-new-subscription"></a>Aplicación de una nueva suscripción

Es posible que, por razones empresariales, tenga que aplicar a su implementación una suscripción diferente a la que se usa actualmente. Si cambia la suscripción, tendrá que cargar un nuevo archivo de activación de sensor. El archivo contiene información sobre las fechas de expiración de la suscripción.

**Para aplicar una nueva suscripción:**

1. Elimine la suscripción que se está utilizando actualmente.
1. Seleccione una nueva.
1. Descargue un archivo de activación para el sensor asociado a la suscripción.
1. Cargue el archivo de activación en el sensor.

## <a name="next-steps"></a>Pasos siguientes

- [Administración de sensores en el portal de Defender para IoT](how-to-manage-sensors-on-the-cloud.md)

- [Activación y configuración de la consola de administración local](how-to-activate-and-set-up-your-on-premises-management-console.md)
