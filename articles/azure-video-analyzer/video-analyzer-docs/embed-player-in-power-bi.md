---
title: 'Inserción del widget de reproductor en Power BI: Azure Video Analyzer'
description: Puede usar Azure Video Analyzer para la grabación continua de vídeo o la grabación basada en eventos. En este artículo se habla sobre cómo insertar vídeos en Microsoft Power BI para proporcionar una interfaz de usuario personalizable para los usuarios.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 08/06/2021
ms.openlocfilehash: 38fcd60352fe151637e0e7f8fa4dfe9159b12915
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620718"
---
# <a name="embed-player-widget-in-power-bi"></a>Inserción del widget de reproductor en Power BI

Azure Video Analyzer le permite [grabar](detect-motion-record-video-clips-cloud.md) vídeo y los metadatos de inferencia asociados en el recurso de nube de Video Analyzer. Video Analyzer incluye un [widget de reproductor](player-widget.md), un widget fácil de insertar que permite a las aplicaciones cliente reproducir vídeo y metadatos de inferencia.

Los paneles son una manera esclarecedora de supervisar el negocio y ver todas las métricas más importantes de un vistazo. Los paneles de Power BI son una herramienta eficaz para combinar vídeo con varios orígenes de datos, incluida la telemetría de IoT Hub. En este tutorial, aprenderá a agregar uno o varios widgets de reproductor a un panel mediante el servicio web de [Microsoft Power BI](https://powerbi.microsoft.com/).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/embed-block-diagram.png" alt-text="Diagrama de bloques para insertar el widget del reproductor de Azure Video Analyzer en Microsoft Power BI.":::

## <a name="suggested-pre-reading"></a>Sugerencias antes de la lectura

- [Widget de reproductor](player-widget.md) de Azure Video Analyzer
- Introducción a los [paneles de Power BI](/power-bi/create-reports/service-dashboards)

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), en caso de que aún no lo haya hecho.
- Complete los inicios rápidos [Detección de movimiento y grabación de vídeo](detect-motion-record-video-clips-cloud.md) o [Grabación continua de vídeo](continuous-video-recording.md); se requiere una canalización con un receptor de vídeo.

  > [!NOTE]
  > La cuenta del analizador de vídeo debe tener un vídeo grabado como mínimo para continuar. Para comprobar la lista de vídeos, inicie sesión en su cuenta de Azure Video Analyzer > Vídeos > Video Analyzer.

- Una cuenta de [Power BI](https://powerbi.microsoft.com/).

## <a name="create-a-token"></a>Creación de un token

1. Siga estos pasos para [crear un token](access-policies.md#creating-a-token).
2. Asegúrese de guardar los valores generados para _emisor, audiencia, tipo de clave, algoritmo, identificador de clave, módulo de clave RSA, exponente de clave RSA, token_. Los necesitará al crear una directiva de acceso a continuación.

## <a name="get-embed-code-for-player-widget"></a>Obtención de código para insertar para el widget de reproductor

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con sus credenciales. Busque la cuenta de Video Analyzer usada para completar los requisitos previos y abra el panel de la cuenta de Video Analyzer.
2. Siga los pasos para [crear una directiva de acceso](access-policies.md#creating-an-access-policy).
3. Seleccione **Vídeos** en la sección **Video Analyzer**.
4. Seleccione cualquier vídeo de la lista.
5. Haga clic en el programa de instalación de **Widget**. Se abre un panel **Use widget in your application** (Usar widget en la aplicación) en el lado derecho. Desplácese hasta **Option 2 – using HTML** (Opción 2: con HTML) y péguelo en un editor de texto. Haga clic en el botón **Cerrar**.

   :::image type="content" source="./media/power-bi/widget-code.png" alt-text="Captura de pantalla que muestra cómo copiar el código HTML del widget desde el portal de AVA y guardarlo para más adelante.":::

6. Edite el código HTML copiado en el paso 5 para reemplazar los valores de:
   - Token **AVA-API-JWT-TOKEN**: reemplácelo por el valor de token que guardó en el paso de creación de un token. Asegúrese de quitar los corchetes angulares.
   - Opcional: puede realizar otros cambios de la interfaz de usuario en este código, por ejemplo, cambiar el encabezado de "Widget de reproductor de ejemplo" por "Grabación continua de vídeo".

## <a name="add-widget-in-power-bi-dashboard"></a>Adición del widget al panel de Power BI

1. Abra el [servicio Power BI](http://app.powerbi.com/) en el explorador. En el panel de navegación, seleccione **Mis áreas de trabajo**.

   :::image type="content" source="./media/power-bi/powerbi-ws.png" alt-text="Captura de pantalla de la página principal del área de trabajo de Power BI.":::

2. Cree un panel haciendo clic en **Nuevo** > **Panel** o abra uno ya existente. Seleccione la flecha desplegable **Editar** y, luego, **Add a tile** (Agregar un icono). Seleccione **Contenido web** > **Siguiente**.
3. En el icono **Agregar contenido web**, escriba el **código para insertar** de la sección anterior. Haga clic en **Aplicar**.

   :::image type="content" source="./media/power-bi/embed-code.png" alt-text="Captura de pantalla de la inserción de código HTML en un icono de panel de Power BI.":::

4. Verá un widget de reproductor anclado al panel con un vídeo.

   :::image type="content" source="./media/power-bi/one-player.png" alt-text="Captura de pantalla de un widget de reproductor de vídeo agregado.":::

5. Para agregar más vídeos desde la sección Vídeos de Azure Video Analyzer, siga los mismos pasos de esta sección.

> [!NOTE]
> Para agregar varios vídeos desde la misma cuenta de Video Analyzer, basta con un único conjunto de directivas de acceso y un token.

Este es un ejemplo de varios vídeos anclados a un único panel de Power BI.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/two-players.png" alt-text="Captura de pantalla de dos widgets de reproductor de vídeo agregados como ejemplo.":::

## <a name="next-steps"></a>Pasos siguientes

- [Visualización en tiempo real de eventos de inferencia de IA con Power BI](visualize-ai-events-power-bi.md)
- Más información sobre la [API de widget](https://github.com/Azure/video-analyzer/tree/main/widgets)
