---
title: Conexión de una máquina híbrida con servidores habilitados para Azure Arc
description: Aprenda a conectar una máquina híbrida con servidores habilitados para Azure Arc y a registrarla en ellos.
ms.topic: quickstart
ms.date: 12/15/2020
ms.openlocfilehash: d5f1699447093f148b0dadbdd23857c9e16e13a3
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772666"
---
# <a name="quickstart-connect-hybrid-machines-with-azure-arc-enabled-servers"></a>Inicio rápido: conexión de máquinas híbridas con servidores habilitados para Azure Arc

Los [servidores habilitados para Azure Arc](../overview.md) permiten administrar y controlar las máquinas Windows y Linux hospedadas en entornos locales, perimetrales y multinube. En este inicio rápido, implementará y configurará el agente de máquina conectada de una máquina Windows o Linux hospedada fuera de Azure para que lo administren servidores habilitados para Arc.

## <a name="prerequisites"></a>Requisitos previos

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

* La implementación del agente de máquina híbrida conectada para servidores habilitados para Arc requiere tener permisos de administrador en la máquina para instalar y configurar el agente. En Linux, esto se realiza mediante la cuenta raíz, mientras que en Windows con una cuenta que pertenezca al grupo de administradores locales.

* Antes de empezar, asegúrese de revisar los [requisitos previos](../agent-overview.md#prerequisites) del agente y compruebe que:

    * La máquina de destino utiliza un [sistema operativo](../agent-overview.md#supported-operating-systems) compatible.

    * A su cuenta se le ha concedido una asignación a los [roles de Azure requeridos](../agent-overview.md#required-permissions).

    * Si la máquina se conecta mediante un firewall o un servidor proxy para comunicarse a través de Internet, asegúrese de que las direcciones URL de la [lista](../agent-overview.md#networking-configuration) no están bloqueadas.

    * Los servidores habilitados para Azure Arc solo admiten las regiones que se especifican [aquí](../overview.md#supported-regions).

> [!WARNING]
> El nombre de host de Linux o de equipo de Windows no puede usar una de las palabras reservadas o marcas en el nombre; de lo contrario, si intenta registrar la máquina conectada con Azure, se producirá un error. Consulte [Resolución de errores en los nombres de recursos reservados](../../../azure-resource-manager/templates/error-reserved-resource-name.md) para obtener una lista de las palabras reservadas.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Registro de proveedores de recursos de Azure

Los servidores habilitados para Azure Arc dependen de los siguientes proveedores de recursos de Azure en la suscripción a fin de poder usar este servicio:

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

Regístrelos con los comandos siguientes:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Generación de un script de instalación

El script para tanto automatizar la descarga e instalación, como para establecer la conexión con Azure Arc, está disponible en Azure Portal. Para completar el proceso, haga lo siguiente:

1. Inicie el servicio Azure Arc en Azure Portal. Para ello, haga clic en **Todos los servicios** y, a continuación, busque y seleccione **Servidores: Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Búsqueda de servidores habilitados para Arc en todos los servicios" border="false":::

1. En la página **Servidores: Azure Arc**, seleccione **Agregar** en la parte superior izquierda.

1. En la página **Seleccionar un método**, seleccione el icono **Agregar servidores mediante un script interactivo** y, después, seleccione **Generar script**.

1. En la página **Generar script**, seleccione la suscripción y el grupo de recursos en el que desea que se administre la máquina en Azure. Seleccione la ubicación de Azure en la que se almacenarán los metadatos de la máquina. Esta ubicación puede ser la misma u otra diferente que la del grupo de recursos.

1. En la página **Requisitos previos**, revise la información y, luego, seleccione **Siguiente: Detalles del recurso**.

1. En la página **Detalles del recurso**, proporcione lo siguiente:

    1. En la lista desplegable **Grupo de recursos**, seleccione el grupo de recursos desde el que se administrará la máquina.
    1. En la lista desplegable **Región**, seleccione la región de Azure en la que se almacenarán los metadatos de los servidores.
    1. En la lista desplegable **Sistema operativo**, seleccione el sistema operativo en el que se ejecutará el script.
    1. Si la máquina se comunica mediante un servidor proxy para conectarse a Internet, especifique la dirección IP del servidor proxy o el nombre y el número de puerto que usará la máquina para comunicarse con el servidor proxy. Escriba el valor con el formato `http://<proxyURL>:<proxyport>`.
    1. Seleccione **Siguiente: Etiquetas**.

1. En la página **Etiquetas**, revise las **etiquetas de ubicación física** predeterminadas sugeridas y escriba un valor, o bien especifique una o varias **etiquetas personalizadas** para que cumplan sus estándares.

1. Seleccione **Siguiente: Descargar y ejecutar el script**.

1. En la pestaña **Descargar y ejecutar el script**, revise la información del resumen y seleccione **Descargar**. Si todavía necesita realizar cambios, seleccione **Anterior**.

## <a name="install-the-agent-using-the-script"></a>Instalación del agente mediante el script

### <a name="windows-agent"></a>Agente de Windows

1. Inicie sesión en el servidor.

1. Abra un símbolo del sistema de PowerShell de 64 bits con privilegios elevados.

1. Cambie a la carpeta o recurso compartido en el que copió el script y ejecútelo en el servidor mediante el script `./OnboardingScript.ps1`.

### <a name="linux-agent"></a>Agente Linux

1. Para instalar el agente Linux en la máquina de destino que pueda comunicarse directamente con Azure, ejecute el siguiente comando:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * Si la máquina de destino se comunica a través de un servidor proxy, ejecute el siguiente comando:

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Comprobación de la conexión con Azure Arc

Después de instalar el agente y configurarlo para que se conecte a los servidores habilitados para Azure Arc, vaya a Azure Portal a fin de comprobar que el servidor se ha conectado correctamente. Vea la máquina en [Azure Portal](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Conexión de una máquina realizada correctamente" border="false":::

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha habilitado un máquina híbrida de Linux o Windows y la ha conectado correctamente al servicio, está listo para habilitar Azure Policy para conocer el cumplimiento en Azure.

Para aprender a identificar una máquina habilitada para los servidores habilitados para Azure Arc que no tiene instalado el agente de Log Analytics, continúe con el tutorial:

> [!div class="nextstepaction"]
> [Creación de una asignación de directiva para identificar recursos no compatibles](tutorial-assign-policy-portal.md)
