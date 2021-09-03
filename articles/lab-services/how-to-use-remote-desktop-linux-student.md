---
title: Conexión a una máquina virtual Linux en Azure Lab Services | Microsoft Docs
description: Obtenga información sobre cómo usar Escritorio remoto para máquinas virtuales Linux en un laboratorio en Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 04a86ba98df3e1600ac95d19e690815515eb4c6d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729814"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Conexión a máquinas virtuales Linux en un laboratorio de clase de Azure Lab Services
En este artículo se muestra cómo los alumnos pueden conectarse a una máquina virtual (VM) Linux en un laboratorio mediante lo siguiente:
- Terminal de SSH (protocolo de Shell seguro)
- Escritorio remoto de la GUI (interfaz gráfica de usuario)

> [!IMPORTANT] 
> SSH se configura automáticamente para que los alumnos y el formador se puedan conectar mediante SSH a máquinas virtuales Linux sin necesidad de realizar ninguna configuración adicional. Pero, si los alumnos necesitan conectarse mediante un escritorio remoto de la GUI, puede que el formador necesite realizar una configuración adicional.  Para obtener más información, consulte [Habilitación del Escritorio remoto para máquinas virtuales Linux en un laboratorio de Azure Lab Services](how-to-enable-remote-desktop-linux.md).

## <a name="connect-to-the-student-vm-using-ssh"></a>Conexión a la máquina virtual del alumno mediante SSH

1. Cuando un alumno inicia sesión en el portal de laboratorios directamente (`https://labs.azure.com`) o mediante un vínculo de registro (`https://labs.azure.com/register/<registrationCode>`), se muestra un icono para cada laboratorio al que el alumno tiene acceso. 
   
1. En el icono, alterne el botón para iniciar la máquina virtual si está en estado detenido. 

2. Seleccione **Conectar**. Verá dos opciones para conectarse a la VM: **SSH** y **RDP**.

    ![VM de alumnos: opciones de conexión](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. Seleccione la opción **SSH** y verá el cuadro de diálogo **Conectarse a su máquina virtual**:  

    ![Cadena de conexión de SSH](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Haga clic en el botón **Copiar** situado junto al cuadro de texto para copiar la información de conexión de SSH en el portapapeles. 

5. Guarde la información de conexión de SSH, como en el panel Texto, para que se pueda usar esta información de conexión en el paso siguiente.

6. Desde un terminal de SSH (como [Putty](https://www.putty.org/)), conéctese a la máquina virtual.

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>Conexión a la máquina virtual del alumno mediante el escritorio remoto de la GUI
El formador puede optar por configurar máquinas virtuales para que los alumnos también puedan conectarse mediante un escritorio remoto de la GUI.  En este caso, los alumnos necesitan que el formador les indique si deben conectarse a sus máquinas virtuales mediante el **Escritorio remoto de Microsoft (RDP)** o la aplicación cliente de **X2Go**.  Ambas aplicaciones permiten a un alumno conectarse de forma remota a su máquina virtual y mostrar el escritorio gráfico de Linux en su equipo local.

> [!WARNING]
>  Se recomienda usar un entorno de escritorio gráfico diferente al de [GNOME](https://www.gnome.org/).  Debe evitar la instalación de GNOME en VM de laboratorio, porque GNOME tiene un conflicto con el Agente Linux de Azure, que es necesario para que las VM funcionen correctamente en Azure Lab Services.  Por ejemplo, se recomienda usar un entorno de escritorio gráfico, como XFCE.

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>Conexión a la máquina virtual del alumno mediante el Escritorio remoto de Microsoft (RDP)
Los alumnos pueden usar el Escritorio remoto (RDP) de Microsoft a fin de conectarse a sus VM Linux después de que el instructor configure su laboratorio con los paquetes de GUI y RDP para un entorno de escritorio gráfico de Linux (por ejemplo, XFCE, MATE, etc.). Estos son los pasos para conectarse: 

1. En el icono de la máquina virtual, asegúrese de que esta se está ejecutando y haga clic en **Conectar**. Verá dos opciones para conectarse a la VM: **SSH** y **RDP**.

    ![VM de alumnos: opciones de conexión](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. Seleccione la opción **RDP**.  Cuando el archivo RDP se descargue en la máquina, guárdelo en la máquina virtual.

3. Si se conecta desde un equipo Windows, normalmente el cliente de Escritorio remoto de Microsoft (RDP) ya está instalado y configurado.  Como resultado, lo único que debe hacer es hacer clic en el archivo RDP para abrirlo e iniciar la sesión remota.

    En cambio, si se va a conectar desde un equipo Mac o Chromebook, consulte los pasos siguientes:
   - [Conexión a una máquina virtual mediante RDP en un equipo Mac](connect-virtual-machine-mac-remote-desktop.md).
   - [Conexión a una máquina virtual mediante RDP en un equipo Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).

### <a name="connect-to-the-student-vm-using-x2go"></a>Conexión a la máquina virtual del alumno mediante X2Go
Los alumnos pueden usar X2Go a fin de conectarse a sus VM Linux después de que el instructor configure su laboratorio con los paquetes de X2Go y GUI para un entorno de escritorio gráfico de Linux (por ejemplo, XFCE, MATE, etc.).

Los alumnos necesitan que el formador les diga qué entorno de escritorio gráfico de Linux ha instalado.  Esta información es necesaria en los pasos siguientes para conectarse mediante el cliente de X2Go.

1. Instale el [cliente de X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) en el equipo local.

1. Siga las instrucciones de la [primera sección](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) para copiar la información de conexión de SSH de la máquina virtual.  Necesita esta información para conectarse mediante el cliente de X2Go.

1. Una vez que tenga la información de conexión de SSH, abra el cliente de X2Go y seleccione **Sesión** > **Nueva sesión**.
   ![Creación de nueva sesión de X2Go](./media/how-to-use-classroom-lab/x2go-new-session.png)

1. Escriba los valores en el panel **Preferencias de la sesión** en función de la información de conexión de SSH.  Por ejemplo, la información de conexión tendrá un aspecto similar al siguiente:

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    Mediante este ejemplo, se escriben los valores siguientes:

   - **Nombre de sesión**: especifique un nombre, como el nombre de la máquina virtual.
   - **Host**: id. de la máquina virtual; por ejemplo, **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** .
   - **Inicio de sesión**: nombre de usuario de la máquina virtual; por ejemplo, **alumno**.
   - **Puerto SSH**: el puerto único asignado a la máquina virtual; por ejemplo, **12345**.
   - **Tipo de sesión**: seleccione el entorno de escritorio gráfico de Linux en el que el formador ha configurado la máquina virtual.  Debe obtener esta información del formador.

    Por último, haga clic en **Aceptar** para crear la sesión.

    ![Preferencias de sesión de X2Go](./media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  Haga clic en la sesión situada en el panel derecho.

    ![Inicio de nueva sesión de X2Go](./media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > Si aparece un mensaje similar al siguiente, seleccione **sí** para continuar escribiendo la contraseña: **No se puede establecer la autenticidad del host "[`00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com`]:12345".  La huella digital de la clave ECDSA es SHA256:00000000000000000000000000000000000000000000. ¿Está seguro de que quiere continuar con la conexión (sí/no)?**

2. Cuando se le solicite, escriba la contraseña y haga clic en **Aceptar**.  Ahora estará conectado de forma remota al entorno de escritorio de la GUI de la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre cómo habilitar la característica de conexión a Escritorio remoto para las máquinas virtuales Linux en un laboratorio de clase, consulte [Habilitación de Escritorio remoto para máquinas virtuales Linux](how-to-enable-remote-desktop-linux.md). 

