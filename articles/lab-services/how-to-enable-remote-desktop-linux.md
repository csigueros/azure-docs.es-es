---
title: Habilitación del escritorio remoto gráfico para Linux en Azure Lab Services | Microsoft Docs
description: Obtenga información sobre cómo habilitar Escritorio remoto para máquinas virtuales Linux en un laboratorio en Azure Lab Services.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c870faf1f85c192f12739c17809cc9c9088ac1fd
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180331"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Habilitación del escritorio remoto gráfico para máquinas virtuales Linux en Azure Lab Services
En este artículo se muestra cómo realizar las tareas siguientes:

- Habilitación de sesiones de escritorio remoto gráfico para una máquina virtual Linux
- Procedimientos para conectar a una máquina virtual Linux mediante el Protocolo de escritorio remoto (RDP) o los clientes de escritorio remoto de X2Go

## <a name="set-up-graphical-remote-desktop-solution"></a>Configuración de la solución de escritorio remoto gráfico
Cuando se crea un laboratorio a partir de una imagen de **Linux**, el acceso **SSH** (Secure Shell) se configura automáticamente para que el instructor pueda conectarse a la máquina virtual de plantilla desde la línea de comandos mediante SSH.  Del mismo modo, cuando se publica la máquina virtual de plantilla, los alumnos también pueden conectarse a las máquinas virtuales mediante SSH.

Para conectarse a una máquina virtual Linux mediante una **GUI** (interfaz gráfica de usuario), se recomienda usar **RDP** o **X2Go**.  En el resto de este artículo se muestran los pasos para configurar RDP o X2Go en una máquina virtual de plantilla de laboratorio.

> [!NOTE]
> Linux usa una versión de código abierto de RDP denominada [Xrdp](https://en.wikipedia.org/wiki/Xrdp).  Para simplificar, usamos el término RDP en este artículo.

### <a name="rdp-setup"></a>Configuración de RDP
Para usar RDP, el instructor debe:
  - Habilitar la conexión a escritorio remoto. Esto es específicamente necesario para abrir el puerto de la máquina virtual para RDP.
  - Instalar el servidor de escritorio remoto con RDP.
  - Instalar un entorno de escritorio gráfico de Linux.

### <a name="x2go-setup"></a>Configuración de X2Go
Para usar X2Go, el instructor debe:
- Instalar el servidor de escritorio remoto X2Go.
- Instalar un entorno de escritorio gráfico de Linux.

X2Go usa el mismo puerto que ya está habilitado para SSH.  Como consecuencia, no se requiere ninguna configuración adicional para abrir un puerto en la máquina virtual para X2Go.

> [!NOTE]
> En algunos casos, como con Ubuntu LTS 18,04, el rendimiento de X2Go es mejor.  Si usa RDP y observa latencia al utilizar el entorno de escritorio gráfico, considere la posibilidad de probar X2Go, ya que puede mejorar el rendimiento.

> [!IMPORTANT]
>  Algunas imágenes de Marketplace ya tienen instalados un entorno de escritorio gráfico y un servidor de escritorio remoto.  Por ejemplo, [Data Science Virtual Machine- Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) ya tiene [XFCE y X2Go Server instalados y configurados para aceptar conexiones de cliente](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#x2go).

## <a name="enable-remote-desktop-connection-for-rdp"></a>Habilitación de la conexión de escritorio remoto para RDP

Este paso solo es necesario para conectarse mediante RDP.  Si va a usar X2Go, puede ir directamente a la sección siguiente, ya que X2Go usa el puerto SSH.

1.  Durante la creación del laboratorio, el instructor tiene la opción de **habilitar la conexión a Escritorio remoto**.  El instructor debe **habilitar** esta opción para abrir el puerto en la máquina virtual Linux que se necesita para una sesión de escritorio remoto con RDP.  Si esta opción se mantiene **deshabilitada**, solo se abre el puerto para SSH.
  
    ![Captura de pantalla que muestra la ventana "Nuevo laboratorio" con la opción "Habilitar Conexión a Escritorio remoto".](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. En el cuadro de mensaje **Enabling Remote Desktop Connection** (Habilitar conexión a Escritorio remoto), seleccione **Continue with Remote Desktop** (Continuar con Escritorio remoto). 

    ![Habilitación de la conexión a Escritorio remoto para una imagen de Linux](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>Instalación de RDP o X2Go

Una vez creado el laboratorio, el instructor debe asegurarse de que la máquina virtual de plantilla tenga instalados un entorno de escritorio remoto y un servidor de escritorio remoto.  En primer lugar, el instructor debe conectarse a la máquina virtual de plantilla mediante SSH para instalar los paquetes de:
- El servidor de escritorio remoto RDP o X2Go.
- Un entorno de escritorio gráfico, como [GNOME](https://www.gnome.org/), [MATE](https://mate-desktop.org/), [XFCE](https://www.xfce.org/), [Xubuntu](https://xubuntu.org/), etc.

Una vez que se ha configurado esto, el instructor puede conectarse a la máquina virtual de plantilla mediante el cliente de **Escritorio remoto de Microsoft (RDP)** o el cliente de **X2Go**.

Siga los pasos que se indican a continuación para configurar la máquina virtual de plantilla:

1. Si ve **Customize template** (Personalizar plantilla) en la barra de herramientas, selecciónelo. A continuación, seleccione **Continue** (Continuar) en el cuadro de diálogo **Customize template** (Personalizar plantilla). Esta acción inicia la máquina virtual de la plantilla.  

    ![Customize template (Personalizar plantilla)](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. Una vez iniciada la máquina virtual de plantilla, puede seleccionar **Connect template** (Conectar plantilla) y **Connect via SSH** (Conectar mediante SSH) en la barra de herramientas. 

    ![Conexión a una plantilla a través de RDP una vez creado el laboratorio](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. Verá el cuadro de diálogo **Conectarse a su máquina virtual** siguiente. Seleccione el botón **Copiar** situado junto al cuadro de texto para copiarlo en el Portapapeles. Guarde la información de conexión de SSH. Utilice esta cadena de conexión desde un terminal de SSH (como [Putty](https://www.putty.org/)) para conectarse a la máquina virtual.
 
    ![Cadena de conexión de SSH](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Finalmente, instale RDP o X2Go junto con el entorno de escritorio gráfico de su elección.

Para obtener un rendimiento óptimo, normalmente se recomienda usar el escritorio gráfico XFCE para que los usuarios se conecten al escritorio mediante X2Go.  Para configurar XFCE con X2Go en Ubuntu, siga estas instrucciones:
  - [Instalación y configuración de X2Go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/LinuxGraphicalDesktopSetup/XFCE_Xubuntu/ReadMe.md)

En los casos en los que, por el contrario, necesite que los usuarios se conecten al escritorio gráfico con RDP, utilice las siguientes instrucciones para Ubuntu:
  - [Instalación y configuración de RDP](../virtual-machines/linux/use-remote-desktop.md)

En el caso de los entornos de escritorio gráfico GNOME o MATE, es posible que se produzca un conflicto de red con el agente de Linux de Azure, necesario para que las máquinas virtuales funcionen correctamente en Azure Lab Services.  Por ejemplo, de forma predeterminada, si crea un laboratorio a partir de una imagen de Ubuntu 18.04 LTS que tiene instalado GNOME o MATE, se producirá un error en la creación del laboratorio con el siguiente mensaje de error: **No se pudo establecer la comunicación con el agente de máquina virtual. Compruebe que el agente de máquina virtual está habilitado y funcionando.**  Del mismo modo, este conflicto de red hará que la publicación se bloquee al intentar aprovisionar las máquinas virtuales de los alumnos.

Se recomienda usar las instrucciones siguientes para configurar los escritorios gráficos GNOME o MATE en Ubuntu.  Estas instrucciones incluyen una corrección para el conflicto de red que existe con Ubuntu 18.04 LTS.  También admiten Ubuntu 20.04 LTS y 21.04 LTS:
 - [Instalación y configuración de GNOME/RDP y MATE/X2go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/LinuxGraphicalDesktopSetup/GNOME_MATE/ReadMe.md)

## <a name="connect-to-the-template-vm-via-the-gui"></a>Conexión a máquina virtual de plantilla mediante la GUI

Una vez configurada la máquina virtual de plantilla, el instructor puede conectarse a través de la GUI mediante el cliente de **Escritorio remoto de Microsoft (RDP)** o el cliente de **X2Go**.  El cliente que se utilice dependerá de si se ha configurado RDP o X2Go como servidor de escritorio remoto en la máquina virtual de plantilla.  

### <a name="microsoft-remote-desktop-rdp-client"></a>Cliente de Escritorio remoto de Microsoft (RDP)

El cliente de Escritorio remoto de Microsoft (RDP) se usa para la conexión a una máquina virtual de plantilla que tiene configurado RDP.  El cliente de Escritorio remoto se puede usar en equipos Windows, Chromebook, Mac, etc.  Consulte el artículo [Clientes de Escritorio remoto](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) para obtener más detalles.

Realice los pasos siguientes en función del tipo de equipo usado para conectarse a la máquina virtual de plantilla:

- Windows
  1. Haga clic en **Conectar con la plantilla** en la barra de herramientas del laboratorio y seleccione **Conectar mediante RDP** para conectarse a la máquina virtual de plantilla. 
  1. Guarde el archivo RDP y úselo para conectarse a la máquina virtual de plantilla mediante el cliente de Escritorio remoto. 
  1. Normalmente, el cliente de Escritorio remoto está instalado y configurado en Windows.  Como consecuencia, solo tiene que hacer clic en el archivo RDP para abrirlo e iniciar la sesión remota.

- Mac
  1. Haga clic en **Conectar con la plantilla** en la barra de herramientas del laboratorio y, a continuación, seleccione **Conectar mediante RDP** para guardar el archivo RDP.  
  1. Después, consulte el artículo [Conexión a una máquina virtual mediante RDP en un equipo Mac](connect-virtual-machine-mac-remote-desktop.md).

- Chromebook
  1. Haga clic en **Conectar con la plantilla** en la barra de herramientas del laboratorio y, a continuación, seleccione **Conectar mediante RDP** para guardar el archivo RDP.  
  1. Después, consulte el artículo [Conexión a una máquina virtual mediante RDP en un equipo Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).

### <a name="x2go-client"></a>Cliente de X2Go

El cliente de X2Go se usa para la conexión a una máquina virtual de plantilla que tiene configurado X2Go.  Utilice la información de conexión de SSH de la máquina virtual de plantilla para seguir los pasos del artículo de procedimientos sobre la [conexión a una máquina virtual mediante X2Go](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go).

## <a name="next-steps"></a>Pasos siguientes
Después de que un instructor configure RDP o X2Go en la máquina virtual de plantilla y la publique, los alumnos podrán conectarse a las máquinas virtuales mediante el escritorio remoto de la GUI o a través de SSH.

Para más información, consulte:
 - [Conexión a una máquina virtual Linux](how-to-use-remote-desktop-linux-student.md)