---
title: Conexión de máquinas híbridas a Azure desde Windows Admin Center
description: En este artículo, obtendrá información sobre cómo instalar el agente y conectar máquinas a Azure mediante servidores habilitados para Azure Arc desde Windows Admin Center.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: cc5e47c1f52f0a35048cc796015184d2a0d2dda7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788959"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>Conexión de máquinas híbridas a Azure desde Windows Admin Center

Puede habilitar servidores habilitados para Azure Arc para una o varias máquinas Windows en el entorno si realiza una serie de pasos manualmente. O bien puede usar [Windows Admin Center](/windows-server/manage/windows-admin-center/understand/what-is) para implementar el agente de Connected Machine y registrar los servidores locales sin tener que realizar ningún paso fuera de esta herramienta.

## <a name="prerequisites"></a>Requisitos previos

* Servidores habilitados para Azure Arc: revise los [requisitos previos](agent-overview.md#prerequisites) y compruebe que su suscripción, su cuenta de Azure y sus recursos cumplen con los requisitos.

* Windows Admin Center: revise los requisitos para [preparar el entorno](/windows-server/manage/windows-admin-center/deploy/prepare-environment) a fin de implementar y [configurar la integración de Azure](/windows-server/manage/windows-admin-center/azure/azure-integration).

* Suscripción a Azure. Si no tiene una, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

* Los servidores Windows de destino que quiere administrar deben tener conectividad de Internet para acceder a Azure.

### <a name="security"></a>Seguridad

Este método de implementación requiere que tenga derechos de administrador en la máquina o el servidor Windows de destino para instalar y configurar el agente. También debe ser miembro del rol [**Usuarios de puerta de enlace**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles).

## <a name="deploy"></a>Implementación

Siga estos pasos para configurar el servidor Windows con servidores habilitados para Azure Arc.

1. Inicie sesión en Windows Admin Center.

1. En la lista de servidores Windows conectados de la lista de conexiones de la página **Información general**, seleccione un servidor al cual conectarse.

1. En el panel de la izquierda, seleccione **Servicios híbridos de Azure**.

1. En la página **Servicios híbridos de Azure**, seleccione **Detección de servicios de Azure**.

1. En la página **Detección de servicios de Azure**, en **Leverage Azure policies and solutions to manage your servers with Azure Arc** (Aprovechar las directivas y soluciones de Azure para administrar servidores con Azure Arc), seleccione **Configurar**.

1. En la página **Configuración\Azure Arc para servidores**, autentíquese en Azure si se le solicita y, luego, seleccione **Comenzar**.

1. En la página **Conexión de un servidor a Azure**, brinde la información siguiente:

    1. En la lista desplegable **Suscripción de Azure**, seleccione la suscripción de Azure.
    1. En **Grupo de recursos**, seleccione **Nuevo** para crear un grupo de recursos o, en la lista desplegable **Grupo de recursos**, seleccione un grupo de recursos existente desde donde registrar y administrar la máquina.
    1. En la lista desplegable **Región**, seleccione la región de Azure en la que se almacenarán los metadatos de los servidores.
    1. Si la máquina o el servidor se comunica a través de un servidor proxy para conectarse a Internet, seleccione la opción **Usar servidor proxy**. Con esta configuración, el agente se comunica mediante el servidor proxy mediante el protocolo HTTP. Especifique la dirección IP del servidor proxy o el nombre y el número de puerto que usará la máquina para comunicarse con el servidor proxy.

1. Seleccione **Configurar** para continuar con la configuración del servidor Windows con servidores habilitados para Azure Arc.

El servidor Windows se conectará a Azure, descargará el agente de Connected Machine, lo instalará y registrará con servidores habilitados para Azure Arc. Para llevar el seguimiento del progreso, seleccione **Notificaciones** en el menú.

Para confirmar la instalación del agente de Connected Machine, en Windows Admin Center, seleccione [**Eventos**](/windows-server/manage/windows-admin-center/use/manage-servers#events) en el panel de la izquierda para revisar los eventos de *MsiInstaller* en el registro de eventos de la aplicación.

## <a name="verify-the-connection-with-azure-arc"></a>Comprobación de la conexión con Azure Arc

Después de instalar el agente y configurarlo para que se conecte a los servidores habilitados para Azure Arc, vaya a Azure Portal a fin de comprobar que el servidor se ha conectado correctamente. Vea la máquina en [Azure Portal](https://portal.azure.com).

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Conexión de una máquina realizada correctamente" border="false":::

## <a name="next-steps"></a>Pasos siguientes

* Puede encontrar información sobre la solución de problemas en la guía [Solución de problemas de conexión del agente de Connected Machine](troubleshoot-agent-onboard.md).

* Examine la [guía de planeamiento e implementación](plan-at-scale-deployment.md) para planear la implementación de servidores habilitados para Azure Arc a cualquier escala e implementar la administración y supervisión centralizadas.

* Aprenda a administrar la máquina con [Azure Policy](../../governance/policy/overview.md) para tareas como la [configuración de invitado](../../governance/policy/concepts/guest-configuration.md) de la máquina virtual, la comprobación de que la máquina informa al área de trabajo de Log Analytics esperada, la habilitación de la supervisión con [Información de máquinas virtuales](../../azure-monitor/vm/vminsights-enable-policy.md) y mucho más.
