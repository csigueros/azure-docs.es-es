---
title: Configuración de un laboratorio con React mediante Azure Lab Services
description: Aprenda a configurar laboratorios para una clase de desarrollo con React.
author: emaher
ms.topic: article
ms.date: 05/16/2021
ms.author: enewman
ms.openlocfilehash: 60ac7c3a95564fad5c271c543beac875334b05a1
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483507"
---
# <a name="set-up-lab-for-react-on-linux"></a>Configuración del laboratorio para React en Linux

[React](https://reactjs.org/) es una popular biblioteca de JavaScript para crear interfaces de usuario (UI). React es una manera declarativa de crear componentes reutilizables para el sitio web.  Hay muchas otras bibliotecas populares para el desarrollo front-end basado en JavaScript.  Usaremos algunas de estas bibliotecas durante la creación de nuestro laboratorio.  [Redux](https://redux.js.org/) es una biblioteca que proporciona un contenedor de estado predecible para las aplicaciones JavaScript y que a menudo se usa en conjunto con React. [JSX](https://reactjs.org/docs/introducing-jsx.html) es una extensión de sintaxis de biblioteca para JavaScript que a menudo se usa con React para describir el aspecto de la interfaz de usuario.  [NodeJS](https://nodejs.org/) es una manera cómoda de ejecutar un servidor web para la aplicación React.

En este artículo se muestra cómo instalar [Visual Studio Code](https://code.visualstudio.com/) para el entorno de desarrollo, las herramientas y bibliotecas necesarias para una clase de desarrollo web con React.

## <a name="lab-configuration"></a>Configuración del laboratorio

Para configurar este laboratorio, para empezar, necesita una suscripción a Azure y una cuenta de laboratorio. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. Una vez que obtenga una suscripción a Azure, puede crear una nueva cuenta de laboratorio en Azure Lab Services. Para más información sobre la creación de una nueva cuenta de laboratorio, consulte el tutorial sobre la [configuración de una cuenta de laboratorio](./tutorial-setup-lab-account.md). También puede usar una cuenta de laboratorio existente.

### <a name="lab-account-settings"></a>Configuración de la cuenta de laboratorio

Habilite la configuración de la cuenta de laboratorio tal y como se describe en la tabla siguiente. Para obtener más información sobre cómo habilitar imágenes de Azure Marketplace, consulte [Especificación de las imágenes de Azure Marketplace disponibles para los creadores de laboratorios](./specify-marketplace-images.md).

| Configuración de la cuenta de laboratorio | Instructions |
| ----------- | ------------ |  
| Imágenes de Marketplace | Habilite la imagen"Ubuntu Server 18.04 LTS" para su uso en la cuenta de laboratorio. |

### <a name="lab-settings"></a>Configuración del laboratorio

El tamaño de la máquina virtual (VM) que se recomienda depende de los tipos de cargas de trabajo de las que los alumnos tienen que encargarse.  

| Configuración del laboratorio | Valor y descripción |
| ------------ | ------------------ |
| Tamaño de la máquina virtual | **Pequeña**|

Se recomienda probar las cargas de trabajo para ver si se necesita un mayor tamaño.  Para obtener más información sobre cada tamaño, consulte [Tamaño de máquina virtual](administrator-guide.md#vm-sizing).

## <a name="template-machine-configuration"></a>Configuración de la máquina de plantilla

Los pasos de esta sección muestran cómo rellenar los siguientes datos para configurar la plantilla de máquina virtual:

1. Instalación de herramientas de desarrollo.
1. Instalación de extensiones del depurador para el explorador web.
1. Actualización de la configuración del firewall.

### <a name="install-development-tools"></a>Instalación de herramientas de desarrollo

1. Instale el explorador web que prefiera.  
1. [Instale Node.js](https://nodejs.org).

    ```bash
    sudo apt install nodejs
    ```

1. Instale el [Administrador de paquetes de Node](https://www.npmjs.com/), que se usará para instalar React, Redux y JSX.

    ```bash
    sudo apt install npm
    ```

1. Instale [Visual Studio Code](https://code.visualstudio.com/docs/setup/linux).
1. Instale la [extensión Herramientas de React Native para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=msjsdiag.vscode-react-native).
1. Opcionalmente, instale las extensiones para el desarrollo con [Redux](https://marketplace.visualstudio.com/search?term=Redux&target=VSCode&category=All%20categories&sortBy=Relevance) y [JSX](https://marketplace.visualstudio.com/search?term=JSX&target=VSCode&category=All%20categories&sortBy=Relevance).

[Create React App](https://create-react-app.dev/) es una manera oficialmente compatible de crear una aplicación ReactApp y no requiere ninguna configuración adicional si se usa npm 5.2 y versiones posteriores.  Para obtener instrucciones sobre cómo crear una aplicación React, consulte la documentación de [introducción](https://create-react-app.dev/docs/getting-started).

Otros componentes necesarios para un sitio web basado en React se instalan mediante NPM en una aplicación específica. Por ejemplo, escriba los siguientes comandos para instalar las bibliotecas Redux y JSX:

```bash
npm install react-redux
npm install react-jsx
```

### <a name="install-debugger-extensions"></a>Instalación de extensiones del depurador

Instale las extensiones React Developer Tools del explorador para que pueda inspeccionar los componentes de React y registrar la información de rendimiento.  

- [Complemento React Developer Tools de Edge](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)
- [Extensión React Developer Tools de Chrome](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- [Complemento React Developer Tools de FireFox](https://addons.mozilla.org/firefox/addon/react-devtools/)

Para ejecutar la aplicación en modo de desarrollo, use el comando integrado `npm start`.  Las direcciones URL locales y de red se mostrarán en la salida del comando.  Para usar HTTPS en lugar de HTTP, consulte [Creación de una aplicación React mediante HTTPS en desarrollo](https://create-react-app.dev/docs/using-https-in-development).

>[!IMPORTANT]
>Los instructores deben usar la plantilla de máquina virtual u otra máquina virtual de laboratorio para acceder al sitio web de un alumno.

## <a name="cost"></a>Coste

Veamos un ejemplo de una posible estimación de costos para esta clase.  Supongamos que tiene una clase de 25 alumnos. Cada alumno tiene 20 horas de clase programadas.  Se asignan otras 10 horas de cuota para deberes o asignaciones fuera de las horas de clase programadas para cada alumno.  El tamaño de la máquina virtual que elegimos es **Mediana**, que tiene 20 unidades de laboratorio.

- 25 alumnos &times; (20 horas programadas + 10 horas de cuota) &times; 20 unidades de laboratorio &times; 0,01 USD por hora = 150,00 USD

> [!IMPORTANT]
> La estimación de costos solo se utiliza con fines de ejemplo.  Para obtener información sobre los precios actuales, consulte [Precios de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Pasos siguientes

Ahora se puede publicar la imagen de plantilla en el laboratorio. Consulte [Publicación de la plantilla de máquina virtual](how-to-create-manage-template.md#publish-the-template-vm) para obtener más instrucciones.

Cuando configure el laboratorio, consulte los siguientes artículos:

- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de cuotas](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users)
