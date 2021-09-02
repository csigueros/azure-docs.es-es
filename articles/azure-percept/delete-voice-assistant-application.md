---
title: Eliminación de la aplicación de asistente para voz de Azure Percept Audio
description: En este artículo se muestra cómo eliminar una aplicación de asistente para voz creada anteriormente.
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: template-how-to
ms.openlocfilehash: e7f9f9dd0bf1caead19151eb8081e967963fae9a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224960"
---
# <a name="delete-your-azure-percept-audio-voice-assistant-application"></a>Eliminación de la aplicación de asistente para voz de Azure Percept Audio

Estas instrucciones muestran cómo eliminar una aplicación de asistente para voz del dispositivo Azure Percept Audio.

## <a name="prerequisites"></a>Requisitos previos

- [Una aplicación de asistente para voz creada anteriormente](./tutorial-no-code-speech.md)
- Azure Percept DK encendido y el accesorio Azure Percept Audio conectado a través de un cable USB.

## <a name="remove-all-voice-assistant-resources-from-the-azure-portal"></a>Eliminación de todos los recursos de asistente para voz desde Azure Portal

Cuando haya terminado de trabajar con la aplicación de asistente para voz, siga estos pasos para limpiar los recursos de voz implementados al crear la aplicación.

1. En [Azure Portal](https://portal.azure.com), seleccione **Grupos de recursos** en el panel de menú de la izquierda o escríbalo en la barra de búsqueda.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Captura de pantalla de la página principal de Azure Portal que muestra el panel de menú izquierdo y los grupos de recursos.":::

1. Seleccione el grupo de recursos que necesite.

1. Seleccione los seis recursos que contienen el prefijo de la aplicación y luego el icono **Eliminar** en el panel de menús superior.

    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="Captura de pantalla de los recursos de voz seleccionados para su eliminación.":::

1. Para confirmar la eliminación, escriba **sí** en el cuadro de confirmación, compruebe que ha seleccionado los recursos correctos y seleccione **Eliminar**.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Captura de pantalla de la ventana de confirmación de eliminación.":::

> [!WARNING]
> Se eliminarán todas las palabras clave personalizadas creadas con los recursos de voz que va a eliminar y la demostración del asistente para voz dejará de funcionar.


## <a name="next-steps"></a>Pasos siguientes
Ahora que ha quitado la aplicación de asistente para voz, intente crear otras aplicaciones en Azure Percept DK con estos tutoriales.
- [Creación de una solución de visión sin código](./tutorial-nocode-vision.md)
- [Creación de una aplicación de asistente para voz sin código](./tutorial-no-code-speech.md)


