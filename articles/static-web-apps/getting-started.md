---
title: 'Inicio rápido: creación del primer sitio estático con Azure Static Web Apps'
description: Aprenda a implementar un sitio estático en Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 06/16/2021
ms.author: cshoe
ms.openlocfilehash: 3ec36a06dafb8622f97ded317f8ab2617e96662a
ms.sourcegitcommit: d137460f55a38a0e8f8b9e6594e480d5e5f662ed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112428837"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Inicio rápido: creación del primer sitio estático con Azure Static Web Apps

Azure Static Web Apps publica un sitio web mediante la creación de una aplicación desde un repositorio de código. En este inicio rápido se implementa una aplicación en Azure Static Web Apps mediante la extensión de Visual Studio Code.

Si no tiene ninguna suscripción a Azure, [cree una cuenta de evaluación gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Requisitos previos

- [GitHub](https://github.com)
- Cuenta de [Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Extensión de Azure Static Web Apps para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Instalación de Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Después, abra Visual Studio Code y vaya a **Archivo > Abrir carpeta** para abrir el repositorio clonado en el editor.

## <a name="create-a-static-web-app"></a>Creación de una aplicación web estática

1. En Visual Studio Code, seleccione el logotipo de Azure en la barra de actividades para abrir la ventana extensiones de Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logotipo de Azure":::

    > [!NOTE]
    > Tendrá que iniciar sesión en Azure y GitHub en Visual Studio Code para continuar. Si todavía no está autenticado, la extensión le solicitará que inicie sesión en los dos servicios durante el proceso de creación.

1. En la etiqueta _Static Web Apps_, seleccione el **signo más**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Nombre de la aplicación":::

1. La paleta de comandos se abre en la parte superior del editor y le pide que seleccione un nombre de suscripción.

    Seleccione la suscripción y, después, presione, <kbd>Entrar</kbd>.

    :::image type="content" source="media/getting-started/extension-subscription.png" alt-text="Selección de una suscripción de Azure":::

1. Después, asigne un nombre a la aplicación.

    Escriba **my-first-static-web-app** y presione <kbd>Entrar</kbd>.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Creación de la aplicación web estática":::

1. Seleccione los valores preestablecidos que coincidan con el tipo de aplicación.

    # <a name="no-framework"></a>[Ningún marco](#tab/vanilla-javascript)

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Valores preestablecidos de la aplicación: Ningún marco":::

    Escriba **./** como ubicación de los archivos de la aplicación y presione <kbd>Entrar</kbd>.

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Ubicación de la salida de la compilación de aplicación":::

    Escriba **./** como la ubicación de la salida de compilación y presione <kbd>Entrar</kbd>.

    # <a name="angular"></a>[Angular](#tab/angular)

    Aunque hay un valor preestablecido de Angular, seleccione la opción **Personalizar** para proporcionar una ubicación de salida adecuada para esta aplicación.

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Valores preestablecidos de la aplicación: Angular":::

    Escriba **./** como la ubicación de los archivos de aplicación.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Ubicación de los archivos de la aplicación: Angular":::

    Especifique **dist/angular-basic** como la ubicación de salida de compilación.

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="Ubicación de la salida de compilación de aplicación: Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Valores preestablecidos de la aplicación: React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Valores preestablecidos de la aplicación: Vue":::

    ---

1. Una vez creada la aplicación, se muestra una notificación de confirmación en Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Confirmación creada":::

    A medida que la implementación está en curso, la extensión Visual Studio Code le informa del estado de compilación.

    :::image type="content" source="media/getting-started/extension-waiting-for-deployment.png" alt-text="Esperando la implementación":::

    Cuando se complete la implementación, puede navegar directamente al sitio web.

1. Para ver el sitio web en el explorador, haga clic con el botón derecho en el proyecto en la extensión de Static Web Apps y seleccione **Browse site** (Examinar sitio).

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Examinar sitio":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, puede eliminar la instancia de Azure Static Web Apps mediante la extensión.

En la ventana Explorador de Visual Studio Code, vuelva a la sección _Static Web Apps_ y haga clic con el botón derecho en **my-first-static-web-app** y seleccione **Eliminar**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Eliminar la aplicación":::

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Adición de una API](add-api.md)
