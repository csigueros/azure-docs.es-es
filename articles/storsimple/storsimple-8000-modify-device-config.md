---
title: Modificación de la configuración del dispositivo de la serie StorSimple 8000 | Microsoft Docs
description: Describe cómo usar el servicio StorSimple Device Manager para volver a configurar un dispositivo StorSimple que ya se ha implementado.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 8183d00f9846d3006ff0d246c8ea55407c45ac4b
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113430257"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Uso del servicio StorSimple Device Manager para modificar la configuración del dispositivo StorSimple

## <a name="overview"></a>Información general

La sección **Configuración del dispositivo** de la hoja **Configuración** de Azure Portal contiene todos los parámetros de dispositivo que puede volver a configurar en un dispositivo StorSimple administrado mediante un servicio StorSimple Device Manager. En este tutorial se explica cómo usar la hoja **Configurar** para realizar las siguientes tareas de nivel de dispositivo:

* Modificar el nombre descriptivo del dispositivo
* Modificar la configuración de hora del dispositivo
* Asignar un DNS secundario
* Modificar las interfaces de red
* Intercambiar o volver a asignar direcciones IP

## <a name="modify-device-friendly-name"></a>Modificar el nombre descriptivo del dispositivo

Puede usar Azure Portal para cambiar el nombre del dispositivo y asignarle un nombre descriptivo único de su elección. Use la hoja **Configuración general** en el dispositivo para modificar el nombre descriptivo del dispositivo. El nombre descriptivo puede contener cualquier carácter y tener un máximo de 64 caracteres.

> [!NOTE] 
> Solo se puede modificar el nombre del dispositivo en Azure Portal antes de completar la configuración del dispositivo. No se puede cambiar el nombre del dispositivo una vez que se haya completado su configuración mínima.

![Nombre del dispositivo en Configuración general](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

A un dispositivo StorSimple que está conectado al servicio StorSimple Device Manager se le asigna un nombre predeterminado. El nombre predeterminado normalmente refleja el número de serie del dispositivo. Por ejemplo, un nombre de dispositivo predeterminado que tiene 15 caracteres, como 8600-SHX0991003G44HT indica lo siguiente:

* **8600**: indica el modelo del dispositivo.
* **SHX** : indica el centro de producción.
* **0991003** : indica un producto específico.
* **G44HT**: los últimos 5 dígitos se incrementan para crear números de serie únicos. Es posible que esto no sea un conjunto secuencial.

## <a name="modify-device-description"></a>Modificar la descripción del dispositivo

Use la hoja **Configuración general** del dispositivo para modificar la descripción de dicho dispositivo.

![Descripción del dispositivo en Configuración general](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

La descripción del dispositivo suele ayudar a identificar el propietario y la ubicación física del dispositivo. El campo de descripción debe contener menos de 256 caracteres.

## <a name="modify-time-settings"></a>Modificar la configuración del tiempo

El dispositivo debe sincronizar la hora para autenticarse con su proveedor de servicios de almacenamiento en la nube. Use la hoja **Configuración general** del dispositivo para modificar la descripción de hora de dicho dispositivo.

![Descripción del dispositivo en Configuración general 2](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Seleccione la zona horaria en la lista desplegable. Puede especificar hasta dos servidores de protocolo de tiempo de red (NTP):

 - **Servidor NTP principal**: es necesario y se especifica cuando se usa Windows PowerShell para StorSimple para configurar el dispositivo. Puede especificar el Windows Server predeterminado **time.windows.com** como el servidor NTP. Puede ver la configuración del servidor NTP principal a través de Azure Portal, pero debe usar la interfaz de Windows PowerShell para cambiarla. Use el cmdlet `Set-HcsNTPClientServerAddress` para modificar el servidor NTP principal del dispositivo. Para más información, consulte la sintaxis del cmdlet [Set-HcsNTPClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)).

- **Servidor NTP secundario**: la configuración es opcional. Puede usar el portal para configurar un servidor NTP secundario.

Al configurar el servidor NTP, asegúrese de que su red permite el paso del tráfico NTP del centro de datos a Internet. Al especificar un servidor NTP público, debe asegurarse de que los firewalls de red y otros dispositivos de seguridad estén configurados para permitir que el tráfico NTP viaje hacia y desde la red externa. Si no se permite el tráfico NTP bidireccional, debe usar un servidor NTP interno (esta función la proporciona un controlador de dominio de Windows). Si el dispositivo no puede sincronizar la hora, es posible que no sea capaz de comunicarse con el proveedor de almacenamiento en la nube.

Para ver una lista de servidores NTP públicos, vaya a la [Web de servidores NTP](https://www.pool.ntp.org/zone/us).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>¿Qué sucede si el dispositivo se implementa en una zona horaria diferente?

Si el dispositivo se implementa en una zona horaria distinta, cambiará la zona horaria del dispositivo. Dado que todas las directivas de copia de seguridad usan la zona horaria del dispositivo, las directivas de copia de seguridad se ajustarán automáticamente según la nueva zona horaria. No se requiere ninguna intervención del usuario.

## <a name="modify-dns-settings"></a>Modificar la configuración de DNS

Cuando el dispositivo intenta comunicarse con el proveedor de servicios de almacenamiento en la nube, se usa un servidor DNS. Use la hoja **Configuración de red** del dispositivo para ver y modificar la configuración de DNS especificada. 

![Configuración de DNS en Configuración de red](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Para lograr alta disponibilidad, se debe configurar el servidor DNS principal y el secundario durante la implementación inicial del dispositivo.

**Servidor DNS principal**: use Windows PowerShell para StorSimple para especificar primero el servidor DNS principal durante la configuración inicial. Solo puede volver a configurar el servidor DNS principal mediante la interfaz de Windows PowerShell. Use el cmdlet `Set-HcsDNSClientServerAddress` para modificar el servidor DNS principal de su dispositivo. Para más información, consulte la sintaxis del cmdlet [Set-HcsDNSClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)).

**Servidor DNS secundario**: para modificar el servidor DNS secundario, use el cmdlet `Set-HcsDNSClientServerAddress` de la interfaz de Windows PowerShell del dispositivo o la hoja **Configuración de red** del dispositivo StorSimple en Azure Portal.

Para modificar el servidor DNS secundario en Azure Portal, realice los pasos siguientes.

1. Vaya al servicio StorSimple Device Manager. En la lista de dispositivos que se muestra, seleccione el dispositivo y haga clic en él.

2. En la hoja **Configuración**, vaya a **Configuración del dispositivo > Red**. Se abre la hoja **Configuración de red**. Haga clic en el icono de **Configuración DNS**. Modifique la dirección IP del servidor DNS secundario.

    ![Modificación de la dirección IP del servidor DNS secundario](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. En la barra de comandos, haga clic en **Guardar** y, cuando se le solicite confirmación, haga clic en **Aceptar**.

    ![Guardado y confirmación de los cambios](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Modificar las interfaces de red

El dispositivo tiene seis interfaces de red de dispositivo, cuatro de las cuales son de 1 GbE y dos de 10 GbE. Estas interfaces están etiquetadas mediante DATA 0 – DATA 5. DATA 0, DATA 1, DATA 4 y DATA 5 son de 1 GbE, mientras que DATA 2 y DATA 3 son interfaces de red de 10 GbE.

Use la hoja **Configuración de red** para configurar cada una de las interfaces que se usarán.

![Configuración de las interfaces de red mediante Configuración de red](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Para garantizar una alta disponibilidad, se recomienda tener al menos dos interfaces de iSCSI y dos interfaces compatibles con la nube en el dispositivo. Es recomendable, pero no obligatorio, deshabilitar las interfaces no usadas.

Para cada interfaz de red, se muestran los parámetros siguientes:

* **Velocidad** : no es un parámetro configurable por el usuario. DATA 0, DATA 1, DATA 4 y DATA 5 son siempre de 1 GbE, mientras que DATA 2 y DATA 3 son interfaces de 10 GbE.
  
  > [!NOTE]
  > La velocidad y el dúplex siempre se negocian automáticamente. Las tramas gigantes no son compatibles.
  
* **Estado de interfaz** : una interfaz se puede habilitar o deshabilitar. Si está habilitada, el dispositivo intentará usar la interfaz. Es recomendable habilitar tan solo las interfaces que están conectadas a la red y que se usan. Deshabilite las interfaces que no esté usando.
* **Tipo de interfaz** : este parámetro permite aislar el tráfico iSCSI del tráfico de almacenamiento de la nube. Este parámetro puede ser uno de los siguientes:
  
  * **Compatible con la nube** : si está habilitado, el dispositivo usará esta interfaz para comunicarse con la nube.
  * **Compatible con iSCSI** : si está habilitado, el dispositivo usará esta interfaz para comunicarse con el host iSCSI.
    
    Es recomendable aislar el tráfico iSCSI del tráfico de almacenamiento en la nube. Tenga también en cuenta que si el host se encuentra dentro de la misma subred que el dispositivo, no es necesario asignar una puerta de enlace; sin embargo, si el host está en una subred diferente a la de su dispositivo, deberá asignar una puerta de enlace.
* **Dirección IP**: al configurar cualquiera de las interfaces de red, debe configurar una dirección IP virtual (VIP). Puede ser IPv4, IPv6 o ambas. Las familias de direcciones IPv4 e IPv6 son compatibles con las interfaces de red del dispositivo. Si usa IPv4, especifique una dirección IP de 32 bits (*xxx.xxx.xxx.xxx*) en notación punto-decimal. Cuando use IPv6, solo tiene que proporcionar un prefijo de 4 dígitos y se generará automáticamente una dirección de 128 bits para la interfaz de red del dispositivo en función de ese prefijo.
* **Subred** : esto hace referencia a la máscara de subred y se configura mediante la interfaz de Windows PowerShell.
* **Puerta de enlace** : se trata de la puerta de enlace predeterminada que debe usar esta interfaz cuando intenta comunicarse con nodos que no están en el mismo espacio de direcciones IP (subred). La puerta de enlace predeterminada debe estar en el mismo espacio de direcciones (subred) que la dirección IP de la interfaz, según lo determinado por la máscara de subred.
* **Dirección IP fija** : este campo solo está disponible mientras se configura la interfaz DATA 0. Para efectuar operaciones como las actualizaciones o la solución de problemas del dispositivo, es posible que necesite conectarse directamente con el controlador del dispositivo. La dirección IP fija puede usarse para tener acceso al controlador activo y el pasivo en el dispositivo.

> [!NOTE]
> * Para garantizar un funcionamiento correcto, compruebe la velocidad de la interfaz y dúplex en el conmutador al que está conectado la interfaz de cada dispositivo. Las interfaces de conmutador deben negociar con o configurarse para Gigabit Ethernet (1000 Mbps) y ser de dúplex completo. Las interfaces que funcionan a velocidades menores o en un semidúplex darán como resultado problemas de rendimiento.
> * Para minimizar las interrupciones y el tiempo de inactividad, es recomendable habilitar portfast en cada uno de los puertos de conmutador a los que se va a conectar la interfaz de red iSCSI del dispositivo. Esto garantizará que la conectividad de red se pueda establecer rápidamente si se produce una conmutación por error.

### <a name="configure-data-0"></a>Configuración de DATA 0

DATA 0 es compatible con la nube de manera predeterminada. Al configurar DATA 0, también se deben configurar dos direcciones IP fijas, una para cada controlador. Estas direcciones IP fijas pueden usarse para tener acceso directamente a los controladores de dispositivos y son útiles cuando se instalan actualizaciones en el dispositivo, para que la recolección de elementos no reutilizados funcione correctamente o al obtener acceso a los controladores con el fin de solucionar problemas.

Puede volver a configurar los controladores fijos de IP mediante la hoja de configuración de DATA 0.

![Configuración de la interfaz de red: DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Las direcciones IP fijas del controlador se usan para el mantenimiento de las actualizaciones del dispositivo y que los algoritmos de recuperación de espacio (recopilación de elementos no reutilizados) funcione correctamente. Por lo tanto, las direcciones IP fijas deben ser enrutables y disponer de capacidad de conexión a Internet.

### <a name="configure-data-1---data-5"></a>Configuración de DATA 1 a DATA5

Para las interfaces de red de DATA 1 a DATA 5, puede configurar todos los parámetros de red mostrados en la siguiente captura de pantalla:

![Configuración de las interfaces de red de DATA 1 a DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Intercambiar o volver a asignar direcciones IP

Actualmente, si se le asigna una VIP que esté siendo usado (por el mismo dispositivo u otro de la red) a cualquier interfaz de red del controlador, a continuación, el controlador conmutará por error. Si intercambia o reasigna direcciones IP virtuales para una interfaz de red de dispositivo, debe seguir un procedimiento adecuado ya que podría crear una situación de IP duplicada.

Realice los pasos siguientes para intercambiar o volver a asignar las VIP para cualquiera de las interfaces de red:

#### <a name="to-reassign-ips"></a>Para volver a asignar direcciones IP

1. Borre la dirección IP de ambas interfaces.
2. Una vez borradas las direcciones IP, asigne las nuevas direcciones IP a las interfaces correspondientes.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [configurar MPIO para el dispositivo StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Aprenda a [usar el servicio StorSimple Device Manager para administrar el dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
