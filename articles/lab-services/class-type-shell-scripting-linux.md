---
title: Configuración de un laboratorio de scripting de shell en Linux con Azure Lab Services | Microsoft Docs
description: Aprenda a configurar un laboratorio para enseñar el scripting de shell en Linux.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 0faf5c9a11ad4aed3bf1157bc18dbeb84f60030b
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176555"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Configuración de un laboratorio para enseñar el scripting de shell en Linux
En este artículo se muestra cómo configurar un laboratorio para enseñar el scripting de shell en Linux. El scripting es una parte útil de la administración del sistema que permite a los administradores evitar tareas repetitivas. En este escenario de ejemplo, la clase cubre scripts bash tradicionales y scripts mejorados. Los scripts mejorados son scripts que combinan comandos bash y Ruby. Este enfoque permite a Ruby pasar datos y comandos bash para interactuar con el shell. 

Los alumnos que sigan estas clases de scripting obtienen una máquina virtual Linux para conocer los aspectos básicos de Linux y también familiarizarse con el scripting de shell de bash. La máquina virtual Linux incluye el acceso al escritorio remoto habilitado y con los editores de texto [gedit](https://help.gnome.org/users/gedit/stable/) y [Visual Studio Code](https://code.visualstudio.com/) instalados.

## <a name="lab-configuration"></a>Configuración del laboratorio
Para configurar este laboratorio, para empezar necesita una suscripción de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. Una vez que tenga una suscripción de Azure, puede crear una nueva cuenta de laboratorio en Azure Lab Services o usar una existente. Consulte el siguiente tutorial sobre la creación de una nueva cuenta de laboratorio: [Tutorial de configuración de una cuenta de laboratorio](tutorial-setup-lab-account.md).

Después de crear la cuenta de laboratorio, habilite la siguiente configuración en ella: 

| Configuración de la cuenta de laboratorio | Instructions |
| ----------- | ------------ |  
| Imágenes de Marketplace | Habilite la imagen de Ubuntu Server 18.04 LTS para su uso en la cuenta de laboratorio. Para más información, consulte [Especificación de las imágenes de Marketplace disponibles para los creadores de laboratorios](specify-marketplace-images.md). | 

Siga [este tutorial](tutorial-setup-classroom-lab.md) para crear un laboratorio y aplique la configuración siguiente:

| Configuración del laboratorio | Valor/instrucciones | 
| ------------ | ------------------ |
| Tamaño de la máquina virtual | Pequeña  |
| Imagen de la máquina virtual | Ubuntu Server 18.04 LTS|
| Habilitar la conexión a Escritorio remoto | Enable (Habilitar). <p>La habilitación de esta configuración permitirá a los profesores y alumnos conectarse a sus máquinas virtuales mediante el escritorio remoto (RDP). Para más información, consulte [Habilitación del Escritorio remoto para máquinas virtuales Linux en un laboratorio de Azure Lab Services](how-to-enable-remote-desktop-linux.md). </p>|

## <a name="install-desktop-and-rdp"></a>Instalación del escritorio y RDP
La imagen de Ubuntu Server 18.04 LTS no tiene instalado el servidor de escritorio remoto RDP de manera predeterminada. Siga las instrucciones del artículo [Instalación y configuración del escritorio remoto para conectarse a una máquina virtual Linux en Azure](../virtual-machines/linux/use-remote-desktop.md) para instalar los paquetes necesarios en la máquina de plantilla a fin de conectarse mediante el protocolo de escritorio remoto (RDP).

## <a name="install-ruby"></a>Instalación de Ruby
Ruby es un lenguaje dinámico de código abierto que se puede combinar con scripts de Bash. En esta sección se muestra cómo usar `apt-get` para instalar la versión más reciente de [Ruby](https://www.ruby-lang.org/).

1. Instale las actualizaciones mediante la ejecución de los comandos siguientes:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Instale [Ruby](https://www.ruby-lang.org/).  Ruby es un lenguaje dinámico de código abierto que se puede combinar con scripts de Bash. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Instalación de herramientas de desarrollo
En esta sección se muestra cómo instalar un par de editores de texto. Gedit es el editor de texto predeterminado para el entorno de escritorio Gnome. Está diseñado como editor de texto de uso general. Visual Studio Code es un editor de texto que incluye compatibilidad con la depuración y la integración de control del código fuente.

> [!NOTE]
> Hay varios editores de texto disponibles. Visual Studio Code y gedit son solo dos ejemplos.

1. Instale [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Instale [Visual Studio Code](https://code.visualstudio.com/).  Visual Studio Code se puede instalar desde Snap Store.  Para ver las opciones de instalación alternativas, consulte las [alternativas de descarga de Visual Studio Code](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    La plantilla se ha actualizado y tiene el lenguaje de programación y las herramientas de desarrollo necesarias para completar el laboratorio. Ahora se puede publicar la imagen de plantilla en el laboratorio. Seleccione el botón **Publicar** en la página de la plantilla para publicarla en el laboratorio.  

## <a name="cost"></a>Coste 
Si desea calcular el costo de este laboratorio, puede usar el ejemplo siguiente:
 
Para una clase de 25 alumnos con 20 horas de clase programadas y 10 horas de cuota para deberes o tareas, el precio del laboratorio sería: 

25 alumnos * (20 + 10) horas * 20 unidades de laboratorio * 0,01 USD por hora = 150 USD

En el siguiente documento encontrará más información sobre los precios: [Precios de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusión
En este artículo se explican los pasos necesarios para crear un laboratorio para clases de scripting. Aunque este artículo se centra en la configuración de las herramientas de scripting de Ruby en una máquina Linux, se puede usar la misma configuración para otras clases de scripting, como Python, en Linux.

## <a name="next-steps"></a>Pasos siguientes
Los pasos siguientes son comunes a la configuración de cualquier laboratorio:

- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de la cuota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users) 





