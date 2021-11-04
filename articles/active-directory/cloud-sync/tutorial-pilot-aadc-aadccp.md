---
title: 'Tutorial: Prueba piloto de la sincronización en la nube de Azure AD Connect para un bosque de AD sincronizado que ya existe'
description: Aprenda a hacer una prueba piloto de la sincronización en la nube de un bosque de Active Directory de prueba que ya está sincronizado mediante la sincronización de la conexión de Azure Active Directory (Azure AD).
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff64e9778b035d43a7502fcf416ffe037ff54ffa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057616"
---
# <a name="pilot-cloud-sync-for-an-existing-synced-ad-forest"></a>Sincronización piloto en la nube para un bosque de AD sincronizado existente 

En este tutorial se explica como hacer una prueba piloto de la sincronización en la nube de un bosque de Active Directory de prueba que ya está sincronizado mediante la sincronización de la conexión de Azure Active Directory (Azure AD).

![Crear](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

## <a name="considerations"></a>Consideraciones
Antes de empezar con este tutorial, considere los siguientes elementos:
1. Asegúrese de que conoce los conceptos básicos de la sincronización en la nube. 
2. Asegúrese de que ejecuta la versión 1.4.32.0 de la sincronización de Azure AD Connect, o cualquier versión posterior, y de que ha configurado las reglas de sincronización como se ha documentado. Al realizar una prueba piloto, eliminará una unidad organizativa o grupo de prueba del ámbito de sincronización de Azure AD Connect. Sacar objetos del ámbito provoca la eliminación de esos objetos en Azure AD. En el caso de los objetos de usuario, los objetos de Azure AD se eliminan de manera temporal y se pueden restaurar. En el caso de los objetos de usuario, los objetos de Azure AD se eliminan de forma permanente y no se pueden restaurar. Se ha introducido un nuevo tipo de vínculo en la sincronización de Azure AD Connect que evitará la eliminación en un escenario de prueba piloto. 
3. Asegúrese de que los objetos del ámbito de la prueba ya contengan un valor de ms-ds-consistencyGUID para que la sincronización en la nube proporcione una coincidencia exacta de los objetos. 

   > [!NOTE]
   > La sincronización de Azure AD Connect no rellena *ms-ds-consistencyGUID* de forma predeterminada para los objetos del grupo.

4. Este es un escenario avanzado. Asegúrese de que sigue los pasos de este tutorial con precisión.

## <a name="prerequisites"></a>Prerrequisitos
A continuación, se indican los requisitos previos necesarios para completar este tutorial:
- Un entorno de prueba con la versión 1.4.32.0, o cualquier versión posterior, de la sincronización de Azure AD Connect.
- Una unidad organizativa o un grupo que estén en el ámbito de la sincronización y que pueda usar en la prueba piloto. Se recomienda empezar con un pequeño conjunto de objetos.
- Un servidor que ejecuta Windows Server 2012 R2, o cualquier versión posterior, que hospedará el agente de aprovisionamiento.
- El delimitador de origen para la sincronización de Azure AD Connect debe ser *objectGuid* o *ms-ds-consistencyGUID*.

## <a name="update-azure-ad-connect"></a>Actualización de Azure AD Connect

Como mínimo, debe tener [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)1.4.32.0. Para actualizar la sincronización de Azure AD Connect, realice los pasos que se describen en [Azure AD Connect: actualización a la versión más reciente](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Detención del programador
La sincronización de Azure AD Connect sincroniza los campos que se incluyen en el directorio local mediante un programador. Para modificar y agregar reglas personalizadas puede que desee deshabilitar el programador para que no se ejecuten sincronizaciones mientras trabaja en esto.  Siga estos pasos:

1.  En el servidor que ejecuta la sincronización de Azure AD Connect, abra PowerShell con privilegios administrativos.
2.  Ejecute `Stop-ADSyncSyncCycle`.  Presione Entrar.
3.  Ejecute `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE] 
>Si va a ejecutar su propio programador personalizado para la sincronización de Azure AD Connect, deshabilite el programador. 

## <a name="create-custom-user-inbound-rule"></a>Creación de una regla entrante de usuarios personalizada

 1. Inicie el editor de sincronización desde el menú de la aplicación de escritorio, tal como se muestra a continuación:</br>
 ![Menú del editor de reglas de sincronización](media/tutorial-migrate-aadc-aadccp/user-8.png)</br>
 
 2. Seleccione **Entrante** en la lista desplegable Dirección y haga clic en **Agregar nueva regla**.
 ![Captura de pantalla que muestra la ventana "Ver y administrar reglas de sincronización" con la opción "Entrante" y el botón "Agregar nueva regla" seleccionados.](media/tutorial-migrate-aadc-aadccp/user-1.png)</br>
 
 3. En la página **Descripción**, especifique los siguientes elementos y haga clic en **Siguiente**:

    **Nombre:** asigne un nombre descriptivo a la regla.<br>
    **Descripción:** agregue una descripción significativa.<br>
    **Sistema conectado:** elija el conector de AD para el que va a escribir la regla de sincronización personalizada<br>
    **Tipo de objeto de sistema conectado:** Usuario<br>
    **Tipo de objeto de metaverso:** Person<br>
    **Tipo de vínculo:** Join<br>
    **Prioridad:** especifique un valor que sea único en el sistema.<br>
    **Etiqueta:** deje esto en blanco.<br>
    ![Captura de pantalla que muestra la página "Crear regla de sincronización de entrada - Descripción" con los valores especificados.](media/tutorial-migrate-aadc-aadccp/user-2.png)</br>
 
 4. En la página **Scoping filter** (Filtro de ámbito), especifique la unidad organizativa o el grupo de seguridad en que desea que deje de basarse la prueba piloto.  Para filtrar por unidad organizativa, agregue la parte de la unidad organizativa del nombre distintivo. Esta regla se aplicará a todos los usuarios que se encuentren en esa unidad organizativa.  Por tanto, si el nombre de dominio acaba en "OU=CPUsers,DC=contoso,DC=com", debería agregar este filtro.  A continuación, haga clic en **Siguiente**. 

    |Regla|Atributo|Operator|Value|
    |-----|----|----|-----|
    |Ámbito de unidad organizativa|DN|ENDSWITH|Nombre distintivo de la unidad organizativa.|
    |Ámbito de grupo||ISMEMBEROF|Nombre distintivo del grupo de seguridad.|

    ![Captura de pantalla que muestra la página "Crear regla de sincronización de entrada - Filtro de ámbito" con un valor de filtro de ámbito especificado.](media/tutorial-migrate-aadc-aadccp/user-3.png)</br>
 
 5. En la página de reglas **Join** (Combinación), haga clic en **Next** (Siguiente).
 6. En la página **Transformations** (Transformaciones), agregue una transformación de tipo Constant, con el atributo cloudNoFlow establecido en True. Haga clic en **Agregar**.
 ![Captura de pantalla que muestra la página "Crear regla de sincronización de entrada - Transformaciones" con un flujo de "transformación constante" agregado.](media/tutorial-migrate-aadc-aadccp/user-4.png)</br>

Los mismos pasos deben seguirse para todos los tipos de objetos (usuario, grupo y contacto). Repita los pasos en todas las instancias de AD Connector configuradas y bosques de AD. 

## <a name="create-custom-user-outbound-rule"></a>Creación de una regla de salida de usuarios personalizada

 1. Seleccione **Saliente** en la lista desplegable Dirección y haga clic en **Agregar regla**.
 ![Captura de pantalla que muestra la dirección "Saliente" seleccionada y el botón "Agregar nueva regla" resaltado.](media/tutorial-migrate-aadc-aadccp/user-5.png)</br>
 
 2. En la página **Descripción**, especifique los siguientes elementos y haga clic en **Siguiente**:

    **Nombre:** asigne un nombre descriptivo a la regla.<br>
    **Descripción:** agregue una descripción significativa.<br>
    **Sistema conectado:** elija el conector de Azure AD para el que va a escribir la regla de sincronización personalizada.<br>
    **Tipo de objeto de sistema conectado:** Usuario<br>
    **Tipo de objeto de metaverso:** Person<br>
    **Tipo de vínculo:** JoinNoFlow<br>
    **Prioridad:** especifique un valor que sea único en el sistema.<br>
    **Etiqueta:** deje esto en blanco.<br>
    
    ![Captura de pantalla que muestra la página "Descripción" con las propiedades especificadas.](media/tutorial-migrate-aadc-aadccp/user-6.png)</br>
 
 3. En la página **Scoping filter** (Filtro de ámbito), elija **cloudNoFlow** igual a **True**. A continuación, haga clic en **Siguiente**.
 ![Regla personalizada](media/tutorial-migrate-aadc-aadccp/user-7.png)</br>
 
 4. En la página de reglas **Join** (Combinación), haga clic en **Next** (Siguiente).
 5. En la página **Transformations** (Transformaciones), haga clic en **Add** (Agregar).

Los mismos pasos deben seguirse para todos los tipos de objetos (usuario, grupo y contacto).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instalación del agente de aprovisionamiento de Azure AD Connect
1. Inicie sesión en el servidor que va a usar con permisos de administrador de empresa.  Si usa el tutorial [Entorno básico de AD y Azure](tutorial-basic-ad-azure.md), sería CP1.
2. Descargue el agente de aprovisionamiento en la nube de Azure AD Connect siguiendo los pasos descritos [aquí](how-to-install.md#install-the-agent).
3. Ejecute la sincronización en la nube de Azure AD Connect (AADConnectProvisioningAgent.Installer).
3. En la pantalla de presentación, **acepte** los términos de la licencia y haga clic en **Install** (Instalar).</br>
![Captura de pantalla que muestra la pantalla de presentación del "agente de aprovisionamiento de Microsoft Azure AD Connect".](media/how-to-install/install-1.png)</br>

4. Una vez que finalice esta operación, se iniciará el asistente para configuración.  Inicie sesión con su cuenta de administrador global de Azure AD.
5. En la pantalla **Connect Active Directory** (Conectar Active Directory), haga clic en **Add directory** (Agregar directorio) e inicie sesión con su cuenta de administrador de Active Directory.  Esta operación permitirá agregar su directorio local.  Haga clic en **Next**.</br>
![Captura de pantalla que muestra la pantalla "Conectar Active Directory" con un valor de directorio especificado.](media/how-to-install/install-3a.png)</br>

6. En la pantalla **Configuración completa**, haga clic en **Confirmar**.  Esta operación registrará el agente y lo reiniciará.</br>
![Captura de pantalla que muestra la pantalla "Configuración completada" con el botón "Confirmar" seleccionado.](media/how-to-install/install-4a.png)</br>

7. Una vez que se complete esta operación, debería aparecer un aviso **Your was successfully verified** (Se ha comprobado correctamente).  Puede hacer clic en **Salir**.</br>
![Pantalla principal](media/how-to-install/install-5.png)</br>
8. Si la pantalla de presentación inicial no desaparece, haga clic en **Cerrar**.

## <a name="verify-agent-installation"></a>Comprobación de la instalación del agente
La comprobación del agente se produce en Azure Portal y en el servidor local que ejecuta el agente.

### <a name="azure-portal-agent-verification"></a>Comprobación del agente en Azure Portal
Para comprobar que Azure ve el agente, siga estos pasos:

1. Inicie sesión en Azure Portal.
2. A la izquierda, seleccione **Azure Active Directory**, haga clic en **Azure AD Connect** y, en el centro, seleccione **Manage cloud sync** (Administrar sincronización en la nube).</br>
![Azure Portal](media/how-to-install/install-6.png)</br>

3.  En la pantalla de la **sincronización en la nube de Azure AD Connect**, haga clic en **Revisar todos los agentes**.
![Aprovisionamiento de Azure AD](media/how-to-install/install-7.png)</br>
 
4. En la pantalla **On-premises provisioning agents** (Agentes de aprovisionamiento locales) verá los agentes que ha instalado.  Compruebe que el agente en cuestión está ahí y que se ha marcado como **Deshabilitado**.  El agente está deshabilitado de manera predeterminada ![Agentes de aprovisionamiento](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>En el servidor local
Para comprobar que el agente se ejecuta, siga estos pasos:

1.  Inicie sesión en el servidor con una cuenta de administrador.
2.  Abra **Servicios**. Para ello, vaya ahí o a Inicio/Ejecutar/Services.msc.
3.  En **Servicios**, asegúrese de que tanto el **Actualizador del Agente de Microsoft Azure AD Connect** como el **Agente de aprovisionamiento de Microsoft Azure AD Connect** están ahí y que su estado es **En ejecución**.
![Servicios](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-sync"></a>Configuración de la sincronización en la nube de Azure AD Connect
Use los pasos siguientes para configurar el aprovisionamiento:

 1. Inicie sesión en el portal de Azure AD.
 2. Haga clic en **Azure Active Directory**.
 3. Haga clic en **Azure AD Connect**.
 4. Seleccione **Manage cloud sync** (Administrar sincronización en la nube) 
 ![Captura de pantalla que muestra el vínculo para administrar la sincronización en la nube.](media/how-to-configure/manage-1.png)</br>
 5.  Haga clic en **Nueva configuración**
 ![Captura de pantalla de la sincronización de Azure AD Connect, con el vínculo "Nueva configuración" resaltado.](media/tutorial-single-forest/configure-1.png)</br>
 6.  En la pantalla de configuración, escriba un **correo electrónico de notificación**, mueva el selector a **Habilitar** y haga clic en **Guardar**.
 ![Captura de la pantalla de configuración con un correo electrónico de notificación rellenado y Habilitar seleccionado.](media/tutorial-single-forest/configure-2.png)</br>
 7. En **Configurar**, seleccione **Todos los usuarios** para cambiar el ámbito de la regla de configuración.
 ![Captura de pantalla de la pantalla Configuración con "Todos los usuarios" resaltado junto a "Usuarios del ámbito".](media/how-to-configure/scope-2.png)</br>
 8. A la derecha, cambie el ámbito para que incluya la unidad organizativa específica que acaba de crear "OU=CPUsers,DC=contoso,DC=com".
 ![Captura de pantalla de la pantalla Usuarios del ámbito que resalta el ámbito cambiado a la unidad organizativa creada.](media/tutorial-existing-forest/scope-2.png)</br>
 9.  Haga clic en **Listo** y en **Guardar**.
 10. Ahora el ámbito se debe establecer en una unidad organizativa. 
 ![Captura de pantalla de la pantalla Configuración con "1 unidad organizativa" resaltada junto a "Usuarios del ámbito".](media/tutorial-existing-forest/scope-3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-sync"></a>Comprobación de que la sincronización en la nube aprovisiona los usuarios
Ahora vamos a comprobar que los usuarios que tenía en nuestro directorio local se han sincronizado y que ya existen en el inquilino de Azure AD.  Tenga en cuenta que esta acción puede tardar unas horas en completarse.  Para comprobar que la sincronización en la nube aprovisiona los usuarios, siga estos pasos:

1. Vaya a [Azure Portal](https://portal.azure.com) e inicie sesión con una cuenta que tenga una suscripción de Azure.
2. En la parte izquierda, seleccione **Azure Active Directory**.
3. Haga clic en **Azure AD Connect**.
4. Haga clic en **Manage cloud sync** (Administrar sincronización en la nube).
5. Haga clic en el botón **Registros**.
6. Busque un nombre de usuario para confirmar que la sincronización en la nube ha aprovisionado el usuario.

Además, puede comprobar que tanto el usuario como el grupo existen en Azure AD.

## <a name="start-the-scheduler"></a>Inicio del programador
La sincronización de Azure AD Connect sincroniza los campos que se incluyen en el directorio local mediante un programador. Ahora que ha modificado las reglas, puede volver a iniciar el programador.  Siga estos pasos:

1.  En el servidor que ejecuta la sincronización de Azure AD Connect, abra PowerShell con privilegios administrativos
2.  Ejecute `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3.  Ejecute `Start-ADSyncSyncCycle`.  Presione Entrar.  

>[!NOTE] 
>Si va a ejecutar su propio programador personalizado para la sincronización de Azure AD Connect, habilite el programador. 

Una vez que el programador está habilitado, Azure AD Connect deja de exportar los cambios realizados en los objetos con `cloudNoFlow=true` en el metaverso, a menos que se esté actualizando algún atributo de referencia (por ejemplo, `manager`). En caso de que haya alguna actualización de atributos de referencia en el objeto, Azure AD Connect omitirá la señal `cloudNoFlow` y exportará todas las actualizaciones del objeto.

## <a name="something-went-wrong"></a>Hubo un problema
Si la prueba piloto no funciona como cabría esperar, puede volver a la configuración de la sincronización de Azure AD Connect siguiendo estos pasos:
1.  Deshabilite la configuración del aprovisionamiento en Azure Portal. 
2.  Deshabilite todas las reglas de sincronización personalizadas creadas para el aprovisionamiento en la nube mediante la herramienta Editor de reglas de sincronización. La deshabilitación debería provocar la sincronización total en todos los conectores.



## <a name="next-steps"></a>Pasos siguientes 

- [¿Qué es el aprovisionamiento?](what-is-provisioning.md)
- [¿Qué es la sincronización en la nube de Azure AD Connect?](what-is-cloud-sync.md)

