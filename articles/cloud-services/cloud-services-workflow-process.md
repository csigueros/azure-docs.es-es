---
title: Flujo de trabajo de la arquitectura de máquinas virtuales de Microsoft Azure | Microsoft Docs
description: En este artículo se proporciona información general de los procesos de flujo de trabajo cuando implementa un servicio.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5e4725cf756435ac50eeafdd70b87c83e4b0a867
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113091058"
---
# <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Flujo de trabajo de la arquitectura clásica de máquinas virtuales de Microsoft Azure 

> [!IMPORTANT]
> [Azure Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md) es un nuevo modelo de implementación basado en Azure Resource Manager para el producto Azure Cloud Services. Con este cambio, se ha modificado el nombre del modelo de implementación basado en Azure Cloud Services para Azure Service Manager a Cloud Services (clásico), y todas las implementaciones nuevas deben usar [Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md).

En este artículo se proporciona información general de los procesos de flujo de trabajo que se producen al implementar o actualizar un recurso de Azure, como una máquina virtual. 

> [!NOTE]
>Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: el de Resource Manager y el clásico. Este artículo trata del modelo de implementación clásico.

El diagrama siguiente presenta la arquitectura de los recursos de Azure.

:::image type="content" source="./media/cloud-services-workflow-process/workflow.jpg" alt-text="<alt Imagen del flujo de trabajo de Azure>":::

## <a name="workflow-basics"></a>Conceptos básicos del flujo de trabajo
   
**R**. RDFE/FFE es la ruta de comunicación desde el usuario hasta el tejido. RDFE (Front-end RedDog) es la API expuesta públicamente que es el front-end para el Portal de administración y Service Management API, como Visual Studio, MMC de Azure, etc.  Todas las solicitudes del usuario se realizan a través de RDFE. FFE (Front-end de tejido) es la capa que convierte las solicitudes de RDFE en los comandos de tejido. Todas las solicitudes de RDFE pasan por FFE para llegar hasta el controlador de tejido.

**B**. El controlador de tejido es el responsable del mantenimiento y la supervisión de todos los recursos del centro de datos. Se comunica con los agentes de host de tejido en el SO de tejido enviando información, como la versión del SO invitado, el Service Pack, la configuración del servicio y el estado del servicio.

**C**. El agente de host reside en el sistema operativo del host y es el responsable de configurar el SO invitado y de comunicarse con el agente invitado (WindowsAzureGuestAgent) para actualizar el rol según un estado previsto deseado y realizar comprobaciones de latido con el agente invitado. Si el agente de host no recibe respuesta de latido durante diez minutos, reinicia el SO invitado.

**C2**. WaAppAgent es el responsable de instalar, configurar y actualizar WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent es responsable de lo siguiente:

1. Configurar el SO invitado, lo que incluye el firewall, las ACL, los recursos LocalStorage, la configuración y el paquete de servicio, y los certificados.
2. Configurar el SID de la cuenta de usuario en la que se ejecutará el rol.
3. Comunicar el estado del rol al tejido.
4. Iniciar WaHostBootstrapper y supervisarlo para asegurarse de que el rol se encuentra en el estado previsto.

**E**. WaHostBootstrapper es el responsable de:

1. Leer la configuración del rol e iniciar todas las tareas y procesos apropiados para configurar y ejecutar dicho rol.
2. Supervisar todos sus procesos secundarios.
3. Generar el evento StatusCheck en el proceso de host del rol.

**F**. IISConfigurator se ejecuta si el rol se configura como un rol web de IIS completo. Es el responsable de:

1. Iniciar los servicios de IIS estándar
2. Configurar el módulo de reescritura en la configuración web
3. Configurar AppPool para el rol configurado en el modelo de servicio
4. Configurar el registro de IIS para que apunte a la carpeta DiagnosticStore LocalStorage
5. Configurar los permisos y las ACL
6. El sitio web reside en %roleroot%:\sitesroot\0 y AppPool apunta a esta ubicación para ejecutar IIS. 

**G**. El modelo de rol define las tareas de inicio y WaHostBootstrapper las inicia. Las tareas de inicio se pueden configurar para que se ejecuten de forma asincrónica en segundo plano y el arranque del host iniciará la tarea de inicio y, luego, continuará con otras tareas de inicio. Las tareas de inicio también pueden configurarse para ejecutarse en modo Simple (predeterminado) en el que el arranque del host esperará a que la tarea de inicio termine de ejecutarse y devuelva un código de salida correcto (0) antes de continuar con la siguiente tarea de inicio.

**H**. Estas tareas forman parte del SDK y se definen como complementos en la definición del servicio del rol (.csdef). Cuando se expande en las tareas de inicio, **DiagnosticsAgent** y **RemoteAccessAgent** son únicos en que cada uno de ellos define dos tareas de inicio, una normal y otra que tiene un parámetro **/blockStartup**. La tarea de inicio normal se define como una tarea de inicio en segundo plano para que pueda ejecutarse en segundo plano mientras se está ejecutando el propio rol. La tarea de inicio **/blockStartup** se define como una tarea de inicio Simple para que WaHostBootstrapper espere a que salga antes de continuar. La tarea **/blockStartup** espera a que la tarea normal termine de inicializarse y luego se cierra y permite que el arranque del host continúe. Esto se hace para que los diagnósticos y el acceso RDP se puedan configurar antes de que los procesos de rol se inicien (esto se hace a través de la tarea /blockStartup). Esto también permite que los diagnósticos y el acceso RDP continúen ejecutándose después de que el arranque del host haya finalizado las tareas de inicio (esto se hace a través de la tarea Normal).

**I**. WaWorkerHost es el proceso de host estándar para los roles de trabajo normales. Este proceso de host hospeda los archivos DLL y código de punto de entrada, como OnStart y Run.

**J**. WaIISHost es el proceso de host para el código de punto de entrada de rol para roles web que usan IIS completo. Este proceso carga el primer archivo DLL que se encuentra que usa la clase **RoleEntryPoint** y ejecuta el código de esta clase (OnStart, Run, OnStop). Todos los eventos **RoleEnvironment** (como StatusCheck y Changed) que se crean en la clase RoleEntryPoint se generan en este proceso.

**K**. W3wp es el proceso de trabajo de IIS estándar que se usa si el rol está configurado para usar IIS completo. Esto ejecuta AppPool que se configura desde IISConfigurator. Todos los eventos RoleEnvironment (como StatusCheck y Changed) que se crean aquí se generan en este proceso. Tenga en cuenta que los eventos RoleEnvironment se activarán en ambas ubicaciones (WaIISHost y w3wp.exe) si se suscribe a eventos en ambos procesos.

## <a name="workflow-processes"></a>Procesos de flujo de trabajo

1. Un usuario realiza una solicitud, como cargar archivos ".cspkg" y ".cscfg", indicando a un recurso que detenga o haga un cambio de configuración, etc. Esto se puede hacer a través de Azure Portal o una herramienta que utiliza Service Management API, como la característica Publicar de Visual Studio. Esta solicitud se pasa a RDFE para hacer todo el trabajo relacionado con las suscripciones y luego comunica la solicitud a FFE. El resto de estos pasos de flujo de trabajo son para implementar un nuevo paquete e iniciarlo.
2. FFE encuentra el grupo de máquinas correcto (en función de las aportaciones del cliente, como el grupo de afinidad o la ubicación geográfica, y de la entrada del tejido, como la disponibilidad de la máquina) y se comunica con el controlador de tejido maestro de ese grupo de máquinas.
3. El controlador de tejido encuentra un host que tiene núcleos de CPU disponibles (o implementa un nuevo host). El Service Pack y la configuración del servicio se copian en el host y el controlador de tejido se comunica con el agente de host en el SO del host para implementar el paquete (configurar DIP, puertos, SO invitado, etc.).
4. El agente de host inicia el SO invitado y se comunica con el agente invitado (WindowsAzureGuestAgent). El host envía los latidos al invitado para asegurarse de que el rol funciona para conseguir el estado previsto.
5. WindowsAzureGuestAgent configura el SO invitado (firewall, ACL, LocalStorage, etc.), copia un nuevo archivo de configuración XML en c:\Config y, por último, inicia el proceso WaHostBootstrapper.
6. Para los roles web de IIS completo, WaHostBootstrapper inicia IISConfigurator y le indica que elimine cualquier AppPool existente para el rol web de IIS.
7. WaHostBootstrapper lee las tareas **Startup** de E:\RoleModel.xml y comienza a ejecutar las tareas de inicio. WaHostBootstrapper espera a que todas las tareas de inicio de modo Simple terminen y devuelvan un mensaje que indica que la operación se ha realizado correctamente.
8. Para los roles web de IIS completo, WaHostBootstrapper indica a IISConfigurator que configure la instancia de AppPool de IIS y apunta el sitio a `E:\Sitesroot\<index>`, donde `<index>` es un índice basado en cero en el número de `<Sites>` elementos definidos para el servicio.
9. WaHostBootstrapper iniciará el proceso de host según el tipo de rol:
    1. **Rol de trabajo**: WaWorkerHost.exe se inicia. WaHostBootstrapper ejecuta el método OnStart(). Después de regresar, WaHostBootstrapper comienza a ejecutar el método Run() y luego marca simultáneamente el rol como Listo y lo coloca en la rotación del equilibrador de carga (si se definen elementos InputEndpoints). Después, WaHostBootsrapper entra en un bucle de comprobación del estado del rol.
    2. **Rol web de IIS completo**: aIISHost se inicia. WaHostBootstrapper ejecuta el método OnStart(). Después de regresar, comienza a ejecutar el método Run() y luego marca simultáneamente el rol como Listo y lo coloca en la rotación del equilibrador de carga. Después, WaHostBootsrapper entra en un bucle de comprobación del estado del rol.
10. Las solicitudes web entrantes a un rol web de IIS completo activan IIS para iniciar el proceso W3WP y atender la solicitud, de la misma manera que lo haría en un entorno de IIS local.

## <a name="log-file-locations"></a>Ubicaciones del archivo de registro

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Este registro contiene los cambios realizados en el servicio, como los inicios, las detenciones y las nuevas configuraciones. Si el servicio no cambia, puede esperar ver grandes espacios de tiempo en este archivo de registro.
- C:\Logs\WaAppAgent.Log.  
Este registro contiene las actualizaciones de estado y notificaciones de latido y se actualiza cada 2 o 3 segundos.  Este registro contiene una vista histórica del estado de la instancia y le indicará cuándo la instancia no estaba en estado Listo.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**Registros de IIS**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Registros de eventos de Windows**

`D:\Windows\System32\Winevt\Logs`
