---
title: Configuración de la evaluación gratuita de Azure Defender para IoT
description: En este tutorial, aprenderá a incorporarse a Azure Defender para IoT con un sensor virtual, en una máquina virtual, con una suscripción de prueba de Azure Defender para IoT.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/06/2021
ms.custom: template-tutorial
ms.openlocfilehash: 66d95f5700cb3445aa5e2facabe12ea7c70ef92e
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544796"
---
# <a name="tutorial-azure-defender-for-iot-trial-setup"></a>Tutorial: Configuración de la evaluación gratuita de Azure Defender para IoT

En este tutorial se describe cómo incorporarse a Azure Defender para IoT con un sensor virtual, en una máquina virtual, con una suscripción de prueba de Azure Defender para IoT. En este tutorial se muestra la configuración óptima para alguien que desea probar Azure Defender para IoT, antes de registrarse e incorporarlo a su entorno.

Al usar entornos virtuales, junto con el software necesario para crear un sensor, Defender para IoT le permite hacer lo siguiente:

- Use la supervisión de red pasiva y sin agente para obtener un inventario completo de todos sus dispositivos de IoT y OT, sus detalles y cómo se comunican, sin impacto alguno en la red de IoT y OT.

- Identifique los riesgos y las vulnerabilidades en su entorno de IoT y OT. Por ejemplo, identifique los dispositivos no revisados, los puertos abiertos y las aplicaciones y conexiones no autorizadas. También puede identificar los cambios en las configuraciones de dispositivos, el código de PLC y el firmware.

- Detecte actividades anómalas o no autorizadas con inteligencia de amenazas y análisis de comportamiento especializados en IoT y OT. Incluso puede detectar amenazas avanzadas que los IOC estáticos hayan pasado por alto, como malware de día cero, malware sin archivos y tácticas que aprovechan recursos ya existentes.

- Realice la integración en Azure Sentinel para obtener una visión general de toda la organización. Implemente la gobernanza de seguridad de IoT y OT unificada con la integración en los flujos de trabajo existentes, incluidas herramientas de terceros como Splunk, IBM QRadar y ServiceNow.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Incorporarse a Azure Defender para IoT
> * Descargar la ISO para el sensor virtual
> * Crear una máquina virtual para el sensor
> * Instalar el software del sensor virtual
> * Configurar un puerto SPAN
> * Incorporar y activar el sensor virtual

## <a name="prerequisites"></a>Requisitos previos

- Permisos: **propietarios de suscripción** de Azure o nivel de **colaboradores de suscripción**.

- Al menos un dispositivo que supervisar conectado a un puerto SPAN en el conmutador.

- VMware (ESXi 5.5 o posterior) o el hipervisor de Hyper-V (Windows 10 Pro o Enterprise) están instalados y en funcionamiento.

- Una cuenta de Azure. Si aún no tiene una cuenta de Azure, puede [crear una cuenta gratuita de Azure ahora](https://azure.microsoft.com/free/).

## <a name="onboard-with-azure-defender-for-iot"></a>Incorporarse a Azure Defender para IoT

Para empezar a trabajar con Azure Defender para IoT, debe tener una suscripción a Microsoft Azure. Si no tiene una suscripción, puede [crear una cuenta gratuita de Azure ahora](https://azure.microsoft.com/free/).

Para evaluar Defender para IoT, puede usar una suscripción de prueba. La prueba es válida durante 30 días y admite hasta 1000 dispositivos confirmados. La prueba le permite implementar un sensor virtual en la red. Use los sensores para supervisar el tráfico, analizar datos, generar alertas, obtener información sobre riesgos y vulnerabilidades de la red, etc. La prueba también le permite implementar una consola de administración local virtual para ver la información agregada generada por el sensor.

**Para incorporar una suscripción a Azure Defender para IoT**:

1. Acceda a [Azure Portal](https://ms.portal.azure.com/).

1. Busque y seleccione **Azure Defender para IoT**.

1. Seleccione **Onboard subscription** (Incorporar suscripción).

    :::image type="content" source="media/tutorial-onboarding/onboard-subscription.png" alt-text="Captura de pantalla de la selección del botón de incorporación de la suscripción en la página Getting started (Introducción).":::

1. En la página Pricing (Precios), seleccione **Empezar con una prueba**.

   :::image type="content" source="media/tutorial-onboarding/start-with-trial.png" alt-text="Captura de pantalla del inicio con un botón de prueba para abrir la ventana de la prueba.":::

1. Seleccione una suscripción en el panel "Onboard trial subscription" (Incorporar suscripción de prueba); a continuación, seleccione **Evaluar**.

1. Confirme la evaluación.

## <a name="download-the-iso-for-the-virtual-sensor"></a>Descargar la ISO para el sensor virtual

Las aplicaciones virtuales tienen especificaciones mínimas necesarias para el sensor y la consola de administración. En la tabla siguiente se muestran las especificaciones necesarias para el sensor en función de su entorno.

### <a name="virtual-sensor"></a>Sensor virtual

| Tipo | Corporativos | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| Memoria | 32 GB | 32 GB | 8 GB |
| Storage | 5,6 TB | 1,8 TB | 500 GB |

**Para descargar el archivo ISO para el sensor virtual**:

1. Acceda a [Azure Portal](https://ms.portal.azure.com/).

1. Busque y seleccione **Azure Defender para IoT**.

1. En la página Getting started (Introducción), seleccione la pestaña **Sensor**.

1. Seleccione **Descargar**.

    :::image type="content" source="media/tutorial-onboarding/sensor-download.png" alt-text="Captura de pantalla de la pestaña Sensor, donde se selecciona Descargar para descargar el archivo ISO del sensor virtual.":::

## <a name="create-a-virtual-machine-for-the-sensor"></a>Crear una máquina virtual para el sensor

El sensor virtual admite las opciones de implementación de Hyper-V y VMware. Antes de iniciar la instalación, asegúrese de disponer de los siguientes elementos:

- VMware (ESXi 5.5 o posterior) o el hipervisor de Hyper-V (Windows 10 Pro o Enterprise) instalados y en funcionamiento.

- Recursos de hardware disponibles para la máquina virtual.

- Archivo de instalación ISO para el sensor de Azure Defender para IoT.

- Asegúrese de que el hipervisor esté en ejecución.

### <a name="create-the-virtual-machine-for-the-sensor-with-esxi"></a>Creación de la máquina virtual para el sensor con ESXi

**Para crear la máquina virtual para el sensor (ESXi)** :

1. Inicie sesión en la máquina ESXi, elija el **almacén de datos** pertinente y seleccione **Explorador del almacén de datos**.

1. **Cargue** la imagen y seleccione **Cerrar**.

1. Vaya a **Virtual Machines** (máquinas virtuales) y, a continuación, seleccione **Create/Register VM** (crear/registrar máquina virtual).

1. Seleccione **Create new virtual machine** (crear nueva máquina virtual) y después seleccione **Next** (siguiente).

1. Agregue un nombre de sensor y elija lo siguiente:

   - Compatibilidad: **&lt;última versión de ESXi&gt;**

   - Familia del SO invitado: **Linux**

   - Versión del SO invitado: **Ubuntu Linux (solo 64 bits)**

1. Seleccione **Next** (Siguiente).

1. Elija el almacén de datos pertinente y seleccione **Next** (siguiente).

1. Cambie los parámetros del hardware virtual según la [arquitectura](#download-the-iso-for-the-virtual-sensor) requerida.

1. En **CD/DVD Drive 1** (unidad de CD/DVD 1), seleccione **Datastore ISO file** (archivo ISO de almacén de datos) y elija el archivo ISO que cargó anteriormente.

1. Seleccione **Siguiente** > **Finalizar**.

1. Encienda la máquina virtual y abra una consola.

### <a name="create-a-virtual-machine-for-the-sensor-with-hyper-v"></a>Creación de una máquina virtual para el sensor con Hyper-V

En este procedimiento se describe cómo crear una máquina virtual mediante Hyper-V.

**Para crear una máquina virtual con Hyper-V**:

1. Cree un disco virtual en el administrador de Hyper-V.

1. Seleccione **format = VHDX**.

1. Seleccione **type = Dynamic Expanding**.

1. Escriba el nombre y la ubicación del VHD.

1. Escriba el tamaño requerido (según la [arquitectura](#download-the-iso-for-the-virtual-sensor)).

1. Revise el resumen y seleccione **Finalizar**.

1. En el menú **Actions** (acciones), cree una nueva máquina virtual.

1. Escriba un nombre para la máquina virtual.

1. Seleccione **Specify Generation** > **Generation 1** (especificar generación > generación 1).

1. Especifique la asignación de memoria (de acuerdo con la [arquitectura](#download-the-iso-for-the-virtual-sensor)) y marque la casilla de la memoria dinámica.

1. Configure el adaptador de red según la topología de red del servidor.

1. Conecte el VHDX creado previamente a la máquina virtual.

1. Revise el resumen y seleccione **Finalizar**.

1. Haga clic con el botón secundario en la máquina virtual nueva y seleccione **Settings** (configuración).

1. Seleccione **Add Hardware** (agregar hardware) y agregue un nuevo adaptador de red.

1. Seleccione el conmutador virtual que se conectará a la red de administración del sensor.

1. Asigne recursos de CPU (según la [arquitectura](#download-the-iso-for-the-virtual-sensor)).

1. Conecte la imagen ISO de la consola de administración a una unidad de DVD virtual.

1. Inicie la máquina virtual.

1. En el menú **Actions** (acciones), seleccione **Connect** (conectar) para continuar con la instalación del software.

## <a name="install-the-virtual-sensor-software-with-esxi-or-hyper-v"></a>Instalación del software del sensor virtual con ESXi o Hyper-V

ESXi o Hyper-V se pueden usar para instalar el software para el sensor virtual.

**Para instalar el software en el sensor virtual**:

1. Abra la consola de la máquina virtual.

1. La máquina virtual se iniciará a partir de la imagen ISO y aparecerá la pantalla de selección de idioma. Seleccione **English** (inglés).

1. Seleccione la [arquitectura](#download-the-iso-for-the-virtual-sensor) requerida.

1. Defina el perfil de la aplicación y las propiedades de la red:

    | Parámetro | Configuración |
    | ----------| ------------- |
    | **Perfil de hardware** | Basado en la [arquitectura](#download-the-iso-for-the-virtual-sensor) requerida. |
    | **Interfaz de administración** | **ens192** |
    | **Parámetros de red (proporcionados por el cliente)** | **dirección IP de la red de administración:** <br/>**máscara de subred:** <br>**nombre de host del dispositivo:** <br/>**DNS:** <br/>**puerta de enlace predeterminada:** <br/>**interfaces de entrada:**|
    | **interfaces de puente:** | no es necesario configurar la interfaz de puente. Esta opción solo es para casos de uso especiales. |

1. Escriba **Y** para aceptar la configuración.

1. Las credenciales de inicio de sesión se generan y presentan automáticamente. Copie el nombre de usuario y la contraseña en un lugar seguro, ya que se necesitarán para el inicio de sesión y para la administración del dispositivo. El nombre de usuario y la contraseña no se volverán a mostrar.

    - **Support**: usuario administrativo para la administración de usuarios.

    - **CyberX**: equivalente de usuario raíz para acceder al dispositivo.

1. El dispositivo se reinicia.

1. Acceda al sensor a través de la dirección IP configurada anteriormente: `https://ip_address`.

### <a name="post-installation-validation"></a>Validación después de la instalación

Para validar la instalación de un dispositivo físico, debe realizar muchas pruebas.

La validación está disponible para los usuarios **Support** y **CyberX**.

**Para acceder a la herramienta posterior a la validación:**

1. Inicie sesión en el sensor.

1. En el panel del lado izquierdo, seleccione **Configuración del sistema**.

1. Haga clic en el botón :::image type="icon" source="media/tutorial-onboarding/system-statistics-icon.png" border="false":::.

    :::image type="content" source="media/tutorial-onboarding/system-health-check-screen.png" alt-text="Captura de pantalla de la comprobación de estado del sistema." lightbox="media/tutorial-onboarding/system-health-check-screen-expanded.png":::

Para la validación posterior a la instalación, debe comprobar que el sistema se está ejecutando y que tiene la versión correcta, y que se están ejecutando todas las interfaces de entrada que se configuraron durante el proceso de instalación.

**Para comprobar que el sistema se está ejecutando**:

1. Seleccione **Dispositivo** y asegúrese de que cada elemento de línea muestra `Running` y la línea inferior indica `System is up`.

1. Seleccione **Versión** y asegúrese de que aparece la versión correcta.

1. Seleccione **ifconfig** para mostrar los parámetros de las interfaces físicas del dispositivo y asegúrese de que son correctos.

## <a name="configure-a-span-port"></a>Configurar un puerto SPAN

Una instancia de vSwitch no tiene funcionalidades de creación de reflejo, pero se puede usar una solución alternativa para implementar un puerto SPAN. Puede implementar la solución alternativa con ESXi o Hyper-V.

### <a name="configure-a-span-port-with-esxi"></a>Configuración de un puerto SPAN con ESXi

**Para configurar un puerto SPAN con ESXi**:

1. Abra las propiedades de vSwitch.

1. Seleccione **Agregar**.

1. Seleccione **Virtual Machine** > **Next** (máquina virtual > siguiente).

1. Inserte una etiqueta de red **SPAN Network**, seleccione **VLAN ID** > **All** (id. de VLAN > todos) y, después, seleccione **Next** (siguiente).

1. Seleccione **Finalizar**.

1. Seleccione **SPAN Network** > **Edit* (red SPAN > editar).

1. Seleccione **Security** (seguridad) y compruebe que la directiva **Promiscuous Mode** (modo promiscuo) esté establecida en el modo **Accept** (aceptar).

1. Seleccione **OK** (aceptar) y, después, seleccione **Close** (cerrar) para cerrar las propiedades de vSwitch.

1. Abras la propiedades de **XSense VM**.

1. En **Network Adapter 2** (adaptador de red 2), seleccione la red **SPAN**.

1. Seleccione **Aceptar**.

1. Conéctese al sensor y compruebe que la creación de reflejos funcione.

### <a name="configure-a-span-port-with-hyper-v"></a>Configuración de un puerto SPAN con Hyper-V

Antes de comenzar, deberá:

- Asegurarse de que no hay ninguna instancia de ClearPass VA en ejecución.

- Habilitar Ensure SPAN (Asegurar SPAN) en el puerto de datos y no en el puerto de administración.

- Asegurarse de que la configuración de SPAN del puerto de datos no está definida con una dirección IP.

**Para configurar un puerto SPAN con Hyper-V**:

1. Abra el Administrador de conmutadores virtuales.

1. En la lista Conmutadores virtuales, seleccione **Nuevo conmutador de red virtual** > **Externo** como el tipo de adaptador de red distribuido dedicado.

    :::image type="content" source="media/tutorial-onboarding/new-virtual-network.png" alt-text="Captura de pantalla de la selección de una nueva red virtual y externa antes de crear el conmutador virtual.":::

1. Seleccione **Crear conmutador virtual**.

1. En Tipo de conexión, seleccione **Red externa**.

1. Asegúrese de que la casilla **Permitir que el sistema operativo de administración comparta este adaptador de red** esté activada.

   :::image type="content" source="media/tutorial-onboarding/external-network.png" alt-text="Seleccione la red externa y permita que el sistema operativo de administración comparta el adaptador de red.":::

1. Seleccione **Aceptar**.

#### <a name="attach-a-clearpass-span-virtual-interface-to-the-virtual-switch"></a>Asociación de una interfaz virtual Clearpass SPAN al conmutador virtual

Puede asociar una interfaz virtual ClearPass SPAN al conmutador virtual mediante Windows PowerShell o el administrador de Hyper-V.

**Para asociar una interfaz virtual Clearpass SPAN al conmutador virtual con PowerShell**:

1. Seleccione el conmutador virtual SPAN recién agregado y agregue un nuevo adaptador de red con el siguiente comando:

    ```bash
    ADD-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 -Name Monitor -SwitchName vSwitch_Span
    ```

1. Habilite la creación de reflejo del puerto para la interfaz seleccionada como destino de la distribución con el siguiente comando:

    ```bash
    Get-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 | ? Name -eq Monitor | Set-VMNetworkAdapter -PortMirroring Destination
    ```

    | Parámetro | Descripción |
    |--|--|
    | VK-C1000V-LongRunning-650 | Nombre de CPPM VA |
    |vSwitch_Span |Nombre del conmutador virtual SPAN recién agregado |
    |Monitor |Nombre del adaptador recién agregado |

1. Seleccione **Aceptar**.

Estos comandos establecen el nombre del hardware del adaptador recién agregado como `Monitor`. Si usa el administrador de Hyper-V, el nombre del hardware del adaptador recién agregado se establece en `Network Adapter`.

**Para asociar una interfaz virtual Clearpass SPAN al conmutador virtual con el administrador de Hyper-V**:

1. En la lista Hardware, seleccione **Adaptador de red**.

1. En el campo Conmutador virtual, seleccione **vSwitch_Span**.

    :::image type="content" source="media/tutorial-onboarding/vswitch-span.png" alt-text="Captura de pantalla de la selección de las siguientes opciones en la pantalla del conmutador virtual.":::

1. En la lista Hardware, en la lista desplegable Adaptador de red, seleccione **Características avanzadas**.

1. En la sección Creación de reflejo del puerto, seleccione **Destino** como modo de creación de reflejo para la nueva interfaz virtual.

    :::image type="content" source="media/tutorial-onboarding/destination.png" alt-text="Captura de pantalla de las selecciones necesarias para configurar el modo de creación de reflejo.":::

1. Seleccione **Aceptar**.

#### <a name="enable-microsoft-ndis-capture-extensions-for-the-virtual-switch"></a>Habilitación de las extensiones de captura de NDIS de Microsoft para el conmutador virtual

Las extensiones de captura de NDIS de Microsoft deben estar habilitadas para el nuevo conmutador virtual.

**Para habilitar las extensiones de captura de NDIS de Microsoft para el conmutador virtual recién agregado**:

1. Abra el Administrador de conmutador virtual en el host de Hyper-V.

1. En la lista Conmutadores virtuales, expanda el nombre del conmutador virtual `vSwitch_Span` y seleccione **Extensiones**.

1. En el campo Extensiones de conmutador, seleccione **Captura de NDIS de Microsoft**.

    :::image type="content" source="media/tutorial-onboarding/microsoft-ndis.png" alt-text="Captura de pantalla de la habilitación de NDIS de Microsoft al seleccionar esta opción en el menú de extensiones de conmutador.":::

1. Seleccione **Aceptar**.

#### <a name="set-the-mirroring-mode-on-the-external-port"></a>Establecimiento del modo de creación de reflejo en el puerto externo

El modo de creación de reflejo debe establecerse en el puerto externo del nuevo conmutador virtual como origen.

Deberá configurar el conmutador virtual de Hyper-V (vSwitch_Span) para reenviar cualquier tráfico que llegue al puerto de origen externo al adaptador de red virtual que ha configurado como destino.

Use los siguientes comandos de PowerShell para establecer el puerto del conmutador virtual externo en el modo de reflejo de origen:

```bash
$ExtPortFeature=Get-VMSystemSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings"
$ExtPortFeature.SettingData.MonitorMode=2
Add-VMSwitchExtensionPortFeature -ExternalPort -SwitchName vSwitch_Span -VMSwitchExtensionFeature $ExtPortFeature
```

| Parámetro | Descripción |
|--|--|
| vSwitch_Span | Nombre del conmutador virtual SPAN recién agregado. |
| MonitorMode=2 | Origen |
| MonitorMode=1 | Destination |
| MonitorMode=0 | Ninguno |

Use el siguiente comando de PowerShell para comprobar el estado del modo de supervisión:

```bash
Get-VMSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings" -SwitchName vSwitch_Span -ExternalPort | select -ExpandProperty SettingData
```

| Parámetro | Descripción |
|--|--|
| vSwitch_Span | Nombre del conmutador virtual SPAN recién agregado |
## <a name="onboard-and-activate-the-virtual-sensor"></a>Incorporar y activar el sensor virtual

Para poder empezar a usar el sensor de Defender para IoT, deberá incorporar el sensor virtual creado a la suscripción de Azure y descargar el archivo de activación del sensor virtual para activar el sensor.

### <a name="onboard-the-virtual-sensor"></a>Incorporación del sensor virtual

**Para incorporar el sensor virtual:**

1. Vaya a la **página principal** del [portal de Defender para IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

1. Seleccione **Onboard sensor** (Incorporar sensor).

   :::image type="content" source="media/tutorial-onboarding/onboard-a-sensor.png" alt-text="Captura de pantalla en la que se selecciona la opción de incorporación del sensor para iniciar el proceso de incorporación del sensor.":::

1. Escriba un nombre para el sensor.

   Se recomienda incluir la dirección IP del sensor como parte del nombre o usar un nombre que sea fácil de identificar. Al elegir un nombre así para el sensor, será más fácil realizar un seguimiento.

1. Seleccione una suscripción en el menú desplegable.

    :::image type="content" source="media/tutorial-onboarding/name-subscription.png" alt-text="Captura de pantalla en la que se escribe un nombre descriptivo y se conecta el sensor a una suscripción.":::

1. Elija un modo de conexión del sensor mediante el botón de alternancia **Cloud connected** (Conectado a la nube). Si el botón de alternancia está activado, el sensor está conectado a la nube. Si el botón de alternancia está desactivado, el sensor está administrado de forma local.

   - **Cloud-connected sensors** (Sensores conectados a la nube): la información que detecta el sensor se muestra en la consola del sensor. Además, se proporciona información de alertas mediante un centro de IoT, que puede compartirse con otros servicios de Azure, como Azure Sentinel. Asimismo, los paquetes de inteligencia sobre amenazas pueden insertarse en los sensores desde el portal de Azure Defender para IoT. Por el contrario, cuando el sensor no está conectado a la nube, debe descargar los paquetes de inteligencia sobre amenazas y, después, cargarlos en los sensores de la empresa. Para permitir que Defender para IoT inserte los paquetes en los sensores, habilite el botón de alternancia **Automatic Threat Intelligence Updates** (Actualizaciones automáticas de inteligencia sobre amenazas). Para obtener más información, vea [Investigación y paquetes de inteligencia sobre amenazas](how-to-work-with-threat-intelligence-packages.md).

      En el caso de los sensores conectados a la nube, el nombre que se define durante la incorporación es el nombre que aparece en la consola del sensor. Este nombre no se puede cambiar directamente desde la consola. En el caso de los sensores administrados de forma local, el nombre que se aplique durante la incorporación se almacenará en Azure, pero se puede actualizar en la consola del sensor.

   - **Locally managed sensors** (Sensores administrados de forma local): la información que detectan los sensores se muestra en la consola del sensor. Si trabaja en una red aislada y quiere una vista unificada de toda la información detectada por varios sensores administrados de forma local, trabaje con la consola de administración local.

1. Seleccione un sitio al que asociar el sensor dentro de un centro de IoT. Elija un centro de IoT que sirva como puerta de enlace entre este sensor y Azure Defender para IoT. Defina el nombre para mostrar y la zona. También puede agregar etiquetas descriptivas. El nombre para mostrar, la zona y las etiquetas son entradas descriptivas en [Visualización de sensores incorporados](how-to-manage-sensors-on-the-cloud.md#view-onboarded-sensors).

1. Seleccione **Registrar**.

### <a name="download-the-sensor-activation-file"></a>Descarga del archivo de activación del sensor

Una vez completado el registro del sensor, podrá descargar un archivo de activación para este. El archivo de activación del sensor contiene instrucciones sobre el modo de administración del sensor. El archivo de activación que descargue será único para cada sensor que implemente. Un usuario que inicia sesión en la consola del sensor por primera vez cargará el archivo de activación en el sensor.

**Para descargar un archivo de activación:**

1. En la página **Onboard Sensor** (Sensor incorporado), seleccione **Register** (Registrar).

1. Seleccione **Download activation file** (Descargar archivo de activación).

1. Ponga el archivo a disposición del usuario que inicia sesión en la consola del sensor por primera vez.

### <a name="sign-in-and-activate-the-sensor"></a>Inicio de sesión y activación del sensor

**Para iniciar sesión y realizar la activación, haga lo siguiente:**

1. Vaya a la consola del sensor desde el explorador web mediante la dirección IP definida durante la instalación.

    :::image type="content" source="media/tutorial-onboarding/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Captura de pantalla del sensor de Azure Defender para IoT.":::

1. Escriba las credenciales definidas durante la instalación del sensor.

1. Después de iniciar sesión, se abrirá el cuadro de diálogo **Activar**. Seleccione **Cargar** y vaya al archivo de activación que ha descargado durante la incorporación del sensor.

   :::image type="content" source="media/tutorial-onboarding/activation-upload-screen-with-upload-button.png" alt-text="Captura de pantalla en la que se selecciona Cargar y se va al archivo de activación.":::

1. Acepte los términos y condiciones.

1. Seleccione **Activar**. Se abre el cuadro de diálogo de certificado SSL/TLS.

1. Defina un nombre de certificado.

1. Cargue los archivos de clave y CRT.

1. Escriba una frase de contraseña y cargue un archivo PEM, si es necesario.

1. Seleccione **Next** (Siguiente). Se abre la pantalla de validación. De manera predeterminada, la validación entre la consola de administración y los sensores conectados está habilitada.

1. Desactive el botón de alternancia **Habilitar la validación en todo el sistema** para deshabilitar la validación. Se recomienda que esté habilitada.

1. Seleccione **Guardar**.  

Es posible que tenga que actualizar la pantalla después de cargar el certificado firmado por la entidad de certificación.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a configurar [dispositivos adicionales](how-to-install-software.md#about-defender-for-iot-appliances).
Obtenga información sobre la [arquitectura sin agente](architecture.md).
