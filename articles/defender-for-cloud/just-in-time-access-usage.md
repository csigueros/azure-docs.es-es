---
title: Acceso a máquinas virtuales Just-In-Time en Microsoft Defender for Cloud | Microsoft Docs
description: Obtenga información sobre cómo el acceso a máquinas virtuales Just-In-Time (JIT) en Microsoft Defender for Cloud le ayuda a controlar el acceso a las máquinas virtuales de Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: f1eb796873ceffced83a63c077ced3fa1a838c8b
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525776"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Protección de los puertos de administración con acceso Just-in-Time

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Bloquee el tráfico de entrada a Azure Virtual Machines con la característica de acceso a la máquina virtual (VM) Just-In-Time (JIT) de Microsoft Defender for Cloud. Esto reduce la exposición a los ataques a la vez que proporciona un acceso sencillo cuando necesita conectarse a una máquina virtual.

Para obtener una explicación completa sobre cómo funciona JIT y la lógica subyacente, consulte la [explicación sobre Just-in-Time](just-in-time-access-overview.md).

Esta página le enseña cómo incluir JIT en el programa de seguridad. Aprenderá a: 

- **Habilitar JIT en las máquinas virtuales**: puede habilitar JIT con sus propias opciones personalizadas para una o varias máquinas virtuales mediante Defender for Cloud, PowerShell o la API de REST. Como alternativa, puede habilitar JIT con parámetros predeterminados codificados de forma rígida de las máquinas virtuales de Azure. Cuando se habilita, JIT bloquea el tráfico entrante a las máquinas virtuales de Azure mediante la creación de una regla en el grupo de seguridad de red.
- **Solicitud de acceso a una máquina virtual que tiene JIT habilitado**: el objetivo de JIT es asegurarse de que, aunque el tráfico entrante esté bloqueado, Defender for Cloud sigue facilitando el acceso para conectarse a las máquinas virtuales cuando sea necesario. Puede solicitar acceso a una máquina virtual habilitada para JIT desde Defender for Cloud, máquinas virtuales de Azure, PowerShell o la API de REST.
- **Auditar la actividad**: para asegurarse de que las máquinas virtuales estén protegidas correctamente, revise los accesos a las máquinas virtuales habilitadas para JIT como parte de las comprobaciones de seguridad periódicas.   



## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponibilidad general (GA)|
|Precios:|Requiere [Microsoft Defender para servidores](defender-for-servers-introduction.md)|
|Máquinas virtuales admitidas:|:::image type="icon" source="./media/icons/yes-icon.png"::: Máquinas virtuales implementadas por medio de Azure Resource Manager.<br>:::image type="icon" source="./media/icons/no-icon.png"::: Máquinas virtuales con modelos de implementación clásica. [Más información acerca de estos modelos de implementación](../azure-resource-manager/management/deployment-models.md).<br>:::image type="icon" source="./media/icons/no-icon.png"::: Máquinas virtuales que las instancias de Azure Firewall protegen y que [Azure Firewall Manager](../firewall-manager/overview.md) controla|
|Roles y permisos necesarios:|Los roles **Lector** y **SecurityReader** pueden ver el estado y los parámetros de JIT.<br>Para crear roles personalizados que puedan funcionar con JIT, consulte [¿Cuáles son los permisos necesarios para configurar y usar Just-In-Time?](just-in-time-access-overview.md#what-permissions-are-needed-to-configure-and-use-jit).<br>Para crear un rol con menos privilegios para los usuarios que necesiten solicitar acceso JIT a una máquina virtual y no realizar ninguna otra operación JIT, use el [script Set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/main/Powershell%20scripts/JIT%20Scripts/JIT%20Custom%20Role) de las páginas de la comunidad de Defender for Cloud GitHub.|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Nacionales (Azure Government, Azure China 21Vianet)|
|||


## <a name="enable-jit-vm-access"></a>Habilitación del acceso a máquinas virtuales JIT <a name="jit-configure"></a>

Puede habilitar el acceso a máquinas virtuales JIT con sus propias opciones personalizadas para una o varias máquinas virtuales mediante Defender for Cloud o mediante programación. 

Como alternativa, puede habilitar JIT con parámetros predeterminados codificados de forma rígida de las máquinas virtuales de Azure.

Cada una de estas opciones se explica en una pestaña independiente a continuación.

### <a name="microsoft-defender-for-cloud"></a>[**Microsoft Defender for Cloud**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-microsoft-defender-for-cloud"></a>Habilitación de JIT en las máquinas virtuales desde Microsoft Defender for Cloud<a name="jit-asc"></a>

:::image type="content" source="./media/just-in-time-access-usage/jit-config-security-center.gif" alt-text="Configuración del acceso a máquinas virtuales JIT en Microsoft Defender for Cloud.":::

Desde Defender for Cloud, puede habilitar y configurar el acceso a la máquina virtual JIT.

1. Abra el panel **Protección de cargas de trabajo** y, en el área de protección avanzada, seleccione **Acceso a máquina virtual Just-In-Time.**

    Se abrirá la página **Acceso de máquina virtual Just-In-Time** con las máquinas virtuales agrupadas en las siguientes pestañas:

    - **Configurado**: máquinas virtuales que ya se han configurado para admitir el acceso a máquinas virtuales Just-In-Time. Para cada máquina virtual, la pestaña Configurado muestra:
        - el número de solicitudes JIT aprobadas en los últimos siete días
        - la fecha y hora del último acceso
        - los detalles de la conexión configurados
        - el último usuario
    - **No configurado**: máquinas virtuales sin JIT habilitado, pero que pueden admitir JIT. Se recomienda habilitar JIT para estas máquinas virtuales.
    - **No admitido**: máquinas virtuales sin JIT habilitado y que no admiten la característica. Es posible que su máquina virtual esté en esta pestaña por las siguientes razones:
      - Falta un grupo de seguridad de red (NSG) o Azure Firewall: JIT requiere que se configure un grupo de seguridad de red o una configuración de firewall (o ambos)
      - Máquina virtual clásica: JIT es compatible con las máquinas virtuales que se implementan a través de Azure Resource Manager, no con la "implementación clásica". [Obtenga más información sobre los modelos de implementación clásica y de Resource Manager](../azure-resource-manager/management/deployment-models.md).
      - Otro: la máquina virtual puede estar en esta pestaña si la solución JIT está deshabilitada en la directiva de seguridad de la suscripción o el grupo de recursos.

1. En la pestaña **No configurado**, marque las máquinas virtuales que desea proteger con JIT y seleccione **Habilitar JIT en VM**. 

    Se abre la página de acceso a la máquina virtual JIT con los puertos que Defender for Cloud recomienda proteger:
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM

    Para aceptar la configuración predeterminada, seleccione **Guardar**.

1. Para personalizar las opciones de JIT:

    - Agregue puertos personalizados con el botón **Agregar**. 
    - Modifique uno de los puertos predeterminados seleccionándolo en la lista.

    Para cada puerto (personalizado y predeterminado), el panel **Agregar configuración de puerto** ofrece las siguientes opciones:

    - **Protocolo**: el protocolo que se permite en este puerto cuando se aprueba una solicitud.
    - **Direcciones IP de origen permitidas**: los intervalos de direcciones IP que se permiten en este puerto cuando se aprueba una solicitud.
    - **Tiempo de solicitud máximo**: el período de tiempo máximo durante el que puede estar abierto un puerto específico.

     1. Establezca la seguridad del puerto según sus necesidades.

     1. Seleccione **Aceptar**.

1. Seleccione **Guardar**.



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-defender-for-cloud"></a>Edición de la configuración JIT en una máquina virtual habilitada para JIT mediante Defender for Cloud <a name="jit-modify"></a>

Puede modificar la configuración Just-In-Time de una máquina virtual agregando y configurando un puerto nuevo que proteja dicha máquina virtual, o bien cambiando otro valor relacionado con un puerto ya protegido.

Para editar las reglas JIT existentes para una máquina virtual:

1. Abra el panel **Protección de cargas de trabajo** y, en el área de protección avanzada, seleccione **Acceso a máquina virtual Just-In-Time.**

1. En la pestaña **Configurado**, haga clic con el botón derecho en la máquina virtual a la que desea agregar un puerto y seleccione Editar. 

    ![Edición de una configuración de acceso de máquina virtual JIT en Microsoft Defender for Cloud.](./media/just-in-time-access-usage/jit-policy-edit-security-center.png)

1. En **Configuración de acceso a máquina virtual del tipo JIT** , puede modificar la configuración existente de un puerto protegido, o bien agregar un puerto personalizado.

1. Cuando haya terminado de editar los puertos, seleccione **Guardar**.
 


### <a name="azure-virtual-machines"></a>[**Máquinas virtuales de Azure**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>Habilitación de JIT en las máquinas virtuales desde máquinas virtuales de Azure

Puede habilitar JIT en una máquina virtual desde las páginas de máquinas virtuales de Azure Portal.

![Configuración del acceso JIT a máquinas virtuales en máquinas virtuales de Azure.](./media/just-in-time-access-usage/jit-config-virtual-machines.gif)

> [!TIP]
> Si una máquina virtual ya tiene habilitado Just-In-Time, cuando vaya a su página de configuración verá que Just-In-Time está habilitado y puede usar el vínculo para abrir la página de acceso a la máquina virtual Just-In-Time en Defender for Cloud y ver y cambiar la configuración.

1. En [Azure Portal](https://ms.portal.azure.com), busque y seleccione **Máquinas virtuales**. 

1. Seleccione la máquina virtual que desee proteger con JIT.

1. En el menú, seleccione **Configuración**.

1. En **Acceso Just-In-Time**, seleccione **Habilitar Just-In-Time**. 

    Esto habilita el acceso Just-In-Time para la máquina virtual con las siguientes configuraciones predeterminadas:

    - Máquinas Windows:
        - Puerto RDP 3389
        - Se permite un tiempo de acceso máximo de tres horas
        - Las direcciones IP de origen permitidas se establecen en Cualquiera
    - Máquinas Linux:
        - Puerto SSH 22
        - Se permite un tiempo de acceso máximo de tres horas
        - Las direcciones IP de origen permitidas se establecen en Cualquiera

1. Para editar cualquiera de estos valores o agregar más puertos a la configuración JIT, use la página Just-In-Time de Microsoft Defender for Cloud:

    1. En el menú de Defender for Cloud, seleccione **Acceso a máquina virtual Just-In-Time.**

    1. En la pestaña **Configurado**, haga clic con el botón derecho en la máquina virtual a la que desea agregar un puerto y seleccione Editar. 

        ![Edición de una configuración de acceso de máquina virtual JIT en Microsoft Defender for Cloud.](./media/just-in-time-access-usage/jit-policy-edit-security-center.png)

    1. En **Configuración de acceso a máquina virtual del tipo JIT** , puede modificar la configuración existente de un puerto protegido, o bien agregar un puerto personalizado.

    1. Cuando haya terminado de editar los puertos, seleccione **Guardar**.


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>Habilitación de JIT en las máquinas virtuales mediante PowerShell

Para habilitar el acceso a máquinas virtuales Just-In-Time desde PowerShell, use el cmdlet oficial deMicrosoft Defender for Cloud PowerShell `Set-AzJitNetworkAccessPolicy`.

**Ejemplo**: habilite el acceso a máquinas virtuales Just-in-Time en una máquina virtual específica con las siguientes reglas:

* Cierre los puertos 22 y 3389.
* Establezca una ventana de tiempo máximo de 3 horas para cada uno, para que así puedan abrirse según la solicitud aprobada.
* Deje que el usuario que solicita el acceso controle las direcciones IP de origen.
* Deje que el usuario que solicita el acceso establezca una sesión correcta tras una solicitud de acceso del tipo Just-In-Time aprobada.

Los siguientes comandos de PowerShell crean esta configuración JIT:

1. Asigne una variable que contenga las reglas de acceso a máquinas virtuales Just-in-Time para una máquina virtual:

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})
    ```

1. Inserte las reglas de acceso a máquinas virtuales Just-in-Time de la máquina virtual en una matriz:
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. Configure las reglas de acceso a máquinas virtuales Just-in-Time en la máquina virtual seleccionada:
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    Use el parámetro -Name para especificar una máquina virtual. Por ejemplo, para establecer la configuración JIT para dos máquinas virtuales diferentes, VM1 y VM2, use ```Set-AzJitNetworkAccessPolicy -Name VM1``` y ```Set-AzJitNetworkAccessPolicy -Name VM2```.


### <a name="rest-api"></a>[**API DE REST**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>Habilitación de JIT en las máquinas virtuales mediante API REST

La característica de acceso a máquinas virtuales Just-In-Time se puede usar a través de la API de Microsoft Defender for Cloud. Use esta API para obtener más información acerca de las máquinas virtuales configuradas, cómo agregar otras nuevas o solicitar acceso a una máquina virtual, entre otras cosas. 

Obtenga más información en [Directivas de acceso a redes JIT](/rest/api/securitycenter/jitnetworkaccesspolicies).

---

## <a name="request-access-to-a-jit-enabled-vm"></a>Solicitud de acceso a una máquina virtual habilitada para JIT

Puede solicitar acceso a una máquina virtual habilitada para JIT desde la Azure Portal (Defender for Cloud o Azure Virtual machines) o mediante programación.

Cada una de estas opciones se explica en una pestaña independiente a continuación.

### <a name="microsoft-defender-for-cloud"></a>[**Microsoft Defender for Cloud**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-microsoft-defender-for-cloud"></a>Solicitud de acceso a una máquina virtual habilitada para JIT desde Microsoft Defender for Cloud 

Cuando una máquina virtual tiene JIT habilitado, tiene que solicitar acceso para conectarse a ella. Puede solicitar acceso de cualquiera de las maneras admitidas, independientemente de cómo haya habilitado JIT.

:::image type="content" source="./media/just-in-time-access-usage/jit-request-security-center.gif" alt-text="Solicitud de acceso JIT desdeDefender for Cloud.":::

1. En la página **Acceso de máquina virtual Just-In-Time**, seleccione la pestaña **Configurado**.

1. Marque las máquinas virtuales a las que desea acceder.

    - El icono de la columna **Detalles de la conexión** indica si JIT está habilitado en el grupo de seguridad de red o el firewall. Si está habilitado en ambos, solo aparece el icono de firewall.

    - En la columna **Detalles de la conexión** se proporciona la información necesaria para conectarse a la máquina virtual y sus puertos abiertos.

1. Seleccione **Solicitar acceso**. Se abre la ventana **Solicitar acceso**.

1. En **Solicitar acceso**, configure para cada máquina virtual los puertos que desee que se abran y las direcciones IP de origen en que se abre el puerto y el tiempo durante el que permanecerá abierto. Solo será posible solicitar acceso a los puertos configurados. Cada puerto tiene un tiempo máximo permitido que se basa en la configuración JIT que ha creado.

1. Seleccione **Open ports** (Abrir puertos).

> [!NOTE]
> Si un usuario que solicita acceso está detrás de un proxy, es posible que la opción de **Mi IP** no funcione. Es posible que necesite definir todo el intervalo de direcciones IP de la organización.



### <a name="azure-virtual-machines"></a>[**Máquinas virtuales de Azure**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Solicitud de acceso a una máquina virtual habilitada para JIT desde la página de conexión de la máquina virtual de Azure

Cuando una máquina virtual tiene JIT habilitado, tiene que solicitar acceso para conectarse a ella. Puede solicitar acceso de cualquiera de las maneras admitidas, independientemente de cómo haya habilitado JIT.

  >![Solicitud Just-In-Time (JIT).](./media/just-in-time-access-usage/jit-request-vm.png)


Para solicitar acceso desde máquinas virtuales de Azure:

1. En Azure Portal, abra las páginas de máquinas virtuales.

1. Seleccione la máquina virtual a la que desea conectarse y abra la página **Conectar**.

    Azure comprueba si JIT está habilitado en esa máquina virtual.

    - Si JIT no está habilitado para la máquina virtual, se le pedirá que lo habilite.

    - Si está JIT está habilitado, seleccione **Solicitar acceso** para pasar una solicitud de acceso con la dirección IP de solicitud, el intervalo de tiempo y los puertos que se configuraron para esa máquina virtual.

> [!NOTE]
> Una vez aprobada una solicitud para una máquina virtual protegida por Azure Firewall, Defender for Cloud proporciona al usuario los detalles de conexión adecuados (la asignación de puertos de la tabla DNAT) que se usarán para conectarse a la máquina virtual.



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>Solicitud de acceso a una máquina virtual habilitada para JIT mediante PowerShell

En el siguiente ejemplo, puede ver una solicitud de acceso a máquina virtual del tipo Just-In-Time para una máquina virtual específica, en la que se solicita que se abra el puerto 22 para una dirección IP específica y durante un período concreto:

Ejecute lo siguiente en PowerShell:

1. Configure las propiedades de acceso de solicitud de la máquina virtual:

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. Inserte los parámetros de solicitud de acceso a la máquina virtual en una matriz:

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. Envíe el acceso de solicitud (use el id. de recurso del paso 1).

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

Para más información, consulte la [documentación del cmdlet de PowerShell](/powershell/scripting/developer/cmdlet/cmdlet-overview).



### <a name="rest-api"></a>[**API DE REST**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>Solicitud de acceso a una máquina virtual habilitada para JIT mediante API REST

La característica de acceso a máquinas virtuales Just-In-Time se puede usar a través de la API de Microsoft Defender for Cloud. Use esta API para obtener más información acerca de las máquinas virtuales configuradas, cómo agregar otras nuevas o solicitar acceso a una máquina virtual, entre otras cosas. 

Obtenga más información en [Directivas de acceso a redes JIT](/rest/api/securitycenter/jitnetworkaccesspolicies).

---

## <a name="audit-jit-access-activity-in-defender-for-cloud"></a>Auditar la actividad de acceso JIT en Defender for Cloud

Puede usar la búsqueda de registros para obtener información sobre las actividades de las máquinas virtuales. Para ver los registros:

1. En **Acceso de máquina virtual Just-In-Time**, seleccione la pestaña **Configurado**.

1. Para la máquina virtual que desea auditar, abra el menú de puntos suspensivos al final de la fila.
 
1. Seleccione **Registro de actividades** en el menú.

   ![Selección del registro de actividad Just-in-Time (JIT).](./media/just-in-time-access-usage/jit-select-activity-log.png)

   El registro de actividades proporciona una vista filtrada de las operaciones anteriores para esa máquina virtual, junto con el tiempo, la fecha y la suscripción.

1. Para descargar la información de registro, seleccione **Descargar como CSV**.



## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido _a_ configurar y usar el acceso Just-in-Time a máquinas virtuales. Para saber _por qué_ se debe usar JIT, lea el artículo de conceptos en el que se explican las amenazas contra las que protege:

> [!div class="nextstepaction"]
> [JIT explicado](just-in-time-access-overview.md)
