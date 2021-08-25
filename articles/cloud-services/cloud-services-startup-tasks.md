---
title: Ejecución de tareas de inicio en Azure Cloud Services (clásico) | Microsoft Docs
description: Las tareas de inicio ayudan a preparar el entorno de servicio en la nube para su aplicación. Esto le enseñará cómo funcionan las tareas de inicio y cómo realizarlas
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 4d235a944a17c388cc0fa7a88ac87cf291eff8b2
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821865"
---
# <a name="how-to-configure-and-run-startup-tasks-for-an-azure-cloud-service-classic"></a>Configuración y ejecución de tareas de inicio en un servicio en la nube de Azure (clásico)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Puede usar las tareas de inicio para realizar operaciones antes de que se inicie un rol. Estas operaciones incluyen la instalación de un componente, el registro de componentes COM, el establecimiento de las claves del registro o el inicio de un proceso de ejecución largo.

> [!NOTE]
> Las tareas de inicio no son aplicables a las máquinas virtuales, solo a los roles web y de trabajo del servicio en la nube.
> 
> 

## <a name="how-startup-tasks-work"></a>Cómo funcionan las tareas de inicio
Las tareas de inicio son acciones que se emprenden antes de comenzar los roles y se definen en el archivo [ServiceDefinition.csdef] con el uso del elemento [Task] dentro del elemento [Startup]. Con frecuencia, las tareas de inicio son archivos por lotes, pero también pueden ser aplicaciones de consola o archivos por lotes que inician scripts de PowerShell.

Las variables de entorno pasan información a una tarea de inicio y el almacenamiento local puede usarse para pasar información de una tarea de inicio. Por ejemplo, una variable de entorno puede especificar la ruta de acceso a un programa que quiera instalar, los archivos pueden escribirse en un almacenamiento local desde donde los roles los pueden leer más tarde.

La tarea de inicio puede registrar información y errores en el directorio especificado por la variable de entorno **TEMP** . Durante la tarea de inicio, la variable de entorno **TEMP** se resuelve en el directorio *C:\\Resources\\temp\\[guid].[rolename]\\RoleTemp* cuando se ejecuta en la nube.

Las tareas de inicio también se puede ejecutar varias veces entre reinicios. Por ejemplo, se ejecutará la tarea de inicio cada vez que el rol se recicla y los reciclajes de rol pueden no incluir siempre un reinicio. Las tareas de inicio deben escribirse de forma que les sea posible ejecutarse varias veces sin problemas.

Las tareas de inicio tienen que terminar con un **errorlevel** (o código de salida) de cero para que el proceso de inicio se complete. Si una tarea de inicio finaliza con un **errorlevel** distinto de cero, el rol no se iniciará.

## <a name="role-startup-order"></a>Orden de inicio de rol
A continuación se enumera el procedimiento de inicio de rol en Azure:

1. La instancia se marca como **Starting** y no recibe tráfico.
2. Todas las tareas de inicio se ejecutan de acuerdo con su atributo **taskType** .
   
   * Las tareas **sencillas** se ejecutan sincrónicamente, de una en una.
   * Las tareas en **segundo plano** y en **primer plano** se inician de forma asincrónica, paralelas a la tarea de inicio.  
     
     > [!WARNING]
     > Puede que IIS no se haya configurado por completo durante la fase de la tarea de inicio en el proceso de inicio, por lo que los datos específicos de rol pueden no estar disponibles. Las tareas de inicio que requieren datos específicos de la role tienen que usar [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. Se inicia el proceso de host de rol y se crea el sitio en IIS.
4. Se llama al método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) .
5. La instancia se marca como **Ready** y el tráfico se enruta a la instancia.
6. Se llama al método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](/previous-versions/azure/reference/ee772746(v=azure.100)) .

## <a name="example-of-a-startup-task"></a>Ejemplo de una tarea de inicio
Las tareas de inicio se definen en el archivo [ServiceDefinition.csdef] , en el elemento **Task** . El atributo **commandLine** especifica el nombre y los parámetros del archivo por lotes o del comando de consola, el atributo **executionContext** especifica el nivel de privilegio de la tarea de inicio y el atributo **taskType** especifica cómo se ejecutará la tarea.

En este ejemplo, una variable de entorno **MyVersionNumber**, se crea para la tarea de inicio y se establece en el valor "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

En el ejemplo siguiente, el archivo por lotes **Startup.cmd** escribe la línea "The current version is 1.0.0.0" en el archivo StartupLog.txt en el directorio especificado por la variable de entorno TEMP. La línea `EXIT /B 0` garantiza que la tarea de inicio finaliza con un **errorlevel** de cero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> En Visual Studio, la propiedad **Copiar en el directorio de salida** para el archivo por lotes de inicio debe establecerse en **Copiar siempre** para asegurarse de que el archivo por lotes de inicio se implementa debidamente en el proyecto en Azure (**approot\\bin** para los roles web y **approot** para los roles de trabajo).
> 
> 

## <a name="description-of-task-attributes"></a>Descripción de los atributos de la tarea
A continuación se describen los atributos del elemento **Task** en el archivo [ServiceDefinition.csdef] :

**commandLine** - especifica la línea de comandos para la tarea de inicio:

* El comando, con los parámetros de línea de comandos opcionales, que comienza la tarea de inicio.
* Con frecuencia es el nombre de archivo de un archivo por lotes .bat o .cmd.
* La tarea es relativa a la carpeta AppRoot\\Bin para la implementación. Las variables de entorno no se expanden para determinar la ruta de acceso y el archivo de la tarea. Si se requiere la expansión del entorno, puede crear un pequeño script .cmd que llame a la tarea de inicio.
* Puede ser una aplicación de consola o un archivo por lotes que inicie un [script de PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** - especifica el nivel de privilegio de la tarea de inicio. El nivel de privilegio puede tener los valores limited o elevated:

* **limited**  
  la tarea de inicio se ejecuta con los mismos privilegios que el rol. Cuando el atributo **executionContext** para el elemento [Runtime] también tiene el valor **limited**, se usan los privilegios de usuario.
* **elevated**  
  la tarea de inicio se ejecuta con privilegios de administrador. Esto permite a las tareas de inicio instalar programas, realizar cambios en la configuración de IIS, realizar cambios en el registro y otras tareas de nivel de administrador, sin aumentar el nivel de privilegio del propio rol.  

> [!NOTE]
> No es preciso que el nivel de privilegios de la tarea de inicio sea el mismo que el del propio rol.
> 
> 

**taskType** - especifica la manera en que se ejecuta una tarea de inicio.

* **simple**  
  Las tareas se ejecutan sincrónicamente, una a una, en el orden especificado en el archivo [ServiceDefinition.csdef] . Cuando una tarea de inicio **simple** finaliza con un **errorlevel** de cero, se ejecuta la siguiente tarea de inicio **simple**. Si no hay más tareas de inicio con el valor **simple** para ejecutarse, se iniciará el propio rol.   
  
  > [!NOTE]
  > Si la tarea **simple** finaliza con un valor **errorlevel** distinto de cero, se bloqueará la instancia. Las subsiguientes tareas de inicio **simple** y el propio rol no se iniciarán.
  > 
  > 
  
    Para asegurarse de que el archivo por lotes finaliza con un valor **errorlevel** de cero, ejecute el comando `EXIT /B 0` al final del proceso de archivo por lotes.
* **background**  
  Las tareas se ejecutan de forma asincrónica, en paralelo con el inicio del rol.
* **foreground**  
  Las tareas se ejecutan de forma asincrónica, en paralelo con el inicio del rol. La diferencia clave entre una tarea con el valor **foreground** y otra con el valor **background** es que una tarea **foreground** impide que el rol se recicle o se cierre hasta que haya finalizado la tarea. Las tareas con el valor **background** no tienen esta restricción.

## <a name="environment-variables"></a>Variables de entorno
Las variables de entorno son una manera de pasar información a una tarea de inicio. Por ejemplo, puede colocar la ruta de acceso a un blob que contiene un programa para instalar, o los números de puerto que usará el rol o las configuraciones que controlan las funciones de la tarea de inicio.

Hay dos tipos de variables de entorno para las tareas de inicio; variables de entorno estáticas y variables de entorno basadas en los miembros de la clase [RoleEnvironment] . Ambos tipos se encuentran en las sección [Environment] del archivo [ServiceDefinition.csdef] y usan el elemento [Variable] y el atributo **name**.

Las variables de entorno estáticas usan el atributo **value** del elemento [Variable] . El ejemplo anterior crea la variable de entorno **MyVersionNumber** que tiene un valor estático de "**1.0.0.0**". Otro ejemplo sería crear una variable de entorno **StagingOrProduction** para la que pueda establecer manualmente los valores de "**staging**" o "**production**" para realizar acciones de inicio diferentes en función del valor de la variable de entorno **StagingOrProduction**.

Las variables de entorno que se basan en los miembros de la clase RoleEnvironment no usan el atributo **value** del elemento [Variable] . En su lugar, el elemento secundario [RoleInstanceValue], con el valor de atributo **XPath** adecuado, se usa para crear una variable de entorno basada en un miembro específico de la clase [RoleEnvironment]. Los valores del atributo **XPath** para acceder a distintos valores [RoleEnvironment] pueden encontrarse [aquí](cloud-services-role-config-xpath.md).

Por ejemplo, para crear una variable de entorno que sea "**true**" cuando se ejecuta la instancia en el emulador de proceso, y "**false**" cuando se ejecuta en la nube, use los siguientes elementos [Variable] y [RoleInstanceValue]:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Pasos siguientes
Aprenda a realizar algunas [tareas de inicio comunes](cloud-services-startup-tasks-common.md) con el servicio en la nube.

[Empaquete](cloud-services-model-and-package.md) el servicio en la nube.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: /previous-versions/azure/reference/gg557552(v=azure.100)#Task
[Startup]: /previous-versions/azure/reference/gg557552(v=azure.100)#Startup
[Tiempo de ejecución]: /previous-versions/azure/reference/gg557552(v=azure.100)#Runtime
[Entorno]: /previous-versions/azure/reference/gg557552(v=azure.100)#Environment
[Variable]: /previous-versions/azure/reference/gg557552(v=azure.100)#Variable
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue
[RoleEnvironment]: /previous-versions/azure/reference/ee773173(v=azure.100)