---
title: 'Migración automática desde Azure Virtual Desktop (clásico) (versión preliminar): Azure'
description: Migración automática desde Azure Virtual Desktop (clásico) a Azure Virtual Desktop mediante el módulo de migración.
author: Heidilohr
ms.topic: how-to
ms.date: 09/15/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: ec3ba6a71ea1430dc060d431776a31bc6c5e1b81
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128547511"
---
# <a name="migrate-automatically-from-azure-virtual-desktop-classic-preview"></a>Migración automática desde Azure Virtual Desktop (clásico) (versión preliminar)

> [!IMPORTANT]
> La herramienta del módulo de migración para Azure Virtual Desktop se encuentra actualmente en versión preliminar pública.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

La herramienta del módulo de migración (versión preliminar) permite migrar la organización desde Azure Virtual Desktop (clásico) a Azure Virtual Desktop automáticamente. Este artículo le mostrará cómo utilizar la herramienta. 

## <a name="requirements"></a>Requisitos

Antes de usar el módulo de migración, asegúrese de que tiene listos los siguientes elementos:

- Una suscripción de Azure en la que creará nuevos objetos de servicios de Azure.

- Debe tener asignado el rol Colaborador para crear objetos de Azure en la suscripción y el rol Administrador de acceso de usuario para asignar usuarios a grupos de aplicaciones.

- Al menos permisos de colaborador de Servicios de Escritorio remoto (RDS) en un inquilino de RDS o en los grupos de hosts específicos que va a migrar.

- La versión más reciente del módulo de PowerShell Microsoft.RdInfra.RDPowershell 

- La versión más reciente del módulo de PowerShell Az.DesktopVirtualization 

- La versión más reciente del módulo de PowerShell Az.Resources 

- Instalación del módulo de migración en el equipo

- PowerShell o PowerShell ISE para ejecutar los scripts que verá en este artículo. El módulo Microsoft.RdInfra.RDPowershell no funciona en PowerShell Core.

>[!IMPORTANT]
>La migración solo crea objetos de servicio en la ubicación geográfica de EE. UU. Si intenta migrar los objetos de servicio a otra ubicación geográfica, no funcionará. Además, si tiene más de 200 grupos de aplicaciones en la implementación de Azure Virtual Desktop (clásico), no podrá realizar la migración. Solo podrá realizar la migración si vuelve a crear el entorno para reducir el número de grupos de aplicaciones dentro del inquilino de Azure Active Directory (Azure AD).

## <a name="prepare-your-powershell-environment"></a>Preparar el entorno de PowerShell

En primer lugar, deberá preparar el entorno de PowerShell para el proceso de migración.

Para preparar el entorno de PowerShell:

1. Antes de empezar, asegúrese de que tiene la versión más reciente de los módulos Az.DesktopVirtualization y Az.Resources mediante la ejecución de los siguientes cmdlets:

    ```powershell
    Get-Module Az.Resources
    Get-Module Az.DesktopVirtualization
    https://www.powershellgallery.com/packages/Az.DesktopVirtualization/
    https://www.powershellgallery.com/packages/Az.Resources/
    ```

    Si no los tiene, instale e importe los módulos mediante la ejecución de estos cmdlets:

    ```powershell
    Install-module Az.Resources
    Import-module Az.Resources
    Install-module Az.DesktopVirtualization
    Import-module Az.DesktopVirtualization
    ```

2. A continuación, desinstale el módulo de PowerShell RDInfra actual mediante la ejecución de este cmdlet:

    ```powershell
    Uninstall-Module -Name Microsoft.RDInfra.RDPowershell -AllVersions
    ```

3. Después, instale el módulo RDPowershell con este cmdlet:

    ```powershell
    Install-Module -Name Microsoft.RDInfra.RDPowershell -RequiredVersion 1.0.3414.0 -force
    Import-module Microsoft.RDInfra.RDPowershell
    ```

4. Cuando haya terminado de instalar todo, ejecute este cmdlet para asegurarse de que tiene las versiones correctas de los módulos:

    ```powershell
    Get-Module Microsoft.RDInfra.RDPowershell
    ```

5. Ahora, vamos a instalar e importar el módulo de migración mediante la ejecución de estos cmdlets:

    ```powershell
    Install-Module -Name PackageManagement -Repository PSGallery -Force
    Install-Module -Name PowerShellGet -Repository PSGallery -Force
    # Then restart shell
    Install-Module -Name Microsoft.RdInfra.RDPowershell.Migration -RequiredVersion 1.0.3725-Prerelease -AllowPrerelease -AllowClobber
    Import-Module <Full path to the location of the migration module>\Microsoft.RdInfra.RDPowershell.Migration.psd1
    ```

6. Cuando haya terminado, inicie sesión en Windows Virtual Desktop (clásico) en la ventana de PowerShell:

    ```powershell
    Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
    ```

7. Iniciar sesión en Azure Resource Manager:

    ```powershell
    Login-AzAccount
    ```

8. Si tiene varias suscripciones, seleccione aquella a la que desea migrar los recursos con este cmdlet:

    ```powershell
    Select-AzSubscription -Subscriptionid <subID>
    ```

9. Registre el proveedor de recursos en Azure Portal para la suscripción seleccionada.

10. Por último, deberá registrar el proveedor. Puede hacer esto de dos formas:
    - Si desea usar PowerShell, ejecute este cmdlet:
       
       ```powershell
       Register-AzResourceProvider -ProviderNamespace Microsoft.DesktopVirtualization
       ```
    
    - Si prefiere usar Azure Portal, abra e inicie sesión en Azure Portal, vaya a **Suscripciones** y seleccione el nombre de la suscripción que desea usar. Después, vaya a **Proveedor de recursos** > **Microsoft.DesktopVirtualization** y seleccione **Volver a registrar**. Todavía no verá ningún cambio en la interfaz de usuario, pero el entorno de PowerShell debería estar listo para ejecutar el módulo.

## <a name="migrate-azure-virtual-desktop-classic-resources-to-azure-resource-manager"></a>Migración de recursos de Azure Virtual Desktop (clásico) a Azure Resource Manager

Ahora que el entorno de PowerShell está listo, puede comenzar el proceso de migración.

Para migrar los recursos de Azure Virtual Desktop (clásico) a Azure Resource Manager:

1. Antes de la migración, si desea comprender cómo se asignarán los recursos clásicos existentes a los nuevos recursos de Azure Resource Manager, ejecute este cmdlet:
    
    ```powershell
    Get-RdsHostPoolMigrationMapping
    ```

    Con **Get-RdsHostPoolMigrationMapping**, puede crear un archivo .csv que asigna dónde irán los recursos. Por ejemplo, si el nombre del inquilino es "Contoso" y desea almacenar el archivo de asignación en el archivo "contosouser", ejecutaría un cmdlet similar al siguiente:

    ```powershell
    Get-RdsHostPoolMigrationMapping -Tenant Contoso -HostPool Office -Location EastUS -OutputFile 'C:\\Users\contosouser\OneDrive - Microsoft\Desktop\mapping.csv'
    ```

2. A continuación, ejecute el cmdlet **Start-RdsHostPoolMigration** para elegir si quiere migrar un único grupo de hosts o todos los grupos de hosts de un inquilino.

    Por ejemplo:

    ```powershell
    Start-RdsHostPoolMigration -Tenant Contoso -Location WestUS
    ```

    Si desea migrar los recursos a un grupo de hosts específico, incluya el nombre del grupo de hosts. Por ejemplo, si desea mover el grupo de hosts llamado "Office", ejecute un comando como este:

    ```powershell
    Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments $false -Location EastUS
    ```

    Si no asigna un nombre de área de trabajo, el módulo creará automáticamente uno en función del nombre del inquilino. Sin embargo, si prefiere usar un área de trabajo específica, puede escribir su identificador de recurso de la siguiente forma:

    ```powershell
    Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments -Location EastUS -Workspace <Resource ID of workspacename>
    ```
    
    Si desea usar un área de trabajo específica, pero no conoce su identificador de recurso, ejecute este cmdlet:

    ```powershell
    Get-AzWvdWorkspace -WorkspaceName <workspace> -ResourceGroupName <resource group> |fl
    ```

  También deberá especificar un modo de asignación de usuarios para las asignaciones de usuarios existentes:

  - Use **Copy** para copiar todas las asignaciones de usuarios de los grupos de aplicaciones antiguos a los grupos de aplicaciones de Azure Resource Manager. Los usuarios podrán ver fuentes de ambas versiones de sus clientes.
  - Use **None** si no desea cambiar las asignaciones de usuarios. Más adelante, puede asignar usuarios o grupos de usuarios a grupos de aplicaciones con Azure Portal, PowerShell o la API. Los usuarios solo podrán ver fuentes mediante los clientes de Azure Virtual Desktop (clásico).

  Solo puede copiar 2000 asignaciones de usuarios por suscripción, por lo que el límite dependerá de cuántas asignaciones existan ya en la suscripción. El módulo calcula el límite en función del número de asignaciones que ya tiene. Si no tiene suficientes asignaciones para copiar, verá un mensaje de error que indica "Cuota de asignación de roles insuficiente para copiar las asignaciones de usuarios. Vuelva a ejecutar el comando sin el modificador -CopyUserAssignments para migrar".

3. Una vez ejecutados los comandos, el módulo puede tardar hasta 15 minutos en crear los objetos de servicio. Si ha copiado o movido asignaciones de usuarios, se agregará al tiempo que el módulo tarda en terminar de configurar todo.

   Una vez que haya finalizado el cmdlet **Start-RdsHostPoolMigration**, debería ver lo siguiente:

      - Los objetos de servicios de Azure para el inquilino o grupo de hosts que especificó

      - Dos nuevos grupos de recursos:

         - Un grupo de recursos llamado "Tenantname", que contiene el área de trabajo.

         - Un grupo de recursos llamado "Tenantname_originalHostPoolName", que contiene el grupo de hosts y los grupos de aplicaciones de escritorio.

      - Cualquier usuario que haya publicado en los grupos de aplicaciones recién creados.

      - Las máquinas virtuales estarán disponibles en los grupos de hosts nuevos y existentes para evitar tiempos de inactividad del usuario durante el proceso de migración. Esto permite a los usuarios conectarse a la misma sesión de usuario.

   Dado que estos nuevos objetos de servicio de Azure son objetos de Azure Resource Manager, el módulo no puede establecer permisos del control de acceso basado en rol (RBAC) ni configuraciones de diagnóstico en ellos. Por lo tanto, deberá actualizar manualmente los permisos y la configuración de RBAC para estos objetos.

   Una vez que el módulo valida las conexiones de usuario iniciales, también puede publicar el grupo de aplicaciones para más usuarios o grupos de usuarios, si lo desea.

   >[!NOTE]
   >Después de la migración, si mueve grupos de aplicaciones a otro grupo de recursos después de asignar permisos a los usuarios, se quitarán todos los roles de RBAC. Tendrá que volver a asignar los permisos de RBAC de los usuarios.

4. Si desea eliminar todos los objetos de servicio de Azure Virtual Desktop (clásico), ejecute **Complete-RdsHostPoolMigration** para finalizar el proceso de migración. Este cmdlet eliminará todos los objetos de Azure Virtual Desktop (clásico), dejando solo los nuevos objetos de Azure. Los usuarios solo podrán ver la fuente de los grupos de aplicaciones recién creados en sus clientes. Una vez finalizado este comando, puede eliminar de forma segura el inquilino de Azure Virtual Desktop (clásico) para finalizar el proceso.

   Por ejemplo:

   ```powershell
   Complete-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   Si desea completar un grupo de hosts específico, puede incluir el nombre del grupo de hosts en el cmdlet. Por ejemplo, si desea completar un grupo de hosts llamado "Office", usaría un comando como este:

    ```powershell
    Complete-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
    ```

    Esto eliminará todos los objetos de servicio creados por Azure Virtual Desktop (clásico). Solo quedarán los nuevos objetos de Azure y los usuarios solo podrán ver la fuente de los grupos de aplicaciones recién creados en sus clientes. Una vez que haya terminado de finalizar la migración, debe eliminar explícitamente el inquilino en Azure Virtual Desktop (clásico).

5. Si ha cambiado de opinión sobre la migración y desea revertir el proceso, ejecute el cmdlet **Revert-RdsHostPoolMigration**.
    
   Por ejemplo:

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   Si desea revertir un grupo de hosts específico, puede incluir el nombre del grupo de hosts en el comando. Por ejemplo, si desea revertir un grupo de hosts llamado "Office", escriba algo parecido a esto:

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
   ```

   Este cmdlet eliminará todos los objetos de servicio de Azure recién creados. Los usuarios solo verán la fuente de los objetos de Azure Virtual Desktop (clásico) en sus clientes.

   Sin embargo, el cmdlet no eliminará el área de trabajo que creó el módulo ni su grupo de recursos asociado. Deberá eliminar manualmente esos elementos para deshacerse de ellos.

6. Si aún no desea eliminar los objetos de servicio de Azure Virtual Desktop (clásico), pero quiere probar la migración, puede ejecutar **Set-RdsHostPoolHidden**.

    Por ejemplo:

    ```powershell
    Set-RdsHostPoolHidden -Tenant Contoso -Hostpool Office -Hidden $true -Location WestUS
    ```

    Al establecer el estado en "true", se ocultarán los recursos de Azure Virtual Desktop (clásico). Si se establece en "false", se mostrarán los recursos a los usuarios.

    El parámetro *-Hostpool* es opcional. Puede usar este parámetro si hay un grupo de hosts de Azure Virtual Desktop (clásico) específico que desea ocultar.

    Este cmdlet ocultará la fuente de usuario y los objetos de servicio de Azure Virtual Desktop (clásico) en lugar de eliminarlos. Sin embargo, esto normalmente solo se usa para las pruebas y no cuenta como una migración completada. Para completar la migración, deberá ejecutar el comando **Complete-RdsHostPoolMigration**. De lo contrario, revierta la implementación mediante la ejecución de **Revert-RdsHostPoolMigration**.

## <a name="troubleshoot-automatic-migration"></a>Solución de problemas de la migración automática

En esta sección, se explica cómo resolver los problemas que se encuentran habitualmente en el módulo de migración.

### <a name="i-cant-access-the-tenant"></a>No puedo acceder al inquilino

En primer lugar, pruebe estas dos cosas:

- Asegúrese de que la cuenta de administrador tenga los permisos necesarios para acceder al inquilino.
- Pruebe a ejecutar **Get-RdsTenant** en el inquilino.

Si estas dos cosas funcionan, pruebe a ejecutar el cmdlet **Set-RdsMigrationContext** para establecer el contexto de RDS y el contexto de ADAL para la migración:

1. Cree el contexto de RDS mediante la ejecución del cmdlet **Add-RdsAccount**.       

2. Busque el contexto de RDS en la variable global *$rdMgmtContext*.         

3. Busque el contexto de ADAL en la variable global *$AdalContext*.

4. Ejecute **Set-RdsMigrationContext** con las variables que ha encontrado con este formato:

   ```powershell
   Set-RdsMigrationContext -RdsContext <rdscontext> -AdalContext <adalcontext>
   ```

## <a name="next-steps"></a>Pasos siguientes

Si quiere obtener información sobre cómo migrar la implementación manualmente, consulte [Migración manual desde Azure Virtual Desktop (clásico)](manual-migration.md).

Una vez que haya migrado, descubra cómo funciona Azure Virtual Desktop consultando [nuestros tutoriales](create-host-pools-azure-marketplace.md). Obtenga información sobre funcionalidades de administración avanzadas en [Expansión de un grupo de hosts existente](expand-existing-host-pool.md) y [Personalización de propiedades de RDP](customize-rdp-properties.md).

Para más información sobre los objetos de servicio, consulte [Entorno de Azure Virtual Desktop](environment-setup.md).