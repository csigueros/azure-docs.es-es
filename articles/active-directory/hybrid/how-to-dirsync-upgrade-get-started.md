---
title: 'Azure AD Connect: actualización desde DirSync | Microsoft Docs'
description: Aprenda a actualizar desde DirSync a Azure AD Connect. En este artículo, se describen los pasos para actualizar desde DirSync a Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: baf52da7-76a8-44c9-8e72-33245790001c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3278e210daf1995366ff4d18cf5a3d3d8f7b344d
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2021
ms.locfileid: "110538937"
---
# <a name="azure-ad-connect-upgrade-from-dirsync"></a>Azure AD Connect: Actualización desde DirSync
Azure AD Connect es el sucesor de DirSync. En este tema, se explican distintas formas de realizar actualizaciones desde DirSync. Estos pasos no funcionan para la actualización desde otra versión de Azure AD Connect ni desde Sincronización de Azure AD.

DirSync y Sincronización de Azure AD no se admiten y ya no funcionarán. Si los sigue usando, DEBE actualizar a AADConnect para reanudar el proceso de sincronización.

Antes de empezar a instalarlo, debe [descargar Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) y completar los pasos de los requisitos previos que se indican en [Azure AD Connect: Hardware y requisitos previos](how-to-connect-install-prerequisites.md). En concreto, lea la información acerca de las siguientes áreas, ya que son diferentes de DirSync:

* La versión necesaria de .NET y PowerShell. Se requiere que en el servidor se encuentren versiones más recientes de las que necesitaba DirSync.
* La configuración del servidor proxy. Si se usa un servidor proxy para conectarse a Internet, este valor debe configurarse antes de realizar la actualización. DirSync siempre utiliza el servidor proxy configurado para el usuario que lo instala, pero Azure AD Connect usa la configuración de la máquina en su lugar.
* Las direcciones URL que deben estar abiertas en el servidor proxy. Para los escenarios básicos, los que también son compatibles con DirSync, los requisitos son los mismos. Si desea utilizar cualquiera de las nuevas características que se incluyen con Azure AD Connect, es preciso abrir nuevas direcciones URL.

> [!NOTE]
> Una vez que haya habilitado el nuevo servidor de Azure AD Connect para que empiece a sincronizar los cambios en Azure AD, no debe volver a usar DirSync o Sincronización de Azure AD. No de admite el cambio a una versión anterior de Azure AD Connect a los clientes heredados, como DirSync y Sincronización de Azure AD, y puede causar problemas, como la pérdida de datos en Azure AD.

Si no va a realizar la actualización desde DirSync, consulte la documentación relacionada sobre otros escenarios.

## <a name="upgrade-from-dirsync"></a>Actualización desde DirSync
Dependiendo de su implementación actual de DirSync, existen diferentes opciones para la actualización. Si el tiempo de actualización esperado es inferior a tres horas, se recomienda realizar una actualización local. Sin embargo, si es superior, se recomienda realizar una implementación paralela en otro servidor. Se calcula que si tiene más de 50.000 objetos, la actualización tarda más de 3 horas en realizarse.

| Escenario |
| --- |
| [Actualización local](#in-place-upgrade) |
| [Implementación paralela](#parallel-deployment) |

> [!NOTE]
> Cuando planee la actualización de DirSync a Azure AD Connect, no desinstale DirSync antes de la actualización. Azure AD Connect leerá y migrará la configuración de DirSync y lo desinstalará después de inspeccionar el servidor.

**Actualización local**  
El asistente muestra el tiempo previsto para completar la actualización. Este cálculo se basa en la suposición de que se tardan tres horas en completar una actualización de una base de datos con 50 000 objetos (usuarios, contactos y grupos). Si el número de objetos que hay en la base de datos es inferior a 50 000, Azure AD Connect recomienda una actualización local. Si decide continuar, la configuración actual se aplica automáticamente en la actualización y el servidor reanuda automáticamente la sincronización activa.

Si desea realizar una migración de la configuración y una implementación paralela, puede anular la recomendación de actualización local. Por ejemplo, podría aprovechar para actualizar el hardware y el sistema operativo. Para más información, consulte la sección [Implementación paralela](#parallel-deployment).

**Implementación paralela**  
Si tiene más de 50.000 objetos se recomienda usar una implementación paralela. Esta implementación evita que los usuarios sufran retrasos operativos. La instalación de Azure AD Connect intenta calcular el tiempo de inactividad previsto para la actualización pero, si ya ha actualizado antes DirSync, probablemente su propia experiencia sea la mejor referencia.

### <a name="supported-dirsync-configurations-to-be-upgraded"></a>Configuraciones de DirSync que se pueden actualizar
La actualización de DirSync admite los siguientes cambios de configuración:

* Filtrado por dominio y unidad organizativa
* Identificador alternativo (UPN)
* Sincronización de contraseñas y configuración híbrida de Exchange
* Configuración de bosque/dominio y Azure AD
* Filtrado basado en atributos de usuario

El siguiente cambio no se puede actualizar. Si tiene esta configuración, la actualización se bloquea:

* Cambios de DirSync no admitidos, como por ejemplo, los atributos eliminados y el uso de un archivo DLL de extensión personalizado

![Actualización bloqueada](./media/how-to-dirsync-upgrade-get-started/analysisblocked.png)

En esos casos, se recomienda instalar un servidor de Azure AD Connect nuevo en [modo provisional](how-to-connect-sync-staging-server.md) y comprobar la configuración antigua de DirSync y la nueva de Azure AD Connect. Vuelva a aplicar los cambios usando la configuración personalizada, como se describe en el artículo sobre la [configuración personalizada de la sincronización de Azure AD Connect](how-to-connect-sync-whatis.md).

Las contraseñas que usa DirSync para las cuentas de servicio no se pueden recuperar y no se migran. Estas contraseñas se restablecen durante la actualización.

### <a name="high-level-steps-for-upgrading-from-dirsync-to-azure-ad-connect"></a>Pasos generales de la actualización de DirSync a Connect de Azure AD
1. Bienvenida a Azure AD Connect
2. Análisis de la configuración actual de DirSync
3. Recopilación de la contraseña de administrador global de Azure AD
4. Recopilación de credenciales para una cuenta de administrador de empresa (solo se usa durante la instalación de Azure AD Connect)
5. Instalación de Azure AD Connect
   * Desinstalar DirSync (o deshabilitarlo temporalmente)
   * Instalación de Azure AD Connect
   * Opcionalmente, iniciar la sincronización

Se requieren pasos adicionales cuando:

* Actualmente usa una instalación completa de SQL Server (local o remota)
* Tiene más de 50.000 objetos en el ámbito para sincronización

## <a name="in-place-upgrade"></a>Actualización local
1. Inicie el instalador de Azure AD Connect (MSI).
2. Revise y acepte los términos de licencia y el aviso de privacidad.  
   ![Azure AD](./media/how-to-dirsync-upgrade-get-started/Welcome.png)
3. Haga clic en Siguiente para realizar el análisis de la instalación existente de DirSync.  
   ![Análisis de la instalación de Directory Sync existente](./media/how-to-dirsync-upgrade-get-started/Analyze.png)
4. Cuando el análisis se complete, verá recomendaciones de cómo proceder.  
   * Si usa SQL Server Express y tiene menos de 50.000 objetos, se muestra la siguiente pantalla:  
     ![Análisis completado listo para actualizar desde DirSync](./media/how-to-dirsync-upgrade-get-started/AnalysisReady.png)
   * Si usa una instancia completa de SQL Server para DirSync, verá esta página en su lugar:  
     ![Captura de pantalla que muestra el servidor de SQL Database existente que se usa.](./media/how-to-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
     , que muestra la información del servidor de base de datos existente de SQL Server que usa DirSync. Si es necesario, realice los ajustes adecuados. Haga clic en **Siguiente** para continuar con la instalación.
   * Si tiene más de 50 000 objetos, verá esta pantalla en su lugar:  
     ![Captura de pantalla que muestra la pantalla que se ve cuando se tienen más de 50 000 objetos para actualizar.](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
     Para continuar con una actualización local, haga clic en la casilla que hay al lado de este mensaje: **Continue upgrading DirSync on this computer** (Continuar actualizando DirSync en este equipo).
     Sin embargo, para realizar una [parallel deployment](#parallel-deployment) , exporte la configuración de DirSync a un nuevo servidor.
5. Proporcione la contraseña para la cuenta que utiliza actualmente para conectarse a Azure AD. Debe ser la cuenta que DirSync está usando actualmente.  
   ![Escriba sus credenciales de Azure AD](./media/how-to-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
   Si aparece un error y tiene problemas de conectividad, consulte [Solución de problemas de conectividad](tshoot-connect-connectivity.md).
6. Proporcione una cuenta de administrador de empresa para Active Directory.  
   ![Escriba sus credenciales de ADDS](./media/how-to-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Ahora está preparado para realizar la configuración. Al hacer clic en **Actualizar**, se desinstala DirSync, se configura Azure AD Connect y comienza la sincronización.  
   ![Listo para configurar](./media/how-to-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Una vez completada la instalación, cierre la sesión e iníciela de nuevo en Windows antes de usar Synchronization Service Manager o el Editor de reglas de sincronización. También puede tratar de realizar cualquier otro cambio en la configuración.

## <a name="parallel-deployment"></a>Implementación paralela
### <a name="export-the-dirsync-configuration"></a>Exportación de la configuración de DirSync
**Implementación paralela con más de 50.000 objetos**

Si tiene más de 50 000 objetos, la instalación de Azure AD Connect recomienda una implementación paralela.

Se muestra una pantalla similar a la siguiente:  
![Análisis completo](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Si desea realizar una implementación paralela, debe seguir estos pasos:

* Haga clic en el botón **Exportar configuración** . Al instalar Azure AD Connect en un servidor independiente, esta configuración se migra desde su DirSync actual a la nueva instalación de Azure AD Connect.

Una vez exportada la configuración correctamente, puede salir del asistente de Azure AD Connect en el servidor DirSync. Continúe con el paso siguiente para instalar Azure AD Connect en un servidor independiente.

**Implementación paralela con menos de 50.000 objetos**

Si tiene menos de 50.000 objetos pero aún así desea realizar una implementación paralela, haga lo siguiente:

1. Ejecute el instalador de Azure AD Connect (MSI).
2. Cuando aparezca la pantalla **Bienvenido a Azure AD Connect** , salga del asistente para instalación haciendo clic en la "X", en la esquina superior derecha de la ventana.
3. Abra un símbolo del sistema.
4. Desde la ubicación de instalación de Azure AD Connect (ruta de acceso predeterminada: C:\Archivos de programa\Microsoft Azure Active Directory Connect) ejecute el siguiente comando: `AzureADConnect.exe /ForceExport`.
5. Haga clic en el botón **Exportar configuración** . Al instalar Azure AD Connect en un servidor independiente, esta configuración se migra desde su DirSync actual a la nueva instalación de Azure AD Connect.

![Captura de pantalla que muestra la opción de exportación de la configuración para migrar la configuración a la nueva instalación de Azure AD Connect.](./media/how-to-dirsync-upgrade-get-started/forceexport.png)

Una vez exportada la configuración correctamente, puede salir del asistente de Azure AD Connect en el servidor DirSync. Continúe con el paso siguiente para instalar Azure AD Connect en un servidor independiente.

### <a name="install-azure-ad-connect-on-separate-server"></a>Instalación de Azure AD Connect en un servidor independiente
Al instalar Azure AD Connect en un nuevo servidor, se presupone que se desea realizar una instalación limpia de Azure AD Connect. Puesto que va a usar la configuración de DirSync, hay que realizar varios pasos adicionales:

1. Ejecute el instalador de Azure AD Connect (MSI).
2. Cuando aparezca la pantalla **Bienvenido a Azure AD Connect** , salga del asistente para instalación haciendo clic en la "X", en la esquina superior derecha de la ventana.
3. Abra un símbolo del sistema.
4. Desde la ubicación de instalación de Azure AD Connect (ruta de acceso predeterminada: C:\Archivos de programa\Microsoft Azure Active Directory Connect) ejecute el siguiente comando: `AzureADConnect.exe /migrate`.
   Se inicia el Asistente para instalación de Azure AD Connect y muestra la siguiente pantalla:  
   ![Captura de pantalla que muestra dónde se debe importar el archivo de configuración al actualizar.](./media/how-to-dirsync-upgrade-get-started/ImportSettings.png)
5. Seleccione el archivo de configuración exportado desde la instalación de DirSync.
6. Configure las opciones avanzadas, que se incluyen:
   * Una ubicación de instalación personalizada para Azure AD Connect.
   * Una instancia existente de SQL Server (valor predeterminado: Azure AD Connect instala SQL Server 2012 Express). No use la misma instancia de base de datos como servidor de DirSync.
   * Una cuenta de servicio usada para conectarse a SQL Server (si la base de datos de SQL Server es remota, esta cuenta debe ser una cuenta de servicio de dominio).
     Estas opciones se pueden ver en esta pantalla:  
     ![Captura de pantalla que muestra las opciones de configuración avanzada para actualizar desde DirSync.](./media/how-to-dirsync-upgrade-get-started/advancedsettings.png)
7. Haga clic en **Next**.
8. En la página **Listo para configurar**, deje seleccionada la opción **Inicie el proceso de sincronización en cuanto se complete la configuración**. El servidor está ya en [modo provisional](how-to-connect-sync-staging-server.md), por lo que los cambios no se exportan a Azure AD.
9. Haga clic en **Instalar**.
10. Una vez completada la instalación, cierre la sesión e iníciela de nuevo en Windows antes de usar Synchronization Service Manager o el Editor de reglas de sincronización. También puede tratar de realizar cualquier otro cambio en la configuración.

> [!NOTE]
> Se inicia la sincronización entre Windows Server Active Directory y Azure Active Directory, pero los cambios que se realizan no se exportan a Azure AD. Solo una herramienta de sincronización puede a exportar activamente los cambios de una vez. Este estado se denomina [modo provisional](how-to-connect-sync-staging-server.md).

### <a name="verify-that-azure-ad-connect-is-ready-to-begin-synchronization"></a>Comprobación de que Azure AD Connect está listo para comenzar la sincronización
Para comprobar si Azure AD Connect está listo para asumir el control de DirSync, es preciso abrir **Synchronization Service Manager** en el grupo **Azure AD Connect**, desde el menú Inicio.

En la aplicación, vaya a la pestaña **Operations** (Operaciones). Confirme en ella que se han completado las siguientes operaciones:

* Importación en AD Connector
* Importación en Azure AD Connector
* Sincronización completa en AD Connector
* Sincronización completa en Azure AD Connector

![Importación y sincronización completadas](./media/how-to-dirsync-upgrade-get-started/importsynccompleted.png)

Revise el resultado de estas operaciones y asegúrese de que no hay errores.

Si desea ver y comprobar los cambios que están a punto de exportarse a Azure AD, lea cómo comprobar la configuración en el [modo provisional](how-to-connect-sync-staging-server.md). Realice los cambios de configuración necesarios hasta que no vea nada inesperado.

Cuando haya completado estos pasos y el resultado le parezca satisfactorio estará listo para cambiar de DirSync a Azure AD.

### <a name="uninstall-dirsync-old-server"></a>Desinstalación de DirSync (servidor antiguo)
* En **Programas y características**, busque **Herramienta de sincronización de Microsoft Azure Active Directory**
* Desinstale la **Herramienta de sincronización de Microsoft Azure Active Directory**
* La desinstalación puede tardar hasta 15 minutos en completarse.

Si prefiere desinstalar DirSync más adelante, puede apagar el servidor temporalmente o deshabilitar el servicio. Si se produce algún problema, este método le permite volver a habilitarlo. Sin embargo, no es habitual que el paso siguiente produzca algún error, por lo que no debería ser necesario.

Si DirSync se ha desinstalado o deshabilitado, no hay servidores activos que exporten a Azure AD. El siguiente paso para habilitar Azure AD Connect se debe completar antes de los cambios que se realicen de su instalación local de Active Directory se sincronicen con Azure AD.

### <a name="enable-azure-ad-connect-new-server"></a>Apertura de Azure AD Connect (nuevo servidor)
Después de la instalación, si vuelve a abrir Azure AD Connect, podrá realizar cambios adicionales en la configuración. Inicie **Azure AD Connect** desde el menú Inicio o desde el acceso directo del escritorio. Asegúrese de no intentar ejecutar de nuevo el MSI de instalación.

Verá lo siguiente:  
![Tareas adicionales](./media/how-to-dirsync-upgrade-get-started/AdditionalTasks.png)

* Seleccione **Configurar modo de almacenamiento provisional**.
* Desactive la casilla **Modo provisional habilitado** para deshabilitar el modo provisional.

![Captura de pantalla que muestra la opción para habilitar el modo de almacenamiento provisional.](./media/how-to-dirsync-upgrade-get-started/configurestaging.png)

* Haga clic en el botón **Siguiente** .
* En la página de confirmación, haga clic en **Instalar** .

Azure AD Connect es el servidor activo y no debe volver a usar el servidor de sincronización de directorios existente.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha instalado Azure AD Connect, puede [comprobar la instalación y asignar licencias](how-to-connect-post-installation.md).

Obtenga más información acerca de estas nuevas características, que se habilitaron con la instalación: [Actualización automática](how-to-connect-install-automatic-upgrade.md), [Evitar eliminaciones involuntarias](how-to-connect-sync-feature-prevent-accidental-deletes.md) y [Azure AD Connect Health](how-to-connect-health-sync.md).

Obtenga información acerca de estos temas habituales: [el programador y cómo desencadenar la sincronización](how-to-connect-sync-feature-scheduler.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](whatis-hybrid-identity.md).
