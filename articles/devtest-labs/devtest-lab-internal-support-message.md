---
title: Adición de una nota de soporte interno a un laboratorio
description: Obtenga información sobre cómo publicar una nota de soporte interno en un laboratorio de Azure DevTest Labs
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 20cd7494eb0a0df340866d344b48d87e94cca490
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612156"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Adición de una nota de soporte interno a un laboratorio de Azure DevTest Labs

Azure DevTest Labs permite personalizar el laboratorio con una nota de soporte interno que proporciona a los usuarios información de soporte técnico sobre el laboratorio. Por ejemplo, puede proporcionar información de contacto para que un usuario sepa cómo contactar con soporte interno si necesita ayuda para solucionar problemas o acceder a los recursos del laboratorio. También puede proporcionar vínculos a sitios web internos o preguntas más frecuentes a los que su equipo puede acceder antes de ponerse en contacto con el servicio de soporte técnico.

Con una nota de soporte técnico interno se pretende permitir la publicación de información del laboratorio que no suele cambiar con demasiada frecuencia. Para notificar a los usuarios sobre información de un laboratorio que, por su naturaleza, es más temporal, como las últimas actualizaciones de las directivas de laboratorio, vea [Publicación de un anuncio en un laboratorio](devtest-lab-announcements.md).

Puede deshabilitar o editar una nota de soporte técnico con facilidad después de que deje de ser aplicable.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Pasos para agregar una nota de soporte técnico a un laboratorio existente

1. Inicie sesión en [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Si es necesario, seleccione **Todos los servicios** y, luego, **DevTest Labs** en la lista. (Puede que su laboratorio ya aparezca en el panel, en **Todos los recursos**).
1. En la lista de laboratorios, seleccione aquel en el que quiera agregar una nota de soporte técnico.  
1. En el área **Introducción** del laboratorio, seleccione **Configuración y directivas**.  

    ![Botón Configuración y directivas](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. A la izquierda en **CONFIGURACIÓN**, seleccione **Soporte interno**.

    ![Botón de soporte interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Para crear un mensaje de soporte interno para los usuarios de este laboratorio, establezca Habilitado en **Sí**.

1. En el campo **Mensaje de soporte**, escriba la nota de soporte interno que desea presentar a los usuarios del laboratorio. El mensaje de soporte acepta Markdown. A medida que escribe el texto del mensaje, puede consultar el área **Vista previa** en la parte inferior de la pantalla para ver cómo el mensaje aparece a los usuarios.

    ![Pantalla de soporte interno para crear el mensaje](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Seleccione **Guardar** cuando la nota de soporte técnico esté lista para publicarse.

Cuando ya no quiera mostrar este mensaje de soporte a los usuarios del laboratorio, vuelva a la página **Soporte interno** y establezca **Habilitado** en **No**.

## <a name="steps-for-users-to-view-the-support-message"></a>Pasos para que los usuarios vean el mensaje de soporte

1. En [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), seleccione un laboratorio.

1. En el área **Introducción** del laboratorio, seleccione **Soporte interno**.  

    ![Botón de soporte interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Si hay algún mensaje de soporte publicado, el usuario puede verlo en el panel de soporte interno.

    ![Panel de soporte interno en que aparece publicado el mensaje de soporte](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Pasos siguientes
* Las notas de soporte interno se utilizan normalmente para proporcionar información de soporte técnico que no cambia con tanta frecuencia. También puede obtener información sobre cómo [publicar un anuncio en un laboratorio](devtest-lab-announcements.md) para informar a los usuarios de cambios temporales o actualizaciones del laboratorio.
* [Configuración de directivas y programaciones](devtest-lab-set-lab-policy.md) proporciona información sobre cómo puede aplicar otras restricciones y convenciones en la suscripción mediante el uso de directivas personalizadas.