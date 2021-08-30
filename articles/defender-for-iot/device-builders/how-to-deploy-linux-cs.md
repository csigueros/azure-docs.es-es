---
title: Instalación e implementación del agente basado en C# para Linux
description: Aprenda a instalar e implementar el agente de seguridad basado en C# de Defender para IoT en Linux.
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 8dd6119d4f1a6e8a738d34521234d2691ba8c883
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018121"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Implementación del agente de seguridad basado en C# de Defender para IoT en Linux

En esta guía se explica cómo instalar e implementar el agente de seguridad basado en C# de Defender para IoT en Linux.

En esta guía, aprenderá a:

- Instalar
- Comprobación de la implementación
- Desinstalación del agente
- Solución de problemas

## <a name="prerequisites"></a>Prerrequisitos

Para obtener información sobre otras plataformas y tipos de agente, vea [Elegir el agente de seguridad correcto](how-to-deploy-agent.md).

1. Para implementar el agente de seguridad, se necesitan derechos de administrador local en el equipo en el que se desee instalar.

1. [Cree un microagente de Defender para IoT](quickstart-create-security-twin.md) para el dispositivo.

## <a name="installation"></a>Instalación

Para implementar el agente de seguridad, realice los pasos siguientes:

1. Descargue en la máquina la última versión desde [GitHub](https://aka.ms/iot-security-github-cs).

1. Extraiga el contenido del paquete y vaya a la carpeta _/Install_.

1. Agregue permisos de ejecución al **script de InstallSecurityAgent**, para lo que debe ejecutar `chmod +x InstallSecurityAgent.sh`

1. A continuación, ejecute el siguiente comando con **privilegios raíz**:

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   Para más información sobre los parámetros de autenticación, vea [Procedimiento para configurar la autenticación](concept-security-agent-authentication-methods.md).

Este script realiza las acciones siguientes:

- Instala los requisitos previos.

- Agrega un usuario de servicio (con el inicio de sesión interactivo deshabilitado).

- Instala el agente como **demonio**: se da por hecho que el dispositivo usa **systemd** para el modelo de implementación clásica.

- Configura **sudoers** para permitir que el agente realice ciertas tareas como raíz.

- Configura al agente con los parámetros de autenticación proporcionados.

Para obtener ayuda adicional, ejecute el script con el parámetro – help: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Desinstalación del agente

Para desinstalar al agente, ejecute el script con el parámetro – u: `./InstallSecurityAgent.sh -u`.

> [!NOTE]
> La desinstalación no quita los requisitos previos que falten que se hayan instalado durante la instalación.

## <a name="troubleshooting"></a>Solución de problemas

1. Compruebe el estado de implementación, para lo que debe ejecutar:

    `systemctl status ASCIoTAgent.service`

1. Habilite el registro.
   Si el agente no se inicia el agente, active el registro para más información.

   Para activar el registro:

   1. Abra el archivo de configuración para poder editarlo en cualquier editor de Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Edite los valores siguientes:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       El valor **logFilePath** se puede configurar.

       > [!NOTE]
       > Se recomienda **desactivar** el registro una vez que se complete la solución de problemas, ya que si se deja **activado** aumenta el tamaño del archivo de registro y el uso de datos.

   1. Para reiniciar el agente, ejecute:

       `systemctl restart ASCIoTAgent.service`

   1. Vea el archivo de registro para más información acerca del error.

       La ubicación del archivo de registro es: `/var/ASCIoTAgent/IotAgentLog.log`

       Cambie la ruta de acceso de la ubicación del archivo en función del nombre que eligió para el **logFilePath** en el paso 2.

## <a name="next-steps"></a>Pasos siguientes

- Lea la [información general](overview.md) del servicio Defender for IoT.
- Más información sobre Defender para IoT [Qué es una solución basada en agente para los generadores de dispositivos](architecture-agent-based.md)
- Habilite el [servicio](quickstart-onboard-iot-hub.md)
- Leer [Preguntas más frecuentes sobre el agente de Azure Defender para IoT](resources-agent-frequently-asked-questions.md)
- Obtenga información acerca de las [alertas](concept-security-alerts.md)
