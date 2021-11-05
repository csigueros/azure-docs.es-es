---
title: Instalación de Azure AD Connect mediante una base de datos existente de ADSync | Microsoft Docs
description: En este tema se describe cómo utilizar una base de datos existente de ADSync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65623f4e37397cdb7a21060d238391e105a4b3fd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131046306"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Instalación de Azure AD Connect mediante una base de datos existente de ADSync
Azure AD Connect requiere una base de datos de SQL Server para almacenar datos. Puede usar la instancia predeterminada de LocalDB incluida en SQL Server 2012 Express que se instala con Azure AD Connect o utilizar su propia versión completa de SQL. Anteriormente, al instalar Azure AD Connect, se creaba siempre una base de datos denominada ADSync. Con Azure AD Connect versión 1.1.613.0 (o posterior), tiene la opción de instalar Azure AD Connect haciendo que apunte a una base de datos existente de ADSync.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Ventajas de usar una base de datos de ADSync ya existente
Si apunta a una base de datos de ADSync ya existente:

- Excepto la información de las credenciales, la configuración de sincronización que se almacena en la base de datos de ADSync (incluidas las reglas de sincronización personalizadas, los conectores, el filtrado y la configuración de características opcionales) se recupera automáticamente y se usa durante la instalación. Las credenciales que usa Azure AD Connect para sincronizar los cambios con implementaciones locales de AD y Azure AD están cifradas y solo se puede acceder a ellas mediante el servidor de Azure AD Connect anterior.
- También se recuperan todos los datos de identidad (asociados con los espacios del conector y el metaverso) y las cookies de sincronización almacenadas en la base de datos de ADSync. El servidor recién instalado de Azure AD Connect puede seguir la sincronización desde donde lo dejó el anterior, en lugar de tener que realizar una sincronización completa.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Escenarios donde es útil usar una base de datos existente de ADSync
Estas ventajas son útiles en los escenarios siguientes:


- Tiene una implementación existente de Azure AD Connect. El servidor de Azure AD Connect existente ya no está funcionando pero el servidor de SQL Server que contiene la base de datos de ADSync todavía está en funcionamiento. Puede instalar un nuevo servidor de Azure AD Connect y hacer que apunte a la base de datos existente de ADSync. 
- Tiene una implementación existente de Azure AD Connect. El servidor de SQL Server que contiene la base de datos de ADSync ha dejado de funcionar. Sin embargo, tiene una copia de seguridad reciente de la base de datos. Puede restaurar la base de datos de ADSync a un nuevo servidor de SQL Server en primer lugar. Transcurrido ese período, puede instalar un nuevo servidor de Azure AD Connect y hacer que apunte a la base de datos restaurada de ADSync.
- Tiene una implementación existente de Azure AD Connect que utiliza LocalDB. Debido al límite de 10 GB impuesto por LocalDB, le gustaría migrar a una implementación completa de SQL. Puede realizar la copia de seguridad de la base de datos de ADSync desde LocalDB y restaurarla en un servidor SQL Server. Transcurrido ese período, puede volver a instalar un nuevo servidor de Azure AD Connect y hacer que apunte a la base de datos restaurada de ADSync.
- Está intentando configurar un servidor de ensayo y desea asegurarse de que su configuración coincide con la del servidor activo actual. Puede realizar la copia de seguridad de la base de datos de ADSync y restaurarla en otro servidor SQL Server. Transcurrido ese período, puede volver a instalar un nuevo servidor de Azure AD Connect y hacer que apunte a la base de datos restaurada de ADSync.

## <a name="prerequisite-information"></a>Información sobre requisitos previos

Notas importantes a tener en cuenta antes de continuar:

- Asegúrese de revisar los requisitos previos para la instalación de Azure AD Connect en la sección de hardware y requisitos previos, y la cuenta y los permisos necesarios para la instalación de Azure AD Connect. Los permisos necesarios para la instalación de Azure AD Connect mediante el modo "Usar base de datos existente" son los mismos que para la instalación "personalizada".
- La implementación de Azure AD Connect en una base de datos de ADSync existente solo es compatible con SQL completo. No se admite con la base de datos local de SQL Express LocalDB. Si tiene una base de datos de ADSync existente en LocalDB que desee usar, primero debe realizar una copia de seguridad de la base de datos de ADSync (LocalDB) y restaurarla en SQL completa. Después, puede implementar Azure AD Connect en la base de datos restaurada con este método.
- La versión de Azure AD Connect usada para la instalación debe cumplir los siguientes criterios:
    - 1.1.613.0 o superior Y
    - Una versión igual o superior a la versión de Azure AD Connect que se utilizó la última vez con la base de datos de ADSync. Si la versión de Azure AD Connect usada para la instalación es superior a la versión que se utilizó por última vez con la base de datos de ADSync, puede que sea necesario realizar una sincronización completa.  Es necesario realizar una sincronización completa si hay cambios en los esquemas o las reglas de sincronización entre las dos versiones. 
- La base de datos de ADSync utilizada debe contener un estado de sincronización que sea relativamente reciente. La última actividad de sincronización con la base de datos existente de ADSync se debe haber producido en las tres últimas semanas.
- Al instalar Azure AD Connect con el método "Usar base de datos existente", no se conserva el método de inicio de sesión configurado en el servidor de Azure AD Connect anterior. Es más, no puede configurar el método de inicio de sesión durante la instalación. Solo puede configurar el método de inicio de sesión una vez completada la instalación.
- No puede hacer que varios servidores de Azure AD Connect compartan la misma base de datos de ADSync. El método "Usar base de datos existente" le permite reutilizar una base de datos de ADSync existente con un nuevo servidor de Azure AD Connect. No admite el uso compartido.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Pasos para instalar Azure AD Connect con el modo "Usar base de datos existente"
1.  Descargue el instalador de Azure AD Connect (AzureADConnect.MSI) en el servidor de Windows. Haga doble clic en el instalador de Azure AD Connect para iniciar la instalación de este.
2.  Una vez finalizada la instalación del MSI, se inicia el asistente de Azure AD Connect con la configuración del modo rápido. Cierre la pantalla haciendo clic en el icono de salida.
![Captura de pantalla que muestra la página "Bienvenido a Azure AD Connect" con la opción "Configuración rápida" del menú izquierdo resaltada.](./media/how-to-connect-install-existing-database/db1.png)
3.  Inicie un nuevo símbolo del sistema o sesión de PowerShell. Vaya a la carpeta "C:\Archivos de programa\Microsoft Azure Active Directory Connect". Ejecute el comando .\AzureADConnect.exe /useexistingdatabase para iniciar el asistente de Azure AD Connect en el modo de configuración "Usar base de datos existente".

> [!NOTE]
> Use el modificador **/UseExistingDatabase** solo cuando la base de datos ya contenga datos de una instalación anterior de Azure AD Connect. Por ejemplo, cuando mueva una base de datos local a una base de datos de SQL Server completa o cuando vuelva a generar el servidor de Azure AD Connect y se restaure una copia de seguridad de SQL de la base de datos de ADSync a partir de una instalación anterior de Azure AD Connect. Si la base de datos está vacía, es decir, no contiene datos de una instalación de Azure AD Connect anterior, omita este paso.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. Aparece la pantalla de bienvenida a Azure AD Connect. Una vez que acepte los términos de licencia y el aviso de privacidad, haga clic en **Continuar**.
   ![Captura de pantalla que muestra la página "Bienvenido a Azure AD Connect"](./media/how-to-connect-install-existing-database/db3.png)
1. En la pantalla **Instalar componentes necesarios**, está habilitada la opción **Usar un SQL Server existente**. Especifique el nombre del servidor de SQL Server que hospeda la base de datos de ADSync. Si la instancia del motor SQL utilizada para hospedar la base de datos de ADSync no es la instancia predeterminada del servidor de SQL Server, deberá especificar el nombre de la instancia del motor SQL. Además, si no está habilitada la exploración de SQL, también deberá especificar el número de puerto de la instancia del motor SQL. Por ejemplo:         
   ![Captura de pantalla que muestra la página "Instalar componentes necesarios".](./media/how-to-connect-install-existing-database/db4.png)           

1. En la pantalla **Conectar con Azure AD**, debe proporcionar las credenciales de un administrador global de su directorio de Azure AD. Se recomienda utilizar una cuenta en el dominio predeterminado onmicrosoft.com. Esta cuenta solo se usa para crear una cuenta de servicio en Azure AD y no se utiliza una vez completado el asistente.
   ![Conexión](./media/how-to-connect-install-existing-database/db5.png)
 
1. En la pantalla **Conectar sus directorios**, el bosque de AD existente configurado para la sincronización de directorios se muestra con un icono de cruz roja junto a él. Para sincronizar los cambios desde un bosque de AD local, se necesita una cuenta de AD DS. El asistente de Azure AD Connect no puede recuperar las credenciales de la cuenta de AD DS almacenada en la base de datos de ADSync porque las credenciales están cifradas y solo las puede descifrar el anterior servidor de Azure AD Connect. Haga clic en **Cambiar credenciales** para especificar la cuenta de AD DS para el bosque de AD.
   ![Directorios](./media/how-to-connect-install-existing-database/db6.png)
 
1. En el cuadro de diálogo emergente, puede (i) proporcionar credenciales de administrador de la organización y dejar que Azure AD Connect cree la cuenta de AD DS en su lugar, o (ii) crear la cuenta de AD DS usted mismo y proporcionar sus credenciales a Azure AD Connect. Una vez que haya seleccionado una opción y proporcionado las credenciales necesarias, haga clic en **Aceptar** para cerrar el cuadro de diálogo emergente.
   ![Captura de pantalla en la que se muestra el cuadro de diálogo emergente "Cuenta del bosque de AD" con la opción "Crear una cuenta de AD" seleccionada.](./media/how-to-connect-install-existing-database/db7.png)
 
1. Una vez que se hayan proporcionado las credenciales, el icono de cruz roja se reemplazará por un icono de marca de verificación verde. Haga clic en **Next**.
   ![Captura de pantalla en la que se muestra la página "Conectar sus directorios".](./media/how-to-connect-install-existing-database/db8.png)
 
1. En la pantalla **Listo para configurar**, haga clic en **Instalar**.
   ![Pantalla de bienvenida](./media/how-to-connect-install-existing-database/db9.png)
 
1. Una vez completada la instalación, el servidor de Azure AD Connect se habilita automáticamente para el modo de ensayo. Se recomienda que revise la configuración del servidor y las exportaciones pendientes para detectar cambios inesperados antes de deshabilitar el modo de ensayo. 

## <a name="post-installation-tasks"></a>Tareas posteriores a la instalación
Cuando se restaura una copia de seguridad de la base de datos creada por una versión de Azure AD Connect anterior a 1.2.65.0, el servidor de almacenamiento provisional seleccionará automáticamente un método de inicio de sesión de **No configurar**. Mientras se restauran las preferencias de sincronización de hash de contraseña y de escritura diferida de contraseñas, deberá cambiar el método de inicio de sesión para que coincida con las demás directivas vigentes para el servidor de sincronización activo.  Si no se siguen estos pasos, es posible que los usuarios no puedan iniciar sesión en caso de que este servidor se active.  

Utilice la siguiente tabla para comprobar los pasos adicionales que se requieren.

|Característica|Pasos|
|-----|-----|
|Sincronización de hash de contraseñas| La sincronización de hash de contraseña y la configuración de la escritura diferida de contraseñas se han restaurada completamente para las versiones de Azure AD Connect a partir de 1.2.65.0.  Si se restaura mediante una versión anterior de Azure AD Connect, revise la configuración de las opciones de sincronización de estas funciones para asegurarse de que coinciden con el servidor de sincronización activo.  No deberían ser necesarios otros pasos de configuración.|
|Federación con AD FS|Las autenticaciones de Azure seguirán utilizando la directiva de AD FS configurada para el servidor de sincronización activo.  Si utiliza Azure AD Connect para administrar la granja de servidores de AD FS, puede cambiar opcionalmente el método de inicio de sesión a la federación de AD FS en preparación para que el servidor en espera se convierta en la instancia de sincronización activa.   Si las opciones de dispositivo están habilitadas en el servidor de sincronización activo, configure esas opciones en este servidor al ejecutar la tarea "Configurar opciones de dispositivo".|
|Autenticación de paso a través e inicio de sesión único de escritorio|Actualice el método de inicio de sesión para que coincida con la configuración del servidor de sincronización activo.  Si esto no se sigue antes de promover el servidor a principal, la autenticación de paso a través junto con el inicio de sesión único de conexión directa se deshabilitará y el inquilino podría quedar bloqueado si no se dispone de la sincronización de hash de contraseñas como opción de inicio de sesión de respaldo. Tenga en cuenta también que cuando habilite la autenticación de paso a través en el modo de almacenamiento provisional, se instalará, registrará y ejecutará un nuevo agente de autenticación como agente de alta disponibilidad que aceptará las solicitudes de inicio de sesión.|
|Federación con PingFederate|Las autenticaciones de Azure seguirán utilizando la directiva PingFederate configurada para el servidor de sincronización activo.  Puede cambiar opcionalmente el método de inicio de sesión a PingFederate en preparación para que el servidor en espera se convierta en la instancia de sincronización activa.  Este paso se puede retrasar hasta que necesite federar dominios adicionales con PingFederate.|

## <a name="next-steps"></a>Pasos siguientes

- Ahora que ha instalado Azure AD Connect, puede [comprobar la instalación y asignar licencias](how-to-connect-post-installation.md).
- Para aprender más acerca de estas características que se habilitaron con la instalación, consulte la información sobre: [cómo evitar eliminaciones accidentales](how-to-connect-sync-feature-prevent-accidental-deletes.md) y [Azure AD Connect Health](how-to-connect-health-sync.md).
- Obtenga información acerca de estos temas habituales: [el programador y cómo desencadenar la sincronización](how-to-connect-sync-feature-scheduler.md).
- Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](whatis-hybrid-identity.md).
