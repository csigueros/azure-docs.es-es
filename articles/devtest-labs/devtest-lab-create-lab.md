---
title: Creación de un laboratorio
description: Este artículo le guiará por el proceso de creación de un laboratorio mediante Azure Portal y Azure DevTest Labs.
ms.topic: how-to
ms.date: 10/27/2021
ms.openlocfilehash: ffec989e93bcd394c175a2b4a19c4cb72978beb0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464503"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Creación de un laboratorio con Laboratorios de desarrollo y pruebas de Azure

Azure DevTest Labs abarca un grupo de recursos, como máquinas virtuales (VM) y redes de Azure. Esta infraestructura permite administrar mejor esos recursos mediante la especificación de límites y cuotas. Este artículo le guiará a través del proceso de creación de un laboratorio mediante Azure Portal.

## <a name="prerequisites"></a>Prerequisites

Suscripción a Azure. Para información sobre las opciones de compra de Azure, consulte [Instrucciones para contratar Azure](https://azure.microsoft.com/pricing/purchase-options/) o [Evaluación gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/). Debe ser el propietario de la suscripción para crear el laboratorio.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Al seleccionar el vínculo siguiente, se le transferirá a la página de Azure Portal donde puede empezar a crear un laboratorio en Azure DevTest Labs.

[Introducción a Azure DevTest Labs en cuestión de minutos](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="create-a-devtest-labs-resource"></a>Creación de un recurso de DevTest Labs

La página **Create Devtest Lab** (Crear laboratorio de Devtest) contiene cinco pestañas. La primera pestaña es **Configuración básica**.

> [!TIP]
> En la parte inferior de cada página, encontrará un vínculo en el que podrá **descargar una plantilla para la automatización**.

### <a name="basic-settings-tab"></a>Pestaña Configuración básica

Proporcione la siguiente información:

|Propiedad | Descripción |
|---|---|
|Subscription| En la lista desplegable, seleccione la suscripción de Azure que se va a usar para el laboratorio.|
|Grupo de recursos| En la lista desplegable, seleccione el grupo de recursos existente o seleccione **Crear**.|
|Nombre del laboratorio| Escriba un nombre único dentro de la suscripción para el laboratorio.|
|Location| En la lista desplegable, seleccione una ubicación para el laboratorio.|
|Entornos públicos| El repositorio de entornos públicos contiene una lista de plantillas de Azure Resource Manager mantenidas que permiten a los usuarios de los laboratorios crear recursos PaaS dentro de Labs. Para más información, vea [Configuración y uso de entornos públicos](devtest-lab-configure-use-public-environments.md).|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-basic-settings.png" alt-text="Captura de pantalla de la pestaña Configuración básica para crear laboratorios de DevTest.":::


### <a name="auto-shutdown-tab"></a>Pestaña Apagado automático

El apagado automático permite apagar automáticamente todas las máquinas de un laboratorio a una hora programada cada día. La característica de apagado automático es principalmente una característica de ahorro de costos. Para cambiar la configuración de apagado automático después de crear el laboratorio, vea [Administración de todas las directivas de un laboratorio en Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).

Proporcione la siguiente información:

|Propiedad | Descripción |
|---|---|
|habilitado| Seleccione **Activado** para habilitar esta directiva, o bien **Desactivado** para deshabilitarla.|
|Apagado programado| Escriba una hora para apagar todas las máquinas virtuales del laboratorio actual.|
|Zona horaria| Seleccione una zona horaria en la lista desplegable.|
|¿Enviar notificación antes del apagado automático? | Seleccione **Sí** o **No** para enviar una notificación 30 minutos antes de la hora especificada de apagado automático. Si elige **Sí**, escriba un punto de conexión de URL del webhook o una dirección de correo electrónico para especificar dónde quiere que se publique o se envíe la notificación. El usuario recibe la notificación y se le ofrece la opción de retrasar el apagado.|
|Dirección URL de Webhook| Se publicará una notificación en el punto de conexión de webhook especificado cuando esté a punto de producirse un apagado automático.|
|Dirección de correo electrónico| Escriba un conjunto de direcciones de correo electrónico separadas por punto y coma para recibir correos electrónicos de notificación de alertas.|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-auto-shutdown.png" alt-text="Captura de pantalla de los detalles de la programación del apagado automático.":::

### <a name="networking-tab"></a>Pestaña Redes

Se creará automáticamente una red predeterminada (que se puede cambiar o configurar más adelante) o se puede seleccionar una red virtual existente.

Proporcione la siguiente información:

|Propiedad | Descripción |
|---|---|
|Red virtual| Mantenga el valor predeterminado o seleccione uno existente en la lista desplegable. Las redes virtuales están aisladas lógicamente entre sí en Azure. De manera predeterminada, las máquinas virtuales de la misma red virtual pueden acceder unas a otras.|
|Subred| Mantenga el valor predeterminado o seleccione uno existente en la lista desplegable. Una subred es un intervalo de direcciones IP de la red virtual que se puede usar para aislar máquinas virtuales entre sí o de Internet.|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-networking.png" alt-text="Captura de pantalla de los detalles de red.":::

### <a name="tags-tab"></a>Pestaña Etiquetas

Las etiquetas son útiles para ayudarle a administrar y organizar los recursos del laboratorio por categoría. Para más información, vea [Adición de etiquetas a un laboratorio](devtest-lab-add-tag.md).

Proporcione la siguiente información:

|Propiedad | Descripción |
|---|---|
|Nombre| Los nombres de etiqueta no distinguen mayúsculas de minúsculas y se limitan a 512 caracteres.|
|Value| Los valores de etiqueta distinguen mayúsculas de minúsculas y se limitan a 256 caracteres.|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-tags.png" alt-text="Captura de pantalla de los detalles de las etiquetas.":::

### <a name="review--create-tab"></a>Pestaña Revisar y crear

En la pestaña **Revisar y crear** se validan todas las configuraciones. Si todas las configuraciones son válidas, aparecerá **Correcto** en la parte superior. Revise la configuración y seleccione **Crear**. Para supervisar el estado del proceso de creación de un laboratorio, inspeccione el área **Notificaciones** en la parte superior de la página del portal. 

:::image type="content" source="./media/devtest-lab-create-lab/portal-review-and-create.png" alt-text="Captura de pantalla de los detalles de Revisar y crear.":::

## <a name="post-creation"></a>Después de la creación

1. Una vez que finalice el proceso de creación, seleccione **Ir al recurso** en la notificación de implementación.

    :::image type="content" source="./media/devtest-lab-create-lab/creation-notification.png" alt-text="Captura de pantalla de la notificación de implementación de DevTest Labs.":::

1. La página **Información general** del laboratorio es similar a la siguiente imagen:

    :::image type="content" source="./media/devtest-lab-create-lab/lab-home-page.png" alt-text="Captura de pantalla de la página de información general de DevTest Labs.":::

## <a name="next-steps"></a>Pasos siguientes

Una vez creado el laboratorio, le presentamos algunos pasos que se deben tener en cuenta:

* [Adición de una máquina virtual a un laboratorio](devtest-lab-add-vm.md)
* [Acceso seguro a un laboratorio](devtest-lab-add-devtest-user.md)
* [Definición de directivas de laboratorio](devtest-lab-set-lab-policy.md)

