---
title: Solución de problemas de errores de conexión de recursos compartidos durante la copia de datos en Azure Data Box | Microsoft Docs
description: Se describe cómo identificar problemas de red que impiden las conexiones de recursos compartidos SMB durante la copia de datos en un dispositivo Azure Data Box.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 08/23/2021
ms.author: alkohli
ms.openlocfilehash: afc76602b610488fd2ce4cf7f17e547821fc406c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868631"
---
# <a name="troubleshoot-share-connection-failure-during-data-copy-to-azure-data-box"></a>Solución de problemas de errores de conexión de recursos compartidos durante la copia de datos en Azure Data Box

En este artículo se describe lo que debe hacer cuando no se pueda conectar a un recurso compartido SMB en el dispositivo Azure Data Box debido a un problema de red.

Las razones más comunes por las que no es posible conectarse a un recurso compartido en el dispositivo son:

- [problema de dominio](#check-for-a-domain-issue)
- [cuenta bloqueada fuera del recurso compartido](#account-locked-out-of-share)
- [una directiva de grupo impide la conexión](#check-for-a-blocking-group-policy)
- [problema de permisos](#check-for-permissions-issues)

## <a name="check-for-a-domain-issue"></a>Comprobación de un problema de dominio

Para averiguar si un problema de dominio impide una conexión al recurso compartido:

- Intente conectarse al dispositivo y use uno de los siguientes formatos para escribir el nombre de usuario:

    - `<device IP address>\<user name>`
    - `\<user name>`

Si puede conectarse al dispositivo, significa que no existe un problema de dominio que impida la conexión al recurso compartido.

## <a name="account-locked-out-of-share"></a>Cuenta bloqueada fuera de recurso compartido

Después de cinco intentos erróneos de conectarse a un recurso compartido con una contraseña incorrecta, el recurso compartido se bloqueará y no podrá conectarse durante 15 minutos.
 
Los intentos de conexión erróneos pueden incluir procesos en segundo plano, como reintentos, que es posible que no tenga en cuenta.

> [!NOTE]
> Si tiene un dispositivo antiguo con la versión 4.0 o anterior de Data Box, la cuenta se bloquea durante 30 minutos después de tres intentos de inicio de sesión erróneos.

**Descripción del error.** En función de cómo acceda al recurso compartido, verá uno de los siguientes errores:

- Si intenta conectarse desde el equipo host a través de SMB, verá un error que dice que la cuenta a la que se hace referencia está bloqueada actualmente y es posible que no haya iniciado sesión en ella.

  En el ejemplo siguiente se muestra la salida de un intento de conexión de este tipo.

  ```
  C:\Users\Databoxuser>net use \\10.100.100.10\mydbresources_BlockBlob /u:10.100.100.10\mydbresources
  Enter the password for '10.100.100.10\mydbresources' to connect to '10.100.100.10':
  System error 1909 has occurred.
  
  The referenced account is currently locked out and may not be logged on to.
  ```

- Si usa el servicio de copia de datos, verá la siguiente notificación en la interfaz de usuario web local del dispositivo:

  ![Captura de pantalla del panel Notificaciones en la interfaz de usuario web local de un dispositivo Data Box. Se resalta una notificación para una cuenta de recurso compartido bloqueada.](media/data-box-troubleshoot-share-access/share-lock-01.png)


**Resolución sugerida.** Para conectarse a un recurso compartido SMB después del bloqueo de una cuenta de recurso compartido, siga estos pasos:

1. Compruebe las credenciales SMB del recurso compartido. En la interfaz de usuario web local del dispositivo, vaya a **Conectar y copiar**, y seleccione **SMB** para el recurso compartido. Verá el siguiente cuadro de diálogo.

    ![Captura de pantalla de la pantalla de acceso a recurso compartido y de copia de datos de un recurso compartido SMB en un dispositivo Data Box. Los iconos de copia de la cuenta, el nombre de usuario y la contraseña están resaltados.](media/data-box-troubleshoot-share-access/get-share-credentials-01.png)

1. Una vez que finalice el período de bloqueo (15 minutos o media hora), el bloqueo desaparecerá. Ahora puede conectarse al recurso compartido.

   - Para conectarse al recurso compartido desde el equipo host a través de SMB, ejecute el siguiente comando. Para ver un procedimiento, consulte [Copia de datos en Data Box mediante](data-box-deploy-copy-data.md#connect-to-data-box).
  
     `net use \\<IP address of the device>\<share name> /u:<IP address of the device>\<user name for the share>`

   - Para conectarse a un recurso compartido mediante el servicio de copia de datos, compruebe si hay una notificación que indique que la cuenta de usuario se ha desbloqueado, como se muestra a continuación. En el panel **Copiar datos**, ahora puede [copiar datos en Data Box](data-box-deploy-copy-data-via-copy-service.md#copy-data-to-data-box).

     ![Captura de pantalla del panel Copiar datos de la interfaz de usuario web local de Data Box. Notificación de que la cuenta de usuario del recurso compartido se desbloqueó y la opción Copia de datos está resaltada.](media/data-box-troubleshoot-share-access/share-lock-02.png)


## <a name="check-for-a-blocking-group-policy"></a>Comprobación de una directiva de grupo que produce el bloqueo

Compruebe si una directiva de grupo en el equipo cliente o host le impide conectarse al recurso compartido. Si es posible, mueva el equipo cliente o host a una unidad organizativa (OU) que no tenga aplicado ningún objeto de directiva de grupo (GPO).

Para asegurarse de que ninguna directiva de grupo impide el acceso a los recursos compartidos en Data Box:

* Asegúrese de que su equipo cliente o host esté en su propia unidad organizativa de Active Directory.

* Asegúrese de que no se aplica ningún GPO al equipo cliente o host. Puede bloquear la herencia para asegurarse de que el equipo cliente o host (nodo secundario) no herede automáticamente los GPO del nodo primario. Para más información, consulte [Bloqueo de la herencia](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11)).

## <a name="check-for-permissions-issues"></a>Comprobación de problemas de permisos

Si no hay ningún problema de dominio y ninguna directiva de grupo bloquea el acceso al recurso compartido, revise los registros de auditoría y los registros de eventos de seguridad para comprobar si hay problemas de permisos en el dispositivo.

### <a name="review-security-event-logs"></a>Revisión de los registros de eventos de seguridad

Revise los registros de eventos de seguridad de Windows en el dispositivo para ver si hay errores que indiquen un error de autenticación.

Puede revisar los registros de eventos `Smbserver.Security` en la carpeta `etw` o ver los errores de seguridad en el Visor de eventos.

Para revisar los registros de eventos de seguridad de Windows del Visor de eventos, siga estos pasos:

1. Para abrir el Visor de eventos de Windows, en la pantalla **Inicio**, escriba **Visor de eventos**.

1. En el panel de navegación del Visor de eventos, expanda **Registros de Windows** y seleccione la carpeta **Seguridad**.

    ![Captura de pantalla del Visor de eventos de Windows que muestra los eventos de seguridad. La carpeta Windows y la subcarpeta Seguridad están resaltadas.](media/data-box-troubleshoot-share-access/event-viewer-01.png)

3. Busque los errores siguientes:

    Error 1:

    ```xml
    SMB Session Authentication Failure
    Client Name: \\<ClientIP>
    Client Address: <ClientIP:Port>
    User Name:
    Session ID: 0x100000000021
    Status: The attempted logon is invalid. This is either due to a bad username or authentication information. (0xC000006D)
    SPN: session setup failed before the SPN could be queried
    SPN Validation Policy: SPN optional / no validation
    ```
      
    Error 2:
    ```xml
     LmCompatibilityLevel value is different from the default.
    Configured LM Compatibility Level: 5
    Default LM Compatibility Level: 3   
    ```

    Cualquiera de los errores indica que debe cambiar el nivel de autenticación de LAN Manager en el dispositivo.
 
### <a name="change-lan-manager-authentication-level"></a>Cambio del nivel de autenticación de LAN Manager
 
Para cambiar el nivel de autenticación de LAN Manager en el dispositivo, puede [usar la directiva de seguridad local](#use-local-security-policy) o [actualizar el Registro directamente](#update-the-registry).

#### <a name="use-local-security-policy"></a>Uso de la directiva de seguridad local

Para cambiar el nivel de autenticación de LAN Manager mediante la directiva de seguridad local, siga estos pasos:
 
1. Para abrir la directiva de seguridad local, en la pantalla **Inicio**, escriba `secpol.msc` y, luego, presione Entrar.

1. Vaya a **Directivas locales** > **Opciones de seguridad** y abra **Seguridad de red: Nivel de autenticación de LAN Manager**.

    ![Captura de pantalla que muestra las opciones de seguridad en el editor de directivas de seguridad locales. Se resalta la directiva "Seguridad de red: Nivel de autenticación de LAN Manager".](media/data-box-troubleshoot-share-access/security-policy-01.png)

1. Cambie el valor a **Enviar solo respuesta NTLMv2. Rechazar LM y NTLM**.

    ![Captura de pantalla que muestra la directiva "Seguridad de red: nivel de autenticación de LAN Manager" en el editor de directivas de seguridad locales. La opción "Enviar solo respuesta NTLMv2. Rechazar LM y NTLM" está resaltada.](media/data-box-troubleshoot-share-access/security-policy-02.png)

#### <a name="update-the-registry"></a>Actualizar el registro

Si no puede cambiar el nivel de autenticación de LAN Manager en la directiva de seguridad local, actualice el Registro directamente.

Para actualizar el Registro directamente, siga estos pasos:

1. Para abrir el Editor del Registro (regedit32.exe), en la pantalla **Inicio**, escriba `regedt32` y, luego, presione Entrar.

1. Vaya a HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Control > LSA.

    ![Captura de pantalla que muestra el Editor del Registro con la carpeta LSA resaltada.](media/data-box-troubleshoot-share-access/security-policy-03.png)

1. En la carpeta LSA, abra la clave del Registro LMCompatibilityLevel y cambie su valor a 5.

    ![Captura de pantalla del cuadro de diálogo que se usa para cambiar la clave LmcompatibilityLevel en el Registro. El campo Información del valor está resaltado.](media/data-box-troubleshoot-share-access/security-policy-04.png)

1. Reinicie el equipo para que los cambios del Registro surtan efecto.

## <a name="next-steps"></a>Pasos siguientes

- [Copia de datos mediante SMB](data-box-deploy-copy-data.md).
- [Solución de problemas de copia de datos en Data Box](data-box-troubleshoot.md).
- [Póngase en contacto con el servicio de soporte técnico de Microsoft](data-box-disk-contact-microsoft-support.md).