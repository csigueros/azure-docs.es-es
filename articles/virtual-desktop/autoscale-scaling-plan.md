---
title: Versión preliminar de la escalabilidad automática del host de sesión de Azure Virtual Desktop
description: Cómo usar la característica de escalabilidad automática para asignar recursos en la implementación.
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 88de9f363851d47fbefcdcf69060111d8fd64bbf
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842385"
---
# <a name="autoscale-preview-for-azure-virtual-desktop-host-pools"></a>Escalabilidad automática (versión preliminar) para grupos de hosts de Azure Virtual Desktop

> [!IMPORTANT]
> La característica de escalado automático está actualmente en vista previa.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

La característica de escalabilidad automática (versión preliminar) permite escalar o reducir verticalmente las máquinas virtuales (VM) de la implementación de Azure Virtual Desktop para optimizar los costos de implementación. En función de sus necesidades, puede crear un plan de escalado basado en:

- Hora del día
- Días específicos de la semana
- Límites de sesión por host de sesión

>[!NOTE]
> - Azure Virtual Desktop (clásico) no admite la característica de escalabilidad automática. 
> - La escalabilidad automática no admite Azure Virtual Desktop para Azure Stack HCI 
> - La escalabilidad automática no admite el escalado de discos efímeros.


Para obtener mejores resultados, se recomienda usar la escalabilidad automática con máquinas virtuales implementadas con plantillas de Azure Resource Manager de Azure Virtual Desktop o herramientas de Microsoft.

>[!IMPORTANT]
>La versión preliminar de esta característica tiene actualmente las siguientes limitaciones:
>
> - Solo puede usar la escalabilidad automática en la nube pública de Azure.
> - Solo puede configurar la escalabilidad automática con Azure Portal.
> - Solo puede implementar el plan de escalado en las regiones de EE. UU. y Europa.

## <a name="requirements"></a>Requisitos

Antes de crear el primer plan de escalado, asegúrese de seguir estas directrices:

- Actualmente, solo puede configurar la escalabilidad automática con grupos de hosts existentes agrupados.
- Todos los grupos de hosts que escale automáticamente deben tener el parámetro MaxSessionLimit configurado. No utilice el valor predeterminado. Puede configurar este valor en la configuración del grupo de hosts en Azure Portal o ejecutar los cmdlets [New-AZWvdHostPool](/powershell/module/az.desktopvirtualization/new-azwvdhostpool?view=azps-5.7.0&preserve-view=true) o [Update-AZWvdHostPool](/powershell/module/az.desktopvirtualization/update-azwvdhostpool?view=azps-5.7.0&preserve-view=true) en PowerShell.
- Debe conceder acceso a Azure Virtual Desktop para administrar el encendido de los recursos de proceso de máquina virtual.

## <a name="create-a-custom-rbac-role"></a>Creación de un rol RBAC personalizado

Para empezar a crear un plan de escalado, primero debe crear un rol personalizado del control de acceso basado en roles (RBAC) en la suscripción. Este rol permitirá que Windows Virtual Desktop administre el encendido de las máquinas virtuales de la suscripción. También permitirá que el servicio aplique acciones en los grupos de hosts y las máquinas virtuales cuando no haya sesiones de usuario activas.

Para crear el rol personalizado, siga las instrucciones de [Roles personalizados de Azure](../role-based-access-control/custom-roles.md) mediante la plantilla JSON siguiente. Esta plantilla ya incluye los permisos que necesita. Para obtener instrucciones más detalladas, vea [Asignación de roles personalizados con Azure Portal](#assign-custom-roles-with-the-azure-portal).
```json
 {
 "properties": {
 "roleName": "Autoscale",
 "description": "Friendly description.",
 "assignableScopes": [
 "/subscriptions/<SubscriptionID>"
 ],
  "permissions": [
   {
   "actions": [
                 "Microsoft.Insights/eventtypes/values/read",
                 "Microsoft.Compute/virtualMachines/deallocate/action",
                 "Microsoft.Compute/virtualMachines/restart/action",
                 "Microsoft.Compute/virtualMachines/powerOff/action",
                 "Microsoft.Compute/virtualMachines/start/action",
                 "Microsoft.Compute/virtualMachines/read",
                 "Microsoft.DesktopVirtualization/hostpools/read",
                 "Microsoft.DesktopVirtualization/hostpools/write",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/write",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/sendMessage/action",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read"
],
  "notActions": [],
  "dataActions": [],
  "notDataActions": []
  }
 ]
}
}
```

## <a name="assign-custom-roles-with-the-azure-portal"></a>Asignación de roles personalizados con Azure Portal

Para crear y asignar el rol personalizado a la suscripción con Azure Portal:

1. Abra Azure Portal y vaya a **Suscripciones**.

2. Seleccione el botón **+** situado en la esquina superior izquierda de la pantalla y, a continuación, seleccione **Agregar rol personalizado** en el menú desplegable, como se muestra en la captura de pantalla siguiente.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla que muestra el menú desplegable que aparece al seleccionar el botón de signo más en el panel de control de Azure Portal. La opción Agregar rol personalizado está seleccionada y resaltada con un borde rojo.](media/add-custom-role.png)

3. A continuación, asigne un nombre al rol personalizado y agregue una descripción. Se recomienda que asigne el nombre "Escalado automático" al rol.

4. En la pestaña **Permisos**, agregue los siguientes permisos a la suscripción a la que va a asignar el rol:

    ```azcopy
        "Microsoft.Insights/eventtypes/values/read"
                 "Microsoft.Compute/virtualMachines/deallocate/action"
                 "Microsoft.Compute/virtualMachines/restart/action"
                 "Microsoft.Compute/virtualMachines/powerOff/action"
                 "Microsoft.Compute/virtualMachines/start/action"
                 "Microsoft.Compute/virtualMachines/read"
                 "Microsoft.DesktopVirtualization/hostpools/read"
                 "Microsoft.DesktopVirtualization/hostpools/write"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/sendMessage/action"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read"
    ```

5. Cuando haya finalizado, seleccione **Aceptar**.

Después de esto, deberá asignar el rol para conceder acceso a Azure Virtual Desktop.

Para asignar el rol personalizado para conceder acceso:

1. En la pestaña **Control de acceso (IAM)** , seleccione **Agregar asignación de roles**.

2. Seleccione el rol que acaba de crear y continúe en la siguiente pantalla.

3. Elija **+ Seleccionar miembros**. En la barra de búsqueda, escriba y seleccione **Windows Virtual Desktop**, como se muestra en la siguiente captura de pantalla. Cuando tenga una implementación de Azure Virtual Desktop (clásico) y una instancia de Azure Virtual Desktop con objetos de Azure Virtual Desktop de Azure Resource Manager, verá dos aplicaciones con el mismo nombre. Selecciónelas las dos.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla del menú Agregar asignación de roles. El campo Seleccionar está resaltado en rojo y el usuario escribe "Windows Virtual Desktop" en el campo de búsqueda.](media/search-for-role.png)

4. Seleccione **Review + assign** (Revisar y asignar) para finalizar la asignación.

## <a name="how-creating-a-scaling-plan-works"></a>Funcionamiento de la creación de un plan de escalado

Antes de crear el plan, tenga en cuenta lo siguiente:

- Puede asignar un plan de escalado a uno o varios grupos de hosts del mismo tipo de grupo de hosts. La programación del plan de escalado también se aplicará en todos los grupos de hosts asignados.

- Solo puede asociar un plan de escalado por grupo de hosts. Si asigna un único plan de escalado a varios grupos de hosts, esos grupos de hosts no se pueden asignar a otro plan de escalado.

- Un plan de escalado solo puede funcionar en su zona horaria configurada.

- Un plan de escalado puede tener una o varias programaciones. Por ejemplo, diferentes programaciones durante los días laborables frente al fin de semana.

- Asegúrese de comprender los patrones de uso antes de definir la programación. Deberá programar para las siguientes horas del día:

    - Ascenso: el inicio del día, cuando aumenta el uso.
    - Horas punta: la hora del día en la que el uso es mayor.
    - Descenso: cuando baja el uso. Normalmente, es aquí cuando se apagan las máquinas virtuales para ahorrar costos.
    - Horas de menor actividad: la hora con el uso más bajo posible. Puede definir el número máximo de máquinas virtuales que pueden estar activas durante este tiempo.

- El plan de escalado se hará efectivo en cuanto lo habilite.

Además, tenga en cuenta estas limitaciones:

- No use la escalabilidad automática en combinación con otras herramientas de escalado de Microsoft o de terceros. Asegúrese de deshabilitarlas para los grupos de hosts a los que aplica los planes de escalado.

- La escalabilidad automática sobrescribe el modo de purga, por lo que debe asegurarse de usar etiquetas de exclusión al actualizar las máquinas virtuales de los grupos de hosts.

- La escalabilidad automática omite los algoritmos de equilibrio de carga existentes en la configuración del grupo de hosts y, en su lugar, aplica el equilibrio de carga en función de la configuración de la programación.

## <a name="create-a-scaling-plan"></a>Creación de un plan de escalado

Para crear un plan de escalado:

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com/).

2. Vaya a **Azure Virtual Desktop** > **Scaling Plans** (Azure Virtual Desktop > Planes de escalado) y, a continuación, seleccione **Create** (Crear).

3. En la pestaña **Basics** (Básico), busque en **Project details** (Detalles del proyecto) y seleccione el nombre de la suscripción a la que va a asignar el plan de escalado.

4. Si desea crear un nuevo grupo de recursos, seleccione **Create new** (Crear nuevo). Si desea usar un grupo de recursos existente, seleccione su nombre en el menú desplegable.

5. Escriba un nombre para el plan de escalado en el campo **Name** (Nombre).

6. Opcionalmente, también puede agregar un nombre "descriptivo" que se mostrará a los usuarios y una descripción del plan.

7. En **Region** (Región), seleccione una región para el plan de escalado. Los metadatos del objeto se almacenarán en la geografía asociada a la región. Para más información sobre las regiones, consulte [Ubicaciones de datos para Azure Virtual Desktop](data-locations.md).

8. En **Time zone** (Zona horaria), seleccione la zona horaria que usará con el plan.

9. En **Exclusion tags** (Etiquetas de exclusión), escriba etiquetas para las máquinas virtuales que no desea incluir en las operaciones de escalado. Por ejemplo, es posible que quiera usar esta funcionalidad para el mantenimiento. Cuando haya establecido máquinas virtuales en modo de purga, use la etiqueta para que la escalabilidad automática no invalide el modo de purga.

10. Seleccione **Next** (Siguiente), lo que le llevará a la pestaña **Schedules** (Programaciones).

## <a name="configure-a-schedule"></a>Configuración de una programación

Las programaciones le permiten definir cuándo va a activar la escalabilidad automática los modos de ascenso y descenso a lo largo del día. En cada fase de la programación, la escalabilidad automática solo desactiva las máquinas virtuales cuando un host de sesión no tiene sesiones activas. Los valores predeterminados que verá al intentar crear una programación son los valores sugeridos para los días laborables, pero puede cambiarlos según sea necesario. 

Para crear o cambiar una programación:

1. En la pestaña **Schedules** (Programaciones), seleccione **Add schedule** (Agregar programación).

2. Escriba un nombre para la programación en el campo **Nombre de programación**.

3. En el campo **Repeat on** (Repetir el), seleccione los días en los que se repetirá la programación.

4. En la pestaña **Ramp up** (Ascenso), rellene los campos siguientes:

    - En **Start time** (Hora de inicio), seleccione una hora en el menú desplegable para empezar a preparar las máquinas virtuales para las horas punta.

    - En **Load balancing algorithm** (Algoritmo de equilibrio de carga), se recomienda seleccionar **breadth-first algorithm** (Algoritmo de equilibrio de carga en amplitud). El equilibrio de carga en amplitud distribuirá a los usuarios entre las máquinas virtuales existentes para mantener los tiempos de acceso rápidos.
        
        >[!NOTE]
        >La preferencia de equilibrio de carga que seleccione aquí invalidará la que seleccionó para la configuración original del grupo de hosts.

    - En **Minimum percentage of session host VMs** (Porcentaje mínimo de máquinas virtuales de host de sesión), escriba la cantidad de recursos de host de sesión que desea usar durante las horas de ascenso y las horas punta. Por ejemplo, si elige el **10 %** y el grupo de hosts tiene 10 hosts de sesión, la escalabilidad automática mantendrá un host de sesión disponible para las conexiones de usuario en todo momento durante las horas de ascenso y las horas punta.
    
    - En **Capacity threshold** (Umbral de capacidad), escriba el porcentaje de uso del grupo de hosts que desencadenará el inicio de las fases de ascenso y de horas punta. Por ejemplo, si elige el **60 %** para un grupo de hosts que puede controlar 100 sesiones, la escalabilidad automática solo activará hosts adicionales una vez que el grupo de hosts supere las 60 sesiones.

5. En la pestaña **Peak hours** (Horas punta), rellene los campos siguientes:

    - En **Horas punta**, escriba una hora de inicio para el momento en el que la tasa de uso sea mayor durante el día. Asegúrese de que la hora esté en la misma zona horaria que especificó para el plan de escalado. Esta hora también es la hora de finalización de la fase de ascenso.

    - En **Load balancing** (Equilibrio de carga), puede seleccionar entre el equilibrio de carga en amplitud o en profundidad. El equilibrio de carga en amplitud distribuye las nuevas sesiones de usuario entre todas las sesiones disponibles del grupo de hosts. El equilibrio de carga en profundidad distribuye las nuevas sesiones de usuario a un host de sesión disponible con el máximo número de conexiones que aún no haya alcanzado su límite de sesiones. Para más información sobre los tipos de equilibrio de carga, consulte [Configuración del método de equilibrio de carga de Azure Virtual Desktop](configure-host-pool-load-balancing.md).

    >[!NOTE]
    >Aquí no se puede cambiar el umbral de capacidad. En su lugar, se llevará a esta configuración la configuración que escribió en **Ramp-up** (Ascenso).

    - En **Ramp-down** (Descenso), escribirá valores en campos similares a los de **Ramp-up** (Ascenso), pero esta vez serán para cuando descienda el uso del grupo de hosts. Esto incluye los siguientes campos:

      - Hora de inicio
      - Algoritmo de equilibrio de carga
      - Porcentaje mínimo de hosts (%)
      - Umbral de capacidad (%)
      - Forzar el cierre de sesión de los usuarios

    - Del mismo modo, el campo **Off-peak hours** (Horas de poca actividad) funciona de la misma manera que el campo **Peak hours** (Horas punta):

      - Hora de inicio, que también es el final del período de descenso.
      - Algoritmo de equilibrio de carga. Se recomienda elegir el **equilibrio de carga en profundidad** para reducir gradualmente el número de hosts de sesión en función de las sesiones de cada máquina virtual.
      - Al igual que en las horas punta, aquí no se puede configurar el umbral de capacidad. En su lugar, se utilizará el valor especificado en **Ramp-down** (Descenso).

## <a name="assign-host-pools"></a>Asignación de grupos de hosts

Ahora que ha configurado el plan de escalado, es el momento de asignar el plan a los grupos de hosts. Seleccione la casilla situada junto a cada grupo de hosts que desee incluir. Si no desea habilitar la escalabilidad automática, anule la selección de todas las casillas. Siempre puede volver a esta configuración más adelante y cambiarla.

>[!NOTE]
>Al crear o actualizar un plan de escalado que ya está asignado a grupos de hosts, los cambios se aplicarán inmediatamente.

## <a name="add-tags"></a>Incorporación de etiquetas 

Después, deberá especificar las etiquetas. Las etiquetas son pares de nombre y valor que categorizan los recursos para la facturación consolidada. Puede aplicar la misma etiqueta a varios recursos y grupo de recursos. Para más información sobre el etiquetado de recursos, consulte [Uso de etiquetas para organizar los recursos de Azure y la jerarquía de administración](../azure-resource-manager/management/tag-resources.md).

>[!NOTE] 
>Si cambia la configuración de los recursos en otras pestañas después de crear etiquetas, las etiquetas se actualizarán automáticamente.

Cuando haya terminado, vaya a la pestaña **Review + create** (Revisar y crear) y seleccione **Create** (Crear) para implementar el grupo de hosts.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado el plan de escalado, puede hacer lo siguiente:

- [Asignación del plan de escalado a grupos de hosts nuevos y existentes](autoscale-new-existing-host-pool.md)
- [Habilitación de los diagnósticos para el plan de escalado](autoscale-diagnostics.md)
