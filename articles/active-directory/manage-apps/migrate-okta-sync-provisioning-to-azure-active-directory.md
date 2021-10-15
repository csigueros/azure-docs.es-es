---
title: Tutorial para la migración del aprovisionamiento de la sincronización de Okta a la sincronización basada en Azure AD Connect
titleSuffix: Active Directory
description: En este tutorial, aprenderá a migrar el aprovisionamiento de la sincronización de Okta a la sincronización basada en Azure AD Connect.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 588764e8eb1864dc702fa94b05fcd7bf32a57c79
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388083"
---
# <a name="tutorial-migrate-okta-sync-provisioning-to-azure-ad-connect-based-synchronization"></a>Tutorial: Migración del aprovisionamiento de la sincronización de Okta a la sincronización basada en Azure AD Connect

En este tutorial, aprenderá cómo su organización puede migrar actualmente el aprovisionamiento de usuarios de Okta a Azure Active Directory (Azure AD) y migrar la sincronización de usuarios o la sincronización universal a Azure AD Connect. Esta funcionalidad permitirá el aprovisionamiento adicional en Azure AD y Office 365.

La migración de plataformas de sincronización no es un cambio menor. Cada paso del proceso mencionado en este artículo se debe validar en su propio entorno antes de quitar Azure AD Connect del modo de almacenamiento provisional o habilitar el agente de aprovisionamiento en la nube de Azure AD.

## <a name="prerequisites"></a>Requisitos previos

Al cambiar del aprovisionamiento de Okta a Azure AD, tiene dos opciones. Puede usar un servidor de Azure AD Connect o un aprovisionamiento en la nube de Azure AD. Para conocer las diferencias entre ambas opciones, lea el [artículo comparativo de Microsoft](../cloud-sync/what-is-cloud-sync.md#comparison-between-azure-ad-connect-and-cloud-sync).

El aprovisionamiento en la nube de Azure AD será la ruta de migración más familiar para los clientes de Okta que usan la sincronización universal o de usuarios. Los agentes de aprovisionamiento en la nube son ligeros. Se pueden instalar en los controladores de dominio o cerca de ellos, como los agentes de sincronización de directorios de Okta. No los instale en el mismo servidor.

Use el servidor de Azure AD Connect si la organización necesita aprovechar cualquiera de las siguientes tecnologías al sincronizar usuarios:

- Sincronización de dispositivos: unión a Azure AD híbrido o Hello para empresas
- Autenticación de paso a través
- Compatibilidad con más de 150 000 objetos
- Compatibilidad con la escritura diferida

>[!NOTE]
>Se deben tener en cuenta todos los requisitos previos al instalar Azure AD Connect o el aprovisionamiento en la nube de Azure AD. Para obtener más información antes de continuar con la instalación, vea [Requisitos previos de Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md).
## <a name="confirm-immutableid-attribute-synchronized-by-okta"></a>Confirmación del atributo ImmutableID sincronizado por Okta

ImmutableID es el atributo principal que se usa para vincular los objetos sincronizados a sus homólogos locales. Okta toma el valor de objectGUID de Active Directory de un objeto local y lo convierte en una cadena codificada en Base 64. A continuación, marca de manera predeterminada esa cadena en el campo ImmutableID de Azure AD.

Puede conectarse a PowerShell de Azure AD y examinar el valor actual de ImmutableID. Si nunca ha usado el módulo de PowerShell de Azure AD, ejecute `Install-Module AzureAD` en una sesión administrativa de PowerShell antes de ejecutar los siguientes comandos:

```Powershell
Import-module AzureAD
Connect-AzureAD
```

Si ya tiene el módulo, puede recibir una advertencia para actualizarlo a la última versión si no está actualizado.

Una vez instalado el módulo, impórtelo y siga estos pasos para conectarse al servicio Azure AD:

1. Escriba sus credenciales de administrador global en la ventana de autenticación moderna.

   ![Captura de pantalla que muestra import-module.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/import-module.png)

1. Después de conectarse al inquilino, compruebe la configuración de los valores ImmutableID. En el ejemplo que se muestra, se usan los valores predeterminados de Okta de objectGUID para ImmutableID.

   ![Captura de pantalla en la que se muestran los valores predeterminados de Okta de objectGUID para ImmutableID.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/okta-default-objectid.png)

1. Hay varias maneras de confirmar manualmente la conversión local de objectGUID a Base 64. Para la validación individual, use este ejemplo:

   ```PowerShell
   Get-ADUser onpremupn | fl objectguid
   $objectguid = 'your-guid-here-1010'
   [system.convert]::ToBase64String(([GUID]$objectGUID).ToByteArray())
   ```

   ![Captura de pantalla en la que se muestra cómo cambiar manualmente los valores objectGUID de Okta para ImmutableID.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/manual-objectguid.png)

## <a name="mass-validation-methods-for-objectguid"></a>Métodos de validación masiva para objectGUID

Antes de pasar a Azure AD Connect, es fundamental validar que los valores de ImmutableID de Azure AD van a coincidir exactamente con sus valores locales.

En el ejemplo, se capturarán *todos* los usuarios de Azure AD locales y se exportará una lista de sus valores de objectGUID e ImmutableID ya calculados a un archivo .csv.

1. Ejecute estos comandos en PowerShell en un controlador de dominio local:

   ```PowerShell
   Get-ADUser -Filter * -Properties objectGUID | Select-Object
   UserPrincipalName, Name, objectGUID, @{Name = 'ImmutableID';
   Expression = {
   [system.convert\]::ToBase64String(([GUID\]\$_.objectGUID).ToByteArray())
   } } | export-csv C:\\Temp\\OnPremIDs.csv
   ```

   ![Captura de pantalla en la que se muestran los comandos del controlador de dominio local.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/domain-controller.png)

1. Ejecute estos comandos en una sesión de PowerShell de Azure AD para recopilar los valores ya sincronizados:

   ```powershell
   Get-AzureADUser -all $true | Where-Object {$_.dirsyncenabled -like
   "true"} | Select-Object UserPrincipalName, @{Name = 'objectGUID';
   Expression = {
   [GUID][System.Convert]::FromBase64String($_.ImmutableID) } },
   ImmutableID | export-csv C:\\temp\\AzureADSyncedIDS.csv
   ```

   ![Captura de pantalla en la que se muestran la sesión de PowerShell en Azure AD.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-powershell.png)

   Una vez que tenga ambas exportaciones, confirme que coincide el valor de ImmutableID para cada usuario.

   >[!IMPORTANT]
   >Si los valores de ImmutableID en la nube no coinciden con los valores de objectGUID, ha modificado los valores predeterminados de la sincronización de Okta. Es probable que haya elegido otro atributo para determinar los valores de ImmutableID. Antes de pasar a la sección siguiente, es fundamental identificar qué atributo de origen rellena los valores de ImmutableID. Asegúrese de actualizar el atributo de Okta que se está sincronizando antes de deshabilitar la sincronización de Okta.
## <a name="install-azure-ad-connect-in-staging-mode"></a>Instalación de Azure AD Connect en modo de almacenamiento provisional

Una vez que haya preparado la lista de objetivos de origen y destino, es el momento de instalar un servidor de Azure AD Connect. Si ha optado por usar el aprovisionamiento en la nube de Azure AD Connect, omita esta sección.

1. Continúe con la [descarga e instalación de Azure AD Connect](../hybrid/how-to-connect-install-custom.md) en el servidor elegido. 

1. En la página **Identificación de usuarios**, en la opción **Seleccione cómo deben identificarse los usuarios con Azure AD**, seleccione la opción **Elija un atributo específico**. A continuación, seleccione **mS-DS-ConsistencyGUID** si no ha modificado los valores predeterminados de Okta.

   >[!WARNING]
   >Este es el paso más crítico de esta página. Antes de seleccionar **Siguiente**, asegúrese de que el atributo que va a seleccionar para el marcador de origen es el que rellena *actualmente* los usuarios de Azure AD existentes. Si selecciona el atributo incorrecto, debe desinstalar y volver a instalar Azure AD Connect para volver a seleccionar esta opción.
   ![Captura de pantalla en la que se muestra mS-DS-ConsistencyGuid](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/consistency-guid.png).

1. En la página **Configurar**, asegúrese de activar la casilla **Habilitar modo de almacenamiento provisional**. A continuación, seleccione **Instalar**.

   ![Captura de pantalla en la que se muestra la casilla Habilitar modo de almacenamiento provisional.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/enable-staging-mode.png)

1. Una vez completada la configuración, seleccione **Salir**.

   Antes de salir del modo de almacenamiento provisional, compruebe que los valores de ImmutableID coinciden correctamente.

1. Abra el **Servicio de sincronización** como administrador.

   ![Captura de pantalla en la que se muestra cómo abrir el Servicio de sincronización.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/open-sync-service.png)

1. Compruebe que la **Sincronización completa** con el espacio del conector de domain.onmicrosoft.com tiene usuarios que se muestran en la pestaña **Connectors with Flow Updates** (Conectores con actualizaciones de flujos).

   ![Captura de pantalla en la que se muestra la pestaña Connectors with Flow Updates (Conectores con actualizaciones de flujos).](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/connector-flow-update.png)

1. Compruebe que no hay eliminaciones pendientes en la exportación. Seleccione la pestaña **Connectors** (Conectores) y, a continuación, resalte el espacio del conector de domain.onmicrosoft.com. A continuación, seleccione **Search Connector Space** (Buscar espacio de conector).

   ![Captura de pantalla que muestra la ventana Search Connector Space (Buscar espacio de conector).](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/search-connector-space.png)

1. En el cuadro de diálogo **Search Connector Space** (Buscar espacio de conector), seleccione el menú desplegable **Scope** (Ámbito) y, después, **Pending Export** (Exportación pendiente).

   ![Captura de pantalla en la que se muestra Pending Export (Exportación pendiente).](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/pending-export.png)

1. Seleccione **Delete** (Eliminar) seguido de **Search** (Buscar). Si todos los objetos coinciden correctamente, no debería haber ningún registro coincidente para **Deletes** (Eliminaciones). Registre los objetos pendientes de eliminación y sus valores locales.

   ![Captura de pantalla en la que se muestran los registros coincidentes eliminados.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/delete-matching-records.png)

1. Desactive **Delete** (Eliminar) y seleccione **Add** (Agregar) y **Modify** (Modificar), seguido de una búsqueda. Debería ver las funciones de actualización para todos los usuarios que se están sincronizando actualmente con Azure AD mediante Okta. Agregue los nuevos objetos que Okta no está sincronizando actualmente, pero que existen en la estructura de unidad organizativa (UO) que se seleccionó durante la instalación de Azure AD Connect.

   ![Captura de pantalla en la que se muestra la adición de un objeto nuevo.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/add-new-object.png)

1. Al hacer doble clic en las actualizaciones, se mostrará lo que Azure AD Connect comunicará con Azure AD.

1. Si hay alguna función de **adición** para un usuario que ya existe en Azure AD, su cuenta local no coincide con su cuenta en la nube. AD Connect ha determinado que creará un objeto y registrará las nuevas adiciones inesperadas. Asegúrese de corregir el valor de ImmutableID en Azure AD antes de salir del modo de almacenamiento provisional.

   En este ejemplo, Okta marcó el atributo **mail** en la cuenta del usuario, aunque el valor local no se hubiera rellenado correctamente. Cuando Azure AD Connect toma la cuenta de John Smith, el atributo **mail** se elimina de su objeto.

   Compruebe que las actualizaciones todavía incluyen todos los atributos esperados en Azure AD. Si se eliminan varios atributos, es posible que deba rellenar manualmente estos valores de AD local antes de quitar el modo de almacenamiento provisional.

   ![Captura de pantalla en la que se muestra cómo rellenar los valores de adición locales.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/on-premises-ad-values.png)

   >[!NOTE]
   >Antes de continuar con el paso siguiente, asegúrese de que todos los atributos de usuario se sincronicen correctamente y se muestren en la pestaña **Pending Export** (Exportación pendiente) según lo previsto. Si se eliminan, asegúrese de que sus valores de ImmutableID coinciden y de que el usuario se encuentra en una de las UO seleccionadas para la sincronización.
## <a name="install-azure-ad-cloud-sync-agents"></a>Instalación de los agentes de sincronización en la nube de Azure AD

Una vez que haya preparado la lista de objetivos de origen y destino, es el momento de [instalar y configurar los agentes de sincronización en la nube de Azure AD](../cloud-sync/tutorial-single-forest.md). Si ha optado por usar un servidor de Azure AD Connect, omita esta sección.

## <a name="disable-okta-provisioning-to-azure-ad"></a>Deshabilitación del aprovisionamiento de Okta en Azure AD

Una vez que se ha comprobado la instalación de Azure AD Connect y que las exportaciones pendientes están en orden, es el momento de deshabilitar el aprovisionamiento de Okta en Azure AD.

1. Vaya al portal de Okta, seleccione **Applications** (Aplicaciones) y, después, seleccione la aplicación de Okta que se usa para aprovisionar usuarios en Azure AD. Abra la pestaña **Provisioning** (Aprovisionamiento) y seleccione la sección **Integration** (Integración).

   ![Captura de pantalla que muestra la sección Integration (Integración) en Okta.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/integration-section.png)

1. Seleccione **Edit** (Editar), desactive la opción **Enable API integration** (Habilitar integración de API) y seleccione **Save** (Guardar).

   ![Captura de pantalla en la que se muestra cómo editar la opción Enable API integration (Habilitar integración de API) en Okta.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/edit-api-integration.png)

   >[!NOTE]
   >Si tiene varias aplicaciones de Office 365 que administran el aprovisionamiento en Azure AD, asegúrese de que todas están desactivadas.
## <a name="disable-staging-mode-in-azure-ad-connect"></a>Deshabilitación del modo de almacenamiento provisional en Azure AD Connect

Una vez deshabilitado el aprovisionamiento de Okta, el servidor de Azure AD Connect está listo para empezar a sincronizar objetos. Si ha elegido usar los agentes de sincronización en la nube de Azure AD, omita esta sección.

1. Vuelva a ejecutar el asistente para la instalación desde el escritorio y seleccione **Configurar**.

   ![Captura de pantalla en la que se muestra el servidor de Azure AD Connect.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-connect-server.png)

1. Seleccione **Configurar modo de almacenamiento provisional** y después seleccione **Siguiente**. Especifique las credenciales de administrador global.

   ![Captura de pantalla en la que se muestra la opción Configurar modo de almacenamiento provisional.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/configure-staging-mode.png)

1. Desactive la opción **Habilitar modo de almacenamiento provisional** y seleccione **Siguiente**.

   ![Captura de pantalla en la que se muestra cómo desactivar la opción Habilitar modo de almacenamiento provisional.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/uncheck-enable-staging-mode.png)

1. Seleccione **Configurar** para continuar.

   ![Captura de pantalla en la que se muestra la selección del botón Configurar.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/ready-to-configure.png)

1. Una vez completada la configuración, abra el **servicio de sincronización** como administrador. Consulte la **Exportación** en el conector de domain.onmicrosoft.com. Compruebe que todas las adiciones, actualizaciones y eliminaciones se han realizado según lo previsto.

   ![Captura de pantalla en la que se muestra el servicio de sincronización.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/verify-sync-service.png)

Ya ha realizado correctamente la migración al aprovisionamiento basado en un servidor de Azure AD Connect. Se pueden volver a ejecutar actualizaciones y expansiones del conjunto de características de Azure AD Connect mediante la ejecución del asistente para la instalación.

## <a name="enable-cloud-sync-agents"></a>Habilitación de los agentes de sincronización en la nube

Una vez deshabilitado el aprovisionamiento de Okta, el agente de sincronización en la nube de Azure AD está listo para empezar a sincronizar objetos. Vuelva al [Portal de Azure AD](https://aad.portal.azure.com/).

1. Modifique el **perfil de configuración** a **Habilitado**.

1. Vuelva al menú de aprovisionamiento y seleccione **Registros**.

1. Evalúe si el conector de aprovisionamiento ha actualizado correctamente los objetos existentes. Los agentes de sincronización en la nube no son destructivos. Se producirá un error en sus actualizaciones si no se ha dado una coincidencia correcta.

1. Si un usuario no coincide, realice las actualizaciones necesarias para enlazar los valores de ImmutableID. Después, reinicie la sincronización del aprovisionamiento en la nube.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre la migración de Okta a Azure AD, vea:

- [Migración de aplicaciones desde Okta a Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)
- [Migración de la federación de Okta a la autenticación administrada de Azure AD](migrate-okta-federation-to-azure-active-directory.md)
- [Migración de directivas de inicio de sesión de Okta al acceso condicional de Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
