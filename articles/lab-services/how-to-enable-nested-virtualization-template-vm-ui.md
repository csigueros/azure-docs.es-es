---
title: Habilitación de la virtualización anidada en una plantilla de máquina virtual en Azure Lab Services (UI) | Microsoft Docs
description: Obtenga información sobre cómo crear una plantilla de máquina virtual con varias máquinas virtuales dentro.  En otras palabras, habilite la virtualización anidada en una plantilla de máquina virtual en Azure Lab Services.
author: emaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: f2f4fa1dc4089b55afaffdb4ef5a5a3c68beedd1
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180483"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Habilitación de la virtualización anidada en una plantilla de máquina virtual en Azure Lab Services manualmente

La virtualización anidada le permite crear un entorno de varias máquinas virtuales dentro de una máquina virtual de plantilla de un laboratorio. La publicación de la plantilla proporcionará a cada usuario del laboratorio una máquina virtual configurada con varias máquinas virtuales dentro.  Para más información sobre la virtualización anidada y Azure Lab Services, consulte [Habilitación de la virtualización anidada en una plantilla de máquina virtual en Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

En este artículo se explica cómo configurar la virtualización anidada en una plantilla de máquina virtual en Azure Lab Services directamente con roles y herramientas de Windows.  Hay algunos aspectos a tener en cuenta para permitir que una clase use la virtualización anidada.  En los pasos siguientes se describe cómo configurar manualmente una plantilla de máquina virtual de Lab Services con Hyper-V.  Los pasos están pensados para Windows Server 2016 o Windows Server 2019.  

>[!IMPORTANT]
>Seleccione **Grande (virtualización anidada)** o **Medio (virtualización anidada)** como tamaño de la máquina virtual al crear el laboratorio.  De lo contrario, la virtualización anidada no funcionará.  

## <a name="enable-hyper-v-role"></a>Habilitación del rol de Hyper-V

En los pasos siguientes se describen las acciones necesarias para habilitar Hyper-V en Windows Server mediante el Administrador del servidor.  Una vez finalizada la instalación correctamente, el administrador de Hyper-V estará disponible para agregar, modificar y eliminar máquinas virtuales cliente.

1. En el **Administrador del servidor**, en la página del panel, haga clic en **Agregar roles y características**.
2. En la página **Antes de comenzar** , haga clic en **Siguiente**.
3. En la página **Seleccionar tipo de instalación**, conserve la selección predeterminada de Instalación basada en características o en roles y, a continuación, haga clic en **Siguiente**.
4. En la página **Seleccionar el servidor de destino**, elija Seleccionar un servidor del grupo de servidores.   El servidor actual ya estará seleccionado.  Haga clic en Siguiente.
5. En la pantalla **Seleccionar roles de servidor**, seleccione **Hyper-V**.  
6. Aparecerá el elemento emergente **Asistente para agregar roles y características**.  Seleccione **Incluir herramientas de administración (si procede)** .  Haga clic en el botón **Agregar características**.
7. En la página **Seleccionar roles de servidor**, haz clic en **Siguiente**.
8. En la página **Selección de características**, haga clic en **Siguiente**.
9. En la página **Hyper-V**, haga clic en **Siguiente**.
10. En la página **Crear conmutadores virtuales**, acepte los valores predeterminados y haga clic en **Siguiente**.
11. En la página **Migración de máquinas virtuales**, acepte los valores predeterminados y haga clic en **Siguiente**.
12. En la página **Almacenes predeterminados**, acepte los valores predeterminados y haga clic en **Siguiente**.
13. En la página **Confirmar selecciones de instalación**, seleccione **Reiniciar automáticamente el servidor de destino en caso necesario**.
14. Cuando aparezca el elemento emergente **Asistente para agregar roles y características**, haga clic en **Sí**.
15. Haga clic en **Instalar**.
16. Espere a que la página **Progreso de la instalación** le indique que el rol de Hyper-V está completo.  La máquina puede reiniciarse en medio de la instalación.
17. Haga clic en **Cerrar**.

## <a name="enable-dhcp-role"></a>Habilitar rol de DHCP

Todas las máquinas virtuales cliente de Hyper-V creadas necesitan una dirección IP en la red NAT.  Vamos a crear la red NAT más adelante.  Una manera de asignar direcciones IP es configurar el host, en este caso la plantilla de máquina virtual del laboratorio, como un servidor DHCP.  A continuación se indican los pasos necesarios para habilitar el rol de DHCP.

1. En el **Administrador del servidor**, en la página del **panel**, haga clic en **Agregar roles y características**.
2. En la página **Antes de comenzar** , haga clic en **Siguiente**.
3. En la página **Seleccionar tipo de instalación**, seleccione **Instalación basada en características o en roles** y, a continuación, en **Siguiente**.
4. En la página **Seleccionar el servidor de destino**, seleccione el servidor actual del grupo de servidores y haga clic en **Siguiente**.
5. En la página **Seleccionar roles de servidor**, seleccione **Servidor DHCP**.  
6. Aparecerá el elemento emergente **Asistente para agregar roles y características**.  Seleccione **Incluir herramientas de administración (si procede)** .  Haga clic en **Agregar características**.

    >[!NOTE]
    >Puede que aparezca un error de validación que indica que no se encontraron direcciones IP estáticas.  Esta advertencia se puede pasar por alto en nuestro escenario.

7. En la página **Seleccionar roles de servidor**, haz clic en **Siguiente**.
8. En la página **Seleccionar características**, haz clic en **Siguiente**.
9. En la página **Servidor DHCP**, haga clic en **Siguiente**.
10. En la página **Confirmar selecciones de instalación**, haga clic en **Instalar**.
11. Espere a que la página **Progreso de la instalación** le indique que el rol de DHCP está completo.
12. Haga clic en Cerrar.

## <a name="enable-routing-and-remote-access-role"></a>Habilitar rol de Enrutamiento y acceso remoto

1. En el **Administrador del servidor**, en la página del **panel**, haga clic en **Agregar roles y características**.
2. En la página **Antes de comenzar** , haga clic en **Siguiente**.
3. En la página **Seleccionar tipo de instalación**, seleccione **Instalación basada en características o en roles** y, a continuación, en **Siguiente**.
4. En la página **Seleccionar el servidor de destino**, seleccione el servidor actual del grupo de servidores y haga clic en **Siguiente**.
5. En la página **Seleccionar roles de servidor**, seleccione **Acceso remoto**. Haga clic en **OK**.
6. En la página **Seleccionar características**, haz clic en **Siguiente**.
7. En la página **Acceso remoto**, haga clic en **Siguiente**.
8. En la página **Servicios de rol**, seleccione **Enrutamiento**.
9. Aparecerá el elemento emergente **Asistente para agregar roles y características**.  Seleccione **Incluir herramientas de administración (si procede)** .  Haga clic en **Agregar características**.
10. Haga clic en **Next**.
11. En la página **Rol Servidor web (IIS)**, haz clic en **Siguiente**.
12. En la página **Seleccionar servicios de rol**, haz clic en **Siguiente**.
13. En la página **Confirmar selecciones de instalación**, haga clic en **Instalar**.
14. Espere a que la página **Progreso de la instalación** le indique que el rol de acceso remoto está completo.  
15. Haga clic en **Cerrar**.

## <a name="create-virtual-nat-network"></a>Creación de una red NAT virtual

Ahora que se han instalado todos los roles necesarios, es hora de crear la red NAT.  El proceso de creación implicará la creación de un conmutador y de la propia red NAT.  Una red NAT (traducción de direcciones de red) asigna una dirección IP pública a un grupo de máquinas virtuales de una red privada para permitir la conectividad a Internet.  En nuestro caso, el grupo de máquinas virtuales privadas serán las máquinas virtuales anidadas.  La red NAT permitirá a las máquinas virtuales anidadas comunicarse unas con otras. Un conmutador es un dispositivo de red que controla la recepción y enrutamiento del tráfico de una red.

### <a name="create-a-new-virtual-switch"></a>Creación de un nuevo conmutador virtual

1. Abra el **administrador de Hyper-V** desde Herramientas administrativas de Windows.
2. Seleccione el servidor actual en el menú de navegación izquierdo.
3. Haga clic en **Administrador de conmutadores virtuales...** en el menú **Acciones** situado a la derecha en el **administrador de Hyper-V**.
4. En el elemento emergente **Administrador de conmutadores virtuales**, seleccione **Interno** como el tipo de conmutador que desea crear.  Haz clic en **Crear conmutador virtual**.
5. Especifique un nombre para el conmutador virtual recién creado que sea fácil de recordar.  En este ejemplo, usaremos "LabServicesSwitch".  Haga clic en **OK**.
6. Se creará un nuevo adaptador de red.  El nombre se parecerá a "vEthernet (LabServicesSwitch)".  Para comprobarlo, abra el **Panel de control**, haga clic en **Redes e Internet** y en **Ver el estado y las tareas de red**.  A la izquierda, haga clic en **Cambiar configuración del adaptador**.

### <a name="create-a-nat-network"></a>Creación de una red NAT

1. Abra la herramienta **Enrutamiento y acceso remoto** en las herramientas administrativas de Windows.
2. Seleccione el servidor local en la página de navegación izquierda.
3. Seleccione **Acción** -> **Configurar y habilitar Enrutamiento y acceso remoto**.
4. Cuando aparezca el **Asistente para la instalación del servidor de enrutamiento y acceso remoto**, haga clic en **Siguiente**.
5. En la página **Configuración**, seleccione la opción **Traducción de direcciones de red (NAT)** .  Haga clic en **Next**.

    >[!WARNING]
    >No elija la opción "Acceso a red privada virtual (VPN) y NAT".

6. En la página **Conexión a Internet NAT**, elija "Ethernet".  No seleccione la conexión "vEthernet (LabServicesSwitch)" que creamos en el administrador de Hyper-V. Haga clic en **Next**.
7. En la última página del asistente, haga clic en **Finalizar**.
8. Cuando aparezca el cuadro de diálogo **Iniciar el servicio**, haga clic en **Iniciar servicio**.
9. Espere hasta que se inicie el servicio.

## <a name="update-network-adapter-settings"></a>Actualización de la configuración del adaptador de red

El adaptador de red se asociará con la IP utilizada como dirección IP de la puerta de enlace predeterminada de la red NAT creada anteriormente.  En este ejemplo, vamos a crear la dirección IP 192.168.0.1 con la máscara de subred 255.255.255.0.  Vamos a usar el conmutador virtual que se creó anteriormente.

1. Abra el **Panel de control**, haga clic en **Redes e Internet** y en **Ver el estado y las tareas de red**.
2. A la izquierda, haga clic en **Cambiar configuración del adaptador**.  
3. En la ventana **Conexiones de red**, haga doble clic en "vEthernet (LabServicesSwitch)" para que aparezca el cuadro de diálogo de detalles **Estado de vEthernet (LabServicesSwitch)** .
4. Haga clic en el botón **Propiedades**.
5. Seleccione el elemento **Protocolo de Internet versión 4 (TCP/IPv4)** y haga clic en el botón **Propiedades**.
6. En el cuadro de diálogo **Propiedades: Protocolo de Internet versión 4 (TCP/IPv4)** , haga clic en **Usar la siguiente dirección IP**.  Como dirección IP, escriba 192.168.0.1. Para la máscara de subred, especifique 255.255.255.0.  Deje la puerta de enlace predeterminada en blanco.  Deje los servidores DNS en blanco también.

    >[!NOTE]
    > El rango de nuestra red NAT será, en notación CIDR, 192.168.0.0/24.  Esto permite crear un rango de direcciones IP utilizables que va desde 192.168.0.1 a 192.168.0.254.  Por convención, las puertas de enlace tienen la primera dirección IP de un rango de subred.

7. Haga clic en Aceptar.

## <a name="create-dhcp-scope"></a>Crear ámbito DHCP

Los pasos siguientes son instrucciones para agregar un ámbito de DHCP.  En este artículo, la red NAT es 192.168.0.0/24 en notación CIDR.  Esto permite crear un rango de direcciones IP utilizables que va desde 192.168.0.1 a 192.168.0.254.  El ámbito creado debe estar dentro del rango de direcciones utilizables sin incluir la dirección IP que se creó anteriormente.

1. Abra **Herramientas administrativas** y abra **DHCP**.
2. En la herramienta **DHCP**, expanda el nodo del servidor actual y seleccione **IPv4**.
3. En el menú Acción, seleccione **Nuevo ámbito…**
4. Cuando aparezca el **Asistente de nuevo ámbito**, haga clic en **Siguiente** en la página **principal**.
5. En la página **Nombre de ámbito**, escriba "LabServicesDhcpScope" o algo que sea fácil de recordar como nombre.  Haga clic en **Next**.
6. En la página **Intervalo de direcciones IP**, escriba los siguientes valores.

    - 192.168.0.100 como dirección IP inicial
    - 192.168.0.200 como dirección IP final
    - 24 como longitud
    - 255.255.255.0 como máscara de subred

7. Haga clic en **Next**.
8. En la página **Agregar exclusiones y retraso**, haga clic en **Siguiente**.
9. En la página **Duración de la concesión**, haga clic en **Siguiente**.
10. En la página **Configurar opciones DHCP**, seleccione **Configurar estas opciones ahora**. Haga clic en **Next**.
11. En **Enrutador (puerta de enlace predeterminada)**
12. agregue 192.168.0.1, si no lo ha hecho anteriormente. Haga clic en **Next**.
13. En la página **Nombre de dominio y servidores DNS**, agregue 168.63.129.16 como dirección IP del servidor DNS si no lo ha hecho anteriormente.  168.63.129.16 es la dirección IP de un servidor DNS estático de Azure. Haga clic en **Next**.
14. En la página **Servidores WINS**, haga clic en **Siguiente**.
15. En la página **Activar ámbito**, seleccione **Activar este ámbito ahora**.  Haga clic en **Next**.
16. En la página **Finalización del asistente de nuevo ámbito**, haga clic en **Finalizar**.

## <a name="conclusion"></a>Conclusión

Ahora la plantilla de máquina virtual está lista para crear máquinas virtuales de Hyper-V.   Consulte [Crear una máquina virtual en Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) para obtener instrucciones sobre cómo crear máquinas virtuales de Hyper-V.  Consulte también [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) para comprobar los sistemas operativos y el software disponibles.

## <a name="next-steps"></a>Pasos siguientes

Los pasos siguientes son comunes a la configuración de cualquier laboratorio.

- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de la cuota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users)