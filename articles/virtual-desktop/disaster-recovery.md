---
title: Plan de recuperación ante desastres de Azure Virtual Desktop
description: Cree un plan de recuperación ante desastres para la implementación de Azure Virtual Desktop con el fin de proteger los datos.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 54202b49e2881d1d128136b876cbf88c2bc87400
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359753"
---
# <a name="azure-virtual-desktop-disaster-recovery"></a>Recuperación ante desastres de Azure Virtual Desktop

Para mantener la seguridad de los datos de su organización, es posible que tenga que adoptar una estrategia de continuidad empresarial y recuperación ante desastres (BCDR). Una estrategia de BCDR adecuada mantiene las aplicaciones y cargas de trabajo en ejecución durante las interrupciones del servicio o de Azure, sean planeadas o no.

Azure Virtual Desktop ofrece BCDR para que el servicio Azure Virtual Desktop conserve los metadatos de los clientes durante las interrupciones. Cuando se produce una interrupción en una región, los componentes de la infraestructura del servicio realizarán una conmutación por error en la ubicación secundaria y seguirán funcionando de forma habitual. Seguirá siendo posible acceder a los metadatos relacionados con el servicio, y los usuarios podrán conectarse a los hosts disponibles. Las conexiones de los usuarios finales permanecerán en línea siempre que los hosts o el entorno de los inquilinos sigan siendo accesibles.

Para asegurarse de que los usuarios puedan seguir conectándose durante una interrupción de la región, debe replicar sus máquinas virtuales (VM) en otra ubicación. Durante las interrupciones, el sitio primario realiza una conmutación por error en las máquinas virtuales replicadas en la ubicación secundaria. Los usuarios podrán seguir accediendo a las aplicaciones desde la ubicación secundaria sin interrupciones. Además de la replicación de las máquinas virtuales, tendrá que mantener las identidades de los usuarios accesibles en la ubicación secundaria. Si utiliza contenedores de perfiles, también tendrá que replicarlos. Por último, asegúrese de que las aplicaciones empresariales que se basan en los datos de la ubicación principal puedan realizar una conmutación por error con el resto de los datos.

En resumen, para mantener a los usuarios conectados durante una interrupción, deberá hacer lo siguiente en este orden:

- Replique las máquinas virtuales en una ubicación secundaria.
- Si utiliza contenedores de perfiles, configure la replicación de datos en la ubicación secundaria.
- Asegúrese de que las identidades de los usuarios que configuró en la ubicación principal estén disponibles en la ubicación secundaria.
- Asegúrese de que las aplicaciones de línea de negocio que se basan en los datos de la ubicación principal realicen una conmutación por error en la ubicación secundaria.

## <a name="vm-replication"></a>Replicación de máquina virtual

En primer lugar, debe replicar las máquinas virtuales en la ubicación secundaria. Las opciones para ello dependen de cómo estén configuradas las máquinas virtuales:

- Puede configurar todas las máquinas virtuales tanto para los grupos de hosts agrupados como los personales con Azure Site Recovery. Con este método, solo tendrá que configurar un grupo de hosts, y sus áreas de trabajo y grupos de aplicaciones relacionados.
- Puede crear un nuevo grupo de hosts en la región de la conmutación por error a la vez que mantiene desactivados todos los recursos de la ubicación de la conmutación por error. Con este método, debe configurar las nuevas áreas de trabajo y los grupos de aplicaciones en la región de la conmutación por error. Luego, puede usar un plan de Azure Site Recovery para activar los grupo de hosts.
- Puede crear un grupo de hosts que las máquinas virtuales compiladas en la región primaria y la de la conmutación por error rellenen mientras se mantienen desactivadas las máquinas virtuales de la región de la conmutación por error. En este caso, solo tendrá que configurar un grupo de hosts, y sus áreas de trabajo y grupos de aplicaciones relacionados. Puede usar un plan de Azure Site Recovery para activar grupos de hosts con este método.

Se recomienda usar [Azure Site Recovery](../site-recovery/site-recovery-overview.md) para administrar la replicación de máquinas virtuales en otras ubicaciones de Azure, tal y como se describe en [Arquitectura de recuperación ante desastres de Azure a Azure](../site-recovery/azure-to-azure-architecture.md). Se recomienda especialmente usar Azure Site Recovery para los grupos de hosts personales, ya que Azure Site Recovery admite [SKU que se basen en el servidor y en el cliente](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

Si usa Azure Site Recovery, no tendrá que registrar estas máquinas virtuales manualmente. El agente de Azure Virtual Desktop de la máquina virtual secundaria usará automáticamente el token de seguridad más reciente para conectarse a la instancia de servicio más cercana. La máquina virtual (host de sesión) de la ubicación secundaria se convierte automáticamente en parte del grupo de hosts. El usuario final tendrá que volver a conectarse durante el proceso, pero, aparte de eso, no hay ninguna otra operación manual.

Si hay conexiones de usuarios existentes durante la interrupción, para que el administrador pueda iniciar la conmutación por error en la región secundaria, habrá que finalizar las conexiones de los usuarios en la región actual.

Para desconectar usuarios de Azure Virtual Desktop (clásico), ejecute este cmdlet:

```powershell
Invoke-RdsUserSessionLogoff
```

Para desconectar usuarios de la versión de Azure Virtual Desktop integrada en Azure, ejecute este cmdlet:

```powershell
Remove-AzWvdUserSession
```

Cuando haya cerrado la sesión de todos los usuarios de la región primaria, podrá realizar la conmutación por error de las máquinas virtuales de la región primaria y permitir que los usuarios se conecten a las máquinas virtuales de la región secundaria. Para obtener más información sobre cómo funciona este proceso, consulte [Replicación de máquinas virtuales de Azure en otra región de Azure](../site-recovery/azure-to-azure-how-to-enable-replication.md).

## <a name="virtual-network"></a>Virtual network

A continuación, observe la conectividad de red durante la interrupción. Tendrá que asegurarse de haber configurado una red virtual (VNET) en la región secundaria. Si los usuarios necesitan tener acceso a recursos locales, deberá configurar esta red virtual para acceder a ellos. Puede establecer conexiones locales con una VPN, ExpressRoute o Virtual WAN.

Se recomienda usar Azure Site Recovery para configurar la red virtual en la región de la conmutación por error porque conserva la configuración de la red principal sin necesidad de emparejamiento.

## <a name="user-identities"></a>Identidades de usuarios

A continuación, asegúrese de que el controlador de dominio esté disponible en la ubicación secundaria. 

Hay tres formas de mantener el controlador de dominio disponible:

   - Tener el controlador de dominio de Active Directory en la ubicación secundaria
   - Usar un controlador de dominio de Active Directory local
   - Replicar el controlador de dominio de Active Directory con [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)

## <a name="user-and-app-data"></a>Datos de los usuarios y las aplicaciones

Si utiliza contenedores de perfiles, el paso siguiente consiste en configurar la replicación de datos en la ubicación secundaria. Tiene cinco opciones para almacenar perfiles de FSLogix:

   - Espacios de almacenamiento directo (S2D)
   - Unidades de red (máquinas virtuales con unidades adicionales)
   - Azure Files
   - Azure NetApp Files
   - Caché en la nube para la replicación

Para obtener más información, consulte [Opciones de almacenamiento para los contenedores de perfiles de FSLogix de Azure Virtual Desktop](store-fslogix-profile.md).

Si va a configurar la recuperación ante desastres para los perfiles, estas son las opciones:

   - Configuración de la replicación nativa de Azure (por ejemplo, replicación de la cuenta de almacenamiento de Azure Files estándar, replicación de Azure NetApp Files o Azure Files Sync para los servidores de archivos).
    
     >[!NOTE]
     >La replicación de NetApp es automática después de configurarla por primera vez. Con los planes de Azure Site Recovery, puede agregar scripts previos y posteriores para conmutar por error aquellos recursos que no sean de máquinas virtuales y que repliquen otros recursos de Azure Storage.

   - Configure la memoria caché en la nube de FSLogix para los datos de los usuarios y las aplicaciones.
   - Configure la recuperación ante desastres solo para los datos de las aplicaciones con el fin de garantizar el acceso a los datos críticos para la empresa en todo momento. Con este método, puede recuperar los datos de los usuarios una vez finalizada la interrupción.

Echemos un vistazo a cómo configurar FSLogix con el fin de establecer la recuperación ante desastres para cada opción.

### <a name="fslogix-configuration"></a>Configuración de FSLogix

El agente de FSLogix puede admitir varias ubicaciones de perfil si configura las entradas del registro para FSLogix.

Para configurar las entradas del registro:

1. Abra el **Editor del Registro**.
2. Vaya a **Equipo** > **HKEY_LOCAL_MACHINE** > **SOFTWARE** > **FSLogix** > **Profiles**.
   
     > [!div class="mx-imgBorder"]
     > ![Captura de pantalla de la ventana Profiles en el Editor del Registro. VHDLocation está seleccionado.](media/regedit-profiles.png)

3. Haga clic con el botón derecho en **VHDLocations** y seleccione **Modificar cadenas múltiples**.

     > [!div class="mx-imgBorder"]
     > ![Captura de pantalla de la ventana Modificar cadenas múltiples. En los datos de los valores se enumeran las ubicaciones Centro de EE. UU. y Este de EE. UU.](media/multi-string-edit.png)

4. En el campo **Información del valor:** , escriba las ubicaciones que quiera usar.
5. Cuando finalice, seleccione **Aceptar**.

Si la primera ubicación no está disponible, el agente de FSLogix realizará una conmutación por error automáticamente en la segunda, y así sucesivamente.

Se recomienda configurar el agente de FSLogix con una ruta de acceso a la ubicación secundaria en la región principal. Una vez que se cierre la ubicación principal, el agente de FLogix se replicará como parte de la replicación de Azure Site Recovery de la máquina virtual. Una vez que las máquinas virtuales replicadas estén listas, el agente intentará automáticamente ir a la ruta de acceso en la región secundaria.

Por ejemplo, supongamos que las máquinas virtuales del host de la sesión principal se encuentran en la región Centro de EE. UU., pero su contenedor de perfiles está en la región Oeste de EE. UU. por motivos de rendimiento.

En este caso, deberá configurar el agente de FSLogix con una ruta de acceso al almacenamiento en Centro de EE. UU. Luego, tendrá que configurar las máquinas virtuales del host de la sesión para que se repliquen en Oeste de EE. UU. Una vez que se produzca un error en la ruta de acceso a Centro de EE. UU., el agente intentará crear una nueva ruta de acceso al almacenamiento en Oeste de EE. UU.

### <a name="s2d"></a>S2D

Dado que S2D controla la replicación entre regiones internamente, no es necesario configurar la ruta de acceso secundaria de forma manual.

### <a name="network-drives-vm-with-extra-drives"></a>Unidades de red (máquinas virtuales con unidades adicionales)

Si replica las máquinas virtuales del almacenamiento de la red mediante Azure Site Recovery como máquinas virtuales del host de la sesión, la recuperación mantendrá la misma ruta de acceso, por lo que no será necesario volver a configurar FSLogix.

### <a name="azure-files"></a>Azure Files

Azure Files admite la replicación asincrónica entre regiones, que se puede especificar al crear la cuenta de almacenamiento. Si la naturaleza asincrónica de Azure Files ya cubre sus objetivos en materia de recuperación ante desastres, no es necesario que aplique ninguna configuración adicional.

Si necesita replicación sincrónica para minimizar la pérdida de datos, se recomienda usar la caché en la nube de FSLogix en su lugar.

>[!NOTE]
>En esta sección no se trata el mecanismo de autenticación de conmutación por error para Azure Files.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Obtenga más información sobre Azure NetApp Files en [Creación de un emparejamiento de replicación para Azure NetApp Files](../azure-netapp-files/cross-region-replication-create-peering.md).

## <a name="app-dependencies"></a>Dependencias de aplicaciones

Por último, asegúrese de que las aplicaciones empresariales que se basan en los datos ubicados en la región primaria puedan realizar una conmutación por error en la ubicación secundaria. Además, asegúrese de configurar los valores que las aplicaciones deban usar en la nueva ubicación. Por ejemplo, si una de las aplicaciones depende del back-end de SQL, asegúrese de replicar SQL en la ubicación secundaria. Debe configurar la aplicación para que use la ubicación secundaria como parte del proceso de conmutación por error o como su configuración predeterminada. Puede modelar las dependencias de la aplicación en planes de Azure Site Recovery. Para obtener más información, consulte [Acerca de los planes de recuperación](../site-recovery/recovery-plan-overview.md).

## <a name="disaster-recovery-testing"></a>Pruebas de recuperación ante desastres

Cuando haya terminado de configurar la recuperación ante desastres, conviene que pruebe el plan para asegurarse de que funcione.

Estas son algunas sugerencias sobre cómo probar el plan:

- Si las máquinas virtuales de prueba tienen acceso a Internet, se ocuparán de cualquier host de sesión existente para las nuevas conexiones, pero todas las conexiones existentes en el host de la sesión original permanecerán activas. Asegúrese de que el administrador que ejecute la prueba cierre la sesión de todos los usuarios activos antes de probar el plan. 
- Solo debe realizar pruebas de recuperación ante desastres completas durante una ventana de mantenimiento para no interrumpir a los usuarios. También puede usar un grupo de hosts en el entorno de validación de la prueba. 
- Asegúrese de que la prueba cubra todas las aplicaciones críticas para la empresa.
- Se recomienda que solo conmute por error un máximo de 100 máquinas virtuales a la vez. Si tiene más máquinas virtuales, le recomendamos que las conmute por error por lotes con una diferencia de 10 minutos.

## <a name="next-steps"></a>Pasos siguientes

Si, además de sobre cómo planear las interrupciones, tiene alguna pregunta sobre cómo mantener los datos seguros, consulte nuestra [guía de seguridad](security-guide.md).