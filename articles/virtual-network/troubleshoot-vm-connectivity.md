---
title: Solución de problemas de conectividad de máquinas virtuales de Azure
description: Aprenda a diagnosticar y resolver problemas de conectividad que afectan a las máquinas virtuales (VM) de Azure.
author: TobyTu
ms.author: kaushika
manager: dcscontentpm
audience: ITPro
ms.topic: troubleshooting
ms.service: virtual-network
localization_priority: Normal
ms.date: 08/29/2019
ms.openlocfilehash: a2d2fc40417e1548a621e26bff70ac6028f8dda7
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/24/2021
ms.locfileid: "116986093"
---
# <a name="troubleshoot-azure-vm-connectivity-problems"></a>Solución de problemas de conectividad de máquinas virtuales de Azure

Este artículo ayuda a los administradores a diagnosticar y resolver problemas de conectividad que afectan a las máquinas virtuales (VM) de Azure.

## <a name="problems"></a>Problemas

- [Una máquina virtual de Azure que se implementa mediante Resource Manager no puede conectarse a otra máquina virtual de Azure en la misma red virtual](#azure-vm-cannot-connect-to-another-azure-vm-in-same-virtual-network).
- [Una máquina virtual de Azure no puede conectarse al segundo adaptador de red de una máquina virtual de Azure en la misma red virtual.](#azure-vm-cannot-connect-to-the-second-network-adapter-of-an-azure-vm-in-same-virtual-network)
- [Una máquina virtual de Azure no puede conectarse a Internet](#azure-vm-cannot-connect-to-the-internet).

Para resolver estos problemas, siga los pasos de la siguiente sección.

## <a name="resolution"></a>Solución

### <a name="azure-vm-cannot-connect-to-another-azure-vm-in-same-virtual-network"></a>Una máquina virtual de Azure no se puede conectar a otra máquina virtual de Azure en la misma red virtual

#### <a name="step-1-verify-that-vms-can-communicate-with-each-other"></a>Paso 1: Compruebe que las máquinas virtuales pueden comunicarse entre sí.

1. Descargue TCping en la máquina virtual de origen.
2. Abra una ventana de símbolo del sistema.
3. Vaya a la carpeta en la que descargó TCping.
4. Haga ping al destino desde la máquina virtual de origen con el siguiente comando:

    ![Captura de pantalla que muestra una ventana del símbolo del sistema que hace ping a una dirección IP continuamente.](media/troubleshoot-vm-connectivity/tcping.png)

    ```cmd
    tcping64.exe -t <destination VM address> 3389
    ```

> [!TIP]
> Si la prueba de ping se realiza correctamente, vaya al paso 3. De lo contrario, vaya al próximo paso.

#### <a name="step-2-check-the-network-security-group-settings&quot;></a>Paso 2: Compruebe la configuración del grupo de seguridad de red.

Para cada máquina virtual, compruebe las reglas de puerto de entrada predeterminadas (&quot;Permitir entrada de red virtual&quot; y &quot;Permitir entrada de Load Balancer"). Asegúrese también de comprobar que no hay reglas de bloqueo que coincidan que se enumeran debajo de una regla de prioridad inferior.

> [!NOTE]
> Las reglas que tengan un número menor se aplicarán primero. Por ejemplo, si tiene una regla que tiene prioridad 1000 y 6500, la regla que tiene prioridad 1000 se aplicará primero.

Después, intente hacer ping al destino desde la máquina virtual de origen de nuevo:

```cmd
tcping64.exe -t <destination VM address> 3389
```

#### <a name="step-3-check-whether-you-can-connect-to-the-destination-vm-by-using-remote-desktop-or-ssh"></a>Paso 3: Compruebe si puede conectarse a la máquina virtual de destino mediante Escritorio remoto o SSH.

Para conectarse mediante Escritorio remoto, siga estos pasos.

**Windows**:

1. Inicie sesión en Azure Portal.
2. En el menú de la izquierda, seleccione **Máquinas virtuales**.
3. Seleccione la máquina virtual en la lista.
4. En la página de la máquina virtual, haga clic en **Conectar**.

Para más información, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows e inicio de sesión en ella](../virtual-machines/windows/connect-logon.md).

**Linux**:

Para más información, consulte [Conexión a una máquina virtual Linux en Azure](../virtual-machines/linux/quick-create-portal.md).

Si la conexión Escritorio remoto o SSH es correcta, vaya al paso siguiente.

#### <a name="step-4-perform-a-connectivity-check"></a>Paso 4: Realice una comprobación de conectividad.

Ejecute una comprobación de conectividad en la máquina virtual de origen y compruebe la respuesta.

**Windows**: [Comprobación de la conectividad con Azure Network Watcher mediante PowerShell](../network-watcher/network-watcher-connectivity-powershell.md)

**Linux**: [Comprobación de la conectividad con Azure Network Watcher usando la CLI de Azure 2.0](../network-watcher/network-watcher-connectivity-cli.md)

Este es un ejemplo de respuesta:

```
ConnectionStatus : Unreachable
AvgLatencyInMs   :
MinLatencyInMs   :
MaxLatencyInMs   :
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "c5222ea0-3213-4f85-a642-cee63217c2f3",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurat
                   ions/ipconfig1",
                       "NextHopIds": [
                         "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa",
                       "Address": "10.1.2.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/fwNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "0f1500cd-c512-4d43-b431-7267e4e67017"
                       ],
                       "Issues": []
                     },
```

#### <a name="step-5-fix-the-issue-in-the-connectivity-check-result"></a>Paso 5: Corrección del problema en el resultado de la comprobación de conectividad.

1. En la sección **Saltos** de la respuesta de comprobación de conectividad que recibió, compruebe las **incidencias** enumeradas.

    ![respuesta de conectividad](media/troubleshoot-vm-connectivity/connectivity-response.png)

2. Busque la resolución correspondiente en la tabla siguiente y siga los pasos indicados para resolver las incidencias.

    |Tipo de problema  |Value  |Acción de resolución |
    |---------|---------|---------|
    |NetworkSecurityRule|Nombre del NSG de bloqueo|Puede eliminar [la regla de NSG](./manage-network-security-group.md#delete-a-security-rule) o modificarla como se describe [aquí](./manage-network-security-group.md#change-a-security-rule).|
    |UserDefinedRoute     |   Nombre de la UDR de bloqueo      | Si no necesita esta ruta, elimine la UDR. Si no puede eliminar la ruta, actualícela con el prefijo de dirección y el próximo salto adecuados. También puede ajustar la aplicación virtual de red para reenviar el tráfico correctamente. Para obtener más información, vea: [Enrutamiento del tráfico de redes virtuales](./virtual-networks-udr-overview.md) y [Enrutamiento del tráfico de red con una tabla de rutas mediante PowerShell](./tutorial-create-route-table-powershell.md).|
    |CPU    |    Uso     |     Siga estas recomendaciones que se describen en [Solución de problemas de rendimiento genérico para la máquina virtual de Azure en Linux o Windows](https://support.microsoft.com/en-in/help/3150851/generic-performance-troubleshooting-for-azure-virtual-machine-running).|
    |Memoria    |      Uso   |    Siga las recomendaciones que se describen en [Solución de problemas de rendimiento genérico para la máquina virtual de Azure en Linux o Windows](https://support.microsoft.com/en-in/help/3150851/generic-performance-troubleshooting-for-azure-virtual-machine-running).|
    |Firewall de invitado    |      Nombre del bloqueo del firewall   |      Siga estos pasos: [Activación o desactivación del Firewall de Windows Defender](https://support.microsoft.com/help/4028544/windows-turn-windows-firewall-on-or-off).|
    |Resolución DNS     |    Nombre del DNS     |    Siga estos pasos: [Guía de solución de problemas de Azure DNS](../dns/dns-troubleshoot.md) y [Resolución de nombres de recursos en redes virtuales de Azure](./virtual-networks-name-resolution-for-vms-and-role-instances.md).     |
    |Error de socket    |      No es aplicable   |     Otra aplicación ya está usando el puerto especificado. Intente usar otro puerto.    |

3. Vuelva a ejecutar la comprobación de conectividad para determinar si se resuelve el problema.

### <a name="azure-vm-cannot-connect-to-the-second-network-adapter-of-an-azure-vm-in-same-virtual-network"></a>Una máquina virtual de Azure no puede conectarse al segundo adaptador de red de una máquina virtual de Azure en la misma red virtual

#### <a name="step-1-make-sure-that-the-second-network-adapter-is-enabled-to-talk-outside-the-subnet"></a>Paso 1: Asegúrese de que el segundo adaptador de red está habilitado para hablar fuera de la subred.

De forma predeterminada, los adaptadores de red secundarios (también conocidos como tarjetas de interfaz de red o adaptadores de red) no están configurados para tener una puerta de enlace predeterminada. Por lo tanto, el flujo de tráfico en el adaptador secundario se limitará a la misma subred.

![Configuraciones de IP](media/troubleshoot-vm-connectivity/ipconfig.png)

Si los usuarios desean habilitar los adaptadores de red para comunicarse fuera de su propia subred, deben agregar una entrada a la tabla de enrutamiento para configurar la puerta de enlace. Para ello, realice los pasos siguientes:

1. En la máquina virtual que tiene configurado el segundo adaptador de red, abra una ventana del símbolo del sistema como administrador.
2. Ejecute el siguiente comando para agregar la entrada en la tabla de enrutamiento:

    ```cmd
    Route add 0.0.0.0 mask 0.0.0.0 -p <Gateway IP>
    ```

    Por ejemplo, si la segunda dirección IP es 192.168.0.4, la dirección IP de puerta de enlace debe ser 192.168.0.1. Para ello, ejecute el siguiente comando:

    ```cmd
    Route add 0.0.0.0 mask 0.0.0.0 -p 192.168.0.1
    ```

3. Ejecute la impresión de ruta. Si la entrada se agrega correctamente, verá una entrada similar a la siguiente:

    ![Ruta de dirección IP](media/troubleshoot-vm-connectivity/iproute.png)

Ahora, intente conectarse al adaptador de red secundario. Si la conexión sigue sin realizarse correctamente, vaya al paso siguiente.

#### <a name="step-2-check-nsg-settings-for-the-network-adapters"></a>Paso 2: Compruebe la configuración del NSG para los adaptadores de red.

Para los adaptadores de red principal y secundario, compruebe las reglas de puerto de entrada predeterminadas (Permitir entrada de red virtual, Permitir Load Balancer) para que entren en ambos adaptadores de red. También debe asegurarse de que no haya reglas de bloqueo que coincidan que tengan una regla de prioridad inferior por encima de ellas.

![Captura de pantalla que muestra la configuración de redes de una máquina virtual donde puede ver Permitir entrada de red virtual y Permitir salida de red virtual.](media/troubleshoot-vm-connectivity/nsg.png)

#### <a name="step-3-run-a-connectivity-check-to-the-secondary-network-adapter"></a>Paso 3: Ejecute una comprobación de conectividad con el adaptador de red secundario.

1. Ejecute una comprobación de conectividad con el adaptador de red secundario.
2. Ejecute una comprobación de conectividad en el entorno para asegurarse de que el proceso funciona de un extremo a otro.

Para obtener más información sobre cómo ejecutar la comprobación de conectividad, consulte los artículos siguientes:

**Windows**: [Comprobación de la conectividad con Azure Network Watcher mediante PowerShell](../network-watcher/network-watcher-connectivity-powershell.md)

**Linux**: [Comprobación de la conectividad con Azure Network Watcher usando la CLI de Azure 2.0](../network-watcher/network-watcher-connectivity-cli.md).

Este es un ejemplo de respuesta:

```
ConnectionStatus : Unreachable
AvgLatencyInMs   : 
MinLatencyInMs   : 
MaxLatencyInMs   : 
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "c5222ea0-3213-4f85-a642-cee63217c2f3",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurat
                   ions/ipconfig1",
                       "NextHopIds": [
                         "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa",
                       "Address": "10.1.2.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/fwNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "0f1500cd-c512-4d43-b431-7267e4e67017"
                       ],
                       "Issues": []
                     },
```

#### <a name="step-4-refer-the-table-under-step-5-and-follow-these-steps-to-resolve-the-issues"></a>Paso 4: Consulte la tabla del [paso 5](#step-5-fix-the-issue-in-the-connectivity-check-result)y siga estos pasos para resolver las incidencias.

### <a name="azure-vm-cannot-connect-to-the-internet"></a>Una máquina virtual de Azure no puede conectarse a Internet

#### <a name="step-1-check-whether-the-network-adapter-is-in-a-failed-state"></a>Paso 1: Compruebe si el adaptador de red está en estado de error.

Siga estos pasos para comprobar el estado de la NIC:

1. Inicie sesión en el portal Explorador de recursos.
2. En el panel izquierdo, seleccione **Suscripciones**.
3. En el panel izquierdo, seleccione el grupo de recursos al que pertenece el adaptador de red o la máquina virtual afectados.
4. Vaya a **Microsoft Network.**
5. Seleccione la opción **Interfaces de red**.
6. Seleccione la interfaz de red requerida.
7. Seleccione la opción **Lectura/escritura** en la parte superior del portal.
8. Seleccione la opción **Editar**.

    ![NIC1](media/troubleshoot-vm-connectivity/nicedit1.png)

    > [!NOTE]
    > Después de seleccionar la opción **Editar**, la opción “Get” cambia a una opción **Put**.

    ![NIC2](media/troubleshoot-vm-connectivity/nicedit2.png)

9. Seleccione la interfaz de red afectada y, a continuación, elija la opción **Put**.

    > [!NOTE]
    > Después de realizar esta selección, **ProvisioningState** se muestra como **Actualizando**. El mismo resultado se muestra en el portal de Azure Resource Manager normal. Si la operación se completa correctamente, el valor **ProvisioningState** cambia a **Correcto**, como se muestra.
10. Actualice el portal. El adaptador de red debe estar en un estado correcto.

#### <a name="step-2-follow-step-4-to-run-a-connectivity-check"></a>Paso 2: Siga el [paso 4](#step-4-perform-a-connectivity-check) para ejecutar una comprobación de conectividad.

#### <a name="step-3-refer-the-table-under-step-5-and-follow-the-steps-to-resolve-the-issues"></a>Paso 3: Consulte la tabla del [paso 5](#step-5-fix-the-issue-in-the-connectivity-check-result)y siga estos pasos para resolver las incidencias.

## <a name="next-steps"></a>Pasos siguientes

[Solución de problemas de conectividad entre máquinas virtuales de Azure](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
