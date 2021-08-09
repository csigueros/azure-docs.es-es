---
title: Configuración de un laboratorio para enseñar desarrollo front-end con React en Windows mediante Azure Lab Services
description: Aprenda a configurar laboratorios para dar clases de ingeniería con React.
author: emaher
ms.topic: article
ms.date: 05/16/2021
ms.author: enewman
ms.openlocfilehash: 0fe26d50a7afcb08325396d7c9a22279b01bfd9b
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483500"
---
# <a name="set-up-lab-for-react-on-windows"></a>Configuración del laboratorio para React en Windows

[React](https://reactjs.org/) es una popular biblioteca de JavaScript para crear interfaces de usuario (UI). React es una manera declarativa de crear componentes reutilizables para el sitio web.  Hay muchas otras bibliotecas populares para el desarrollo front-end basado en JavaScript.  Usaremos algunas de estas bibliotecas durante la creación de nuestro laboratorio.  [Redux](https://redux.js.org/) es una biblioteca que proporciona un contenedor de estado predecible para las aplicaciones JavaScript y que a menudo se usa en conjunto con React. [JSX](https://reactjs.org/docs/introducing-jsx.html) es una extensión de sintaxis de biblioteca para JavaScript que a menudo se usa con React para describir el aspecto de la interfaz de usuario.  [NodeJS](https://nodejs.org/) es una manera cómoda de ejecutar un servidor web para la aplicación React.

En este artículo se muestra cómo instalar [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) para el entorno de desarrollo, así como las herramientas y bibliotecas necesarias para una clase de desarrollo web con React.

## <a name="lab-configuration"></a>Configuración del laboratorio

Para configurar este laboratorio, para empezar, necesita una suscripción a Azure y una cuenta de laboratorio. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. Una vez que obtenga una suscripción a Azure, puede crear una nueva cuenta de laboratorio en Azure Lab Services. Para más información sobre la creación de una nueva cuenta de laboratorio, consulte el tutorial sobre la [configuración de una cuenta de laboratorio](./tutorial-setup-lab-account.md). También puede usar una cuenta de laboratorio existente.

### <a name="lab-account-settings"></a>Configuración de la cuenta de laboratorio

Habilite la configuración de la cuenta de laboratorio tal y como se describe en la tabla siguiente. Para obtener más información sobre cómo habilitar imágenes de Azure Marketplace, consulte [Especificación de las imágenes de Azure Marketplace disponibles para los creadores de laboratorios](./specify-marketplace-images.md).

| Configuración de la cuenta de laboratorio | Instructions |
| -------------------- | ----- |
| Imagen de Marketplace | Habilite la imagen "Visual Studio 2019 Community (versión más reciente) en Windows Server 2019 (x64)" para usarla en su cuenta de laboratorio. |

### <a name="lab-settings"></a>Configuración del laboratorio

El tamaño de la máquina virtual (VM) que se recomienda depende de los tipos de cargas de trabajo de las que los alumnos tienen que encargarse.  

| Configuración del laboratorio | Valor y descripción |
| ------------ | ------------------ |
| Tamaño de la máquina virtual | **Media** |

Se recomienda probar las cargas de trabajo para ver si se necesita un mayor tamaño.  Para obtener más información sobre cada tamaño, consulte [Tamaño de máquina virtual](administrator-guide.md#vm-sizing).

## <a name="template-machine-configuration"></a>Configuración de la máquina de plantilla

Los pasos de esta sección muestran cómo rellenar los siguientes datos para configurar la plantilla de máquina virtual:

1. Instalación de herramientas de desarrollo.
1. Instalación de extensiones del depurador para el explorador web.
1. Actualización de la configuración del firewall.

### <a name="install-development-tools"></a>Instalación de herramientas de desarrollo

La imagen "Visual Studio 2019 Community (versión más reciente) en Windows Server 2019 (x64)" ya tiene instalada la [carga de trabajo de **desarrollo de Node.js**](/visualstudio/javascript/tutorial-nodejs-with-react-and-jsx?view=vs-2019&preserve-view=true#prerequisites) necesaria para [Visual Studio 2019](https://visualstudio.microsoft.com/vs/).

1. Instale el explorador web que prefiera.  La imagen ya tiene instalado Internet Explorer de manera predeterminada.
1. Vaya al sitio web de [Node.js](https://nodejs.org) y seleccione el botón **Descargar**.  Puede usar la versión más reciente del servicio a largo plazo (LTS), la versión actual con las características más recientes o una versión anterior.  La instalación de NodeJS también instalará el [Administrador de paquetes de Node](https://www.npmjs.com/), que se usará para instalar React, Redux y JSX.
1. [Actualice Visual Studio 2019](/visualstudio/install/update-visual-studio?view=vs-2019&preserve-view=true) a la versión más reciente, si es necesario.

Otros componentes necesarios para un sitio web basado en React se instalan mediante NPM en una aplicación específica.  Para agregar paquetes NPM, consulte cómo [administrar los paquetes NPM en Visual Studio](/visualstudio/javascript/npm-package-management?view=vs-2019&preserve-view=true#add-npm-packages).  

Por ejemplo, si usa la [ventana interactiva de Node.js](/visualstudio/javascript/nodejs-interactive-repl?view=vs-2019&preserve-view=true) en un proyecto, escriba los siguientes comandos para instalar las bibliotecas React, Redux y JSX:

```bash
.npm install react
.npm install react-dom
.npm install react-redux
.npm install react-jsx
```

Para crear la primera aplicación de Node.js con React en Visual Studio, consulte [Tutorial: Crear una aplicación Node.js y React en Visual Studio](/visualstudio/javascript/tutorial-nodejs-with-react-and-jsx.md?view=vs-2019&preserve-view=true).

### <a name="install-debugger-extensions"></a>Instalación de extensiones del depurador

Instale las extensiones React Developer Tools del explorador para que pueda inspeccionar los componentes de React y registrar la información de rendimiento.  

- [Complemento React Developer Tools de Edge](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)
- [Extensión React Developer Tools de Chrome](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- [Complemento React Developer Tools de FireFox](https://addons.mozilla.org/firefox/addon/react-devtools/)

### <a name="update-firewall-settings"></a>Actualización de la configuración del firewall

De manera predeterminada, se bloqueará el tráfico entrante del servidor de Node.js.  Si quiere acceder al sitio web de un alumno mientras está en ejecución, agregue una regla de firewall enlazada para permitir el tráfico.  Consulte la propiedad **Puerto de la aplicación** del proyecto para ver qué puerto se usará durante la depuración.  En el ejemplo siguiente se usa el cmdlet de PowerShell [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule?view=windowsserver2019-ps&preserve-view=true) para permitir el acceso al puerto 1337.  

```powershell
New-NetFirewallRule -DisplayName "Allow access to Port 1337" -Direction Inbound -LocalPort 1337 -Protocol TCP -Action Allow
```

>[!IMPORTANT]
>Los instructores deben usar la plantilla de máquina virtual u otra máquina virtual de laboratorio para acceder al sitio web de un alumno.

## <a name="cost"></a>Coste

Veamos un ejemplo de una posible estimación de costos para esta clase.  Supongamos que tiene una clase de 25 alumnos. Cada alumno tiene 20 horas de clase programadas.  Se asignan otras 10 horas de cuota para deberes o asignaciones fuera de las horas de clase programadas para cada alumno.  El tamaño de la máquina virtual que elegimos es **Mediana**, que tiene 55 unidades de laboratorio.

- 25 alumnos &times; (20 horas programadas + 10 horas de cuota) &times; 55 unidades de laboratorio &times; 0,01 USD por hora = 412,50 USD

> [!IMPORTANT]
> La estimación de costos solo se utiliza con fines de ejemplo.  Para obtener información sobre los precios actuales, consulte [Precios de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Pasos siguientes

Ahora se puede publicar la imagen de plantilla en el laboratorio. Consulte [Publicación de la plantilla de máquina virtual](how-to-create-manage-template.md#publish-the-template-vm) para obtener más instrucciones.

Cuando configure el laboratorio, consulte los siguientes artículos:

- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de cuotas](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users)
