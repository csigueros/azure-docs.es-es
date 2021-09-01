---
title: Redireccionamiento multimedia en Azure Virtual Desktop - Azure
description: Uso del redireccionamiento multimedia para Azure Virtual Desktop (versión preliminar)
author: Heidilohr
ms.topic: how-to
ms.date: 08/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 574a10f6ef79ff3d40f5d62e49db9ebf198d2a79
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122272328"
---
# <a name="multimedia-redirection-for-azure-virtual-desktop-preview"></a>Redireccionamiento multimedia para Azure Virtual Desktop (versión preliminar)

> [!IMPORTANT]
> El redireccionamiento multimedia para Azure Virtual Desktop se encuentra actualmente en versión preliminar.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

>[!NOTE]
>Azure Virtual Desktop no admite actualmente el redireccionamiento multimedia en Azure Virtual Desktop para entornos Microsoft 365 Government (GCC), GCC-High y Microsoft 365 DoD.
>
>El redireccionamiento multimedia en Azure Virtual Desktop solo está disponible para el cliente de escritorio de Windows en máquinas con Windows 10. El redireccionamiento multimedia requiere el cliente de escritorio de Windows, versión 1.2.2222 o posterior.

El redireccionamiento multimedia (MMR) proporciona una reproducción de vídeo sin problemas cuando se ven vídeos en el explorador de Azure Virtual Desktop. El redireccionamiento multimedia envía el elemento multimedia desde el explorador a la máquina local para un procesamiento y una representación más rápidos. Tanto Microsoft Edge como Google Chrome admiten la característica de redireccionamiento multimedia. Sin embargo, la versión preliminar pública del redireccionamiento multimedia para Azure Virtual Desktop tiene una reproducción restringida en YouTube. Para probar YouTube dentro de la implementación de su organización, deberá [habilitar una extensión](#managing-group-policies-for-the-multimedia-redirection-browser-extension).

## <a name="requirements"></a>Requisitos

Para poder usar el redireccionamiento multimdia en Azure Virtual Desktop, tendrá que hacer lo siguiente:

1. Instale el [cliente de escritorio de Windows](./user-documentation/connect-windows-7-10.md#install-the-windows-desktop-client) en un dispositivo con Windows 10 o Windows 10 IoT Enterprise que cumpla los [requisitos de hardware de Teams de un equipo Windows](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/). La instalación de la versión 1.2.2222 o posterior del cliente también instalará el complemento de redireccionamiento multimedia (MsMmrDVCPlugin.dll) en el dispositivo cliente. Para más información sobre las actualizaciones y las nuevas versiones, consulte [Novedades del cliente de escritorio de Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew).

2. [Configure la máquina cliente para el grupo de Insider](create-host-pools-azure-marketplace.md).

3. Instale el [servicio Multimedia Redirector](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWIzIk) y cualquier extensión del navegador necesaria en la máquina virtual.

4. Configure la máquina cliente para permitir que los usuarios accedan al programa Insiders. Para configurar el cliente para el grupo de Insider, establece la siguiente información de registro:

   - **Clave**: HKLM\\Software\\Microsoft\\MSRDC\\Policies
   - **Tipo**: REG_SZ
   - **Nombre**: ReleaseRing
   - **Datos**: insider

   Para más información sobre el programa Insiders, consulte [Cliente de escritorio de Windows para administradores](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin#configure-user-groups).

5. Use el [instalador de MSI (MsMmrHostMri)](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWIzIk) para instalar las extensiones de redireccionamiento multimedia para el explorador de Internet en la máquina virtual de Azure. Actualmente, el redireccionamiento multimedia para Azure Virtual Desktop solo admite Microsoft Edge y Google Chrome.

## <a name="managing-group-policies-for-the-multimedia-redirection-browser-extension"></a>Administración de directivas de grupo para la extensión del navegador de redireccionamiento multimedia

El uso del archivo MSI de redireccionamiento multimedia instalará las extensiones del navegador. Sin embargo, como este servicio todavía está en versión preliminar pública, la experiencia del usuario puede variar. Para obtener más información acerca de los problemas conocidos, consulte [Problemas conocidos](#known-issues-and-limitations).

En algunos casos, puede cambiar la directiva de grupo para administrar las extensiones del navegador y mejorar la experiencia del usuario. Por ejemplo:

- Puede instalar la extensión sin interacción por parte del usuario.
- Puede restringir qué sitios web usan el redireccionamiento multimedia.
- Puede anclar el icono de extensión en Google Chrome de forma predeterminada. El icono de extensión ya está anclado de forma predeterminada en Microsoft Edge, por lo que solo tendrá que cambiar esta configuración en Chrome.

### <a name="configure-microsoft-edge-group-policies-for-multimedia-redirection"></a>Configuración de directivas de grupo de Microsoft Edge para el redireccionamiento multimedia

Para configurar las directivas de grupo, deberá editar la plantilla administrativa de Microsoft Ege. Las opciones de configuración de extensiones se encuentran en **Administrative Templates > Microsoft Edge > Extensions >**  > **Configure extension management settings** (Plantillas administrativas > Microsoft Edge > Extensiones > Configurar opciones de administración de extensiones).

El código siguiente es un ejemplo de una directiva de grupo de Microsoft Edge que hace que el explorador instale la extensión de redireccionamiento multimedia y solo permita la carga de redireccionamiento multimedia en YouTube:

```cmd
{ "joeclbldhdmoijbaagobkhlpfjglcihd": { "installation_mode": "force_installed", "runtime_allowed_hosts": [ "*://*.youtube.com" ], "runtime_blocked_hosts": [ "*://*" ], "update_url": "https://edge.microsoft.com/extensionwebstorebase/v1/crx" } }
```

Para más información sobre la configuración de directivas de grupo, consulte [Configurar las opciones de directiva de Microsoft Edge en Windows](/DeployEdge/configure-microsoft-edge).

### <a name="configure-google-chrome-group-policies-for-multimedia-redirection"></a>Configuración de directivas de grupo de Google Chrome para el redireccionamiento multimedia

Para configurar las directivas de grupo de Google Chrome, deberá editar la plantilla administrativa de Google Chrome. Las opciones de configuración de extensiones se encuentran en **Administrative Templates** > **Google** > **Google Chrome Extensions** > **Extension management settings**(Plantillas administrativas > Google Edge > Extensiones de Google Chrome > Configuración de administración de extensiones).

El ejemplo siguiente es muy parecido al ejemplo de código de [Configuración de directivas de grupo de Microsoft Edge para el redireccionamiento multimedia](#configure-microsoft-edge-group-policies-for-multimedia-redirection). Esta directiva forzará la instalación de la extensión de redireccionamiento multimedia con el icono anclado en el menú superior derecho y solo permitirá que el redireccionamiento multimedia se cargue en YouTube.

```cmd
{ "lfmemoeeciijgkjkgbgikoonlkabmlno": { "installation_mode": "force_installed", "runtime_allowed_hosts": [ "*://*.youtube.com" ], "runtime_blocked_hosts": [ "*://*" ], "toolbar_pin": "force_pinned", "update_url": "https://clients2.google.com/service/update2/crx" } }
```

Información adicional sobre la configuración de la [directiva de grupo de Google Chrome](https://support.google.com/chrome/a/answer/187202#zippy=%2Cwindows).

## <a name="run-the-multimedia-redirection-extension-manually-on-a-browser"></a>Ejecución manual de la extensión de redireccionamiento multimedia en un explorador

MMR usa aplicaciones remotas y el escritorio de sesión para los exploradores Microsoft Edge y Google Chrome. Una vez que haya cumplido los [requisitos](#requirements), abra el explorador compatible. Si no instaló los exploradores o la extensión con una directiva de grupo, los usuarios tendrán que ejecutar manualmente la extensión. En esta sección se explica cómo ejecutar manualmente la extensión en uno de los exploradores admitidos actualmente.

### <a name="microsoft-edge"></a>Microsoft Edge

Para ejecutar la extensión en Microsoft Edge manualmente, busque el signo de exclamación amarillo en el menú de desbordamiento. Debería ver un mensaje para habilitar la extensión de redireccionamiento multimedia de Azure Virtual Desktop. Seleccione **Habilitar extensión**.

### <a name="google-chrome"></a>Google Chrome

Para ejecutar la extensión en Google Chrome manualmente, busque el mensaje de notificación que indica que se ha instalado la nueva extensión, tal como se muestra en la captura de pantalla siguiente. 

![Captura de pantalla de la barra de tareas de Google Chrome. Hay una pestaña de notificación que dice "Nueva extensión añadida".](media/chrome-notification.png)

Seleccione la notificación para permitir que los usuarios habiliten la extensión. Los usuarios también deben anclar la extensión para que puedan ver en el icono si el redireccionamiento multimedia está conectado.

### <a name="the-multimedia-redirection-status-icon"></a>Icono de estado de redireccionamiento multimedia

Para saber rápidamente si el redireccionamiento multimedia está activo en el explorador, hemos agregado los siguientes estados de icono:

| Estado del icono  | Definición  |
|-----------------|-----------------|
| [Icono de programa predeterminado de Azure Virtual Desktop sin estado aplicado.](/media/icon-default.png) | Apariencia del icono predeterminado sin estado aplicado. |
| [Icono de programa de Azure Virtual Desktop con un cuadrado rojo con una x que indica que el redireccionamiento multimedia no funciona.](/media/icon-disconnect.png) | El cuadrado rojo con una "X" en su interior significa que el cliente no pudo conectarse al redireccionamiento multimedia. |
| [Icono de programa de Azure Virtual Desktop con un cuadrado verde con una marca de verificación en su interior, que indica que el redireccionamiento multimedia funciona.](/media/icon-connect.png) | El cuadrado verde con una marca de verificación en su interior significa que el cliente se conectó correctamente al redireccionamiento multimedia. |

Al seleccionar el icono se mostrará un menú emergente con una casilla que puede seleccionar para habilitar o deshabilitar el redireccionamiento multimedia en todos los sitios web. También se enumeran los números de versión de cada componente del servicio.

## <a name="send-feedback-during-public-preview"></a>Envío de comentarios durante la versión preliminar pública

Si tiene algún problema, puede indicárnoslo en el concentrador de comentarios, ya sea en el cliente o en el host de máquina virtual.

Para enviarnos sus comentarios:

1. Abra el **concentrador de comentarios** en el cliente y el servidor.

2. Seleccione **Notificar un problema**.

3. Use el mismo título en ambos informes de problemas, pero especifique desde dónde va a enviar el informe incluyendo "[Cliente]" o "[Host]" al principio.

    Por ejemplo, si envía un problema desde el cliente, tendría el siguiente formato:

    >[Cliente] Título del informe

    Si envía un problema desde el host, tendría el siguiente formato:

    >[Host] Título del informe

4. En el campo **Explain in more detail** (Explicar con más detalle), describa el problema que está teniendo. También se recomienda incluir la dirección URL del vídeo que estaba viendo cuando se produjo el problema.

5. Cuando finalice, seleccione **Next** (Siguiente).

6. Seleccione la burbuja **Problem** (Problema), después seleccione **Apps** (Aplicaciones) y **Remote Desktop** (Escritorio remoto) en los dos menús desplegables, como se muestra en la siguiente captura.

    ![Captura de pantalla de la ventana "2. Choose a category" (2. Elija una categoría). El usuario ha seleccionado la burbuja Problem (Problema) y, a continuación, ha seleccionado Apps (Aplicaciones) y Remote Desktop (Escritorio remoto) en los menús desplegables debajo de ella.](media/problem-category.png)

7. Seleccione **Siguiente**.

8. Compruebe si hay un problema similar en la lista al que planea enviar.
   
   - Si aparece una burbuja con un vínculo a un error activo, asegúrese de que la descripción del error coincide con el problema que está notificando. Si es así, seleccione la burbuja y, a continuación, seleccione **Link to bug** (Vincular al error), como se muestra en la captura de pantalla siguiente.

    ![Captura de pantalla de la ventana "3. Find similar feedback" (Buscar comentarios similares). El usuario ha seleccionado la burbuja para la opción "Link to bug number 32350300 Active" (Vincular al número de error 32350300 activo).](media/link-to-bug.png)

    - Si no ve un problema similar, seleccione **Make new bug** (Crear nuevo error).

    ![Captura de pantalla de la ventana "3. Find similar feedback" (Buscar comentarios similares). Esta vez, la opción "Link to bug" (Vincular al error) no está presente y el usuario ha seleccionado en su lugar "Make new bug" (Crear nuevo error).](media/make-new-bug.png)

9. Seleccione **Siguiente**.

10. En la ventana **Add more details** (Agregar más detalles), seleccione **Include data about Remote Desktop (Default)** (Incluir datos sobre Escritorio remoto [valor predeterminado]) y, a continuación, responda a todas las preguntas con tantos detalles como sea posible.

    Si desea agregar una grabación de vídeo del problema, seleccione **Include data about Remote Desktop (Default)** (Incluir datos sobre Escritorio remoto [valor predeterminado]) y, a continuación, seleccione el botón **Start recording** (Iniciar grabación). Mientras graba, abra Escritorio remoto y realice el proceso que condujo al problema. Cuando haya terminado, vuelva al explorador y pruebe el vídeo para asegurarse de que se ha grabado correctamente.

    Cuando haya terminado, acepte el envío de los archivos adjuntos y los diagnósticos a Microsoft y, a continuación, seleccione **Submit** (Enviar).

### <a name="known-issues-and-limitations"></a>Limitaciones y problemas conocidos

A continuación se indican problemas que ya conocemos, por lo que no tendrá que notificarlos:

- El redireccionamiento multimedia solo funciona en el cliente del escritorio de Windows, no en el cliente web.

- Actualmente, el redireccionamiento multimedia no admite contenido protegido, por lo que los vídeos de Pluralsight y Netflix no funcionarán.

- Durante la versión preliminar pública, el redireccionamiento multimedia estará deshabilitado en todos los sitios excepto YouTube. Sin embargo, si tiene la extensión, puede habilitar el redireccionamiento multimedia para todos los sitios web. Hemos agregado la extensión para que las organizaciones puedan probar la característica en los sitios web de su empresa.

- Existe una pequeña posibilidad de que el instalador de MSI no pueda instalar la extensión durante las pruebas internas. Si se encuentra con este problema, deberá instalar la extensión de redireccionamiento multimedia desde Microsoft Edge Store o Google Chrome Store.

    - [Extensión del navegador de redireccionamiento multimedia (Microsoft Edge)](https://microsoftedge.microsoft.com/addons/detail/wvd-multimedia-redirectio/joeclbldhdmoijbaagobkhlpfjglcihd)
    - [Extensión del explorador multimedia (Google Chrome)](https://chrome.google.com/webstore/detail/wvd-multimedia-redirectio/lfmemoeeciijgkjkgbgikoonlkabmlno)

- La instalación de la extensión en equipos host con el instalador de MSI pedirá a los usuarios que acepten la extensión la primera vez que abran el explorador o bien mostrará un mensaje de error o advertencia. Si los usuarios deniegan este mensaje, puede que la extensión no se cargue. Para evitar este problema, instale las extensiones [editando la directiva de grupo](#managing-group-policies-for-the-multimedia-redirection-browser-extension).

- Al cambiar el tamaño de la ventana de vídeo, el tamaño de la ventana se ajustará más rápido que el propio vídeo. También sucederá este problema al minimizar y maximizar la ventana.

## <a name="next-steps"></a>Pasos siguientes

Si está interesado en el streaming de vídeo en otras partes de Azure Virtual Desktop, consulte [Uso de Microsoft Teams en Azure Virtual Desktop](teams-on-avd.md).
