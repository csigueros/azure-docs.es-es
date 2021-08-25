---
title: Tareas de inicio comunes para Cloud Services (clásico) | Microsoft Docs
description: Este artículo proporciona algunos ejemplos de tareas de inicio comunes que puede realizar en el rol web o rol de trabajo del servicio en la nube.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: e200a809cc266b26c09a964e86f7f25cd8113be9
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823161"
---
# <a name="common-cloud-service-classic-startup-tasks"></a>Tareas de inicio comunes de un servicio en la nube (clásico)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Este artículo proporciona algunos ejemplos de tareas comunes de inicio que puede realizar en su servicio en la nube. Puede usar las tareas de inicio para realizar operaciones antes de que se inicie un rol. Estas operaciones incluyen la instalación de un componente, el registro de componentes COM, el establecimiento de las claves del registro o el inicio de un proceso de ejecución largo. 

Consulte [este artículo](cloud-services-startup-tasks.md) para entender cómo funcionan las tareas de inicio y de forma específica cómo crear las entradas que definen una tarea de inicio.

> [!NOTE]
> Las tareas de inicio no son aplicables a las máquinas virtuales, solo a los roles web y de trabajo del servicio en la nube.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definición de variables de entorno antes de que se inicie un rol
Si necesita las variables de entorno definidas para una tarea específica, use el elemento [Entorno] dentro del elemento [Task].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

También se pueden usar un [valor válido xPath en Azure](cloud-services-role-config-xpath.md) para hacer referencia a algo acerca de la implementación. En lugar de usar el atributo `value` de atributo, defina un elemento secundario [RoleInstanceValue] .

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Configuración del inicio IIS con AppCmd.exe
La herramienta de línea de comandos [AppCmd.exe](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj635852(v=ws.11)) puede usarse para administrar la configuración de IIS en el inicio de Azure. *AppCmd.exe* proporciona acceso práctico a través de la línea de comandos a los ajustes de configuración para su uso en las tareas de inicio en Azure. Con *AppCmd.exe* se pueden agregar modificar o quitar ajustes del sitio web para aplicaciones y sitios.

Sin embargo, hay algunos aspectos que hay que tener en cuenta cuando se use *AppCmd.exe* como una tarea de inicio:

* Las tareas de inicio se pueden ejecutar más de una vez entre reinicios. Por ejemplo, cuando un rol se recicla.
* Si una acción *AppCmd.exe* se realiza más de una vez, puede generar un error. Por ejemplo, si intenta agregar una sección a *Web.config* dos veces, podría producirse un error.
* Si las tareas de inicio devuelven un código de salida distinto de cero o un **errorlevel** se producirá un error en las mismas. Por ejemplo, cuando *AppCmd.exe* genera un error.

Es recomendable comprobar las respuestas **errorlevel** después de llamar a *AppCmd.exe*, esto es sencillo si encapsula la llamada a *AppCmd.exe* con un archivo *.cmd*. Si se detecta una respuesta **errorlevel** conocida, puede ignorarla o devolverla.

Los mensajes errorlevel que devuelve *AppCmd.exe* se enumeran en el archivo winerror.h y también se pueden ver en [MSDN](/windows/desktop/Debug/system-error-codes--0-499-).

### <a name="example-of-managing-the-error-level"></a>Ejemplo de administración del nivel de error
Este ejemplo agrega una sección de compresión y una entrada de compresión para JSON al archivo *Web.config* con control de errores y registro.

Las secciones relevantes del archivo [ServiceDefinition.csdef] se muestran aquí, e incluyen la configuración del atributo [executionContext](/previous-versions/azure/reference/gg557552(v=azure.100)#task) como `elevated` para dar a *AppCmd.exe* permisos suficientes para cambiar la configuración en el archivo *Web.config*:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

El archivo por lotes *Startup.cmd* usa *AppCmd.exe* para agregar una sección de comprensión y una entrada de comprensión para JSON al archivo *Web.config*. El **errorlevel** esperado de 183 se establece en cero mediante el programa de línea de comandos VERIFY.EXE. Los errorlevels inesperados se registran en StartupErrorLog.txt.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Adición de reglas de firewall
A efectos prácticos, hay dos firewalls en Azure. El primer firewall controla las conexiones entre la máquina virtual y el exterior. El firewall se controla mediante el elemento [Extremos] en el archivo [ServiceDefinition.csdef].

El segundo firewall controla las conexiones entre la máquina virtual y los procesos dentro de esa máquina virtual. Este firewall puede controlarse mediante la herramienta de línea de comandos `netsh advfirewall firewall`.

Azure crea reglas de firewall para los procesos que se inician en sus roles. Por ejemplo, cuando se inicia un servicio o programa, Azure crea automáticamente las reglas de firewall necesarias para permitir que el servicio se comunique con Internet. Sin embargo, si crea un servicio que se inicia con un proceso fuera de su rol (por ejemplo, un servicio COM+ o una tarea programada de Windows), tendrá que crear manualmente una regla de firewall para permitir el acceso a ese servicio. Estas reglas de firewall pueden crearse mediante una tarea de inicio.

Una tarea de inicio que crea una regla de firewall tiene que tener para el atributo [executionContext][Task] un valor **elevated** se producirá un error en las mismas. Agregue la siguiente tarea de inicio al archivo [ServiceDefinition.csdef] .

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Para agregar la regla de firewall, tiene que usar los comandos `netsh advfirewall firewall` adecuados en el archivo por lotes de inicio. En este ejemplo, la tarea de inicio requiere seguridad y cifrado para el puerto TCP 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Bloqueo de una dirección IP específica
Puede restringir un acceso de rol web de Azure a un conjunto de direcciones IP especificadas modificando su archivo **web.config** de IIS. También debe usar un archivo de comandos que desbloquee la sección **ipSecurity** del archivo **ApplicationHost.config**.

Para desbloquear la sección **ipSecurity** del archivo **ApplicationHost.config**, cree un archivo de comandos que se ejecute al iniciarse el rol. Cree una carpeta en el nivel raíz del rol web llamada **startup** y, dentro de esta carpeta, cree un archivo por lotes denominado **startup.cmd**. Agregue este archivo a su proyecto de Visual Studio y establezca las propiedades en **Copiar siempre** para asegurarse de que se incluye en el paquete.

Agregue la siguiente tarea de inicio al archivo [ServiceDefinition.csdef] .

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Agregue este comando al archivo **startup.cmd** :

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Esta tarea hace que el archivo por lotes **startup.cmd** se ejecute cada vez que se inicializa el rol web, asegurándose de que la sección **ipSecurity** necesaria está desbloqueada.

Por último, modifique la [sección system.webServer](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) del archivo de rol web **web.config** para agregar una lista de direcciones IP a las que se concede acceso, tal como se muestra en el ejemplo siguiente:

Esta configuración de ejemplo **permite** a todas las direcciones IP el acceso al servidor con la excepción de las dos definidas

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Esta configuración de ejemplo **deniega** a todas las direcciones IP el acceso al servidor con la excepción de las dos definidas

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>Creación de una tarea de inicio de PowerShell
No se puede llamar directamente a los scripts de Windows PowerShell desde el archivo [ServiceDefinition.csdef] , pero se pueden invocar desde dentro de un archivo por lotes de inicio.

PowerShell (de forma predeterminada) no ejecuta scripts sin firmar. A menos que firme su script, tendrá que configurar PowerShell para ejecutar scripts sin firmar. Para ejecutar scripts sin firmar, **ExecutionPolicy** tiene que establecerse en **Unrestricted**. El ajuste **ExecutionPolicy** que vaya a usar se basa en la versión de Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Si usa un SO invitado que ejecuta PowerShell 2.0 o 1.0 puede forzar la ejecución de la versión 2 y si no está disponible, use la versión 1.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Creación de archivos de una tarea de inicio en un almacenamiento local
Puede usar un recurso de almacenamiento local para almacenar los archivos creados por una tarea de inicio a la que más adelante tiene acceso una aplicación.

Para crear el recurso de almacenamiento local, agregue una sección [LocalResources] al archivo [ServiceDefinition.csdef] y, a continuación, agregue el elemento secundario [LocalStorage]. Asigne al recurso de almacenamiento local un nombre único y un tamaño adecuado para la tarea de inicio.

Para usar un recurso de almacenamiento local en la tarea de inicio, tiene que crear una variable de entorno para hacer referencia a la ubicación del recurso de almacenamiento local. A continuación, la tarea de inicio y la aplicación pueden leer y escribir archivos en el recurso de almacenamiento local.

Las secciones relevantes del archivo **ServiceDefinition.csdef** se muestran aquí:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Por ejemplo, este archivo por lotes **Startup.cmd** usa la variable de entorno **PathToStartupStorage** para crear el archivo **MyTest.txt** en la ubicación de almacenamiento local.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Puede tener acceso a la carpeta de almacenamiento local desde el SDK de Azure con el método [GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) .

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Ejecutar en el emulador o la nube
Puede hacer que la tarea de inicio emprenda acciones diferentes cuando se ejecuta en la nube en comparación con cuando lo hace en el emulador de proceso. Por ejemplo, puede que quiera usar una copia nueva de los datos de SQL solo cuando se ejecuta en el emulador. O puede que desee hacer algunas optimizaciones de rendimiento para la nube que no necesita cuando la ejecución se realiza en el emulador.

Esta capacidad para realizar distintas acciones en el emulador de proceso y en la nube puede conseguirse mediante la creación de una variable de entorno en el archivo [ServiceDefinition.csdef]. A continuación, pruebe esa variable de entorno para un valor en la tarea de inicio.

Para crear la variable de entorno, agregue el elemento [Variable]/[RoleInstanceValue] y cree un valor de XPath de `/RoleEnvironment/Deployment/@emulated`. El valor de la variable de entorno **%ComputeEmulatorRunning%** es `true` cuando se ejecuta en el emulador de proceso y `false` cuando se ejecuta en la nube.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

La tarea puede comprobar ahora la variable de entorno **% ComputeEmulatorRunning %** para realizar distintas acciones en función de si se ejecuta el rol en la nube o en el emulador. Aquí tiene un script de shell de .cmd que busca esa variable de entorno.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Detección de si la tarea ya se ha ejecutado
El rol puede reciclar sin tener que reiniciar con lo que las tareas de inicio se vuelven a ejecutar. No hay ninguna marca para indicar que una tarea ya se ha ejecutado en la VM de hospedaje. Puede que en algunas tareas no importe si se ejecutan varias veces. Sin embargo, puede haber situaciones en las que es necesario evitar que una tarea se ejecute más de una vez.

La manera más sencilla de detectar si una tarea se ha ejecutado ya es crear un archivo en la carpeta **% TEMP %** cuando la tarea se realiza correctamente y buscarlo al inicio de la tarea. Este es un ejemplo script de shell de cmd que lo hace.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>Prácticas recomendadas para las tareas
A continuación presentamos algunas prácticas recomendadas que debería seguir al configurar una tarea para el rol web o de trabajo.

### <a name="always-log-startup-activities"></a>Registre siempre las actividades de inicio
Visual Studio no proporciona un depurador para repasar los archivos por lotes, por eso es conveniente tener tantos datos sobre el funcionamiento de archivos por lotes como sea posible. El registro de la salida de archivos por lotes, **stdout** y **stderr**, puede proporcionar información importante a la hora de depurar y corregir los archivos por lotes. Para registrar **stdout** y **stderr** en el archivo StartupLog.txt en el directorio señalado por la variable de entorno **%TEMP%** , agregue el texto `>>  "%TEMP%\\StartupLog.txt" 2>&1` al final de líneas específicas que desee registrar. Por ejemplo, para ejecutar setup.exe en el directorio **%PathToApp1Install%** : `"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1`

Para simplificar el código xml, puede crear un archivo *cmd* de contenedor que llame a todas las tareas de inicio junto con el registro y garantice que cada tarea secundaria comparte las mismas variables de entorno.

Puede resultarle molesto, sin embargo, a la hora de usar `>> "%TEMP%\StartupLog.txt" 2>&1` en el extremo de cada tarea de inicio. Puede aplicar el registro de tareas mediante la creación de un contenedor que controle el registro automáticamente. Este contenedor llama al archivo por lotes real que desea ejecutar. Cualquier salida del archivo por lotes de destino se redirigirá al archivo *Startuplog.txt*.

En el ejemplo siguiente se muestra cómo redirigir todas las salidas de un archivo por lotes de inicio. En este ejemplo, el archivo ServerDefinition.csdef crea una tarea de inicio que llama a *logwrap.cmd*. *logwrap.cmd* llama a *Startup2.cmd*, redirigiendo todas las salidas a **%TEMP%\\StartupLog.txt**.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap.cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2.cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

Salida de ejemplo en el archivo **StartupLog.txt**:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> El archivo **StartupLog.txt** se encuentra en la carpeta *C:\Resources\temp\\\RoleTemp {identificador de rol}* .
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Establezca executionContext correctamente para las tareas de inicio
Establezca correctamente los privilegios para la tarea de inicio. A veces las tareas de inicio tienen que ejecutarse con privilegios elevados, incluso si el rol se ejecuta con privilegios normales.

La herramienta de línea de comandos [executionContext][Task] establece el nivel de privilegio de la tarea de inicio. Si usa `executionContext="limited"` la tarea de inicio tendrá el mismo nivel de privilegio que el rol. Si usa `executionContext="elevated"` la tarea de inicio tendrá privilegios de administrador, lo que permite que la tarea de inicio realice tareas de administrador sin otorgar privilegios de administrador a su rol.

Un ejemplo de tarea de inicio que requiere privilegios de nivel "elevated" sería una tarea de inicio que use **AppCmd.exe** para configurar IIS. **AppCmd.exe** requiere `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Use el valor de taskType apropiado
El atributo [taskType][Task] determina la forma en la que se ejecuta la tarea de inicio. Hay tres valores: **simple**, **background** y **foreground**. Las tareas con valor background y foreground se inician de forma asincrónica, mientras que las tareas con valor simple se ejecutan sincrónicamente una después de otra.

Con las tareas de inicio **simple**, puede establecer el orden en que se ejecutan las tareas a través del orden de las tareas en el archivo ServiceDefinition.csdef. Si una tarea **simple** finaliza con un código de salida distinto de cero, el procedimiento de inicio se detendrá y el rol no se iniciará.

La diferencia entre las tareas de inicio con valor **background** y aquellas con valor **foreground** es que las tareas **foreground** mantienen el rol en ejecución hasta que la tarea **foreground** finaliza. Esto también significa que si la tarea **foreground** se bloquea o falla, el rol no se reciclará hasta que se fuerce el cierre de la tarea **foreground**. Por este motivo, las tareas **background** se recomiendan para las tareas de inicio asincrónicas a menos que necesite esa característica de la tarea **foreground**.

### <a name="end-batch-files-with-exit-b-0"></a>Finalice lo archivos por lotes con EXIT /B 0
El rol solo se iniciará si **errorlevel** de cada una de las tareas de inicio de valor simple es cero. No todos los programas establecen el **errorlevel** (código de salida) correctamente, por lo que el archivo por lotes debe terminar con `EXIT /B 0` si todo se ejecutó correctamente.

La falta de `EXIT /B 0` al final de un archivo por lotes de inicio es una causa común de que un rol no se inicie.

> [!NOTE]
> He observado que los archivos por lotes anidados a veces dejan de responder al usar el parámetro `/B`. Puede que desee asegurarse de que este problema no se produce si otro archivo por lotes llama a su archivo por lotes actual, como si usara el [contenedor del registro](#always-log-startup-activities). Puede omitir el parámetro `/B` en este caso.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Espere que las tareas de inicio se ejecuten más de una vez
No todos los reciclajes de rol incluyen un reinicio, pero todos incluyen la ejecución de todas las tareas de inicio. Esto significa que las tareas de inicio tienen que poder ejecutarse varias veces entre reinicios sin problemas. Esto se explica en la [sección anterior](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Use el almacenamiento local para almacenar los archivos que tienen que accederse en el rol
Si desea copiar o crear un archivo durante la tarea de inicio que en ese momento es accesible para el rol, ese archivo tiene que colocarse en el almacenamiento local. Consulte la [sección anterior](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Pasos siguientes
Revisar el [modelo de servicio y paquete](cloud-services-model-and-package.md)

Obtener más información acerca de cómo funcionan las [tareas](cloud-services-startup-tasks.md) .

[Crear e implementar](cloud-services-how-to-create-deploy-portal.md) el paquete de servicio en la nube

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: /previous-versions/azure/reference/gg557552(v=azure.100)#Task
[Startup]: /previous-versions/azure/reference/gg557552(v=azure.100)#Startup
[Runtime]: /previous-versions/azure/reference/gg557552(v=azure.100)#Runtime
[Entorno]: /previous-versions/azure/reference/gg557552(v=azure.100)#Environment
[Variable]: /previous-versions/azure/reference/gg557552(v=azure.100)#Variable
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue
[RoleEnvironment]: /previous-versions/azure/reference/ee773173(v=azure.100)
[Extremos]: /previous-versions/azure/reference/gg557552(v=azure.100)#Endpoints
[LocalStorage]: /previous-versions/azure/reference/gg557552(v=azure.100)#LocalStorage
[LocalResources]: /previous-versions/azure/reference/gg557552(v=azure.100)#LocalResources
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue