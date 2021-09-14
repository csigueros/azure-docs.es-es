---
title: Tutorial para la migración del aprovisionamiento de la sincronización de Okta a la sincronización basada en Azure AD Connect
titleSuffix: Active Directory
description: Obtenga información sobre cómo migrar el aprovisionamiento de la sincronización de Okta a la sincronización basada en Azure AD Connect.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 4d88932bc6cc945b38d90f1e7d79702f4b3013c9
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440285"
---
# <a name="tutorial-migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization"></a>Tutorial: Migración del aprovisionamiento de la sincronización de Okta a la sincronización basada en Azure Active Directory Connect

En este artículo, se guía a las organizaciones que usan actualmente el aprovisionamiento de usuarios de Okta a Azure Active Directory (Azure AD) para migrar la sincronización de usuarios o la sincronización universal a Azure AD Connect. Esto permitirá el aprovisionamiento adicional en Azure AD y Office 365.

La migración de plataformas de sincronización no es un cambio menor. Cada paso del proceso mencionado en este artículo se debe validar en su propio entorno antes de quitar Azure AD Connect del modo de almacenamiento provisional o habilitar el agente de aprovisionamiento en la nube de Azure AD.

## <a name="prerequisites"></a>Requisitos previos

Al cambiar del aprovisionamiento de Okta a Azure AD, los clientes tienen dos opciones: un servidor de Azure AD Connect o el aprovisionamiento en la nube de Azure AD. Se recomienda leer el [artículo completo de la comparativa de Microsoft](https://docs.microsoft.com/azure/active-directory/cloud-sync/what-is-cloud-sync#comparison-between-azure-ad-connect-and-cloud-provisioning) para comprender las diferencias entre los dos productos.

El aprovisionamiento en la nube de Azure AD será la ruta de migración más familiar para los clientes de Okta que usan la sincronización universal o de usuario. Los agentes de aprovisionamiento en la nube son ligeros y se pueden instalar en los controladores de dominio o cerca de ellos, como los agentes de sincronización de directorios de Okta. No se recomienda instalarlos en el mismo servidor.

Se debe elegir la opción de servidor de Azure AD Connect si la organización necesita aprovechar cualquiera de las siguientes tecnologías al sincronizar usuarios.

- Sincronización de dispositivos: unión a Azure AD híbrido o Hello para empresas

- Autenticación de paso a través

- Compatibilidad con más de 150 000 objetos

- Compatibilidad con la escritura diferida

>[!NOTE]
>Se deben tener en cuenta todos los requisitos previos al instalar Azure AD Connect o el aprovisionamiento en la nube de Azure AD. Para más información, consulte este [artículo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites) antes de la instalación.  

## <a name="step-1---confirm-immutableid-attribute-synchronized-by-okta"></a>Paso 1: Confirmación del atributo ImmutableID sincronizado por Okta

ImmutableID es el atributo principal que se usa para vincular los objetos sincronizados a sus homólogos locales. Okta toma el valor de objectGUID de Active Directory de un objeto local y lo convierte en una cadena codificada en Base 64. A continuación, marca de manera predeterminada esa cadena en el campo ImmutableID de Azure AD.

Puede conectarse a PowerShell de Azure AD y examinar el valor actual de ImmutableID. Si nunca ha usado el módulo de PowerShell de Azure AD, ejecute `Install-Module AzureAD` en una sesión administrativa de PowerShell antes de ejecutar los siguientes comandos.

```Powershell
Import-module AzureAD
Connect-AzureAD
```

En caso de que ya tenga el módulo, puede recibir una advertencia para actualizarlo a la versión más reciente si no está actualizado.

Una vez instalado el módulo, impórtelo y siga estos pasos para conectarse al servicio Azure AD:

1. Escriba sus credenciales de administrador global en la ventana de autenticación moderna.

   ![Imagen que muestra la importación del módulo](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/import-module.png)

2. Después de conectarse al inquilino, compruebe el valor de ImmutableID. En el ejemplo que se muestra, se usan los valores predeterminados de Okta de objectGUID a ImmutableID.

   ![Imagen que muestra los valores predeterminados de Okta de objectGUID a ImmutableID](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/okta-default-objectid.png)

3. Hay varias maneras de confirmar manualmente la conversión local de objectGUID a Base 64; para la validación individual, use este ejemplo:

   ```PowerShell
   Get-ADUser onpremupn | fl objectguid
   $objectguid = 'your-guid-here-1010'

   [system.convert]::ToBase64String(([GUID]$objectGUID).ToByteArray())
   ```

   ![Imagen que muestra cómo cambiar manualmente de objectGUID de Okta a ImmutableID](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/manual-objectguid.png)

## <a name="step-2---mass-validation-methods-for-objectguid"></a>Paso 2: Métodos de validación masiva para objectGUID

Antes de pasar a Azure AD Connect, es fundamental validar que los valores de ImmutableID de Azure AD van a coincidir exactamente con sus valores locales.

En el ejemplo, se capturarán **todos** los usuarios de AD locales y se exportará una lista de sus valores de objectGUID e ImmutableID ya calculados a un archivo .csv.

1. Ejecute estos comandos en PowerShell en un controlador de dominio local. 

   ```PowerShell
   Get-ADUser -Filter * -Properties objectGUID | Select-Object
   UserPrincipalName, Name, objectGUID, @{Name = 'ImmutableID';
   Expression = {
   [system.convert\]::ToBase64String(([GUID\]\$_.objectGUID).ToByteArray())
   } } | export-csv C:\\Temp\\OnPremIDs.csv
   ```

   ![Imagen que muestra los comandos del controlador de dominio local](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/domain-controller.png)

2. Ejecute estos comandos en una sesión de PowerShell de Azure AD para recopilar los valores ya sincronizados:

   ```powershell

   Get-AzureADUser -all $true | Where-Object {$_.dirsyncenabled -like
   "true"} | Select-Object UserPrincipalName, @{Name = 'objectGUID';
   Expression = {
   [GUID][System.Convert]::FromBase64String($_.ImmutableID) } },
   ImmutableID | export-csv C:\\temp\\AzureADSyncedIDS.csv
   ```

   ![Imagen que muestra la sesión de PowerShell de Azure AD](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-powershell.png)

   Una vez que tenga ambas exportaciones, confirme que coincide el valor de ImmutableID para cada usuario.

   >[!IMPORTANT]
   >Si los valores de ImmutableID en la nube no coinciden con los valores de objectGUID, ha modificado los valores predeterminados de la sincronización de Okta. Es probable que haya elegido otro atributo para determinar los valores de ImmutableID. Antes de pasar a la sección siguiente, es fundamental identificar qué atributo de origen rellena los valores de ImmutableID.      Asegúrese de actualizar el atributo de Okta que se está sincronizando antes de deshabilitar la sincronización de Okta.

## <a name="step-3---install-azure-ad-connect-in-staging-mode"></a>Paso 3: Instalación de Azure AD Connect en modo de almacenamiento provisional

Una vez que haya preparado la lista de objetivos de origen y destino, es el momento de instalar el servidor de Azure AD Connect. Si ha optado por usar el aprovisionamiento en la nube de Azure AD Connect, omita esta sección.

1. Continúe con la [descarga e instalación de Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) en el servidor elegido. 

2. En la página **Identificación de usuarios**, en la opción **Seleccione cómo deben identificarse los usuarios con Azure AD**, seleccione el botón radial **Elija un atributo específico**. A continuación, seleccione **mS-DS-ConsistencyGUID** si no ha modificado los valores predeterminados de Okta.

   >[!WARNING]
   >Este es el paso más crítico antes de seleccionar **Siguiente** en esta página. Asegúrese de que el atributo va a seleccionar para el marcador de origen es el que rellena **actualmente** los usuarios de Azure AD existentes. Si selecciona el atributo incorrecto, debe desinstalar y volver a instalar Azure AD Connect para volver a seleccionar esta opción.

   ![Imagen que muestra el GUID de coherencia](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/consistency-guid.png)

3. En la página **Configurar**, asegúrese de activar la casilla **Habilitar modo de almacenamiento provisional** seguido de **Instalar**.

   ![Imagen que muestra cómo habilitar el modo de almacenamiento provisional](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/enable-staging-mode.png)

4. Una vez completada la configuración, seleccione **Salir**.

Antes de salir del modo de almacenamiento provisional, es importante comprobar que los valores de ImmutableID coinciden correctamente.

1. Abra el servicio de sincronización como **administrador**.

   ![Imagen que muestra la apertura del servicio de sincronización](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/open-sync-service.png)

2. En primer lugar, compruebe que la sincronización completa con el espacio del conector de domain.onmicrosoft.com tiene usuarios que se muestran en la pestaña **Connectors with Flow Updates** (Conectores con actualizaciones de flujos).

   ![Imagen que muestra un conector con actualización de flujos](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/connector-flow-update.png)

3. A continuación, compruebe que no hay eliminaciones pendientes en la exportación. Seleccione la pestaña **Connectors** (Conectores) y, a continuación, resalte el espacio del conector de domain.onmicrosoft.com. A continuación, seleccione **Search Connector Space** (Buscar espacio de conector).

   ![Imagen que muestra la búsqueda del espacio de conector](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/search-connector-space.png)

4. En la búsqueda del espacio de conector, seleccione la lista desplegable Scope (Ámbito) y seleccione **Pending Export** (Exportación pendiente).

   ![Imagen que muestra la exportación pendiente](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/pending-export.png)

5. Seleccione **Delete** (Eliminar) seguido de **Search** (Buscar) si todos los objetos coinciden correctamente; no debería haber ningún registro coincidente para eliminaciones. Registre los objetos pendientes de eliminación y sus valores locales.

   ![Imagen que muestra los registros coincidentes eliminados](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/delete-matching-records.png)

6. A continuación, desactive **Delete** (Eliminar) y seleccione **Add (Agregar) y Modify (Modificar)** , seguido de una búsqueda. Debería ver las funciones de actualización para todos los usuarios que se están sincronizando actualmente con Azure AD mediante Okta. Agregue los nuevos objetos que Okta no está sincronizando actualmente, pero que existen en la estructura de unidad organizativa (OU) que se seleccionó durante la instalación de Azure AD Connect.

   ![Imagen que muestra la adición de nuevo objeto](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/add-new-object.png)

7. Al hacer doble clic en las actualizaciones, se mostrará lo que Azure AD Connect va a comunicar con Azure AD.

8. Si hay alguna función de adición para un usuario que ya existe en Azure AD, su cuenta local no coincide con su cuenta en la nube y AD Connect ha determinado que creará un nuevo objeto y registrará las nuevas adiciones que sean inesperadas. Asegúrese de corregir el valor de ImmutableID en Azure AD antes de salir del modo de almacenamiento provisional.

   En este ejemplo, Okta había marcado el atributo Mail en la cuenta del usuario, aunque el valor local no se hubiera rellenado correctamente. Cuando Azure AD Connect toma la cuenta de John Smith, el atributo Mail se elimina de su objeto.

   Compruebe que las actualizaciones todavía incluyen todos los atributos esperados en Azure AD. Si se eliminan varios atributos, es posible que deba rellenar manualmente estos valores de AD local antes de quitar el modo de almacenamiento provisional.

   ![Imagen que muestra los valores de AD local rellenados](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/on-premises-ad-values.png)

   >[!NOTE]
   >Antes de continuar con el paso siguiente, asegúrese de que todos los atributos de usuario se sincronicen correctamente y se muestren en la pestaña **Pending Export** (Exportación pendiente) según lo previsto. Si se eliminan, asegúrese de la coincidencia de su valor de ImmutableID y que el usuario se encuentre en una de las OU seleccionadas para la sincronización.

## <a name="step-4---install-azure-ad-cloud-sync-agents"></a>Paso 4: Instalación de los agentes de sincronización en la nube de Azure AD

Una vez que haya preparado la lista de objetivos de origen y destino, es el momento de [instalar y configurar los agentes de sincronización en la nube de Azure AD](https://docs.microsoft.com/azure/active-directory/cloud-sync/tutorial-single-forest). Si ha optado por usar un servidor de Azure AD Connect, omita esta sección.

## <a name="step-5---disable-okta-provisioning-to-azure-ad"></a>Paso 5: Deshabilitación del aprovisionamiento de Okta en Azure AD

Una vez que se ha comprobado la instalación de Azure AD Connect y las exportaciones pendientes están en orden, es el momento de deshabilitar el aprovisionamiento de Okta en Azure AD.

1. Vaya al portal de Okta y seleccione **Applications** (Aplicaciones), seguido de la aplicación de Okta que se usa para aprovisionar usuarios en Azure AD. Abra la sección **Integration** (Integración) de la pestaña de aprovisionamiento.

   ![Imagen que muestra la sección de integración de Okta](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/integration-section.png)

2. Seleccione **Edit** (Editar), desactive la opción **Enable API integration** (Habilitar integración de API) y seleccione **Save** (Guardar).

   ![Imagen que muestra la edición para la habilitación de la integración de API en Okta](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/edit-api-integration.png)

   >[!NOTE]
   >Si tiene varias aplicaciones de Office 365 que administran el aprovisionamiento en Azure AD, asegúrese de que todas estén desactivadas.

## <a name="step-6---disable-staging-mode-in-azure-ad-connect"></a>Paso 6: Deshabilitación del modo de almacenamiento provisional en Azure AD Connect

Después de deshabilitar el aprovisionamiento de Okta, el servidor de Azure AD Connect está listo para empezar a sincronizar objetos. Si ha elegido usar los agentes de sincronización en la nube de Azure AD, omita esta sección.

1. Vuelva a ejecutar el asistente para la instalación desde el escritorio y seleccione **Configurar**.

   ![Imagen que muestra el servidor de Azure AD Connect](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-connect-server.png)

2. Seleccione **Configurar modo de almacenamiento provisional**, seguido de **Siguiente**, y escriba sus credenciales de administrador global.

   ![Imagen que muestra la configuración del modo de almacenamiento provisional](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/configure-staging-mode.png)

3. Desactive **Habilitar modo de almacenamiento provisional**, seguido de Siguiente.

   ![Imagen que muestra la desactivación de la opción Habilitar modo de almacenamiento provisional](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/uncheck-enable-staging-mode.png)

4. Seleccione **Configurar** para continuar.

   ![Imagen que muestra que está listo para configurar](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/ready-to-configure.png)

5. Una vez completada la configuración, abra el **servicio de sincronización** como administrador. Consulte la exportación en el conector de domain.onmicrosoft.com. Compruebe que todas las adiciones, actualizaciones y eliminaciones se hayan realizado según lo previsto.

   ![Imagen que muestra la comprobación del servicio de sincronización](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/verify-sync-service.png)

Ahora, ha realizado correctamente la migración al aprovisionamiento basado en un servidor de Azure AD Connect. Se pueden volver a ejecutar actualizaciones y expansiones del conjunto de características de Azure AD Connect mediante la ejecución del asistente para la instalación.

## <a name="step-7---enable-cloud-sync-agents"></a>Paso 7: Habilitación de los agentes de sincronización en la nube

Después de deshabilitar el aprovisionamiento de Okta y una vez que el agente de sincronización en la nube de Azure AD esté listo para empezar a sincronizar objetos, vuelva al [portal de Azure AD](https://aad.portal.azure.com/).

1. Modifique el **perfil de configuración** a **Habilitado**.

2. Después de habilitarlo, vuelva al menú de aprovisionamiento y seleccione **Registros**.

3. Evalúe si el conector de aprovisionamiento ha actualizado correctamente los objetos. Los agentes de sincronización en la nube no son destructivos. Se producirá un error en sus actualizaciones si no se ha dado una coincidencia correcta.

4. Si un usuario no coincide, realice las actualizaciones necesarias para enlazar los valores de ImmutableID y reinicie la sincronización del aprovisionamiento en la nube.

## <a name="next-steps"></a>Pasos siguientes

- [Migración de aplicaciones desde Okta a Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)

- [Migración de la federación de Okta a la autenticación administrada de Azure AD](migrate-okta-federation-to-azure-active-directory.md)

- [Migración de directivas de inicio de sesión de Okta al acceso condicional de Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
