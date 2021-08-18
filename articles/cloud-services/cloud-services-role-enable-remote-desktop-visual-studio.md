---
title: Uso de Visual Studio para habilitar Escritorio remoto para un rol (Azure Cloud Services clásico)
description: Configuración de la aplicación de servicios en la nube de Azure para permitir conexiones a Escritorio remoto
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 103841fdafe082d1a6ca609b2c52456ab5816af8
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113092462"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-visual-studio"></a>Habilitación de la conexión a Escritorio remoto para un rol de Azure Cloud Services (clásico) mediante Visual Studio

> [!IMPORTANT]
> [Azure Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md) es un nuevo modelo de implementación basado en Azure Resource Manager para el producto Azure Cloud Services. Con este cambio, se ha modificado el nombre del modelo de implementación basado en Azure Cloud Services para Azure Service Manager a Cloud Services (clásico), y todas las implementaciones nuevas deben usar [Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Escritorio remoto le permite tener acceso al escritorio de un rol que se ejecuta en Azure. Puede usar la conexión de Escritorio remoto para solucionar y diagnosticar problemas con su aplicación mientras se ejecuta.

El asistente para publicación que Visual Studio proporciona para los servicios en la nube incluye una opción para habilitar Escritorio remoto durante el proceso de publicación, con las credenciales que se hayan proporcionado. El uso de esta opción es adecuado cuando se utiliza Visual Studio 2017 versión 15.4 y versiones anteriores.

Sin embargo, con Visual Studio 2017 versión 15.5 y versiones posteriores, se recomienda que evite habilitar Escritorio remoto mediante el asistente para publicación a menos que trabaje como desarrollador individual. Para cualquier situación en la que el proyecto se pueda abrir por otros desarrolladores, habilite Remote Desktop mediante Azure Portal, con PowerShell, o desde una canalización de versión en un flujo de trabajo de implementación continua. Esta recomendación se debe a un cambio en la forma en que Visual Studio se comunica con Escritorio remoto en la máquina virtual del servicio en la nube, tal como se explica en este artículo.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Configuración de Escritorio remoto mediante Visual Studio 2017 versión 15.4 y versiones anteriores

Cuando se utiliza Visual Studio 2017 versión 15.4 y anteriores, puede usar la opción **Habilitar Escritorio remoto para todos los roles** en el asistente para publicación. Puede utilizar el asistente con Visual Studio 2017 versión 15.5 y versiones posteriores, pero no utilice la opción de Escritorio remoto.

1. En Visual Studio, para iniciar el asistente para publicación, haga clic con el botón derecho en el proyecto de servicio en la nube en el Explorador de soluciones y elija **Publicar**.

2. Si es necesario, inicie sesión en su suscripción de Azure y seleccione **Siguiente**.

3. En la página **Configuración**, seleccione **Habilitar Escritorio remoto para todos los roles** y el vínculo **Configuración...** para abrir el cuadro de diálogo **Configuración de Escritorio remoto**.

4. En la parte inferior del cuadro de diálogo, seleccione **Más opciones**. Este comando muestra una lista desplegable en la que puede crear o seleccionar un certificado para que pueda cifrar la información de credenciales al conectarse a través de escritorio remoto.

   > [!Note]
   > Los certificados que necesita para una conexión de escritorio remoto son distintos de los certificados que se usan para otras operaciones de Azure. El certificado de acceso remoto tiene que tener una clave privada.

5. Seleccione un certificado de la lista o elija **&lt;Crear...&gt;** . Si crea un certificado, proporciónele un nombre descriptivo cuando se le solicite y seleccione **Aceptar**. El nuevo certificado aparece en el cuadro de lista desplegable.

6. Proporcione un nombre de usuario y una contraseña. No se puede usar una cuenta existente. No utilice Administrador como el nombre de usuario de la nueva cuenta.

7. Elija una fecha en la que caducará la cuenta y pasada la cual se bloquearán las conexiones a Escritorio remoto.

8. Una vez que haya dado toda la información necesaria, seleccione **Aceptar**. Visual Studio agrega la configuración de Escritorio remoto a los archivos `.cscfg` y `.csdef` del proyecto, incluida la contraseña cifrada mediante el certificado seleccionado.

9. Complete los pasos restantes con el botón **Siguiente** y, después, seleccione **Publicar** cuando esté listo para publicar el servicio en la nube. Si todavía no está listo para publicar, seleccione **Cancelar** y responda **Sí** cuando se le solicite guardar los cambios. Puede publicar el servicio en la nube más adelante con esta configuración.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Configuración de Escritorio remoto con Visual Studio 2017 versión 15.5 y versiones posteriores

Con Visual Studio 2017 versión 15.5 y versiones posteriores, aún puede usar el asistente para publicación con un proyecto de servicio en la nube. También puede usar la opción **Habilitar Escritorio remoto para todos los roles** si trabaja solo como programador único.

Si trabaja como parte de un equipo, en su lugar debe habilitar el escritorio remoto en el servicio en la nube de Azure mediante [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) o [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Esta recomendación se debe a un cambio en la forma en que Visual Studio 2017 versión 15.5 y versiones posteriores se comunica con la máquina virtual de servicios en la nube. Cuando se habilita Escritorio remoto mediante el asistente para publicación, las versiones anteriores de Visual Studio se comunican con la máquina virtual mediante lo que se denomina "complemento RDP". En cambio, Visual Studio 2017 versión 15.5 y versiones posteriores se comunica mediante la "extensión RDP", más segura y flexible. Este cambio también se alinea con el hecho de que los métodos de Azure Portal y PowerShell para habilitar Escritorio remoto también utilizan la extensión RDP.

Cuando Visual Studio se comunica con la extensión RDP, transmite una contraseña de texto sin formato mediante TLS. Sin embargo, los archivos de configuración del proyecto solo almacenan una contraseña cifrada, que únicamente se puede descifrar en texto sin formato con el certificado local que se utilizó originalmente para cifrarla.

Si implementa el proyecto del servicio en la nube en el mismo equipo de desarrollo cada vez, ese certificado local estará disponible. En este caso, todavía puede usar la opción **Habilitar Escritorio remoto para todos los roles** en el asistente para publicación.

Sin embargo, si usted u otros desarrolladores desean implementar el proyecto de servicio en la nube desde equipos diferentes, estos equipos no tendrán el certificado necesario para descifrar la contraseña. Como resultado, verá el siguiente mensaje de error:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Podría cambiar la contraseña cada vez que implemente el servicio en la nube, pero esa acción se convierte en un inconveniente para todos los que necesiten usar Escritorio remoto.

Si comparte el proyecto con un equipo, lo mejor es desactivar la opción del asistente para publicación y habilitar Escritorio remoto directamente mediante [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) o de [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Implementación desde un servidor de compilación con Visual Studio 2017 versión 15.5 y versiones posteriores

Puede implementar un proyecto de servicio en la nube desde un servidor de compilación (por ejemplo, con Azure DevOps Services) en el que Visual Studio 2017 versión 15.5 o una versión posterior está instalado en el agente de compilación. Con este acuerdo, la implementación se realiza desde el mismo equipo en el que está disponible el certificado de cifrado.

Para utilizar la extensión RDP desde Azure DevOps Services, incluya los siguientes detalles en la canalización de compilación:

1. Incluya `/p:ForceRDPExtensionOverPlugin=true` en los argumentos de MSBuild para asegurarse de que la implementación funciona con la extensión RDP en lugar de con el complemento RDP. Por ejemplo:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Después de los pasos de compilación, agregue el paso **Implementación de servicio en la nube de Azure** y defina sus propiedades.

1. Después del paso de implementación, agregue un paso de **Azure Powershell**, establezca la propiedad **Nombre para mostrar** en "Implementación de Azure: habilitar extensión RDP" (u otro nombre adecuado) y seleccione la suscripción adecuada de Azure.

1. Establezca **Tipo de script** en "Alineado" y pegue el código siguiente en el campo **Script en línea**. (También puede crear un archivo `.ps1` en el proyecto con este script, establecer **Tipo de script** en "Ruta de acceso de archivo de script" y establecer **Ruta de acceso del script** para que apunte al archivo).

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Conexión a un rol de Azure mediante Escritorio remoto

Después de publicar el servicio en la nube en Azure y que haya habilitado Escritorio remoto, puede utilizar el Explorador de servidores de Visual Studio para iniciar sesión en la máquina virtual de servicio en la nube:

1. En el Explorador de servidores, expanda el nodo **Azure** y luego expanda el nodo para un servicio en la nube y uno de sus roles para mostrar una lista de instancias.

2. Haga clic con el botón derecho en un nodo de la instancia y seleccione **Conectar con Escritorio remoto**.

3. Escriba el nombre de usuario y la contraseña que ha creado anteriormente. Ahora está registrado en la sesión remota.

## <a name="additional-resources"></a>Recursos adicionales

[Configuración de Cloud Services](cloud-services-how-to-configure-portal.md)
