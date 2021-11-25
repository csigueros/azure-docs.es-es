---
title: 'Azure AD Connect: Historial de lanzamiento de versiones | Microsoft Docs'
description: En este artículo se muestran todas las versiones de Azure AD Connect y Sincronización de Azure AD.
author: billmath
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 10/21/2021
ms.subservice: hybrid
ms.author: rodejo
ms.custom: has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: de2b1f3a6eb44802d074c813881702072e61339f
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397649"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Historial de lanzamiento de versiones
El equipo de Azure Active Directory (Azure AD) actualiza periódicamente Azure AD Connect con nuevas características y funcionalidades. No todas las adiciones son aplicables a todas las audiencias.

Este artículo está diseñado para ayudarle a realizar un seguimiento de las versiones que se han publicado y para reconocer cuáles son los cambios en la última versión.

## <a name="looking-for-the-latest-versions"></a>¿Busca las últimas versiones?

Puede actualizar el servidor de AAD Connect desde todas las versiones admitidas a las últimas versiones:

 - Si usa **Windows Server 2016 o posterior**, debe usar **Azure AD Connect V2.0**. Para descargar la versión más reciente de Azure AD Connect 2.0, utilice [este vínculo](https://www.microsoft.com/en-us/download/details.aspx?id=47594). Las notas de la versión para la última versión V2.0 están disponibles [aquí](reference-connect-version-history.md#20280).
 - Si sigue usando una **versión anterior de Windows Server**, debe usar **Azure AD Connect V1.6**. Puede descargar la versión más reciente de Azure AD Connect V1 mediante [este vínculo](https://www.microsoft.com/download/details.aspx?id=103336). Las notas de la versión para la última versión V1.6 están disponibles [aquí](reference-connect-version-history.md#16160).
 - A partir de ahora solo se aplican cambios críticos a las versiones V1.x, y es posible que no encuentre algunas de las características y correcciones de V2.0 en los lanzamientos V1.x, por lo que debe actualizar a la versión V2.0 lo antes posible. En concreto, hay un problema por el que la actualización a esta compilación v1.1.16.4.2 o cualquier compilación más reciente restablece el límite al grupo a 50 000. Cuando un servidor se actualiza a esta compilación o cualquier compilación 1.6 más reciente, el cliente debe volver a aplicar los cambios de reglas que aplicaron al aumentar inicialmente el límite de pertenencia a grupos a 250 000 antes de habilitar la sincronización para el servidor. 

Esta tabla es una lista de temas relacionados:

Tema |  Detalles
--------- | --------- |
Pasos para actualizar desde Azure AD Connect | Diferentes métodos para [actualizar desde una versión anterior a la última](how-to-upgrade-previous-version.md) versión de Azure AD Connect.
Permisos necesarios | Para más información sobre los permisos necesarios para aplicar una actualización, consulte [cuentas y permisos](reference-connect-accounts-permissions.md#upgrade).


>[!IMPORTANT]
> **El 31 de agosto de 2022, se retirarán todas las versiones 1.x de Azure Active Directory (Azure AD) Connect porque incluyen componentes de SQL Server 2012 que ya no se admiten.** Actualice a la versión más reciente de Azure AD Connect (versión 2.x) en esa fecha o [evalúe y cambie a la sincronización en la nube de Azure AD](../cloud-sync/what-is-cloud-sync.md).
> 
> Debe asegurarse de que está ejecutando una versión reciente de Azure AD Connect para lograr una experiencia de soporte técnico óptima. 
> 
> Si ejecuta una versión retirada de Azure AD Connect, puede dejar de funcionar de forma inesperada y es posible que usted no tenga las últimas correcciones de seguridad, mejoras de rendimiento, herramientas de diagnóstico y solución de problemas, y mejoras de servicio. Asimismo, si necesita soporte técnico, es posible que no podamos proporcionarle el nivel de servicio que requiere su organización.
> 
> Vaya a este artículo para obtener más información sobre [Azure Active Directory Connect V2.0](whatis-azure-ad-connect-v2.md), lo que ha cambiado en V2.0 y cómo le afecta este cambio.
>
> Consulte [este artículo](./how-to-upgrade-previous-version.md) para obtener más información sobre cómo actualizar Azure AD Connect a la versión más reciente.
>
> Para obtener información sobre el historial de versiones retiradas, consulte [Archivo del historial de lanzamiento de versiones de Azure AD Connect](reference-connect-version-history-archive.md).

>[!NOTE]
>Lanzar una versión nueva de Azure AD Connect es un proceso que requiere varios pasos de control de calidad para garantizar la funcionalidad operativa del servicio y, mientras realizamos este proceso, tanto el número de cada versión nueva como su estado se actualizan para reflejar los cambios más recientes.
No todas las versiones de Azure AD Connect estarán disponibles para la actualización automática. El estado de lanzamiento indicará si una versión está disponible para la actualización automática o solo para la descarga. Si la actualización automática estaba habilitada en el servidor de Azure AD Connect, dicho servidor se actualizará automáticamente a la versión más reciente de Azure AD Connect que se lanza para la actualización automática. Tenga en cuenta que no todas las configuraciones de Azure AD Connect son aptas para la actualización automática. 

>Para aclarar el uso de la actualización automática, está diseñada para que insertar todas las actualizaciones importantes y las correcciones críticas. No se trata necesariamente de la versión más reciente, ya que no todas las versiones requerirán o incluirán la corrección para un problema de seguridad crítico (solo un ejemplo de muchos). Las incidencias de este tipo usualmente se abordan con una versión nueva proporcionada a través de la actualización automática. Si no hay ningún problema de ese tipo, no se insertan actualizaciones mediante la actualización automática y, en general, si usa la versión de actualización automática más reciente, no debería haber problema.
Sin embargo, si quiere obtener las características y actualizaciones más recientes, la mejor manera de ver si hay alguna es revisar esta página e instalarlas como considere oportuno. 

>Siga este vínculo para más información acerca de la [actualización automática](how-to-connect-install-automatic-upgrade.md)


## <a name="16160"></a>1.6.16.0
>[!NOTE] 
>Este es un lanzamiento de actualización de Azure AD Connect. Esta versión está pensada para los clientes que ejecutan una versión anterior de Windows Server y no pueden actualizar su servidor a Windows Server 2016 o a una versión posterior en estos momentos. No puede usar esta versión para actualizar un servidor de Azure AD Connect V2.0. Esta versión no se admite en Windows Server 2016 ni posteriores. Esta versión incluye componentes de SQL Server 2012 y se va a retirar el 31 de agosto de 2022. Debe actualizar el sistema operativo del servidor y la versión de AADConnect antes de esa fecha.
>Hay un problema por el que la actualización a esta compilación v1.6 o cualquier compilación más reciente restablece el límite de pertenencia a grupos a 50 000. Cuando un servidor se actualiza a esta compilación o cualquier compilación 1.6 más reciente, el cliente debe volver a aplicar los cambios de reglas que aplicaron al aumentar inicialmente el límite de pertenencia a grupos a 250 000 antes de habilitar la sincronización para el servidor. 

### <a name="release-status"></a>Estado de la versión
13/10/2021: publicada para descarga y actualización automática.

### <a name="bug-fixes"></a>Corrección de errores
- Se ha corregido un error que consistía en que el proceso de actualización automática intentaba actualizar los servidores de AADConnect con una versión del sistema operativo Windows anterior a la 2008 o 2008 R2 y generaba un error. Estas versiones de Windows Server ya no se admiten. En esta versión solo se intenta realizar la actualización automática en máquinas con Windows Server 2012 o posterior.
- Se ha corregido un problema que consistía en que, en determinadas condiciones, miisserver se bloqueaba debido a una excepción de infracción de acceso.

### <a name="known-issues"></a>Problemas conocidos
 - Hay un problema por el que la actualización a esta compilación v1.6 o cualquier compilación más reciente restablece el límite de pertenencia a grupos a 50 000. Cuando un servidor se actualiza a esta compilación o cualquier compilación 1.6 más reciente, el cliente debe volver a aplicar los cambios de reglas que aplicaron al aumentar inicialmente el límite de pertenencia a grupos a 250 000 antes de habilitar la sincronización para el servidor. 

## <a name="20280"></a>2.0.28.0

>[!NOTE] 
> Esto es una versión de actualización de mantenimiento de Azure AD Connect. Esta versión requiere Windows Server 2016 o posterior.

### <a name="release-status"></a>Estado de la versión
30/09/2021: publicado solo para descargas, no está disponible para actualización automática.

### <a name="bug-fixes"></a>Corrección de errores
 - En la página Permisos de escritura diferida de grupos del Asistente se ha quitado un botón de descarga de un script de PowerShell y se ha cambiado el texto de la página del asistente para incluir un vínculo de más información, que vincula a un artículo en línea donde se puede encontrar el script de PowerShell.

 - Se ha corregido un error por el que el asistente bloqueaba incorrectamente la instalación cuando la versión de .NET en el servidor era posterior a la versión 4.6, debido a que faltaban claves del Registro. Esas claves del Registro no son necesarias y solo deben bloquear la instalación si se establecen intencionadamente en false.

- Se ha corregido un error por el que se generaba un error si se encontraban objetos fantasma durante la inicialización de un paso de sincronización. Esto bloqueaba el paso de sincronización o quitaba objetos transitorios. Ahora se omiten los objetos fantasma.
Nota: Un objeto fantasma es un marcador de posición para un objeto que no está ahí o que aún no se ha visto, por ejemplo, si un objeto de origen tiene una referencia para un objeto de destino que no está allí, se crea el objeto de destino como fantasma.

### <a name="functional-changes"></a>Cambios funcionales

 - Se ha realizado un cambio que permite a un usuario anular la selección de objetos y atributos de la lista de inclusión, incluso si están en uso. En lugar de bloquear esto, ahora se proporciona una advertencia. 

## <a name="16142"></a>1.6.14.2
>[!NOTE] 
>Este es un lanzamiento de actualización de Azure AD Connect. Esta versión está pensada para los clientes que ejecutan una versión anterior de Windows Server y no pueden actualizar su servidor a Windows Server 2016 o a una versión posterior en estos momentos. No puede usar esta versión para actualizar un servidor de Azure AD Connect V2.0.
>Comenzaremos por actualizar automáticamente los inquilinos aptos cuando esta versión esté disponible para su descarga; la actualización automática tardará unas semanas en completarse.
>Hay un problema por el que la actualización a esta compilación v1.6 o cualquier compilación más reciente restablece el límite de pertenencia a grupos a 50 000. Cuando un servidor se actualiza a esta compilación o cualquier compilación 1.6 más reciente, el cliente debe volver a aplicar los cambios de reglas que aplicaron al aumentar inicialmente el límite de pertenencia a grupos a 250 000 antes de habilitar la sincronización para el servidor. 


### <a name="release-status"></a>Estado de la versión
21/9/2021: publicado para descarga y actualización automática.

### <a name="functional-changes"></a>Cambios funcionales
 - Hemos agregado las versiones más recientes de los conectores MIM (1.1.1610.0). Puede encontrar más información en la [página del historial de lanzamientos de los conectores MIM](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#1116100-september-2021).
 - Hemos agregado una opción de configuración para deshabilitar la característica de coincidencia parcial en Azure AD Connect. Recomendamos a los clientes que deshabiliten la coincidencia parcial a menos que la necesiten para admitir cuentas exclusivas de la nube. En [este artículo](/powershell/module/msonline/set-msoldirsyncfeature#example-2--block-soft-matching-for-the-tenant) se explica cómo deshabilitar la coincidencia parcial.

### <a name="bug-fixes"></a>Corrección de errores
 - Se ha corregido un error por el que la configuración de DesktopSSO no se conservaba después de la actualización desde una versión anterior.
 - Se ha corregido un error que provocaba un error en los cmdlets Set-ADSync\*Permission.

## <a name="20251"></a>2.0.25.1

>[!NOTE] 
> Esto es una versión de actualización de revisión de Azure AD Connect. Este lanzamiento requiere Windows Server 2016 o posterior y corrige un problema de seguridad que está presente en la versión 2.0 de Azure AD Connect, así como otras correcciones de errores.

### <a name="release-status"></a>Estado de la versión
14/09/2021: publicado solo para descargas, no está disponible para actualización automática.

### <a name="bug-fixes"></a>Corrección de errores

 - Se ha corregido un problema de seguridad en el que se usaba una ruta de acceso sin comillas para apuntar al servicio de Azure AD Connect. Esta ruta de acceso es ahora una ruta de acceso entre comillas.
 - Se ha corregido un problema de configuración de importación con la escritura diferida habilitada cuando se utiliza la cuenta del conector de AD existente.
 - Se ha corregido un problema en Set-ADSyncExchangeHybridPermissions y otros cmdlets relacionados, que estaban rotos desde la versión 1.6 debido a un tipo de herencia no válido.
 - El cmdlet que publicamos en un lanzamiento anterior para establecer la versión de TLS tenía un problema por el que sobrescribía las claves, lo que destruía los valores que había en ellas. Esto se ha corregido mediante la creación de una clave nueva solo si aún no hay una. También se ha agregado una advertencia para que los usuarios sepan que los cambios del registro TLS no son exclusivos de Azure AD Connect y que pueden afectar también a otras aplicaciones en el mismo servidor.
 - Se ha agregado una comprobación para aplicar la actualización automática a V2.0 y que requiera Windows Server 2016 o posterior.
 - Hemos agregado el permiso "Replicar cambios de directorio" en el cmdlet Set-ADSyncBasicReadPermissions.
 - Hemos realizado un cambio para evitar que UseExistingDatabase y la configuración de importación se utilicen juntos, ya que podrían contener opciones de configuración en conflicto.
 - Hemos realizado un cambio para permitir que un usuario con el rol de administrador de la aplicación cambie la configuración del servicio de proxy de aplicación.
 - Hemos quitado la etiqueta "(Preview)" de las etiquetas de la configuración de Import/Export: esta funcionalidad ha estado en disponibilidad general desde hace ya un tiempo.
 - Cambiamos algunas etiquetas que todavía hacen referencia a "Administrador de empresa": ahora utilizamos el nombre de rol "Administrador global".
 - Hemos creado nuevos cmdlets de PowerShell de Kerberos de AAD "\*-AADKerberosServer" para agregar una regla de transformación de notificaciones a la entidad de servicio de AAD.

### <a name="functional-changes"></a>Cambios funcionales
 - Hemos agregado las versiones más recientes de los conectores MIM (1.1.1610.0). Puede encontrar más información en la [página del historial de lanzamientos de los conectores MIM](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#1116100-september-2021).
 - Hemos agregado una opción de configuración para deshabilitar la característica de coincidencia parcial en Azure AD Connect. Recomendamos a los clientes que deshabiliten la coincidencia parcial a menos que la necesiten para admitir cuentas exclusivas de la nube. En [este artículo](/powershell/module/msonline/set-msoldirsyncfeature#example-2--block-soft-matching-for-the-tenant) se explica cómo deshabilitar la coincidencia parcial.

## <a name="20100"></a>2.0.10.0

### <a name="release-status"></a>Estado de la versión
19/08/2021: publicada solo para descargas, no está disponible para actualización automática.

>[!NOTE] 
>Esto es una versión de actualización de revisión de Azure AD Connect. Esta versión requiere Windows Server 2016 o posterior. Esta revisión soluciona un problema que está presente en la versión 2.0, así como en Azure AD Connect versión 1.6. Si está ejecutando Azure AD Connect en un servidor de Windows anterior, debe instalar la compilación [1.6.13.0](#16130) en su lugar.

### <a name="release-status"></a>Estado de la versión
19/08/2021: publicada solo para descargas, no está disponible para actualización automática.

### <a name="known-issues"></a>Problemas conocidos
 - En determinadas circunstancias, el instalador de esta versión mostrará un error que menciona que TLS 1.2 no está habilitado y detendrá la instalación. Esto se debe a un error en el código que comprueba la configuración del Registro para TLS 1.2. Lo vamos a corregir en un lanzamiento futuro. Los clientes que ven este problema deben seguir las instrucciones para habilitar TLS 1.2, que se pueden encontrar en el artículo "[Aplicar TLS 1.2 para Azure AD Connect](reference-connect-tls-enforcement.md)".

### <a name="bug-fixes"></a>Corrección de errores

 - Se ha corregido un error por el que, cuando se cambiaba el nombre de un dominio, la sincronización de hash de contraseñas generaba un error que indicaba que una conversión especificada no es válida en el registro de eventos. Se trata de una regresión de compilaciones anteriores.

## <a name="16130"></a>1.6.13.0
>[!NOTE] 
>Esto es una versión de actualización de revisión de Azure AD Connect. Esta versión está pensada para los clientes que ejecutan Azure AD Connect en un servidor con Windows Server 2012 o 2012 R2.

19/08/2021: publicada solo para descargas, no está disponible para actualización automática.

### <a name="bug-fixes"></a>Corrección de errores

 - Se ha corregido un error por el que, cuando se cambiaba el nombre de un dominio, la sincronización de hash de contraseñas generaba un error que indicaba que una conversión especificada no es válida en el registro de eventos. Se trata de una regresión de compilaciones anteriores.

### <a name="functional-changes"></a>Cambios funcionales
No hay cambios funcionales en esta versión.

## <a name="2090"></a>2.0.9.0

### <a name="release-status"></a>Estado de la versión
17/08/2021: publicada solo para descargas, no está disponible para actualización automática.

### <a name="bug-fixes"></a>Corrección de errores
>[!NOTE] 
>Esto es una versión de actualización de revisión de Azure AD Connect. Esta versión requiere Windows Server 2016 o posterior. Esta versión soluciona un problema que está presente en la versión 2.0.8.0, pero no en la versión 1.6 de Azure AD Connect.

 - Se ha corregido un error por el que, al sincronizar un gran número de transacciones de sincronización de hash de contraseña, la longitud de entrada del registro de eventos supera la longitud máxima permitida para una entrada de un evento de sincronización de hash de contraseña. Ahora se divide una entrada de registro larga en varias entradas.

## <a name="2080"></a>2.0.8.0
>[!NOTE] 
>Esto es una versión de actualización de seguridad de Azure AD Connect. Esta versión requiere Windows Server 2016 o posterior. Si usa una versión anterior de Windows Server, use la [versión 1.6.11.3](#16113).
>Esta versión aborda una vulnerabilidad como se documenta en [este CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949). Para obtener más información sobre esta vulnerabilidad, consulte el CVE.
>Para descargar la versión más reciente de Azure AD Connect 2.0, utilice [este vínculo](https://www.microsoft.com/en-us/download/details.aspx?id=47594).

### <a name="release-status"></a>Estado de la versión
10/08/2021: publicada solo para descargas, no está disponible para actualización automática. 

### <a name="functional-changes"></a>Cambios funcionales
No hay cambios funcionales en esta versión.

## <a name="16113"></a>1.6.11.3 
>[!NOTE] 
>Esto es una versión de actualización de seguridad de Azure AD Connect. Esta versión está pensada para los clientes que ejecutan una versión anterior de Windows Server y no pueden actualizar su servidor a Windows Server 2016 o a una versión posterior en estos momentos. No puede usar esta versión para actualizar un servidor de Azure AD Connect V2.0.
>Esta versión aborda una vulnerabilidad como se documenta en [este CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949). Para obtener más información sobre esta vulnerabilidad, consulte el CVE.
>Puede descargar la versión más reciente de Azure AD Connect 1.6, utilice [este vínculo](https://www.microsoft.com/download/details.aspx?id=103336).

### <a name="release-status"></a>Estado de la versión
10/08/2021: publicada solo para descargas, no está disponible para actualización automática.

### <a name="functional-changes"></a>Cambios funcionales
No hay cambios funcionales en esta versión.

## <a name="2030"></a>2.0.3.0
>[!NOTE] 
>Se trata de una versión principal de Azure AD Connect. Consulte el artículo [Azure Active Directory V2.0](whatis-azure-ad-connect-v2.md) para obtener más información.

### <a name="release-status"></a>Estado de la versión
20/07/2021: publicada solo para descargas, no está disponible para actualización automática
### <a name="functional-changes"></a>Cambios funcionales
 - Se han actualizado los componentes de LocalDB para SQL Server a SQL 2019. 
 - Esta versión requiere Windows Server 2016 o una versión posterior debido a los requisitos de SQL Server 2019. Tenga en cuenta que no se admite una actualización local de Windows Server en un servidor de Azure AD Connect, por lo que puede que tenga que usar una [migración oscilante](how-to-upgrade-previous-version.md#swing-migration).
 -    En esta versión se aplica el uso de TLS 1.2. Si ha habilitado su servidor de Windows Server para TLS 1.2, AADConnect usará este protocolo. Si TLS 1.2 no está habilitado en el servidor, verá un mensaje de error al intentar instalar AADConnect y su instalación no continuará hasta que no habilite TLS 1.2. Tenga en cuenta que puede usar los nuevos cmdlets "Set-ADSyncToolsTls12" para habilitar TLS 1.2 en el servidor.
 -    Con esta versión, puede usar un usuario con el rol de usuario "Administrador de identidad híbrida" para autenticarse al instalar Azure AD Connect. Ya no necesita el rol de administrador global.
 - Se ha actualizado la biblioteca en tiempo de ejecución de Visual C++ a la versión 14 como requisito previo para SQL Server 2019    
 -    En esta versión se usa la biblioteca MSAL para la autenticación y se ha quitado la antigua biblioteca ADAL, que se retirará en 2022.    
 -    De acuerdo con la guía de seguridad de Windows, ya no se aplica ningún permiso para AdminSDHolders. Se ha cambiado el parámetro "SkipAdminSdHolders" a "IncludeAdminSdHolders" en el módulo ADSyncConfig.psm1.
 -    Ahora, las contraseñas se volverán a evaluar cuando una contraseña expirada pase a estar "no expirada", independientemente de si se ha cambiado la contraseña. Si para un usuario la contraseña está establecida en "Debe cambiar la contraseña en el siguiente inicio de sesión" y esta marca se borra (es decir, se anula la expiración de la contraseña), el estado "no expirada" y el hash de contraseña se sincronizan con Azure AD. De este modo, cuando el usuario intente iniciar sesión en Azure AD, podrá usar la contraseña no expirada.
Para sincronizar una contraseña expirada de Active Directory con Azure Active Directory, use la característica de [sincronización de contraseñas temporales](how-to-connect-password-hash-synchronization.md#synchronizing-temporary-passwords-and-force-password-change-on-next-logon) de Azure AD Connect. Tenga en cuenta que tendrá que habilitar la escritura diferida de contraseñas para usar esta característica, por lo que la contraseña que se actualiza se reescribe también en Active Directory.
 - Se han agregado dos nuevos cmdlets al módulo ADSyncTools para habilitar o recuperar la configuración de TLS 1.2 de Windows Server. 
   - Get-ADSyncToolsTls12
   - Set-ADSyncToolsTls12
   
Puede usar estos cmdlets para recuperar el estado de habilitación de TLS 1.2 o configurarlo según sea necesario. Tenga en cuenta que TLS 1.2 debe estar habilitado en el servidor para que la instalación o AADConnect se ejecuten correctamente.

 -    Se ha renovado ADSyncTools con varios cmdlets nuevos y mejorados. En el [artículo sobre ADSyncTools](reference-connect-adsynctools.md) se ofrecen más detalles de estos cmdlets. 
  Los siguientes cmdlets se han agregado o actualizado: 
    - Clear-ADSyncToolsMsDsConsistencyGuid    
    - ConvertFrom-ADSyncToolsAadDistinguishedName    
    - ConvertFrom-ADSyncToolsImmutableID    
    - ConvertTo-ADSyncToolsAadDistinguishedName    
    - ConvertTo-ADSyncToolsCloudAnchor    
    - ConvertTo-ADSyncToolsImmutableID    
    - Export-ADSyncToolsAadDisconnectors    
    - Export-ADSyncToolsObjects    
    - Export-ADSyncToolsRunHistory    
    - Get-ADSyncToolsAadObject    
    - Get-ADSyncToolsMsDsConsistencyGuid    
    - Import-ADSyncToolsObjects    
    - Import-ADSyncToolsRunHistory    
    - Remove-ADSyncToolsAadObject    
    - Search-ADSyncToolsADobject    
    - Set-ADSyncToolsMsDsConsistencyGuid    
    - Trace-ADSyncToolsADImport    
    - Trace-ADSyncToolsLdapQuery    
-    Ahora se usa el punto de conexión V2 para la importación y la exportación, y se ha corregido el problema del cmdlet Get-ADSyncAADConnectorExportApiVersion. Puede leer más información sobre el punto de conexión V2 en el artículo [API de punto de conexión de Azure AD Connect Sync V2](how-to-connect-sync-endpoint-api-v2.md).
-    Se han agregado las siguientes propiedades de usuario nuevas para la sincronización entre AD local y Azure AD:    
    - employeeType    
    - employeeHireDate    
-    Esta versión requiere la instalación de PowerShell 5.0 o una versión posterior en Windows Server. Tenga en cuenta que esta versión forma parte de Windows Server 2016 y versiones más recientes.    
-    Se han aumentado los límites de sincronización de grupos a un máximo de 250 000 miembros con el nuevo punto de conexión V2.
-    El conector LDAP genérico y el conector SQL genérico se han actualizado a las versiones más recientes. Obtenga más información sobre estos conectores aquí:
    - [Documentación de referencia del conector LDAP genérico](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap)
    - [Documentación de referencia del conector SQL genérico](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql)
-    En el Centro de administración de M365, ahora se informa de la versión del cliente de AADConnect cada vez que hay una actividad de exportación a Azure AD. Esto garantiza que el Centro de administración de M365 siempre tiene la versión más actualizada del cliente de AADConnect y puede detectar cuándo se usa una versión obsoleta.

### <a name="bug-fixes"></a>Corrección de errores
- Se ha corregido un error de accesibilidad en el que el lector de pantalla anunciaba un rol incorrecto del vínculo "Más información".
-    Se ha corregido un error por el que las reglas de sincronización con valores de precedencia grandes (por ejemplo, 387163089) provocaban errores en la actualización. Se ha actualizado el procedimiento almacenado "mms_UpdateSyncRulePrecedence" para convertir el número de precedencia en un entero antes de incrementar el valor.
-    Se ha corregido un error por el que los permisos de escritura diferida de grupos no se establecen en la cuenta de sincronización al importar una configuración de escritura diferida de grupos. Ahora se establecen los permisos de escritura diferida de grupos si esta está habilitada en la configuración importada.
-    Se ha actualizado el agente de Azure AD Connect Health a la versión 3.1.110.0 para corregir un error de instalación.
-    Se está observando un problema con los atributos no predeterminados de las configuraciones exportadas en las que los atributos de extensión de directorio están configurados. Al importar estas configuraciones a un nuevo servidor o una nueva instalación, la lista de inclusión de atributos se invalida en el paso de configuración de la extensión de directorio. De este modo, después de la importación, solo los atributos predeterminados y de extensión de directorio se seleccionan en el administrador de servicios de sincronización (los atributos no predeterminados no se incluyen en la instalación, por lo que el usuario debe volver a habilitarlos manualmente desde el administrador de servicios de sincronización si quiere que sus reglas de sincronización importadas funcionen). Ahora se actualiza el conector de AAD antes de configurar la extensión de directorio para mantener los atributos existentes de la lista de inclusión de atributos.
-    Se ha corregido un problema de accesibilidad por el que el grosor de la fuente del encabezado de página está establecido en "Delgado". Ahora el grosor de la fuente se establece en "Negrita" para el título de página, y esto se aplica al encabezado de todas las páginas.
-    El nombre de la función Get-AdObject en ADSyncSingleObjectSync.ps1 se ha cambiado a Get-AdDirectoryObject para evitar confusiones con el cmdlet de AD.
-    La función de SQL "mms_CheckSynchronizationRuleHasUniquePrecedence" permite la precedencia de duplicados en las reglas de sincronización de salida en diferentes conectores. Se ha quitado la condición que permite la precedencia de reglas duplicadas.
-    Se ha corregido un error por el que el cmdlet de sincronización de un solo objeto falla cuando los datos del flujo de atributos son NULL, por ejemplo, al exportar una operación de eliminación.    
-    Se ha corregido un error por el que la instalación no se realiza correctamente debido a que no se puede iniciar el servicio de arranque de ADSync. Ahora se ha agrega la cuenta de servicio de sincronización al grupo de usuarios local integrado antes de iniciar el servicio de arranque.
-    Se ha corregido un problema de accesibilidad por el que la pestaña activa del asistente para AAD Connect no mostraba el color correcto en el tema de contraste alto. El código de color seleccionado se sobrescribía debido a una condición que faltaba en la configuración del código de color normal.
-    Se ha solucionado un problema por el que los usuarios tenían permiso para anular la selección de objetos y atributos usados en las reglas de sincronización mediante la interfaz de usuario y PowerShell. Ahora se muestra un mensaje de error descriptivo si intenta anular la selección de cualquier atributo u objeto que se use en las reglas de sincronización.
-    Se han realizado algunas actualizaciones en el "código de configuración de la migración" para comprobar y corregir el problema de compatibilidad con versiones anteriores cuando el script se ejecuta en una versión anterior de Azure AD Connect.    
-    Se ha corregido un error por el cual, cuando PHS intenta buscar un objeto incompleto, no usa el mismo algoritmo para resolver el controlador de dominio que el que usó originalmente para capturar las contraseñas. En concreto, ignora la información asociada con el controlador de dominio. La búsqueda de objetos incompletos debe usar la misma lógica para buscar el controlador de dominio en ambas instancias.
-    Se ha corregido un error por el que AADConnect no podía leer los elementos de Application Proxy mediante Microsoft Graph debido a un problema de permisos al llamar a Microsoft Graph directamente en función del identificador de cliente de AAD Connect. Para solucionar este problema, se ha quitado la dependencia de Microsoft Graph y, en su lugar, ahora se usa AAD PowerShell para trabajar con los objetos de aplicación de Application Proxy.
-    Se ha quitado el límite de miembros de escritura diferida para la regla de sincronización "Out to AD - Group SOAInAAD Exchange".    
-    Se ha corregido un error por el cual, si el cambio de los permisos de la cuenta del conector involucra un objeto que no ha cambiado desde la última importación diferencial, no se importará con una importación diferencial. Ahora se muestra una advertencia que alerta al usuario del problema.
-    Se ha corregido un problema de accesibilidad por el que el lector de pantalla no lee la posición del botón de radio. Se ha agregado información sobre la posición al campo de texto de accesibilidad del botón de radio.
-    Se ha actualizado el pack del agente de autenticación de paso a través. El pack anterior no incluía la URL de respuesta correcta para la aplicación propia de HIP en US Gov.    
-    Se ha corregido un error por el que se producía una excepción "stopped-extension-dll-exception" al exportar el conector de AAD después de realizar una instalación limpia de la versión 1.6.X.X de AADConnect (la cual usa de manera predeterminada DirSyncWebServices API V2) mediante una base de datos existente.    Anteriormente, solo se establecía la versión de exportación en v2 para una actualización; esto se ha cambiado para que también se establezca en una instalación limpia.
-    El módulo "ADSyncPrep.psm1" ya no se usa y se ha quitado de la instalación.

### <a name="known-issues"></a>Problemas conocidos
-    El asistente de AADConnect muestra la opción "Importar configuración de sincronización" como "Versión preliminar", pero esta característica está disponible con carácter general.
-    Es posible que algunos conectores de Active Directory se instalen en un orden diferente cuando se usa la salida del script de configuración de la migración para instalar el producto. 
-    La página de opciones de inicio de sesión del usuario en el asistente para Azure AD Connect indica el rol "Administrador de empresa". Este término ya no se usa y debe reemplazarse por "Administrador global".
-    La opción "Exportar configuración" no funciona cuando la opción de inicio de sesión se ha configurado para usar PingFederate.
-    Aunque Azure AD Connect se puede implementar con el rol "Administrador de identidad híbrida", para configurar el autoservicio de restablecimiento de contraseña, seguirá siendo necesario que el usuario tenga el rol "Administrador global".
-    Al importar la configuración de AADConnect durante la implementación para conectarse con un inquilino diferente al de la configuración original de AADConnect, los atributos de extensión de directorio no se configuran correctamente.

## <a name="1640"></a>1.6.4.0

>[!NOTE]
> La API de punto de conexión de Sincronización de Azure AD Connect V2 ya está disponible en estos entornos de Azure:
> - Azure Commercial
> - Nube de Azure China
> - Nube de Azure US Government
> - Esta versión no estará disponible en la nube de Azure en alemán.

### <a name="release-status"></a>Estado de la versión
31/03/2021: publicado solo para descargas, no está disponible para actualización automática

### <a name="bug-fixes"></a>Correcciones de errores
- En esta versión se corrige un error en la versión 1.6.2.4 donde, después de la actualización a esa versión, la característica Azure AD Connect Health no se registró correctamente y no funcionó. Se solicita a los clientes que han implementado 1.6.2.4 de compilación que actualicen su servidor de Azure AD Connect con esta compilación, que registrará correctamente la característica de mantenimiento. 

## <a name="1624"></a>1.6.2.4
>[!IMPORTANT]
> Actualización del 30 de marzo de 2021: se ha detectado un problema en esta compilación. Después de la instalación de esta compilación, no se registran los servicios de mantenimiento. Se recomienda no instalar esta compilación. Publicaremos una corrección en breve.
> Si ya ha instalado esta compilación, puede registrar manualmente los servicios de mantenimiento mediante el cmdlet, tal como se muestra en [este artículo](./how-to-connect-health-agent-install.md#manually-register-azure-ad-connect-health-for-sync)

>[!NOTE]
> - Esta versión estará disponible solo para descarga.
> - Para actualizar a esta versión, será necesaria una sincronización completa debido a los cambios de la regla de sincronización.
> - En esta versión, el valor predeterminado del servidor AADConnect es el nuevo punto de conexión V2.

### <a name="release-status"></a>Estado de la versión
19/03/2021: publicado solo para descargas, no está disponible para actualización automática

### <a name="functional-changes"></a>Cambios funcionales

 - Se han actualizado las reglas de sincronización predeterminadas para limitar la pertenencia en los grupos reescritos a 50 000 miembros.
   - Se han agregado nuevas reglas de sincronización predeterminadas para limitar el recuento de pertenencias en la reescritura de grupos (Out to AD - Group Writeback Member Limit) y la sincronización de grupos a los grupos de Azure Active Directory (Out to AAD - Group Writeup Member Limit).
   - Se ha agregado el atributo de miembro a la regla "Out to AD - Group SOAInAAD - Exchange" para limitar los miembros de los grupos que se reescriben a 50 000.
 - Se han actualizado las reglas de sincronización para admitir la escritura diferida de grupos v2. - Si se clona la regla "In from AAD - Group SOAInAAD" y se actualiza AADConnect.
     - La regla actualizada se deshabilitará de manera predeterminada, por lo que el valor de targetWritebackType será NULL.
     - AADConnect reescribirá todos los grupos en la nube (incluidos los grupos de seguridad de Azure Active Directory habilitados para la reescritura) como grupos de distribución.
   -Si se clona la regla "Out to AD - Group SOAInAAD" y se actualiza AADConnect.
     - La regla actualizada se deshabilitará de forma predeterminada. Sin embargo, se habilitará una nueva regla de sincronización "Out to AD - Group SOAInAAD - Exchange", que se agrega.
     - En función de la prioridad de la regla de sincronización personalizada clonada, AADConnect proporcionará los atributos de correo y de Exchange.
     - Si la regla de sincronización personalizada clonada no proporciona algunos atributos de correo y de Exchange, la nueva regla de sincronización de Exchange agregará esos atributos.
     - Tenga en cuenta que la característica de escritura diferida de grupos V2 se encuentra en versión preliminar privada en este momento y no está disponible públicamente.
 - Se ha agregado compatibilidad con la [sincronización selectiva de hash de contraseña](./how-to-connect-selective-password-hash-synchronization.md).
 - Se ha agregado el nuevo [cmdlet de sincronización de objetos únicos](./how-to-connect-single-object-sync.md). Use este cmdlet para solucionar problemas de la configuración de sincronización de Azure AD Connect. 
 -  Azure AD Connect admite ahora el rol de administrador de identidades híbridas para configurar el servicio.
 - Se ha actualizado el agente de AADConnectHealth a 3.1.83.0.
 - Nueva versión del [módulo ADSyncTools de PowerShell](./reference-connect-adsynctools.md), que tiene varios cmdlets nuevos o mejorados. 
 
   - Clear-ADSyncToolsMsDsConsistencyGuid
   - ConvertFrom-ADSyncToolsAadDistinguishedName
   - ConvertFrom-ADSyncToolsImmutableID
   - ConvertTo-ADSyncToolsAadDistinguishedName
   - ConvertTo-ADSyncToolsCloudAnchor
   - ConvertTo-ADSyncToolsImmutableID
   - Export-ADSyncToolsAadDisconnectors
   - Export-ADSyncToolsObjects
   - Export-ADSyncToolsRunHistory
   - Get-ADSyncToolsAadObject
   - Get-ADSyncToolsMsDsConsistencyGuid
   - Import-ADSyncToolsObjects
   - Import-ADSyncToolsRunHistory
   - Remove-ADSyncToolsAadObject
   - Search-ADSyncToolsADobject
   - Set-ADSyncToolsMsDsConsistencyGuid
   - Trace-ADSyncToolsADImport
   - Trace-ADSyncToolsLdapQuery

 - Se ha actualizado el registro de errores en relación con los errores de adquisición de tokens.
 - Se han actualizado los vínculos "Más información" de la página de configuración para proporcionar más detalles sobre la información vinculada.
 - Se ha quitado la columna explícita de la página de búsqueda de CS en la interfaz de usuario de sincronización antigua.
 - Se ha agregado una interfaz de usuario adicional al flujo de reescritura de grupos para solicitar credenciales al usuario o para que configure sus propios permisos mediante el módulo ADSyncConfig, si aún no se han proporcionado credenciales en un paso anterior.
 - Creación automática de MSA para la cuenta de servicio de ADSync en un controlador de dominio. 
 - Se ha agregado la capacidad para establecer y obtener la característica de reescritura de grupos V2 de DirSync de Azure Active Directory en los cmdlets existentes:
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - Se han agregado dos cmdlets para leer la versión de API de AWS.
    - Get-ADSyncAADConnectorImportApiVersion: para obtener la versión de API de AWS de importación.
    - Get-ADSyncAADConnectorImportApiVersion: para obtener la versión de API de AWS de exportación.

 - Ahora, se hace el seguimiento de los cambios efectuados en las reglas de sincronización para ayudar a solucionar los problemas de los cambios en el servicio. El cmdlet "Get-ADSyncRuleAudit" recuperará los cambios de los que se ha realizado un seguimiento.
 - Se ha actualizado el cmdlet Add-ADSyncADDSConnectorAccount en el [módulo ADSyncConfig de PowerShell](./how-to-connect-configure-ad-ds-connector-account.md#using-the-adsyncconfig-powershell-module) para permitir que un usuario del grupo ADSyncAdmin cambie la cuenta del conector de AD DS. 

### <a name="bug-fixes"></a>Corrección de errores
 - Se ha actualizado el color de primer plano deshabilitado para satisfacer los requisitos de luminosidad sobre un fondo blanco. Se han agregado condiciones adicionales para que el árbol de navegación establezca el color del texto de primer plano en blanco cuando se selecciona una página deshabilitada para satisfacer los requisitos de luminosidad.
 - Aumento de la granularidad del cmdlet Set-ADSyncPasswordHashSyncPermissions: se ha actualizado el script de permisos PHS (Set-ADSyncPasswordHashSyncPermissions) para incluir un parámetro opcional "ADobjectDN". 
 - Corrección de errores de accesibilidad. El lector de pantalla ahora describe el elemento de la experiencia de usuario que contiene la lista de bosques como "**lista de bosques**" en lugar de "**lista de listas de bosques**".
 - Se ha actualizado la salida del lector de pantalla de algunos elementos del asistente de Azure AD Connect. Se ha actualizado el color de desplazamiento del botón para satisfacer los requisitos de contraste. Se ha actualizado el color del título de Synchronization Service Manager para satisfacer los requisitos de contraste.
 - Se ha corregido un problema con la instalación de AADConnect a partir de la configuración exportada con atributos de extensión personalizados: se ha agregado una condición para omitir la comprobación de atributos de extensión en el esquema de destino al aplicar la regla de sincronización.
 - Si la característica de reescritura de grupos está habilitada, se agregan los permisos adecuados en la instalación.
 - Corrección de la prioridad de la regla de sincronización predeterminada duplicada al importar.
 - Se ha corregido un problema que provocaba un error de almacenamiento provisional durante la importación diferencial de la API v2 para un objeto conflictivo que se ha reparado mediante el portal de mantenimiento.
 - Se ha corregido un problema en el motor de sincronización que hacía que los objetos de CS tuvieran un estado de vínculo incoherente.
 - Se han agregado contadores de importación a la salida de Get-ADSyncConnectorStatistics.
 - Se ha corregido el problema de deselección de dominios no accesibles (se seleccionaban anteriormente) en algunos casos excepcionales durante el asistente de pass2.
 - Se ha modificado la importación y exportación de directivas para que se genere un error si la regla personalizada tiene precedencia duplicada. 
 - Se ha corregido un error en la lógica de selección de dominios.
 - Corrige un problema con la compilación 1.5.18.0 si usa mS-DS-ConsistencyGuid como delimitador de origen y ha clonado la regla "In from AD - Group Join".
 - Las nuevas instalaciones de AADConnect usarán el umbral de eliminación de exportación almacenado en la nube si hay uno disponible y no se ha pasado otro.
 - Se ha corregido un problema donde AADConnect no lee los cambios del nombre para mostrar de AD de los dispositivos híbridos unidos.

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Estado de la versión
29/07/2020: publicado para descarga.

### <a name="functional-changes"></a>Cambios funcionales
Se trata de una versión de corrección de errores. No hay cambios funcionales en esta versión.

### <a name="fixed-issues"></a>Problemas corregidos

- Se ha corregido un problema por el que el administrador no podía habilitar el "Inicio de sesión único de conexión directa" si la cuenta de equipo AZUREADSSOACC ya está presente en la instancia de "Active Directory".
- Se ha corregido un problema que provocaba un error de almacenamiento provisional durante la importación diferencial de la API v2 para un objeto conflictivo que se ha reparado mediante el portal de mantenimiento.
- Se ha corregido un problema en la configuración de importación y exportación, en el que una regla personalizada deshabilitada se importaba como habilitada.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](whatis-hybrid-identity.md).
