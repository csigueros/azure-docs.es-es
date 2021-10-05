---
title: Alta disponibilidad de VM de Azure para SAP NW en Windows con Azure Files (SMB) | Microsoft Docs
description: Alta disponibilidad para SAP NetWeaver en máquinas virtuales de Azure en Windows con Azure Files (SMB) para aplicaciones SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: stmuelle
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/13/2021
ms.author: stmuelle
ms.openlocfilehash: d1e01f51d4d83254031407a125c3578fb3feb718
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594549"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-files-premium-smb-for-sap-applications"></a>Alta disponibilidad para SAP NetWeaver en máquinas virtuales de Azure en Windows con Azure Files Premium SMB para aplicaciones SAP

## <a name="introduction"></a>Introducción
Azure Files Premium SMB ahora es totalmente compatible con Microsoft y SAP. **SWPM 1.0 SP32**, **SWPM 2.0 SP09** y posteriores admiten el almacenamiento de Azure Files Premium SMB.  Existen requisitos especiales para el tamaño de los recursos compartidos de Azure Files Premium SMB. Esta documentación contiene recomendaciones específicas sobre cómo distribuir la carga de trabajo en Azure Files Premium SMB, cómo cambiar el tamaño de Azure Files Premium SMB y los requisitos mínimos de instalación de Azure Files Premium SMB.

Las soluciones SAP de alta disponibilidad necesitan un recurso compartido de archivos de alta disponibilidad para hospedar **directorios de interfaz**, **trans** y **sapmnt**. Azure Files Premium SMB es una solución PaaS sencilla de Azure para sistemas de archivos compartidos para SAP en entornos Windows. Azure Files Premium SMB se puede usar junto con conjuntos de disponibilidad y zonas de disponibilidad. Azure Files Premium SMB también se puede usar para escenarios de recuperación ante desastres en otra región.  
 
## <a name="sizing--distribution-of-azure-files-premium-smb-for-sap-systems"></a>Tamaño y distribución de Azure Files Premium SMB para sistemas SAP

Se deben evaluar los puntos siguientes al planear la implementación de Azure Files Premium SMB:
* El nombre del recurso compartido de archivos **sapmnt** se puede crear una vez por cada cuenta de almacenamiento.  Es posible crear SID adicionales como directorios en el mismo recurso compartido **/sapmnt**, como **/sapmnt/\<SID1\>** y **/sapmnt/\<SID2\>** . 
* Elija un tamaño, IOPS y un rendimiento adecuados.  Un tamaño sugerido para el recurso compartido es de 256 GB por SID.  El tamaño máximo de un recurso compartido es de 5120 GB.
* Azure Files Premium SMB puede no tener un rendimiento óptimo para recursos compartidos  **sapmnt** muy grandes con más de uno o dos millones de archivos por cuenta de almacenamiento.Los clientes que tienen millones de trabajos por lotes que crean millones de archivos de registro de trabajos deben reorganizarlos periódicamente según la [nota de SAP 16083][16083]. Si es necesario, los registros de trabajos antiguos se pueden mover o archivar a otra instancia de Azure Files Premium SMB.  Si se espera que **sapmnt** sea muy grande, se deben tener en cuenta opciones alternativas (como Azure ANF).
* Se recomienda usar un punto de conexión de red privada.
* Evite consolidar demasiados SID en una sola cuenta de almacenamiento y su recurso compartido de archivos.
* Como guía general, no se pueden consolidar entre dos y cuatro SID que no sean de producción.
* No consolide todo el entorno de desarrollo, preguntas y respuestas y producción en una cuenta de almacenamiento o recurso compartido de archivos.Si se produce un error en el recurso compartido, se provocará un tiempo de inactividad de todo el entorno de SAP.
* No es aconsejable consolidar los **directorios sapmnt** y **transport** en la misma cuenta de almacenamiento, excepto en sistemas muy pequeños. Durante la instalación de la instancia PAS de SAP, SAPInst solicitará un nombre de host de transporte.  El FQDN de una cuenta de almacenamiento diferente debe especificarse como <cuenta_almacenamiento>.file.core.windows.net.
* No consolide el sistema de archivos utilizado para interfaces en la misma cuenta de almacenamiento que **/sapmnt/\<SID>** . 
* Los usuarios o grupos de SAP deben agregarse al recurso compartido "sapmnt" y deben tener este permiso establecido en Azure Portal: **Colaborador elevado de recursos compartidos de SMB de datos de archivos de Storage**.

Hay motivos importantes para separar **Transport**, **Interface** y **sapmnt** en cuentas de almacenamiento independientes.  La distribución de estos componentes en cuentas de almacenamiento independientes mejora el rendimiento y la resistencia, además de simplificar el análisis de rendimiento.  Si muchos SID y otros sistemas de archivos se consolidan en una sola cuenta de Azure Files Storage y el rendimiento de la cuenta de almacenamiento es deficiente debido a que se alcanzan los límites de rendimiento, es muy difícil identificar qué SID o aplicación está causando el problema. 

## <a name="planning"></a>Planificación 
> [!IMPORTANT]
> La instalación de sistemas de alta disponibilidad de SAP en Azure Files Premium SMB mediante la integración con Active Directory requiere la colaboración entre equipos.  Se recomienda encarecidamente que el equipo base, el equipo de Active Directory y el equipo de Azure trabajen juntos para completar estas tareas: 
>
* Equipo de Azure: instalación y configuración de la cuenta de almacenamiento, la ejecución de scripts y la sincronización de directorios de AD.
* Equipo de Active Directory: creación de cuentas y grupos de usuarios.
* Equipo base: ejecute SWPM y establezca las ACL (si es necesario).

Requisitos previos para la instalación de sistemas de alta disponibilidad de NetWeaver en Azure Files Premium SMB mediante la integración con Active Directory.

* Los servidores de SAP deben estar unidos a un dominio de Active Directory.
* El dominio de Active Directory que contiene los servidores de SAP debe replicarse en Azure Active Directory mediante la conexión a Azure AD.
* Se recomienda encarecidamente que haya al menos un controlador de dominio de Active Directory en el entorno de Azure para evitar atravesar ExpressRoute para ponerse en contacto con controladores de dominio locales.
* El equipo de soporte técnico debe revisar la documentación de Azure Files SMB mediante la [integración con Active Directory](/azure/storage/files/storage-files-identity-auth-active-directory-enable#videos). *En el vídeo se muestran opciones de configuración adicionales que se modificaron (DNS) y se omitieron (DFS-N) por motivos de simplificación*. No obstante, se trata de opciones de configuración válidas. 
* El usuario que ejecuta el script de PowerShell para Azure Files debe tener permiso para crear objetos en Active Directory.
* **Se requieren SWPM versión 1.0 SP32 y SWPM 2.0 SP09 o posterior. La revisión de SAPInst debe ser 749.0.91 o posterior**.
* Debe instalarse una versión actualizada de PowerShell en Windows Server donde se ejecuta el script. 

## <a name="installation-sequence"></a>Secuencia de instalación
 1. El administrador de Active Directory debe crear de antemano tres usuarios de dominio con derechos de **administrador local** y un grupo global en **Windows AD local**: **SAPCONT_ADMIN@SAPCONTOSO.local** tiene derechos de administrador de dominio y se usa para ejecutar **SAPInst**, **\<sid>adm** y **SAPService\<SID>** como usuarios del sistema SAP y el grupo **SAP_\<SAPSID>_GlobalAdmin**. La guía de instalación de SAP contiene los detalles específicos necesarios para estas cuentas.  **Las cuentas de usuario de SAP no deben ser administradores de dominio**. Por lo general, se recomienda **no usar \<sid>adm para ejecutar SAPInst**.
 2. El administrador de Active Directory o el administrador de Azure deben consultar Synchronization Service Manager de **Azure AD Connect**. De forma predeterminada, se tardan aproximadamente 30 minutos en replicarse en **Azure Active Directory**. 
 3. El administrador de Azure debe completar las siguientes tareas:
     1. Cree una cuenta de almacenamiento con el **almacenamiento con redundancia de zona prémium** o el **almacenamiento con redundancia local**. Los clientes con implementación zonal deben elegir ZRS. Aquí es necesario elegir entre configurar una **cuenta estándar** o **Premium**: ![Captura de pantalla de Azure Portal para crear una cuenta de almacenamiento: Paso 1](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-1.png)Azure portal Screenshot for create storage account - Step 1
         > [!IMPORTANT]
         > Para un uso productivo, la recomendación es usar una **cuenta Premium**. Para los no productivos, basta con usar una **cuenta estándar**. 
         >
         ![Captura de pantalla de Azure Portal para crear una cuenta de almacenamiento: Paso 2](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-2.png)Captura de pantalla de Azure Portal para crear una cuenta de almacenamiento: Paso 2
         
         En esta pantalla, la configuración predeterminada debe ser correcta.
        
         ![Captura de pantalla de Azure Portal para crear una cuenta de almacenamiento: Paso 3](media/virtual-machines-shared-sap-high-availability-guide/create-sa-4.png)Captura de pantalla de Azure Portal para crear una cuenta de almacenamiento: Paso 3 
         
         En este paso se toma la decisión de usar un punto de conexión privado.
     1. **Seleccione un punto de conexión de red privada** para la cuenta de almacenamiento.
     Si es necesario, agregue un registro A de DNS a Windows DNS para **<nombre_cuenta_almacenamiento>.file.core.windows.net** (puede que tenga que estar en una nueva zona DNS).  Trate este tema con el administrador de DNS.  La nueva zona no debe actualizarse fuera de una organización.  
         ![pivate-endpoint-creation](media/virtual-machines-shared-sap-high-availability-guide/create-sa-3.png)Captura de pantalla de Azure Portal para la definición del punto de conexión privado.
         ![private-endpoint-dns-1](media/virtual-machines-shared-sap-high-availability-guide/pe-dns-1.png)Captura de pantalla del servidor DNS para la definición de DNS del punto de conexión privado.    
     1. Cree el recurso compartido de archivos **sapmnt** con un tamaño adecuado.  El tamaño sugerido es de 256 GB, lo que proporciona 650 IOPS, 75 MB/s de salida y 50 MB/s de entrada.
         ![create-storage-account-5](media/virtual-machines-shared-sap-high-availability-guide/create-sa-5.png)Captura de pantalla de Azure Portal para la definición del recurso compartido de SMB. 
      
     1. Descargue el contenido de [GitHub sobre Azure Files](/azure/storage/files/storage-files-identity-ad-ds-enable#download-azfileshybrid-module) y ejecute el [script](/azure/storage/files/storage-files-identity-ad-ds-enable#run-join-azstorageaccountforauth).   
     Este script creará una cuenta de equipo o una cuenta de servicio en Active Directory.  El usuario que ejecuta el script debe tener las siguientes propiedades: 
         * El usuario que ejecuta el script debe tener permiso para crear objetos en el dominio de Active Directory que contiene los servidores de SAP. Normalmente, se usa una cuenta de administrador de dominio, como **SAPCONT_ADMIN@SAPCONTOSO.local** . 
         * Antes de ejecutar el script, confirme que la cuenta de usuario del dominio de Active Directory está sincronizada con Azure Active Directory (AAD).  Un ejemplo de esto sería abrir Azure Portal, navegar hasta los usuarios de AAD y comprobar que el usuario **SAPCONT_ADMIN@SAPCONTOSO.local** existe y comprobar la cuenta de usuario de AAD **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** .
         * Conceda el rol de **RBAC de colaborador** a la cuenta de usuario de Azure Active Directory para el grupo de recursos que incluye la cuenta de almacenamiento que contiene el recurso compartido de archivos.  En este ejemplo, al usuario **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** se le concede el **rol de colaborador** al grupo de recursos correspondiente. 
         * El script se debe ejecutar mientras se inicia sesión en un servidor de Windows mediante una cuenta de usuario del dominio de Active Directory con el permiso especificado anteriormente; en este ejemplo, se usaría la cuenta **SAPCONT_ADMIN@SAPCONTOSO.local** .
         >[!IMPORTANT]
         > Al ejecutar el comando de script de PowerShell **Connect-AzAccount**, se recomienda encarecidamente especificar la cuenta de usuario de Azure Active Directory que corresponde y se asigna a la cuenta de usuario del dominio de Active Directory que se usa para iniciar sesión en un servidor de Windows; en este ejemplo, se trata de la cuenta de usuario **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** .
         >
         En este escenario de ejemplo, el administrador de Active Directory iniciaría sesión en Windows Server como **SAPCONT_ADMIN@SAPCONTOSO.local** y, al usar el **comando de PS Connect-AzAccount**, se conectaría como el usuario **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** .  Lo ideal es que el administrador de Active Directory y el administrador de Azure trabajen juntos en esta tarea.
         ![powershell-script-1](media/virtual-machines-shared-sap-high-availability-guide/ps-script-1.png)Captura de pantalla del script de PowerShell que crea una cuenta de AD local.

         ![smb-configured-screenshot](media/virtual-machines-shared-sap-high-availability-guide/smb-config-1.png)Captura de pantalla de Azure Portal después de la ejecución correcta del script de PowerShell 

         Lo siguiente debe aparecer como "Configurado".  
         Almacenamiento -> Recursos compartidos de archivos "Active Directory: Configurado".
     1. Asigne los usuarios de SAP **\<sid>adm**, **SAPService\<SID>** y el grupo **SAP_\<SAPSID>_GlobalAdmin** al recurso compartido de archivos de Azure Files Premium SMB con el rol **Colaborador elevado de recursos compartidos de SMB de datos de archivos de Storage** en Azure Portal. 
     1. Compruebe la ACL en el **recurso compartido de archivos sapmnt** después de la instalación y agregue la cuenta **DOMAIN\CLUSTER_NAME$** , **DOMAIN\\\<sid>adm**, **DOMAIN\SAPService\<SID>** y el **grupo SAP_\<SID>_GlobalAdmin**. Estas cuentas y grupos **deben tener control total del directorio sapmnt**.

         > [!IMPORTANT]
         > Este paso debe completarse antes de la instalación de SAPInst o será difícil o imposible cambiar las ACL después de que SAPInst haya creado directorios y archivos en el recurso compartido de archivos.
         >
         ![Propiedades de ACL](media/virtual-machines-shared-sap-high-availability-guide/smb-share-properties-1.png)Captura de pantalla del Explorador de Windows con los permisos asignados al usuario.

         En las capturas de pantalla siguientes se muestra cómo agregar cuentas de equipo mediante la selección de Tipos de objeto -> Equipo. ![Captura de pantalla de Windows Server donde se agrega el nombre del clúster a AD local](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-2.png)Windows Server screenshot of adding the cluster name to the local AD.
         
         La información de DOMAIN\CLUSTER_NAME$ se puede encontrar al seleccionar "Equipos" en "Tipos de objeto".  
         ![Captura de pantalla para agregar una cuenta de equipo de AD: Paso 2](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-3.png)Screenshot of adding AD computer account - Step 2 ![Captura de pantalla para agregar una cuenta de equipo de AD: Paso 3](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-4.png)Screenshot of adding AD computer account - Step 3 ![Captura de pantalla de las propiedades de acceso a la cuenta de equipo](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-5.png)Screenshot of computer account access properties.

     8. Si es necesario, mueva la cuenta de equipo creada para Azure Files a un contenedor de Active Directory que no tenga expiración para la cuenta.  El nombre de la cuenta de equipo será el nombre corto de la cuenta de almacenamiento. 

     
     > [!IMPORTANT]
     > Para inicializar la ACL de Windows del recurso compartido de SMB, el recurso compartido debe montarse una vez en una letra de unidad.
     >
     La clave de almacenamiento es la contraseña y el usuario de **Azure\\\<SMB share name>** , como se muestra aquí: ![montaje único de net use](media/virtual-machines-shared-sap-high-availability-guide/one-time-net-use-mount-1.png)Captura de pantalla de Windows del montaje único de net use del recurso compartido de SMB.

 4. El administrador de base debe completar las tareas siguientes:
     1. [Instale el clúster de Windows en nodos de ASCS/ERS y adición del testigo en la nube](sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
     2. La primera instalación del nodo de clúster le pedirá el nombre de la cuenta de almacenamiento de Azure Files SMB.  Escriba el FQDN <nombre_cuenta_almacenamiento>.file.core.windows.net.  Si SAPInst no acepta más de trece caracteres, la versión de SWPM es demasiado antigua.
     3. [Modifique el perfil SAP de la instancia de ASCS/SCS](sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761).
     4. [Actualice el puerto de sondeo para el rol \<SID> de SAP en WSFC](sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761).
     5. Continúe con la instalación de SWPM para el segundo nodo de ASCS/ERS. SWPM solo requerirá la ruta de acceso del directorio del perfil.  Escriba la ruta de acceso UNC completa al directorio del perfil.
     6. Escriba la ruta de acceso del perfil UNC para la base de datos y la instalación de PAS/AAS.
     7. La instalación de PAS pedirá el nombre de host de transporte. Proporcione el FQDN de un nombre de cuenta de almacenamiento independiente para el directorio de transporte.
     8. Compruebe las ACL en el SID y el directorio trans.

## <a name="disaster-recovery-setup"></a>Configuración de la recuperación ante desastres
Los escenarios de recuperación ante desastres o los escenarios de replicación entre regiones son compatibles con Azure Files Premium SMB. Todos los datos de los directorios de Azure Files Premium SMB se pueden sincronizar continuamente con una cuenta de almacenamiento de región de recuperación ante desastres mediante este vínculo. Después de un evento de recuperación ante desastres y una conmutación por error de la instancia de ASCS a la región de recuperación ante desastres, cambie el parámetro de perfil SAPGLOBALHOST al punto de Azure Files SMB en la región de recuperación ante desastres. Se deben ejecutar los mismos pasos de preparación en la cuenta de almacenamiento de recuperación ante desastres para unir la cuenta de almacenamiento a Active Directory y asignar roles de RBAC para usuarios y grupos de SAP.

## <a name="troubleshooting"></a>Solución de problemas
Los scripts de PowerShell descargados en el paso 3.c contienen un script de depuración para realizar algunas comprobaciones básicas para validar la configuración.
```powershell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
![Salida del script de PowerShell](media/virtual-machines-shared-sap-high-availability-guide/smb-share-validation-2.png)Captura de pantalla de PowerShell de la salida del script de depuración.

![Powershell-script-technical-info](media/virtual-machines-shared-sap-high-availability-guide/smb-share-validation-1.png)En la pantalla siguiente se muestra la información técnica para validar una unión a un dominio correcta.
## <a name="useful-links--resources"></a>Recursos y vínculos útiles

* Nota de SAP [2273806][2273806] sobre la compatibilidad de SAP con soluciones relacionadas con el almacenamiento o el sistema de archivos 
* [Instalación de alta disponibilidad de SAP NetWeaver en un clúster de conmutación por error de Windows y un recurso compartido de archivos para instancias de ASCS/SCS de SAP en Azure](./sap-high-availability-installation-wsfc-file-share.md) 
* [Escenarios y arquitectura de alta disponibilidad de Azure Virtual Machines para SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
* [Agregar puerto de sondeo en la configuración de clúster de ASCS](sap-high-availability-installation-wsfc-file-share.md)
* [Instalación de una instancia de (A) SCS en un clúster de conmutación por error](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)

[16083]:https://launchpad.support.sap.com/#/notes/16083
[2273806]:https://launchpad.support.sap.com/#/notes/2273806
