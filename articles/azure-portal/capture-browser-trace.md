---
title: Captura de un seguimiento del explorador para solucionar problemas
description: Capture la información de red desde un seguimiento del explorador para ayudar a solucionar problemas con Azure Portal.
ms.date: 08/16/2021
ms.topic: troubleshooting
ms.openlocfilehash: d1db82c9671879c435a6dba73929d9a4eb183f7f
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228878"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Captura de un seguimiento del explorador para solucionar problemas

Si está solucionando un problema con Azure Portal y necesita contactar con el soporte de Microsoft, se recomienda capturar primero un seguimiento del explorador e información adicional. La información que recopile puede proporcionar detalles importantes sobre el portal en el momento en que se produce el problema. Siga los pasos de este artículo para las herramientas de desarrollo en el explorador que usa: Google Chrome o Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML), Apple Safari o Firefox.

> [!IMPORTANT]
> El equipo de soporte técnico de Microsoft usa estos seguimientos solo con fines de solución de problemas. Tenga en cuenta con quién comparte los seguimientos, ya que pueden contener información confidencial sobre el entorno.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome y Microsoft Edge (Chromium)

Google Chrome y Microsoft Edge (Chromium) se basan en el [proyecto de código abierto de Chromium](https://www.chromium.org/Home). En los pasos siguientes se muestra cómo usar las herramientas de desarrollo, que son muy similares en los dos exploradores. Para obtener más información, consulte [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) y [Herramientas de desarrollo de Microsoft Edge (Chromium)](/microsoft-edge/devtools-guide-chromium).

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Es importante iniciar sesión _antes_ de iniciar el seguimiento para que este no contenga información confidencial relacionada con el inicio de sesión.

1. Empiece a grabar las acciones que se llevan a cabo en el portal mediante la [Grabación de acciones](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. En el portal, vaya al paso que se encuentra justo antes del problema.

1. Presione F12 o seleccione ![Captura de pantalla que muestra el icono de configuración del explorador.](media/capture-browser-trace/chromium-icon-settings.png) > **Más herramientas** > **Herramientas de desarrollo**.

1. De forma predeterminada, el explorador solo conserva la información de seguimiento de la página que está cargada actualmente. Establezca las siguientes opciones para que el explorador mantenga toda la información de seguimiento, incluso si la reproducción requiere ir a más de una página:

    1. Seleccione la pestaña **Red** y **Conservar registro**.

          ![Captura de pantalla que resalta la opción Conservar registro en la pestaña Red.](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Seleccione la pestaña **Consola**, seleccione **Configuración de la consola** y, a continuación, seleccione **Conservar registro**. Vuelva a seleccionar **Configuración de la consola** para cerrar el panel de configuración.

          ![Captura de pantalla que resalta la opción Conservar registro en la pestaña Consola.](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Seleccione la pestaña **Red** y, a continuación, seleccione **Dejar de grabar el registro de red** y **Borrar**.

    ![Captura de pantalla de "Dejar de grabar el registro de red" y "Borrar"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Seleccione **Grabar registro de red** y reproduzca el problema en el portal.

    ![Captura de pantalla que muestra cómo realizar el registro de red.](media/capture-browser-trace/chromium-start-session.png)

    Verá un resultado de sesión similar al de la imagen siguiente.

    ![Captura de pantalla que muestra la salida de la sesión.](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Una vez que haya reproducido el comportamiento inesperado del portal, seleccione **Dejar de grabar el registro de red** y, a continuación, seleccione **Exportar HAR** y guarde el archivo.

    ![Captura de pantalla que muestra cómo exportar archivos HAR en la pestaña Red.](media/capture-browser-trace/chromium-network-export-har.png)

1. Detenga la grabación de acciones y guarde el archivo.

1. En el panel de herramientas de desarrollo del explorador, seleccione la pestaña **Consola**. Haga clic con el botón derecho en uno de los mensajes y, a continuación, seleccione **Guardar como...** y guarde la salida de la consola en un archivo de texto.

    ![Captura de pantalla en la que se resalta la pestaña Consola y el menú Guardar como.](media/capture-browser-trace/chromium-console-select.png)

1. Empaquete el archivo HAR, el resultado de la consola y la grabación de pantalla en un formato comprimido como .zip, y comparta esta información con el soporte técnico de Microsoft.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

En los pasos siguientes se muestra cómo usar las herramientas de desarrollo de Microsoft Edge (EdgeHTML). Para obtener más información, vea [Herramientas de desarrollo de Microsoft Edge (EdgeHTML)](/microsoft-edge/devtools-guide).

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Es importante iniciar sesión _antes_ de iniciar el seguimiento para que este no contenga información confidencial relacionada con el inicio de sesión. 

1. Empiece a grabar las acciones que se llevan a cabo en el portal mediante la [Grabación de acciones](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. En el portal, vaya al paso que se encuentra justo antes del problema.

1. Presione F12 o seleccione ![Captura de pantalla del icono de configuración del explorador.](media/capture-browser-trace/edge-icon-settings.png) > **Más herramientas** > **Herramientas de desarrollo**.

1. De forma predeterminada, el explorador solo conserva la información de seguimiento de la página que está cargada actualmente. Establezca las siguientes opciones para que el explorador mantenga toda la información de seguimiento, incluso si la reproducción requiere ir a más de una página:

    1. Seleccione la pestaña **Red** y, a continuación, desactive la opción **Borrar entradas al navegar**.

          ![Captura de pantalla de "Borrar entradas al navegar"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Seleccione la pestaña **Consola** y **Conservar registro**.

          ![Captura de pantalla de "Conservar registro"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Seleccione la pestaña **Red** y, a continuación, seleccione **Detener la sesión de generación de perfiles** y **Borrar sesión**.

    ![Captura de pantalla de "Detener la sesión de generación de perfiles" y "Borrar sesión"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Seleccione **Iniciar sesión de generación de perfiles** y reproduzca el problema en el portal.

    ![Captura de pantalla de "Inicio de la generación de perfiles"](media/capture-browser-trace/edge-start-session.png)

    Verá un resultado de sesión similar al de la imagen siguiente.

    ![Captura de pantalla que muestra la salida de la sesión de generación de perfiles.](media/capture-browser-trace/edge-browser-trace-results.png)

1. Una vez que haya reproducido el comportamiento inesperado del portal, seleccione **Detener la sesión de generación de perfiles** y, a continuación, seleccione **Exportar como HAR** y guarde el archivo.

    ![Captura de pantalla de "Exportar como HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Detenga la grabación de acciones y guarde el archivo.

1. En el panel de herramientas de desarrollo del explorador, seleccione la pestaña **Consola** y expanda la ventana. Coloque el cursor al principio del resultado de la consola y, a continuación, arrastre y seleccione todo el contenido del resultado. Haga clic con el botón derecho y seleccione **Copiar** y guarde el resultado de la consola en un archivo de texto.

    ![Captura de pantalla que resalta la opción de menú Copiar.](media/capture-browser-trace/edge-console-select.png)

1. Empaquete el archivo HAR, el resultado de la consola y la grabación de pantalla en un formato comprimido como .zip, y comparta esta información con el soporte técnico de Microsoft.

## <a name="apple-safari"></a>Apple Safari

En los pasos siguientes se muestra cómo usar las herramientas de desarrollo en Apple Safari. Para obtener más información, consulte [Introducción a las Herramientas de desarrollo de Safari](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Habilite las herramientas de desarrollo en Apple Safari

    1. Seleccione **Safari** y, a continuación, seleccione **Preferencias**.

        ![Captura de pantalla de las preferencias de Safari](media/capture-browser-trace/safari-preferences.png)

    1. Seleccione la pestaña **Avanzadas** y, a continuación, seleccione **Mostrar menú Desarrollar en la barra de menús**.

        ![Captura de pantalla de las preferencias avanzadas de Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Es importante iniciar sesión _antes_ de iniciar el seguimiento para que este no contenga información confidencial relacionada con el inicio de sesión. 

1. Empiece a grabar las acciones que se llevan a cabo en el portal. Para obtener más información, consulte [Cómo grabar la pantalla en el Mac](https://support.apple.com/HT208721).

1. En el portal, vaya al paso que se encuentra justo antes del problema.

1. Seleccione **Desarrollar** y, a continuación, seleccione **Mostrar inspector web**.

    ![Captura de pantalla de "Mostrar inspector web"](media/capture-browser-trace/safari-show-web-inspector.png)

1. De forma predeterminada, el explorador solo conserva la información de seguimiento de la página que está cargada actualmente. Establezca las siguientes opciones para que el explorador mantenga toda la información de seguimiento, incluso si la reproducción requiere ir a más de una página:

    1. Seleccione la pestaña **Red** y **Conservar registro**.

          ![Captura de pantalla que muestra la opción Conservar registro.](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Seleccione la pestaña **Consola** y **Conservar registro**.

          ![Captura de pantalla que muestra la opción Conservar registro en la pestaña Consola.](media/capture-browser-trace/safari-console-preserve-log.png)

1. Seleccione la pestaña **Red** y **Borrar elementos de red**.

    ![Captura de pantalla de "Borrar elementos de red"](media/capture-browser-trace/safari-clear-session.png)

1. Reproduzca el problema en el portal. Verá un resultado de sesión similar al de la imagen siguiente.

    ![Captura de pantalla que muestra la salida después de reproducir el problema.](media/capture-browser-trace/safari-browser-trace-results.png)

1. Una vez que haya reproducido el comportamiento inesperado del portal, seleccione **Exportar** y guarde el archivo.

    ![Captura de pantalla de "Exportar"](media/capture-browser-trace/safari-network-export-har.png)

1. Detenga la grabadora de pantalla y guarde la grabación.

1. En el panel de herramientas de desarrollo del explorador, seleccione la pestaña **Consola** y expanda la ventana. Coloque el cursor al principio del resultado de la consola y, a continuación, arrastre y seleccione todo el contenido del resultado. Use Comando-C para copiar el resultado y guárdelo en un archivo de texto.

    ![Captura de pantalla que resalta que puede ver y copiar la salida.](media/capture-browser-trace/safari-console-select.png)

1. Empaquete el archivo HAR, el resultado de la consola y la grabación de pantalla en un formato comprimido como .zip, y comparta esta información con el soporte técnico de Microsoft.

## <a name="firefox"></a>Firefox

En los pasos siguientes se muestra cómo usar las herramientas de desarrollo en Firefox. Para más información, consulte [Herramientas de desarrollo de Firefox](https://developer.mozilla.org/docs/Tools).

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Es importante iniciar sesión _antes_ de iniciar el seguimiento para que este no contenga información confidencial relacionada con el inicio de sesión.

1. Empiece a grabar las acciones que se llevan a cabo en el portal. Use la [grabación de acciones](https://support.microsoft.com/help/22878/windows-10-record-steps) en Windows o consulte [Cómo grabar la pantalla en el Mac](https://support.apple.com/HT208721).

1. En el portal, vaya al paso que se encuentra justo antes del problema.

1. Presione F12 o seleccione ![Captura de pantalla del icono de configuración del explorador](media/capture-browser-trace/firefox-icon-settings.png) > **Desarrollo web** > **Alternar herramientas**.

1. De forma predeterminada, el explorador solo conserva la información de seguimiento de la página que está cargada actualmente. Establezca las siguientes opciones para que el explorador mantenga toda la información de seguimiento, incluso si la reproducción requiere ir a más de una página:

    1. Seleccione la pestaña **Red** y, a continuación, **Conservar los registros**.

          ![Captura de pantalla que resalta la opción Conservar los registros.](media/capture-browser-trace/firefox-network-persist-logs.png)

    1. Seleccione la pestaña **Consola**, seleccione **Configuración de la consola** y, después, **Conservar los registros**.

          ![Captura de pantalla de "Conservar los registros"](media/capture-browser-trace/firefox-console-persist-logs.png)

1. Seleccione la pestaña **Red** y, a continuación, **Borrar**.

    ![Captura de pantalla de "Borrar"](media/capture-browser-trace/firefox-clear-session.png)

1. Reproduzca el problema en el portal. Verá un resultado de sesión similar al de la imagen siguiente.

    ![Captura de pantalla de los resultados de seguimiento del explorador](media/capture-browser-trace/firefox-browser-trace-results.png)

1. Una vez que haya reproducido el comportamiento inesperado del portal, seleccione **Guardar todo como HAR**.

    ![Captura de pantalla de "Exportar HAR"](media/capture-browser-trace/firefox-network-export-har.png)

1. Detenga la grabadora de acciones en Windows o la grabación de pantalla en Mac y guarde la grabación.

1. En el panel de herramientas de desarrollo del explorador, seleccione la pestaña **Consola**. Haga clic con el botón derecho en uno de los mensajes y, a continuación, seleccione **Exportar mensajes visibles a** y guarde la salida de la consola en un archivo de texto.

    ![Captura de pantalla del resultado de la consola](media/capture-browser-trace/firefox-console-select.png)

1. Empaquete el archivo HAR, el resultado de la consola y la grabación de pantalla en un formato comprimido como .zip, y comparta esta información con el soporte técnico de Microsoft.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Azure Portal](azure-portal-overview.md).
- Obtenga información sobre cómo [abrir una solicitud de soporte técnico](supportability/how-to-create-azure-support-request.md) en Azure Portal.
