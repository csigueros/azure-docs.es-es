---
title: Instalación del agente de C# en dispositivos de Windows
description: Aprenda a instalar el agente de Defender for IoT en dispositivos Windows de 32 o 64 bits.
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 1a9bab1f9e24fba6a2b04b6d0bb5d9f2560dcc1d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643980"
---
# <a name="deploy-a-defender-for-iot-c-based-security-agent-for-windows"></a>Implementación de un agente de seguridad basado en C# de Defender para IoT en Windows

En esta guía se explica cómo instalar el agente de seguridad basado en C# de Defender for IoT en Windows.

En esta guía, aprenderá a:

- Instalar
- Comprobación de la implementación
- Desinstalación del agente
- Solución de problemas

## <a name="prerequisites"></a>Prerrequisitos

Para obtener información sobre otras plataformas y tipos de agente, vea [Elegir el agente de seguridad correcto](how-to-deploy-agent.md).

1. Derechos de administrador local en el equipo de instalación.

1. [Cree un microagente de Defender para IoT](quickstart-create-security-twin.md) para el dispositivo.

## <a name="installation"></a>Instalación

Para instalar al agente de seguridad, use el siguiente flujo de trabajo:

1. Instale el agente basado en C# de Defender for IoT para Windows en el dispositivo. Descargue la última versión en la máquina desde el [repositorio de GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS) de Defender for IoT.

1. Extraiga el contenido del paquete y vaya a la carpeta /Install.

1. Abra Windows PowerShell como administrador.
1. Agregue permisos de ejecución al script de InstallSecurityAgent, para lo que debe ejecutar:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    A continuación, ejecute:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Por ejemplo:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    Para más información sobre los parámetros de autenticación, vea [Procedimiento para configurar la autenticación](concept-security-agent-authentication-methods.md).

El script hace las siguientes acciones:

* Instala los requisitos previos.
* Agrega un usuario de servicio (con el inicio de sesión interactivo deshabilitado).
* Instala el agente como un **servicio del sistema**.
* Configura al agente con los parámetros de autenticación proporcionados.

Si necesita ayuda adicional, use el comando Get-Help de PowerShell.

Ejemplo de Get-Help: ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Comprobación del estado de la implementación

Compruebe el estado de la implementación del agente, para lo que debe ejecutar:

`sc.exe query "ASC IoT Agent"`

### <a name="uninstall-the-agent"></a>Desinstalación del agente

Para desinstalar el agente:

1. Ejecute el siguiente script de PowerShell con el parámetro **-mode** parámetro establecido en **Uninstall**.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Solución de problemas

Si el agente no se inicia, active el registro (el registro está *desactivado* de forma predeterminada) para más información.

Para activar el registro:

1. Abra el archivo de configuración (General.config) de la edición mediante un editor de archivos estándar.

1. Edite los valores siguientes:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Se recomienda **desactivar** el registro una vez que se complete la solución de problemas, ya que si se deja **activado** aumenta el tamaño del archivo de registro y el uso de datos.

1. Reinicie el agente, para lo que debe ejecutar lo siguiente en PowerShell o en la línea de comandos:

    **PowerShell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   or

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. Examine el archivo de registro para más información acerca del error. El archivo de registro se encuentra en el directorio de trabajo en el que se ejecuta el script. 

   Ubicación del archivo de registro: `.\IoTAgentLog.log`

## <a name="next-steps"></a>Pasos siguientes

* Lea la [información general](overview.md) del servicio Defender for IoT.
* Más información sobre Defender para IoT [Qué es una solución basada en agente para los generadores de dispositivos](architecture-agent-based.md)
* Habilite el [servicio](quickstart-onboard-iot-hub.md)
* Leer [Preguntas más frecuentes sobre el agente de Azure Defender para IoT](resources-agent-frequently-asked-questions.md)
* Obtenga información acerca de las [alertas](concept-security-alerts.md)
