---
title: Instalación e implementación del agente basado en C para Linux
description: Aprenda a instalar e implementar el agente de seguridad basado en C de Defender for IoT en Linux.
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 8317d91fe51c06b67529b1f7bfe8894cef153232
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018129"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Implementación del agente de seguridad basado en C de Defender for IoT en Linux

En esta guía se explica cómo instalar e implementar el agente de seguridad basado en C de Defender for IoT en Linux.

- Instalar
- Comprobación de la implementación
- Desinstalación del agente
- Solución de problemas

## <a name="prerequisites"></a>Prerrequisitos

Para obtener información sobre otras plataformas y tipos de agente, vea [Elegir el agente de seguridad correcto](how-to-deploy-agent.md).

1. Para implementar el agente de seguridad, se necesitan derechos de administrador local en el equipo de instalación (sudo).

1. [Cree un microagente de Defender para IoT](quickstart-create-security-twin.md) para el dispositivo.

## <a name="installation"></a>Instalación

Para instalar e implementar el agente de seguridad, use el siguiente flujo de trabajo:

1. Descargue en la máquina la última versión desde [GitHub](https://aka.ms/iot-security-github-c).

1. Extraiga el contenido del paquete y vaya a la carpeta _/src/installation_.

1. Agregue permisos de ejecución al **script de InstallSecurityAgent**, para lo que debe ejecutar el siguiente comando:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. A continuación, ejecute:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Para más información acerca de los parámetros de autenticación, consulte [Procedimiento para configurar la autenticación](concept-security-agent-authentication-methods.md).

Este script realiza la siguiente función:

1. Instala los requisitos previos.

1. Agrega un usuario de servicio (con el inicio de sesión interactivo deshabilitado).

1. Instala el agente como **daemon** (se da por hecho que el dispositivo usa **systemd** para la administración del servicio).

1. Configura al agente con los parámetros de autenticación proporcionados.

Para obtener ayuda adicional, ejecute el script con el parámetro – help:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Desinstalación del agente

Para desinstalar al agente, ejecute el script con el parámetro –-uninstall:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Solución de problemas

Compruebe el estado de implementación, para lo que debe ejecutar:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Pasos siguientes

- Lea la [información general](overview.md) del servicio Defender for IoT.
- Más información sobre Defender para IoT [Qué es una solución basada en agente para los generadores de dispositivos](architecture-agent-based.md)
- Habilite el [servicio](quickstart-onboard-iot-hub.md)
- Leer [Preguntas más frecuentes sobre el agente de Azure Defender para IoT](resources-agent-frequently-asked-questions.md)
- Obtenga información acerca de las [alertas de seguridad](concept-security-alerts.md)
