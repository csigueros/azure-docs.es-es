---
title: Generación de plantillas de instalación de aplicaciones
description: Cómo generar plantillas de aplicación de SAP para usarlas con el marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 7d9ade08ce2e07c6a93866ea137eca558e9d0c19
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725354"
---
# <a name="generate-sap-application-templates-for-automation"></a>Generación de plantillas de aplicación de SAP para la automatización

El [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md) usa una lista de materiales (BOM) para definir la aplicación de SAP. Para poder implementar un sistema mediante una BOM personalizada, también debe crear las plantillas para los archivos .ini usados en la instalación de SAP desatendida. En esta guía se explica cómo crear las plantillas de aplicación para una implementación de SAP/S4. El proceso es el mismo para las otras aplicaciones de SAP.

## <a name="prerequisites"></a>Requisitos previos

- [Obtenga, descargue y prepare los elementos multimedia de instalación de SAP y los archivos relacionados](automation-bom-get-files.md) si aún no lo ha hecho. Asegúrese de que el [nombre del archivo de utilidad de APCAR que ha descargado](automation-bom-get-files.md#acquire-media) esté disponible.
- Si aún no lo ha hecho, [prepare la BOM](automation-bom-prepare.md). Asegúrese de que el archivo BOM que creó esté disponible.
- Suscripción a Azure. Si aún no tiene una suscripción a Azure, [cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una cuenta de SAP con permisos para trabajar con la base de datos que desea usar.
- Opcionalmente, cree una máquina virtual (VM) en Azure para usarla para transferir elementos multimedia de SAP desde la cuenta de almacenamiento. Este método mejora la velocidad de transferencia. Asegúrese de que tiene conectividad entre la máquina virtual y la máquina virtual de SAP de destino. Por ejemplo, compruebe que las claves SSH están en su lugar.

## <a name="check-media-and-tools"></a>Comprobación de elementos multimedia y herramientas

Antes de generar una plantilla de aplicación de SAP, asegúrese de que tiene todos los elementos multimedia y herramientas de instalación necesarios.

1. Inicie sesión en la máquina virtual de destino como usuario raíz.

1. Cambie la contraseña de usuario raíz a un valor conocido. Usará esta contraseña más adelante para conectarse al administrador de aprovisionamiento de software de SAP (SWPM).

1. Cree un directorio temporal y cambie a este.

    ```bash
    mkdir /tmp/workdir; cd $_
    ```

1. Asegúrese de que hay un directorio temporal para la plantilla de aplicación de SAP.

    ```bash
    mkdir /tmp/app_template/
    ```

1. Cambie los permisos de la utilidad SAPCAR para que este archivo sea ejecutable. Reemplace `<SAPCAR>.EXE` por el nombre del archivo que ha descargado. Por ejemplo, `SAPCAR_1311-80000935.EXE`.

    ```bash
    chmod +x /usr/sap/install/download_basket/<SAPCAR>.EXE
    ```

1. Asegúrese de que existe la carpeta de instalación de SWPM.

    ```bash
    mkdir -p /usr/sap/install/SWPM
    ```

1. Extraiga el archivo de instalación de SWPM mediante la utilidad SAPCAR.

```bash
/usr/sap/install/download_basket/SAPCAR_1311-80000935.EXE -xf /usr/sap/install/SWPM20SP07_0-80003424.SAR -R /usr/sap/install/SWPM/
```

Puede realizar instalaciones de SAP desatendidas con archivos de parámetros. Estos archivos pasan todos los parámetros necesarios al instalador de SWPM. 

> [!NOTE]
> Para generar el archivo de parámetros, debe realizar parcialmente una instalación manual. Para más información, consulte la [nota de SAP 2230669](https://launchpad.support.sap.com/#/notes/2230669).

## <a name="generate-ascs-parameter-file"></a>Generación de un archivo de parámetros ASCS

Para generar el archivo de parámetros de instalación desatendida para ASCS:

1. Inicie sesión en la máquina virtual como usuario raíz a través de la interfaz de la línea de comandos (CLI).

1. Ejecute el comando `hostname` para obtener el nombre de host de la máquina virtual desde la que se ejecuta la instalación. Anote tanto el nombre de host único (donde `<example-vm-hostname>` se encuentra en la salida de ejemplo) como la dirección URL completa de la GUI.
   

1. [Compruebe que tiene todos los elementos multimedia y herramientas necesarios instalados en la máquina virtual](#check-media-and-tools).

1. Inicie SWPM como se muestra a continuación. 

    1. Reemplace `<target-VM-hostname>` por el nombre de host que obtuvo anteriormente.

    1. Reemplace `<XML-stack-file-path>` por el archivo de pila XML que ha creado. Por ejemplo, `/usr/sap/install/config/MP_STACK_S4_2020_v001.xml`.

    ```bash
    /usr/sap/install/SWPM/sapinst                      \
    SAPINST_XML_FILE=<XML-stack-file-path>.xml         \
    SAPINST_USE_HOSTNAME=<target-VM-hostname>
    SAPINST_START_GUISERVER=true \
    SAPINST_STOP_AFTER_DIALOG_PHASE=true 
    
    ```

    ```output
    Connecting to the ASCS VM to launch
    ********************************************************************************
    Open your browser and paste the following URL address to access the GUI
    https://<example-VM-hostname>.internal.cloudapp.net:4237/sapinst/docs/index.html
    Logon users: [root]
    ********************************************************************************
    ```


1. Abra el explorador y visite la dirección URL de la GUI que obtuvo anteriormente.

    1. Acepte la advertencia de riesgo de seguridad.
    
    1. Autentíquese con las credenciales de usuario raíz del sistema.
    
1. En el menú desplegable, seleccione **Servidor SAP S/4HANA 2020** &gt; **Base de datos de SAP HANA** &gt; **Instalación** &gt; **Servidor de aplicaciones ABAP** &gt; **Sistema distribuido** &gt; **Instancia de ASCS**.
        
1. En **Modo de parámetro**, seleccione **Personalizado**. Después, seleccione **Siguiente**.

1. Configure las opciones del sistema SAP:

    1. Asegúrese de que el identificador del sistema SAP es `{SID}`.
    
    1. Asegúrese de que el valor del directorio de montaje de SAP es `/sapmnt`.
    
    1. Seleccione **Next** (Siguiente).

1. Configure las opciones de nombre de dominio completo (FQDN): 

    1. Asegúrese de que el valor de FQDN se rellena automáticamente.
    
    1. Asegúrese de habilitar **Establecer nombre de dominio completo para el sistema SAP**.
    
    1. Seleccione **Next** (Siguiente).

1. Configure una contraseña principal, que solo se usará durante la creación de esta instancia de ASCS. Solo puede usar caracteres alfanuméricos y los caracteres especiales `#`, `$`, `@` y `_` para la contraseña. Tampoco puede usar un dígito ni un guion bajo como primer carácter.

    1. Escriba una contraseña principal.

    1. Confirme la contraseña principal.

    1. Seleccione **Next** (Siguiente). 

1. Configure más opciones de administrador. Otros campos de contraseña se rellenan previamente en función de la contraseña principal que establezca.

    1. Establezca el identificador del usuario del sistema operativo administrador (`<sid>adm` donde `<sid`> es el SID) en `2000`.

    1. Establezca el identificador del sistema SAP (`sapsys`) en `2000`.

    1. Seleccione **Next** (Siguiente).

1. Cuando se solicite para la ruta de acceso del archivo kernel SAPEXE, escriba `/usr/sap/install/download_basket` y, a continuación, seleccione **Siguiente**.

1. Asegúrese de que el estado del paquete sea **Disponible** y, a continuación, seleccione **Siguiente**.

1. Asegúrese de que el estado del archivo de instalación del agente de host de SAP sea **Disponible** y, a continuación, seleccione **Siguiente**.

1. Proporcione información para el usuario del sistema operativo del administrador de SAP.

    1. Deje la contraseña como heredada de la contraseña principal.

    1. Establezca el identificador de usuario del sistema operativo en `2100`.

    1. Seleccione **Next** (Siguiente).

1. Compruebe la configuración de la instalación.

    1. Asegúrese de que el número de instancia de la instalación es correcto.

    1. Asegúrese de establecer el nombre de host virtual de la instancia.

    1. Seleccione **Next** (Siguiente).

1.  Mantenga la configuración del puerto del servidor de mensajes ABAP. Algunos de estos valores predeterminados son `3600` y `3900`. A continuación, seleccione **Siguiente**.

1. No seleccione ningún otro componente para instalar y, a continuación, seleccione **Siguiente**.

1. Habilite **Omitir la configuración de los parámetros de seguridad** y, a continuación, seleccione **Siguiente**.

1. Habilite **Sí, limpiar los usuarios del sistema operativo** y, a continuación, seleccione **Siguiente**.

1. En **Resumen del parámetro**, aún no es necesario hacer nada.

1. En la CLI, busque el archivo de configuración de instalación en el directorio de instalación temporal de SAP. En este momento, el archivo se denomina `inifile.params`.

    1. Ejecute `ls /tmp/sapinst_instdir/` para enumerar los archivos en el directorio de instalación de SAP.

    1. Si el archivo `.lastInstallationLocation` existe, vea el contenido del archivo y anote el directorio que aparece.

    1. Si existe un directorio para el producto que va a instalar, como `S4HANA2020`, vaya a la carpeta de producto. Por ejemplo, ejecute `cd /tmp/sapinst_instdir/S4HANA2020/CORE/HDB/INSTALL/HA/ABAP/ASCS/`.

1. En el explorador, en la GUI de SWPM, seleccione **Cancelar**. Ahora, tiene los archivos .ini necesarios para compilar la plantilla que puede realizar una instalación desatendida de ASCS.

1. Copie y cambie el nombre de `inifile.params` a `scs.inifile.params` en `/tmp/app_template`. Reemplace `<path-to-INI-file>` por la ruta de acceso al archivo .ini como se muestra a continuación:

    ```bash
    cp <path-to-INI-file>/inifile.params /tmp/app_template/scs.inifile.params
    ```

## <a name="load-database-content"></a>Carga del contenido de la base de datos

Asegúrese de que la siguiente configuración está en la máquina virtual antes de empezar:

- Instale y configure las instancias de HANA y SCS. Estas instancias deben estar en línea antes de completar la carga de contenido de la base de datos.

- El usuario `<sid>adm` que [creó al generar el archivo de instalar desatendida para ASCS](#generate-ascs-parameter-file) debe ser miembro del grupo `sapinst`.

- El identificador de usuario de `<sid>adm` debe coincidir con el valor de `hdblcm`. En este ejemplo se usa `2000`.

- SWPM necesita acceso a `/sapmnt/<SID>/global/`. Para configurar permisos, ejecute `chown <sid>adm:sapsys /sapmnt/<SID>/global`.

### <a name="generate-database-load-template"></a>Generación de una plantilla de carga de base de datos

Para generar un archivo de parámetros de instalación desatendida para la carga de contenido de la base de datos:

1. Cree un directorio temporal y cambie a este. Reemplace `<sid>` por el SID.
    
    ```bash
    sudo install -d -m 0777 <sid>adm -g sapinst "/tmp/db_workdir"; cd $_
    ```

1. Inicie SWPM y anote la dirección URL enumerada.

    ```bash
    /usr/sap/install/SWPM/sapinst   \
    SAPINST_XML_FILE=/usr/sap/install/config/MP_STACK_S4_2020_v001.xml
    ```

1. En el explorador, visite la dirección URL que anotó.

1. Acepte la advertencia de riesgo de seguridad.
    
1. Autentíquese con las credenciales de usuario raíz del sistema.

1. Cree un sistema distribuido con parámetros personalizados.

    1. En el menú desplegable, vaya a **Servidor SAP S4/HANA 2020** &gt; **Base de datos de SAP HANA** &gt; **Instalación** &gt; **Servidor de aplicaciones ABAP** &gt; **Sistema distribuido** &gt; **Instancia de base de datos** &gt; **Sistema distribuido**.

    1. Seleccione el modo de parámetro **personalizado**.

    1. Seleccione **Next** (Siguiente).

1. Anote la ruta de acceso del directorio de perfil que crea la instalación de ASCS. Por ejemplo, `/usr/sap/<SID>/SYS/profile` donde `<SID>` es el SID. Después, seleccione **Siguiente**.

1. Escriba el puerto del servidor de mensajes de ABAP de la instancia de ASCS. El número de puerto es `36<InstanceNumber>`, donde `<InstanceNumber>` es el número de instancia de HANA. Por ejemplo, si no hay ninguna instancia, `3600` es el número de puerto. Después, seleccione **Siguiente**.

1. Escriba la contraseña principal que se usará durante la instalación del contenido de la base de datos. Después, seleccione **Siguiente**.

1. Asegúrese de que los detalles del usuario administrador `<SID>adm` (donde `SID` es el SID) son correctos. Después, seleccione **Siguiente**.

1. Escriba la información del **inquilino de la base de datos de SAP HANA**. 

    1. En **Host de la base de datos**, escriba el nombre de host de la máquina virtual de la base de datos de HANA. Para buscar este nombre de host, vaya a la página de recursos de Azure Portal.

    1. En **Número de instancia**, escriba el número de instancia de HANA. Por ejemplo, `00`.

    1. Escriba un identificador para el nuevo inquilino de base de datos. Por ejemplo, `S4H`.

    1. Mantenga la contraseña generada automáticamente para el administrador del sistema de base de datos.

    1. Seleccione **Next** (Siguiente).

1. Asegúrese de que los detalles de conexión son correctos. Después, seleccione **Aceptar**.

1. Escriba la contraseña de administrador para la base de datos del sistema. Después, seleccione **Siguiente**.

1. Escriba la ruta de acceso al kernel de SAPEXE, `/usr/sap/install/download_basket`. Después, seleccione **Siguiente**.

1. Revise qué archivos están disponibles.
    
    1. Seleccione **Next** (Siguiente).

    1. Asegúrese de que el archivo `SAPHOSTAGENT` está disponible.

    1. Seleccione **Siguiente** de nuevo.

1. En la página de confirmación de la contraseña, seleccione **Siguiente**.

1. Revise que todos los archivos de exportación de base de datos de HANA principales están disponibles. Después, seleccione **Siguiente**.

1. En **Esquema de la base de datos** de `SAPHANADB`, seleccione **Siguiente**.

1. En **Almacenamiento seguro de la conexión de base de datos**, seleccione **Siguiente**.

1. En **Parámetros de importación de SAP HANA**, seleccione **Siguiente**.

1. Escriba la contraseña del administrador de base de datos de HANA (`<SID>adm`) para la máquina virtual de base de datos. Después, seleccione **Siguiente**.

1. En **Ruta de acceso de instalación del software cliente de SAP HANA**, seleccione **Siguiente**.

1. Asegúrese de que el archivo de cliente de SAP HANA está disponible. Después, seleccione **Siguiente**.

1. Asegúrese de habilitar **Sí, limpiar los usuarios del sistema operativo**. Después, seleccione **Siguiente**.

1. En **Resumen del parámetro**, aún no es necesario seleccionar nada.

1. Abra la CLI y busque el archivo de configuración de instalación.

    1. Enumere los archivos del directorio temporal, `/tmp/sapinst_instdir/`.

    1. Asegúrese de que el archivo de configuración de instalación `inifile.params` está allí.

    1. Si el archivo `lastInstallationLocation` está allí, ábralo. Anote el directorio que aparece en el contenido del archivo.

    1. Si ya hay un directorio para el producto que va a instalar, como `S4HANA2020`, vaya a la carpeta correspondiente. Por ejemplo, `/tmp/sapinst_instdir/S4HANA2020/CORE/HDB/INSTALL/HA/ABAP/DB/`.

1. Vuelva a abrir SWPM.

1. Seleccione **Cancelar**. Ahora puede usar el método desatendido para la carga de contenido de la base de datos.

1. Copie y cambie el nombre del archivo de configuración de instalación como se muestra a continuación. Reemplace `<path_to_config_file>` por la ruta de acceso a su archivo de configuración.

    ```bash
    cp <path_to_config_file>/inifile.params /tmp/app_template/db.inifile.params
    ```

1. Compruebe la versión de la herramienta `sapinst` en SWPM.
    
    ```bash
    /usr/sap/install/SWPM/sapinst -version
    ```

1. Si la versión de `sapinst` es posterior a `749.0.6`, copie también los archivos `keydb.xml` y `instkey.pkey` para seguir la [Nota de SAP 2393060](https://launchpad.support.sap.com/#/notes/2393060). Reemplace `<path_to_config_file>` por la ruta de acceso a su archivo de configuración.
    
    ```bash
    cp <path_to_config_file>/{keydb.xml,instkey.pkey} /tmp/app_template/
    ```

## <a name="generate-pas-parameter-file"></a>Generación de un archivo de parámetros PAS

Genere un archivo de parámetros de instalación desatendida para su uso con PAS. Todos estos archivos comienzan por `inifile`. 

> [!IMPORTANT]
> Es posible que no vea algunas de estas opciones en las versiones de 2020 de los productos de SAP. En ese caso, omita el paso.

1. Conéctese a la máquina virtual mediante la CLI.

1. [Compruebe que tiene todos los elementos multimedia y herramientas necesarios instalados en la máquina virtual](#check-media-and-tools).

1. Cree un directorio temporal y cambie a este. Reemplace `<SID>` por el SID.

    ```bash
    sudo install -d -m 0777 <SID>adm -g sapinst "/tmp/pas_workdir"; cd $_
    ```
    
1. Conéctese al nodo como usuario raíz. 

1. Inicie sesión en SWPM. 

    1. Vaya a la dirección URL de la GUI de SWPM. Obtuvo esta dirección URL cuando [generó el archivo de instalación desatendida para ASCS](#generate-ascs-parameter-file).

    1. Acepte la advertencia de seguridad.
    
    1. Autentíquese con las credenciales de usuario raíz del sistema.

1. En el menú desplegable, vaya a **Servidor SAP S/4HANA 2020** &gt; **Base de datos de SAP HANA** &gt; **Instalación** &gt; **Servidor de aplicaciones ABAP** &gt; **Sistema distribuido** &gt; **Instancia del servidor de aplicaciones principal**.

1. En **Configuración del parámetro**, seleccione **Personalizado**. Después, seleccione **Siguiente**.

1. Asegúrese de que **Directorio del perfil** está establecido en `/sapmnt/<SID>/profile/` o `/usr/sap/<SID>/SYS/profile`, donde `<SID>` es el SID. Después, seleccione **Siguiente**.

1. Establezca el **Puerto del servidor de mensajes** en `36<instance-number>`, donde `<instance-number>` es el número de instancia de ASCS. Por ejemplo, `3600`. Después, seleccione **Siguiente**.

1. Establezca la contraseña principal para todos los usuarios. Después, seleccione **Siguiente**.

1. Espere a que se rellene la lista `below-the-fold-list`. Después, seleccione **Siguiente**.

1. Asegúrese de deshabilitar la configuración **Actualizar el agente de host de SAP a la versión del archivo SAPHOSTAGENT.SAR proporcionado**. Después, seleccione **Siguiente**.

1. Escriba el número de instancia de la base de datos de SAP HANA y la contraseña del administrador del sistema de base de datos. Después, seleccione **Siguiente**.

1. En **Configuración de liveCache de SAP con SAP HANA**, seleccione **Siguiente**.

1. En **Esquema de la base de datos** de `DBACOCKPIT`, seleccione **Siguiente**.

1. En **Esquema de la base de datos** de `SAPHANADB`, seleccione **Siguiente**.

1. En **Almacenamiento seguro de la conexión de base de datos**, seleccione **Siguiente**.

1. Asegúrese de que el número de instancia de PAS y el host de instancia son correctos. Después, seleccione **Siguiente**.

1. En **Puerto del servidor de mensajes de ABAP**, seleccione **Siguiente**.

1. En **Configuración de los procesos de trabajo**, seleccione **Siguiente**.

1. En **Administración del usuario de ICM de Web Dispatcher de SAP** , seleccione **Siguiente**.

1. En **Destino de SLD del nivel de sistema operativo del sistema SAP**, configure estos valores:

    1. Habilite **Sin destino de SLD**. Después, seleccione **Siguiente**.

    1. Habilite **No crear lista de control de acceso al servidor de mensajes**. A continuación, seleccione **Siguiente**.

    1. Habilite **Ejecutar TMS**. 

    1. Establezca la contraseña del usuario de **TMSADM** en **Cliente 000** para la contraseña principal. Después, seleccione **Siguiente**.

    1. Habilite **No para la importación de transportes de ABAP**. Después, seleccione **Siguiente**.

1. En **Lenguajes del sistema SAP adicionales**, seleccione **Siguiente**. 

1. En **Usuarios de DDIC del sistema SAP**, seleccione **Siguiente**.

1. En **Generación de claves de almacenamiento seguro**, asegúrese de seleccionar **Clave individual**. Después, seleccione **Siguiente**.

1. En la pantalla de advertencia:

    1. Copie el identificador de clave y el valor de clave. 
    
    1. Almacene el identificador de clave y el valor de clave de forma segura.

    1. Seleccione **Next** (Siguiente). 

1. En **Limpiar usuarios del sistema operativo**, seleccione **Sí**. Después, seleccione **Siguiente**.

1. En la CLI, abra el directorio temporal para la instalación. 

1. Asegúrese de que hay una copia del archivo de parámetros `inifile.params`. Por ejemplo, `/tmp/sapinst_instdir/S4HANA2020/CORE/HDB/INSTALL/DISTRIBUTED/ABAP/APP1/inifile.params`.

1. En SWPM, seleccione **Cancelar**. Ahora puede instalar PAS mediante el método desatendido.

1. Copie y cambie el nombre del archivo de parámetros PAS `pas.inifile.params` en `/tmp/app_template` como se muestra a continuación. Reemplace `<path_to_config_file>` por la ruta de acceso al archivo de parámetros. 

    ```bash
    cp <path_to_config_file>/inifile.params /tmp/app_template/pas.inifile.params
    ```

1. Cree una copia de `pas.inifile.params` y descárguela en el equipo o la máquina virtual.

## <a name="generate-additional-application-servers-parameter-file"></a>Generación de un archivo de parámetros de servidores de aplicaciones adicionales

Genere un archivo de parámetros de instalación desatendida para su uso con AAS. Todos estos archivos comienzan por `inifile`. 

> [!IMPORTANT]
> Es posible que no vea algunas de estas opciones en las versiones de 2020 de los productos de SAP. En ese caso, omita el paso.

1. Conéctese a la máquina virtual de AAS a través de la CLI.

1. [Compruebe que tiene todos los elementos multimedia y herramientas necesarios instalados en la máquina virtual](#check-media-and-tools).

1. Asegúrese de que el grupo `sapinst` existe.
    
    ```bash
    groupadd -g 2000 sapinst
    ```

1. Cree un directorio temporal para la instalación como se muestra a continuación. Reemplace `<sid>` por el SID.

    ```bash
    sudo install -d -m 0777 <sid>adm -g sapinst "/tmp/aas_workdir"; cd $_
    ```

1. Inicie sesión en SWPM. 

    1. Vaya a la dirección URL de la GUI de SWPM. Obtuvo esta dirección URL cuando [generó el archivo de instalación desatendida para ASCS](#generate-ascs-parameter-file).

    1. Acepte la advertencia de seguridad.
    
    1. Autentíquese con las credenciales de usuario raíz del sistema.

1. En el menú desplegable, vaya a **Servidor SAP S/4HANA 2020** &gt; **Base de datos de SAP HANA** &gt; **Instalación** &gt; **Servidor de aplicaciones ABAP** &gt; **Sistema de alta disponibilidad** &gt; **Instancia del servidor de aplicaciones adicional**.

1. En **Configuración del parámetro**, seleccione **Personalizado**. Después, seleccione **Siguiente**.

1. Asegúrese de que **Directorio del perfil** está establecido en `/sapmnt/<SID>/profile/` o `/usr/sap/<SID>/SYS/profile`, donde `<SID>` es el SID. Después, seleccione **Siguiente**.

1. Establezca el **Puerto del servidor de mensajes** en `36<instance-number>`, donde `<instance-number>` es el número de instancia de ASCS. Después, seleccione **Siguiente**.

1. Establezca la contraseña principal para todos los usuarios. Después, seleccione **Siguiente**.

1. En **Explorador del paquete de software**, establezca **Directorio de búsqueda** en `/usr/sap/install/download_basket`. Después, seleccione **Siguiente**.

1. Espere a que se rellene la lista `below-the-fold-list`. Después, seleccione **Siguiente**.

1. Asegúrese de habilitar **Actualizar el agente de host de SAP a la versión del archivo SAPHOSTAGENT.SAR proporcionado**. Después, seleccione **Siguiente**.

1. Escriba el número de instancia de la base de datos de SAP HANA y la contraseña del administrador del sistema de base de datos. Después, seleccione **Siguiente**.

1. En **Configuración de liveCache de SAP con SAP HANA**, seleccione **Siguiente**.

1. En **Esquema de la base de datos** de `DBACOCKPIT`, seleccione **Siguiente**.

1. En **Esquema de la base de datos** de `SAPHANADB`, seleccione **Siguiente**.

1. En **Almacenamiento seguro de la conexión de base de datos**, seleccione **Siguiente**.

1. Asegúrese de que el número de instancia de AAS y el host de instancia son correctos. Después, seleccione **Siguiente**.

1. En **Puerto del servidor de mensajes de ABAP**, seleccione **Siguiente**.

1. En **Configuración de los procesos de trabajo**, seleccione **Siguiente**.

1. En **Administración del usuario de ICM de Web Dispatcher de SAP** , seleccione **Siguiente**.

1. En **Destino de SLD del nivel del sistema operativo de SAP**, asegúrese de habilitar **Sin destino de SLD**. Después, seleccione **Siguiente**.

1. Habilite **No crear lista de control de acceso al servidor de mensajes**. Después, seleccione **Siguiente**.

1. Habilite **Ejecutar TMS**.

1. Establezca la contraseña del usuario de **TMSADM** en **Cliente 000** para la contraseña principal. Después, seleccione **Siguiente**.

1. Establezca **Archivo de actualización SPAM/SAINT** en `/usr/sap/install/config/KD75371.SAR`.

1. Establezca **Importar transportes de ABAP** en **No**. Después, seleccione **Siguiente**.

1. En **Pantalla de preparación del administrador de actualizaciones de software**, habilite **Extraer el archivo SUM*.SAR**. Después, seleccione **Siguiente**.

1. En **Explorador del paquete de software**, seleccione la tabla **Paquetes detectados**. Si la ubicación del paquete individual de **SUM 2.0** está vacía, establezca la ruta de acceso del paquete en `usr/sap/install/config`. Después, seleccione **Siguiente**.

1. Espere a que se rellene la ubicación del paquete. Después, seleccione **Siguiente**.

1. En **Lenguajes del sistema SAP adicionales**, seleccione **Siguiente**.

1. Asegúrese de habilitar **Sí, limpiar los usuarios del sistema operativo**. Después, seleccione **Siguiente**.

1. A través de la CLI, compruebe que el directorio temporal tiene ahora una copia del archivo de parámetros. Por ejemplo, `/tmp/sapinst_instdir/S4HANA2020/CORE/HDB/INSTALL/AS/APPS/inifile.params`.

1. Copie y cambie el nombre del archivo a `aas.inifile.params` en `/tmp/app_template` como se muestra a continuación. Reemplace `<path_to_inifile>` por la ruta de acceso al archivo de parámetros.

    ```bash
    cp <path_to_inifile>/inifile.params /tmp/app_template/aas.inifile.params
    ```

1. Cree una copia de `aas.inifile.params` y descárguela en el equipo o la máquina virtual.

1. En SWPM, seleccione **Cancelar**. Ahora puede realizar la instalación de AAS mediante el método desatendido.

## <a name="combine-parameter-files"></a>Combinación de archivos de parámetros

Puede combinar los archivos de parámetros, que terminan en `inifile.params`, en un archivo para el proceso de instalación. 

### <a name="create-combination-file"></a>Creación de un archivo de combinación

Para crear un archivo que combine todos los parámetros:

1. Si aún no lo ha hecho, descargue cada archivo de parámetros que ha creado (ASCS, PAS y AAS). Necesita estos archivos en el equipo o la máquina virtual desde donde está trabajando.

1. Realice una copia de seguridad de cada archivo de parámetros.

1. Cree un nuevo archivo de combinación. Asigne a este archivo el nombre del producto de SAP que está usando. Por ejemplo, `S4HANA_2020_ISS_v001.inifile.params`.

1. Abra el archivo de parámetros ASCS (`scs.inifile.params`) en un editor.

1. Copie el encabezado del archivo de parámetros ASCS en el archivo de combinación. Por ejemplo:

    ```yml
    #########################################################################################################################
    #                                                                                                                       #
    # Installation service 'SAP S/4HANA Server 2020 > SAP HANA Database > Installation                                      #
    #   > Application Server ABAP > Distributed System > ASCS Instance', product id 'NW_ABAP_ASCS:S4HANA2020.CORE.HDB.ABAP' #
    #                                                                                                                       #
    #########################################################################################################################
    ```

1. Para cada archivo `inifile.params` que tenga, copie la línea del identificador de producto del encabezado. A continuación, copie los identificadores de producto en el encabezado del archivo de combinación. Por ejemplo:

    ```yml
    #############################################################################################################################################
    #                                                                                                                                           #
    # Installation service 'SAP S/4HANA Server 2020 > SAP HANA Database > Installation                                                          #
    #   > Application Server ABAP > Distributed System > ASCS Instance', product id 'NW_ABAP_ASCS:S4HANA2020.CORE.HDB.ABAP'                     #
    #   > Application Server ABAP > Distributed System > Database Instance', product id 'NW_ABAP_DB:S4HANA2020.CORE.HDB.ABAP'                   #
    #   > Application Server ABAP > Distributed System > Primary Application Server Instance', product id 'NW_ABAP_CI:S4HANA2020.CORE.HDB.ABAP' #
    #   > Additional SAP System Instances > Additional Application Server Instance', product id 'NW_DI:S4HANA2020.CORE.HDB.PD'                  #
    #                                                                                                                                           #
    #############################################################################################################################################
    ```

1. Abra el archivo `bom.yml` en un editor.

1. Copie las secciones de `product_ids` en el archivo de combinación.

1. Para cada archivo `inifile.params` que tenga, copie el identificador de producto del encabezado en la parte adecuada de `product_ids`. Por ejemplo, copie el ASCS en `scs`:

    ```yml
    product_ids:
      scs: "NW_ABAP_ASCS:S4HANA2020.CORE.HDB.ABAP"
      db:  ""
      pas: ""
      aas: ""
      web: ""
    ```

1. Quite las líneas que ha comentado o dejado en blanco.

1. Guarde el archivo de combinación.

### <a name="improve-readability"></a>Mejorar la legibilidad

A continuación, mejore la legibilidad del [archivo de combinación](#create-combination-file):

1. Abra el archivo de combinación en un editor.

1. Ordene todas las líneas que no están en el encabezado.

1. Quite las líneas duplicadas.

1. Alinee todos los signos iguales. Por ejemplo:

    ```yml
    archives.downloadBasket                             = /usr/sap/install/download_basket
    HDB_Schema_Check_Dialogs.schemaName                 = SAPHANADB
    HDB_Schema_Check_Dialogs.schemaPassword             = MyDefaultPassw0rd
    HDB_Userstore.doNotResolveHostnames                 = x00dx0000l09d4
    ```

1. Separe las líneas por prefijos. Por ejemplo, `NW_CI_Instance.*` y `NW_HDB_DB.*`.

1. Actualice las líneas siguientes para usar variables de Ansible:

    1. `archives.downloadBasket                             = {{ download_basket_dir }}`

    1. `HDB_Schema_Check_Dialogs.schemaPassword             = {{ main_password }}`
    
    1. `HDB_Userstore.doNotResolveHostnames                 = {{ hdb_hostname }}`
    
    1. `hostAgent.sapAdmPassword                            = {{ main_password }}`
    
    1. `NW_AS.instanceNumber                                = {{ aas_instance_number }}`
    
    1. `NW_checkMsgServer.abapMSPort                        = 36{{ scs_instance_number }}`
    
    1. `NW_CI_Instance.ascsVirtualHostname                  = {{ scs_hostname }}`
    
    1. `NW_CI_Instance.ciInstanceNumber                     = {{ pas_instance_number }}`
    
    1. `NW_CI_Instance.ciMSPort                             = 36{{ scs_instance_number }}`
    
    1. `NW_CI_Instance.ciVirtualHostname                    = {{ pas_hostname }}`
    
    1. `NW_CI_Instance.scsVirtualHostname                   = {{ scs_hostname }}`
    
    1. `NW_DI_Instance.virtualHostname                      = {{ aas_hostname }}`
    
    1. `NW_getFQDN.FQDN                                     = {{ sap_fqdn }}`
    
    1. `NW_GetMasterPassword.masterPwd                      = {{ main_password }}`
    
    1. `NW_GetSidNoProfiles.sid                             = {{ app_sid | upper }}`
    
    1. `NW_HDB_DB.abapSchemaPassword                        = {{ main_password }}`
    
    1. `NW_HDB_getDBInfo.dbhost                             = {{ hdb_hostname }}`
    
    1. `NW_HDB_getDBInfo.dbsid                              = {{ hdb_sid | upper }}`
    
    1. `NW_HDB_getDBInfo.instanceNumber                     = {{ hdb_instance_number }}`
    
    1. `NW_HDB_getDBInfo.systemDbPassword                   = {{ main_password }}`
    
    1. `NW_HDB_getDBInfo.systemid                           = {{ hdb_sid | upper }}`
    
    1. `NW_HDB_getDBInfo.systemPassword                     = {{ main_password }}`
    
    1. `NW_readProfileDir.profileDir                        = /usr/sap/{{ app_sid | upper }}/SYS/profile`
    
    1. `NW_Recovery_Install_HDB.extractLocation             = /usr/sap/{{ hdb_sid | upper }}/HDB{{ hdb_instance_number }}/backup/data/DB_{{ hdb_sid | upper }}`
    
    1. `NW_Recovery_Install_HDB.sidAdmName                  = {{ hdb_sid | lower }}adm`
    
    1. `NW_Recovery_Install_HDB.sidAdmPassword              = {{ main_password }}`
    
    1. `NW_SAPCrypto.SAPCryptoFile                          = {{ download_basket_dir }}/SAPEXE_300-80004393.SAR`
    
    1. `NW_SCS_Instance.instanceNumber                      = {{ scs_instance_number }}`
    
    1. `NW_Unpack.igsExeSar                                 = {{ download_basket_dir }}/igsexe_12-80003187.sar`
    
    1. `NW_Unpack.igsHelperSar                              = {{ download_basket_dir }}/igshelper_17-10010245.sar`
    
    1. `NW_Unpack.sapExeDbSar                               = {{ download_basket_dir }}/SAPEXEDB_300-80004392.SAR`
    
    1. `NW_Unpack.sapExeSar                                 = {{ download_basket_dir }}/SAPEXE_300-80004393.SAR`
    
    1. `NW_SCS_Instance.scsVirtualHostname                  = {{ scs_hostname }}`
    
    1. `nwUsers.sapadmUID                                   = {{ sapadm_uid }}`
    
    1. `nwUsers.sapsysGID                                   = {{ sapsys_gid }}`
    
    1. `nwUsers.sidadmPassword                              = {{ main_password }}`
    
    1. `nwUsers.sidAdmUID                                   = {{ sidadm_uid }}`
    
    1. `storageBasedCopy.hdb.instanceNumber                 = {{ hdb_instance_number }}`
    
    1. `storageBasedCopy.hdb.systemPassword                 = {{ main_password }}`

### <a name="upload-combination-file"></a>Carga del archivo de combinación

Por último, cargue el archivo de plantilla combinado en la biblioteca SAP.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione o busque las **cuentas de almacenamiento**.

1. Seleccione la cuenta de almacenamiento de la biblioteca SAP.

1. En el menú de la cuenta de almacenamiento, en **Almacenamiento de datos**, seleccione **Contenedores**.

1. Seleccione el contenedor `sapbits`.

1. Vaya a la carpeta de producto de la marca BOM en `sapbits`. Por ejemplo, `boms/S4HANA_2020_ISS_v001`.

1. Si aún no tiene un directorio denominado **plantillas**, créelo.

1. Abra el directorio **plantillas**.

1. Seleccione **Cargar**.

1. En el panel, seleccione **Seleccionar un archivo**.

1. Seleccione el archivo de plantilla combinado.  Por ejemplo, `S4HANA_2020_ISS_v001.inifile.params`.

1. Seleccione **Cargar**.

## <a name="update-bom-with-templates"></a>Actualización de BOM con plantillas

Después de [combinar los archivos de parámetros](#combine-parameter-files), actualice BOM con los nuevos archivos de plantilla.

1. Abra `bom.yml`.

1. En la sección `templates`, agregue los nuevos nombres de archivo de plantilla.  Por ejemplo:

    ```yml
    templates:
      - name:     "S4HANA_2020_ISS_v001 ini file"
        file:     S4HANA_2020_ISS_v001.inifile.params
        override_target_location: "{{ target_media_location }}/config"
    ```

1. Si usa la preparación BOM de aplicación con script, quite antes `#` de la plantilla.

1. Guarde los cambios.

A continuación, cargue el nuevo archivo BOM en la biblioteca SAP.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione o busque las **cuentas de almacenamiento**.

1. Seleccione la cuenta de almacenamiento de la biblioteca SAP.

1. En el menú de la cuenta de almacenamiento, en **Almacenamiento de datos**, seleccione **Contenedores**.

1. Seleccione el contenedor `sapbits`.

1. Vaya a la carpeta de producto de la marca BOM en `sapbits`. Por ejemplo, `boms/S4HANA_2020_ISS_v001`.

1. Abra el directorio `boms`.

1. Seleccione **Cargar**.

1. En el panel, seleccione **Seleccionar un archivo**.

1. Seleccione el archivo BOM, `bom.yml`, en el equipo o la máquina virtual.

1. Asegúrese de habilitar **Sobrescribir si los archivos ya existen**.

1. Seleccione **Cargar**.

