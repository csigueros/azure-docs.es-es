---
title: Solución de problemas de sincronización de hash de contraseñas con la sincronización de Azure AD Connect | Microsoft Docs
description: En este artículo se ofrece información sobre cómo solucionar problemas de sincronización de hash de contraseñas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a796aff2c0882bd07b12a2f6fa6c0ab3eb793834
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746128"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Solución de problemas de sincronización de hash de contraseñas con la sincronización de Azure AD Connect

En este tema se proporcionan los pasos para solucionar problemas relacionados con la sincronización de hash de contraseñas. Si las contraseñas no se sincronizan como se esperaba, puede ser para un subconjunto de usuarios o para todos los usuarios.

Para la implementación de la versión 1.1.614.0 o posterior de Azure Active Directory (Azure AD) Connect, utilice la tarea de solución de problemas del asistente para solucionar problemas de sincronización de hash de contraseñas:

* Si tiene un problema con contraseñas que no se sincronizan, consulte la sección [No se sincronizan las contraseñas: solución de problemas mediante la tarea de solución de problemas](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task).

* Si tiene un problema con objetos individuales, consulte la sección [Un objeto no sincroniza contraseñas: solución de problemas mediante la tarea de solución de problemas](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task).

Para la implementación de la versión 1.1.524.0 u otra posterior, hay un cmdlet de diagnóstico que puede utilizar para solucionar problemas de sincronización de hash de contraseñas:

* Si tiene un problema con contraseñas que no se sincronizan, consulte la sección [No se sincronizan las contraseñas: solución de problemas mediante el cmdlet de diagnóstico](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet).

* Si tiene un problema con objetos individuales, consulte la sección [Un objeto no sincroniza contraseñas: solución de problemas mediante el cmdlet de diagnóstico](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet).

Para versiones anteriores de la implementación de Azure AD Connect:

* Si tiene un problema con contraseñas que no se sincronizan, consulte la sección [No se sincronizan las contraseñas: pasos para la solución manual de problemas](#no-passwords-are-synchronized-manual-troubleshooting-steps).

* Si tiene un problema con objetos individuales, consulte la sección [Un objeto no sincroniza contraseñas: pasos para la solución manual de problemas](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps).



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>No se sincronizan las contraseñas: solución de problemas mediante la tarea correspondiente

Puede usar la tarea de solución de problemas para averiguar por qué no se sincronizan las contraseñas.

> [!NOTE]
> La tarea de solución de problemas está disponible solo para Azure AD Connect versión 1.1.614.0 u otra posterior.

### <a name="run-the-troubleshooting-task"></a>Ejecución de la tarea de solución de problemas

Para solucionar problemas cuando no se sincronizan las contraseñas:

1. Abra una nueva sesión de Windows PowerShell en el servidor de Azure AD Connect mediante la opción **Ejecutar como administrador**.

2. Ejecute `Set-ExecutionPolicy RemoteSigned` o `Set-ExecutionPolicy Unrestricted`.

3. Inicie el asistente de Azure AD Connect.

4. Vaya a la página **Tareas adicionales**, seleccione **Solucionar problemas** y haga clic en **Siguiente**.

5. En la página de solución de problemas, haga clic en **Iniciar** para iniciar el menú de solución de problemas en PowerShell.

6. En el menú principal, seleccione **Solucionar problemas de sincronización de hash de contraseñas**.

7. En el submenú, seleccione **Password Synchronization does not work at all** (La sincronización de hash de contraseñas no funciona en absoluto).

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Descripción de los resultados de la tarea de solución de problemas

La tarea de solución de problemas realiza las siguientes comprobaciones:

* Valida que la característica de sincronización de hash de contraseñas esté habilitada para el inquilino de Azure AD.

* Valida que el servidor de Azure AD Connect no está en modo de almacenamiento provisional.

* Para cada conector Active Directory local (que corresponde a un bosque existente de Active Directory):

   * Valida que la característica de sincronización de hash de contraseñas esté habilitada.
   
   * Busca eventos de latido de sincronización de hash de contraseñas en los registros de eventos de aplicaciones de Windows.

   * Para cada dominio de Active Directory en el conector Active Directory local:

      * Valida que el dominio sea accesible desde el servidor de Azure AD Connect.

      * Valida que las cuentas de Servicios de Dominio de Active Directory (AD DS) que usa el conector Active Directory local tengan un nombre de usuario correcto y una contraseña y los permisos necesarios para la sincronización de hash de contraseñas.

El siguiente diagrama muestra los resultados del cmdlet para una topología de Active Directory local de un solo dominio:

![Salida de diagnóstico de la sincronización de hash de contraseñas](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

En el resto de esta sección se describen los resultados concretos que devuelve la tarea y los correspondientes problemas.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>La característica de sincronización de hash de contraseñas no está habilitada

Si no ha habilitado la sincronización de hash de contraseñas mediante el asistente de Azure AD Connect, se devuelve el siguiente error:

![La sincronización de hash de contraseñas no está habilitada](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>El servidor de Azure AD Connect está en modo de almacenamiento provisional

Si el servidor de Azure AD Connect está en modo de almacenamiento provisional, la sincronización de hash de contraseñas está deshabilitada temporalmente y se devuelve el siguiente error:

![El servidor de Azure AD Connect está en modo de almacenamiento provisional](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>No hay eventos de latido de sincronización de hash de contraseñas

Cada conector Active Directory local tiene su propio canal de sincronización de hash de contraseñas. Cuando se establece el canal de sincronización de hash de contraseñas y no hay cambios de contraseña que sincronizar, se genera un evento de latido (Id. de evento 654) una vez cada 30 minutos en el registro de eventos de aplicaciones de Windows. Para cada conector Active Directory local, el cmdlet busca los correspondientes eventos de latido de las últimas tres horas. Si no se encuentra ningún evento de latido, se devuelve el error siguiente:

![No hay eventos de latido de sincronización de hash de contraseñas](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>La cuenta de AD DS no tiene los permisos correctos

Si la cuenta de AD DS que usa el conector Active Directory local para sincronizar los hash de contraseña no tiene los permisos adecuados, se devuelve el error siguiente:

![Captura de pantalla que muestra el error que se devuelve cuando la cuenta de AD DS tiene un nombre de usuario o una contraseña incorrectos.](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Nombre de usuario o contraseña incorrectos de la cuenta de AD DS

Si la cuenta de AD DS que usa el conector Active Directory local para sincronizar los hash de contraseña tiene un nombre de usuario o una contraseña incorrectos, se devuelve el error siguiente:

![Credencial incorrecta](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Un objeto no sincroniza contraseñas: solución de problemas mediante la tarea correspondiente

Puede usar la tarea de solución de problemas para determinar por qué un objeto no sincroniza contraseñas.

> [!NOTE]
> La tarea de solución de problemas está disponible solo para Azure AD Connect versión 1.1.614.0 u otra posterior.

### <a name="run-the-diagnostics-cmdlet"></a>Ejecución del cmdlet de diagnóstico

Para solucionar problemas de un objeto de usuario específico:

1. Abra una nueva sesión de Windows PowerShell en el servidor de Azure AD Connect mediante la opción **Ejecutar como administrador**.

2. Ejecute `Set-ExecutionPolicy RemoteSigned` o `Set-ExecutionPolicy Unrestricted`.

3. Inicie el asistente de Azure AD Connect.

4. Vaya a la página **Tareas adicionales**, seleccione **Solucionar problemas** y haga clic en **Siguiente**.

5. En la página de solución de problemas, haga clic en **Iniciar** para iniciar el menú de solución de problemas en PowerShell.

6. En el menú principal, seleccione **Solucionar problemas de sincronización de hash de contraseñas**.

7. En el submenú, seleccione **Password is not synchronized for a specific user account** (La contraseña no está sincronizada para una cuenta de usuario específica).

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Descripción de los resultados de la tarea de solución de problemas

La tarea de solución de problemas realiza las siguientes comprobaciones:

* Examina el estado del objeto de Active Directory en el espacio conector Active Directory, el metaverso y el espacio conector Azure AD.

* Valida que haya reglas de sincronización con la sincronización de hash de contraseñas habilitada y se aplican al objeto de Active Directory.

* Intenta recuperar y mostrar los resultados del último intento de sincronización de la contraseña del objeto.

El siguiente diagrama muestra los resultados del cmdlet al solucionar problemas de sincronización de hash de contraseñas de un solo objeto:

![Salida de diagnóstico de la sincronización de hash de contraseñas: un solo objeto](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

En el resto de esta sección se describen los resultados concretos que devuelve el cmdlet y los correspondientes problemas.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>El objeto de Active Directory no se ha exportado a Azure AD

La sincronización de hash de contraseñas para esta cuenta de Active Directory local no se realiza porque no hay un objeto correspondiente en el inquilino de Azure AD. Se devuelve el siguiente error:

![Falta objeto de Azure AD](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>El usuario tiene una contraseña incorrecta

Actualmente, Azure AD Connect no admite la sincronización de contraseñas temporales con Azure AD. Una contraseña se considera temporal si la opción **Change password at next logon** (Cambiar la contraseña en el siguiente inicio de sesión) se establece como activada en el usuario de Active Directory local. Se devuelve el siguiente error:

![La contraseña temporal no se exporta](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>No hay resultados del último intento de sincronización de contraseña disponibles

De manera predeterminada, Azure AD Connect almacena los resultados de los intentos de sincronización de hash de contraseñas durante siete días. Si no hay ningún resultado disponible para el objeto de Active Directory seleccionado, se devuelve la advertencia siguiente:

![Salida de diagnóstico de un solo objeto: no hay historial de sincronización de contraseña](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>No se sincronizan las contraseñas: solución de problemas mediante el cmdlet de diagnóstico

Puede usar el cmdlet `Invoke-ADSyncDiagnostics` para averiguar por qué no se sincronizan las contraseñas.

> [!NOTE]
> El cmdlet `Invoke-ADSyncDiagnostics` solo está disponible para Azure AD Connect versión 1.1.524.0 o posterior.

### <a name="run-the-diagnostics-cmdlet"></a>Ejecución del cmdlet de diagnóstico

Para solucionar problemas cuando no se sincronizan las contraseñas:

1. Abra una nueva sesión de Windows PowerShell en el servidor de Azure AD Connect mediante la opción **Ejecutar como administrador**.

2. Ejecute `Set-ExecutionPolicy RemoteSigned` o `Set-ExecutionPolicy Unrestricted`.

3. Ejecute `Import-Module ADSyncDiagnostics`.

4. Ejecute `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Un objeto no sincroniza contraseñas: solución de problemas mediante el cmdlet de diagnóstico

Puede usar el cmdlet `Invoke-ADSyncDiagnostics` para determinar por qué un objeto no sincroniza contraseñas.

> [!NOTE]
> El cmdlet `Invoke-ADSyncDiagnostics` solo está disponible para Azure AD Connect versión 1.1.524.0 o posterior.

### <a name="run-the-diagnostics-cmdlet"></a>Ejecución del cmdlet de diagnóstico

Para solucionar problemas cuando no se sincronizan las contraseñas de un usuario:

1. Abra una nueva sesión de Windows PowerShell en el servidor de Azure AD Connect mediante la opción **Ejecutar como administrador**.

2. Ejecute `Set-ExecutionPolicy RemoteSigned` o `Set-ExecutionPolicy Unrestricted`.

3. Ejecute `Import-Module ADSyncDiagnostics`.

4. Ejecute el siguiente cmdlet:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Por ejemplo:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>No se sincronizan las contraseñas: pasos para la solución manual de problemas

Siga estos pasos para determinar por qué no se sincronizan las contraseñas:

1. ¿Está el servidor de Connect en [modo de almacenamiento provisional](how-to-connect-sync-staging-server.md)? Un servidor en modo de almacenamiento provisional no sincroniza contraseñas.

2. Ejecute el script de la sección [Obtención del estado de configuración de sincronización de contraseñas](#get-the-status-of-password-sync-settings). Le ofrece una visión general de la configuración de sincronización de contraseñas.  

    ![Salida del script de PowerShell de la configuración de sincronización de contraseñas](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Si la característica no está habilitada en Azure AD o si el estado del canal de sincronización no está habilitado, ejecute el Asistente para instalación de Connect. Seleccione **Personalizar las opciones de sincronización** y anule la selección de sincronización de contraseñas. Este cambio deshabilita temporalmente la característica. Después, vuelva a ejecutar al asistente y habilite de nuevo la sincronización de contraseñas. Vuelva a ejecutar el script para comprobar que la configuración es correcta.

4. Busque errores en el registro de eventos. Busque los siguientes eventos, que podrían indicar un problema:
    * Origen: Identificador de "sincronización de directorios": 0, 611, 652, 655. Si ve estos eventos, tiene un problema de conectividad. El mensaje de registro de eventos contiene información de bosques en los que hay un problema.

5. Si no ve ningún latido o si nada funciona, ejecute [Desencadenamiento de una sincronización completa de todas las contraseñas](#trigger-a-full-sync-of-all-passwords). Ejecute el script una sola vez.

6. Consulte la sección Solución de problemas de un objeto que no sincroniza contraseñas.

### <a name="connectivity-problems"></a>Problemas de conectividad

¿Hay conectividad con Azure AD?

¿La cuenta tiene los permisos necesarios para leer los valores de hash de contraseña en todos los dominios? Si instaló Connect con la configuración rápida, los permisos deben ser los correctos. 

Si realizó una instalación personalizada, establezca los permisos manualmente haciendo lo siguiente:
    
1. Para buscar la cuenta que usa el conector Active Directory, inicie **Synchronization Service Manager**. 
 
2. Vaya a **Conectores** y busque el bosque de Active Directory local cuyos problemas va a solucionar. 
 
3. Seleccione el conector y haga clic en **Propiedades**. 
 
4. Vaya a **Conexión al bosque de Active Directory**.  
    
    ![Cuenta que usa el conector Active Directory](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Apunte el nombre de usuario y el dominio donde está la cuenta.
    
5. Inicie **Usuarios y equipos de Active Directory** y compruebe que la cuenta encontrada anteriormente tiene los permisos siguientes establecidos en la raíz de todos los dominios del bosque:
    * Replicación de cambios de directorio
    * Replicación de todos los cambios de directorio

6. ¿Puede Azure AD Connect acceder a los controladores de dominio? Si el servidor de Connect no puede conectarse a todos los controladores de dominio, configure **Only use preferred domain controller** (Usar solo el controlador de dominio preferido).  
    
    ![Controlador de dominio que usa el conector Active Directory](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  

7. Vuelva a **Synchronization Service Manager** y **Configure Directory Partition** (Configurar partición de directorio). 
 
8. Seleccione el dominio en **Seleccionar particiones de directorio**, active la casilla **Only use preferred domain controller** (Usar solo el controlador de dominio preferido) y haga clic en **Configurar**. 

9. En la lista, escriba los controladores de dominio que Connect debe usar para realizar la sincronización de contraseñas. También se usa la misma lista para los procesos de importación y exportación. Siga estos pasos con todos los dominios.

> [!NOTE]
> Para aplicar estos cambios, reinicie el servicio **Microsoft Azure AD Sync** (ADSync).

10. Si el script muestra que no hay ningún latido, ejecute el script de [Desencadenamiento de una sincronización completa de todas las contraseñas](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Un objeto no sincroniza contraseñas: pasos para la solución manual de problemas

Puede solucionar fácilmente los problemas relacionados con la sincronización de hash de contraseñas si revisa el estado actual de un objeto.

1. En **Usuarios y equipos de Active Directory**, busque el usuario y compruebe que la casilla **El usuario debe cambiar la contraseña en el siguiente inicio de sesión** está desactivada.  

    ![Contraseñas productivas de Active Directory](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Si la casilla está activada, pida al usuario que inicie sesión y cambie la contraseña. Las contraseñas temporales no se sincronizan con Azure AD.

2. Si la contraseña parece correcta en Active Directory, siga al usuario en el motor de sincronización. Al seguir al usuario desde Active Directory local hasta Azure AD, puede ver si hay un error descriptivo en el objeto.

    a. Inicie el [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

    b. Haga clic en **Conectores**.

    c. Seleccione el **conector Active Directory** en el que se encuentra el usuario.

    d. Seleccione **Search Connector Space**(Buscar espacio de conector).

    e. En el cuadro **Ámbito**, seleccione **DN o delimitador** y escriba el DN completo del usuario cuyos problemas va a solucionar.

    ![Búsqueda de usuario en el espacio conector con DN](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Busque el usuario y haga clic en **Propiedades** para ver todos los atributos. Si el usuario no aparece en el resultado de búsqueda, compruebe las [reglas de filtrado](how-to-connect-sync-configure-filtering.md) y asegúrese de ejecutar [Aplicación y comprobación de los cambios](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) para que el usuario se muestre en Connect.

    g. Para ver los detalles de la sincronización de contraseñas del objeto de la semana pasada, haga clic en **Registro**.  

    ![Detalles del registro de objetos](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Si el registro de objetos está vacío, Azure AD Connect no ha sido capaz de leer el valor de hash de contraseña de Active Directory. Continúe solucionando problemas con Errores de conectividad. Si ve un valor distinto de **correcto**, consulte la tabla de [Registro de sincronización de contraseñas](#password-sync-log).

    h. Seleccione la pestaña **Linaje** y asegúrese de que al menos una de las reglas de sincronización de la columna **PasswordSync** está establecida en **True**. En la configuración predeterminada, el nombre de la regla de sincronización es **In from AD - User AccountEnabled**.  

    ![Información de linaje de un usuario](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Haga clic en **Metaverse Object Properties** (Propiedades del objeto de metaverso) para mostrar una lista de atributos de usuario.  

    ![Captura de pantalla que muestra la lista de atributos de usuario para las propiedades del objeto de metaverso.](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Compruebe que no hay ningún atributo **cloudFiltered**. Asegúrese de que los atributos de dominio (domainFQDN y domainNetBios) tienen los valores esperados.

    j. Haga clic en la pestaña **Conectores**. Asegúrese de que ve conectores para Active Directory local y Azure AD.

    ![Información de metaverso](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Seleccione la fila que representa Azure AD, haga clic en **Propiedades** y luego, en la pestaña **Linaje**. El objeto del espacio conector debe tener una regla de salida con la columna **PasswordSync** establecida en **True**. En la configuración predeterminada, el nombre de la regla de sincronización es **Out to AAD - User Join**.  

    ![Cuadro de diálogo de propiedades de objeto del espacio conector](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Registro de sincronización de contraseñas

La columna de estado puede presentar los siguientes valores:

| Estado | Descripción |
| --- | --- |
| Correcto |La contraseña se sincronizó correctamente. |
| FilteredByTarget |La contraseña se establece en **El usuario debe cambiar la contraseña en el siguiente inicio de sesión**. La contraseña no se ha sincronizado. |
| NoTargetConnection |No hay ningún objeto en el metaverso o en el espacio del conector de Azure AD. |
| SourceConnectorNotPresent |No se encontró ningún objeto en el espacio del conector de Active Directory local. |
| TargetNotExportedToDirectory |Aún no se exportó el objeto del espacio del conector de Azure AD. |
| MigratedCheckDetailsForMoreInfo |La entrada de registro se creó antes de la versión 1.0.9125.0 y se muestra en su estado heredado. |
| Error |El servicio devolvió un error desconocido. |
| Desconocido |Ha habido un error al intentar procesar un lote de valores hash de contraseña.  |
| MissingAttribute |Los atributos específicos (por ejemplo, hash de Kerberos) que requiere Azure AD Domain Services no están disponibles. |
| RetryRequestedByTarget |Los atributos específicos (por ejemplo, hash de Kerberos) que requiere Azure AD Domain Services no estaban disponibles anteriormente. Se intenta volver a sincronizar el valor hash de contraseña del usuario. |

## <a name="scripts-to-help-troubleshooting"></a>Scripts para ayudar a solucionar problemas

### <a name="get-the-status-of-password-sync-settings"></a>Obtención del estado de configuración de sincronización de contraseñas

```powershell
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Desencadenamiento de una sincronización completa de todas las contraseñas

> [!NOTE]
> Ejecute este script una sola vez. Si tiene que ejecutarlo varias veces, el problema se debe a otro motivo. Para solucionar el problema, contacte con Soporte técnico de Microsoft.

Puede desencadenar una sincronización completa de todas las contraseñas mediante el siguiente script:

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de la sincronización de hash de contraseñas con la sincronización de Azure AD Connect](how-to-connect-password-hash-synchronization.md)
* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](how-to-connect-sync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](whatis-hybrid-identity.md)
