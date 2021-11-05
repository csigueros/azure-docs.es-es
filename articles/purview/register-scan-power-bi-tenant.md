---
title: Conectar a un inquilino de Power BI y administrarlo
description: Esta guía describe cómo conectarse a un inquilino de Power BI y utilizar las funciones de Purview para explorar y administrar el origen del inquilino de Power BI.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 9ee623656ee83347d2edc1fe010131913a07ccb4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131023792"
---
# <a name="connect-to-and-manage-a-power-bi-tenant-in-azure-purview"></a>Conectar a un inquilino de Power BI y administrarlo en Azure Purview

Este artículo describe cómo registrar un inquilino de Power BI y cómo autenticar e interactuar con el inquilino en Azure Purview. Para obtener más información sobre Azure Purview, consulte el [artículo de introducción](overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register)| [Sí](#scan)| No | No | No | No| [Sí](how-to-lineage-powerbi.md)|

> [!Note]
> Si la instancia de Purview y el inquilino de Power BI están en el mismo inquilino de Azure, solo puede usar la autenticación de identidad administrada (MSI) para configurar un examen de un inquilino de Power BI.

### <a name="known-limitations"></a>Limitaciones conocidas

-   En el escenario entre inquilinos, no hay experiencia de usuario disponible actualmente para registrar y examinar el inquilino de Power BI.
-   La edición del inquilino de Power BI entre inquilinos registrado con PowerShell mediante Purview Studio alterará el registro del origen de datos con un comportamiento de examen incoherente.
-   Revise [Limitaciones del examen de metadatos de Power BI](/power-bi/admin/service-admin-metadata-scanning).


## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

## <a name="register"></a>Register

En esta sección se describe cómo registrar un inquilino de Power BI en Azure Purview en escenarios de [mismo inquilino](#authentication-for-a-same-tenant-scenario) y [entre inquilinos](#steps-to-register-cross-tenant).

### <a name="authentication-for-a-same-tenant-scenario"></a>Autenticación para un escenario del mismo inquilino

Para escenarios del mismo inquilino y entre inquilinos, para configurar la autenticación, cree un grupo de seguridad y agréguele la identidad administrada por Purview.

1. En [Azure Portal](https://portal.azure.com), busque **Azure Active Directory**.
1. Cree un grupo de seguridad en Azure Active Directory mediante las indicaciones de [Creación de un grupo básico e incorporación de miembros con Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > Puede omitir este paso si ya tiene un grupo de seguridad que quiera usar.

1. Seleccione **Seguridad** como **Tipo de grupo**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Captura de pantalla del tipo de grupo de seguridad.":::

1. Agregue la identidad administrada de Purview a este grupo de seguridad. Seleccione **Miembros** y, a continuación, seleccione **+ Agregar miembros**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Captura de pantalla de cómo agregar la instancia administrada del catálogo al grupo.":::

1. Busque la identidad administrada de Purview y selecciónela.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Captura de pantalla que muestra cómo agregar al catálogo buscando por nombre.":::

    Verá una notificación de operación correcta que le muestra que se ha agregado.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Captura de pantalla que muestra la incorporación correcta de MSI al catálogo.":::

#### <a name="associate-the-security-group-with-the-tenant"></a>Asociación del grupo de seguridad con el inquilino

1. Inicie sesión en el [portal de administración de Power BI](https://app.powerbi.com/admin-portal/tenantSettings).
1. Seleccione la página **Configuración de inquilinos**.

    > [!Important]
    > Debe ser administrador de Power BI para ver la página de configuración de inquilinos.

1. Seleccione **Configuración de la API de administración** > **Concesión de permisos a las entidades de servicio para utilizar las API de administración de Power BI de solo lectura (versión preliminar)** .
1. Seleccione **Grupos de seguridad específicos**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Imagen en la que se muestra cómo permitir a las entidades de servicio obtener permisos de API de administración de Power BI de solo lectura":::

1. Seleccione **Configuración de la API de administración** > **Mejora de las respuestas de las API de administración con metadatos detallados** > habilite el botón de alternancia para permitir que el Mapa de datos de Purview detecte automáticamente los metadatos detallados de los conjuntos de datos de Power BI como parte de sus exámenes.

    > [!IMPORTANT]
    > Después de actualizar la configuración de la API de administración en el inquilino de Power BI, espere unos 15 minutos antes de registrar una conexión de examen y prueba.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-sub-artifacts.png" alt-text="Imagen que muestra la configuración del portal de administración de Power BI para habilitar el examen de subartefactos.":::

    > [!Caution]
    > Cuando permite que el grupo de seguridad que ha creado (que tiene como miembro la identidad administrada de Purview) use las API de administración de Power BI de solo lectura, también le permite acceder a los metadatos (por ejemplo, los nombres de los paneles y los informes, los propietarios, las descripciones, etc.) de todos los artefactos de Power BI de este inquilino. Una vez que los metadatos se han extraído en Azure Purview, los permisos de Purview, no los permisos de Power BI, determinan quién puede ver esos metadatos.
  
    > [!Note]
    > Puede quitar el grupo de seguridad de la configuración del desarrollador, pero los metadatos extraídos previamente no se quitarán de la cuenta de Purview. Puede eliminarlo por separado, si quiere.

### <a name="steps-to-register-in-the-same-tenant"></a>Pasos para registrarse en el mismo inquilino

Ahora que le han concedido los permisos de identidad administrada de Purview para conectarse a la API de administración de su inquilino de Power BI, puede configurar el examen desde Azure Purview Studio.

1. Seleccione **Mapa de datos** en el panel de navegación izquierdo.

1. Después, seleccione **Register** (Registrar).

    Seleccione **Power BI** como origen de datos.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Imagen que muestra la lista de orígenes de datos disponibles para elegir.":::

1. Asigne un nombre descriptivo a la instancia de Power BI.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Imagen que muestra el nombre descriptivo del origen de datos de Power BI.":::

    El nombre debe tener entre 3 y 63 caracteres, y solo puede contener letras, números, guiones bajos y guiones.  No están permitidos los espacios.

    De manera predeterminada, el sistema buscará el inquilino de Power BI que exista en la misma suscripción de Azure.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Imagen que muestra el origen registrado de Power BI":::.

    > [!Note]
    > Por Power BI, solo se permite el registro y el examen de orígenes de datos para una instancia.

### <a name="steps-to-register-cross-tenant"></a>Pasos para registrar entre inquilinos

En un escenario entre inquilinos, puede usar PowerShell para registrar y examinar los inquilinos de Power BI. Puede examinar y buscar recursos del inquilino remoto mediante Azure Purview Studio a través de la experiencia de interfaz de usuario. 

Considere la posibilidad de usar esta guía si el inquilino de Azure AD donde se encuentra el inquilino de Power BI es diferente del inquilino de Azure AD donde se aprovisiona la cuenta de Azure Purview. Siga estos pasos para registrar y examinar uno o varios inquilinos de Power BI en Azure Purview en un escenario entre inquilinos:

1. Descargue los [módulos de exámenes administrados de PowerShell](https://github.com/Azure/Purview-Samples/blob/master/Cross-Tenant-Scan-PowerBI/ManagedScanningPowerShell.zip) y extraiga su contenido en la ubicación que prefiera.

1. En el equipo, escriba **PowerShell** en el cuadro de búsqueda de la barra de tareas de Windows. En la lista de búsqueda, seleccione y mantenga presionado o haga clic con el botón derecho en **Windows PowerShell** y seleccione **Ejecutar como administrador**.

1. Instale e importe el módulo en la máquina si aún no se ha instalado.

   ```powershell
    Install-Module -name az
    Import-Module -name az
    Login-AzAccount
    ```

1. Inicie sesión en el entorno de Azure con las credenciales de administrador de Azure AD donde se encuentra el inquilino de Power BI.

   ```powershell
    Login-AzAccount
    ```

1. En la ventana de PowerShell, escriba el comando siguiente, reemplazando `<path-to-managed-scanning-powershell-modules>` por la ruta de acceso a la carpeta de los módulos extraídos, como `C:\Program Files\WindowsPowerShell\Modules\ManagedScanningPowerShell`.

   ```powershell
   dir -Path <path-to-managed-scanning-powershell-modules> -Recurse | Unblock-File
   ```

1. Escriba el siguiente comando para instalar los módulos de PowerShell.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\ManagedScanningPowerShell\Microsoft.DataCatalog.Management.Commands.dll'
   ```

1. Use la misma sesión de PowerShell para establecer los parámetros siguientes. Actualice `purview_tenant_id` con el identificador de inquilino de Azure AD donde se ha implementado Azure Purview, `powerbi_tenant_id` con el inquilino de Azure AD donde se encuentra el inquilino de Power BI, donde `purview_account_name` es la cuenta de Azure Purview existente.

   ```powershell
   $azuretenantId = '<purview_tenant_id>'
   $powerBITenantIdToScan = '<powerbi_tenant_id>'
   $purviewaccount = '<purview_account_name>'
   ```

1. Cree una entidad de servicio entre inquilinos.

   1. Cree un registro de aplicación en Azure Active Directory inquilino donde se encuentra Power BI. Asegúrese de actualizar el campo `password` con una contraseña segura y de actualizar `app_display_name` con un nombre de aplicación inexistente en el inquilino de Azure AD donde se hospeda el inquilino de Power BI.

       ```powershell   
       $SecureStringPassword = ConvertTo-SecureString -String <'password'> -AsPlainText -Force
       $AppName = '<app_display_name>'
       New-AzADApplication -DisplayName $AppName -Password $SecureStringPassword
       ```

   1. En el panel de Azure Active Directory, seleccione la aplicación recién creada y, a continuación, seleccione **Registro de aplicación**. Asigne a la aplicación los siguientes permisos delegados y otorgue consentimiento de administrador al inquilino:

         - Servicio Power BI: Tenant.Read.All
         - Microsoft Graph: openid

   1. En el panel de Azure Active Directory, seleccione la aplicación recién creada y, a continuación, seleccione **Autenticación**. En **Tipos de cuenta admitidos** seleccione **Cuentas en cualquier directorio organizativo (cualquier directorio de Azure AD: multiinquilino)** .

   1. Para crear una dirección URL de inicio de sesión específica del inquilino para la entidad de servicio, ejecute la siguiente dirección URL en el explorador web:

     https://login.microsoftonline.com/<purview_tenant_id>/oauth2/v2.0/authorize?client_id=<client_id_to_delegate_the_pbi_admin>&scope=openid&response_type=id_token&response_mode=fragment&state=1234&nonce=67890

    Asegúrese de reemplazar los parámetros por la información correcta: <purview_tenant_id> es el identificador de inquilino (GUID) de Azure Active Directory donde se aprovisiona la cuenta de Azure Purview.
    <client_id_to_delegate_the_pbi_admin> es el identificador de aplicación correspondiente a la entidad de servicio.

   1. Inicie sesión con cualquier cuenta que no sea de administrador. Esto es necesario para aprovisionar la entidad de servicio en el inquilino externo.

   1. Cuando se le solicite, acepte el permiso solicitado para _Ver su perfil básico_ y _Mantener el acceso a los datos a los que se le ha concedido acceso_.

1. Actualice `client_id_to_delegate_the_pbi_admin` con el identificador de aplicación (cliente) de la aplicación recién creada y ejecute el siguiente comando en la sesión de PowerShell:

   ```powershell
   $ServicePrincipalId = '<client_id_to_delegate_the_pbi_admin>'
   ```

1. Cree una cuenta de usuario en el inquilino de Azure Active Directory donde se encuentra el inquilino de Power BI, y asigne el rol de Azure AD, **administrador de Power BI**. Actualice `pbi_admin_username` y `pbi_admin_password` con la información correspondiente y ejecute las siguientes líneas en el terminal de PowerShell:

    ```powershell
    $UserName = '<pbi_admin_username>'
    $Password = '<pbi_admin_password>'
    ```

1. En la suscripción de Azure Purview, busque la cuenta de Purview y use los roles de Azure RBAC, asigne _administrador de origen de datos de Purview_ a la entidad de servicio y al usuario de Power BI.

1. Para registrar el inquilino de Power BI entre inquilinos como nuevo origen de datos dentro de la cuenta de Azure Purview, actualice `service_principal_key` y ejecute los siguientes cmdlets en la sesión de PowerShell:

    ```powershell
    Set-AzDataCatalogSessionSettings -DataCatalogSession -TenantId $azuretenantId -ServicePrincipalAuthentication -ServicePrincipalApplicationId $ServicePrincipalId -ServicePrincipalKey '<service_principal_key>' -Environment Production -DataCatalogAccountName $purviewaccount

    Set-AzDataCatalogDataSource -Name 'pbidatasource' -AccountType PowerBI -Tenant $powerBITenantIdToScan -Verbose
    ```

## <a name="scan"></a>Examinar

Siga los pasos que tiene a continuación para examinar al inquilino de Power BI e identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

En esta guía se tratan los escenarios de examinación de [mismo inquilino](#create-and-run-scan-for-same-tenant-power-bi) y [entre inquilinos](#create-and-run-scan-for-cross-tenant-power-bi).

### <a name="create-and-run-scan-for-same-tenant-power-bi"></a>Creación y ejecución de un examen para el mismo inquilino de Power BI

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. En Purview Studio, vaya a **Mapa de datos** en el menú izquierdo.

1. Vaya a **Sources** (Orígenes).

1. Seleccione el origen de Power BI registrado.

1. Seleccione **+ New scan** (+ Nuevo examen).

1. Asigne un nombre al examen. A continuación, seleccione la opción para incluir o excluir las áreas de trabajo personales. Tenga en cuenta que el único método de autenticación admitido es **Identidad administrada**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Imagen que muestra la configuración del examen de Power BI.":::

    > [!Note]
    > Al cambiar la configuración de un análisis para incluir o excluir un área de trabajo personal, se desencadenará un análisis completo del origen de Power BI.

1. Seleccione **Probar la conexión** antes de continuar con los pasos siguientes. Si se produce un error al **Probar la conexión**, seleccione **Ver informe** para ver el estado detallado y solucionar el problema.
    1. Acceso: El estado de error significa que ha habido un error en la autenticación del usuario. Los exámenes que usan la identidad administrada siempre se superarán porque no se requiere autenticación de usuario.
    1. Recursos (+ linaje): El estado de error significa que ha habido un error en la autorización de Purview-Power BI. Asegúrese de que la identidad administrada de Purview se agregue al grupo de seguridad asociado en el portal de administración de Power BI.
    1. Metadatos detallados (mejorados): El estado de error significa que el portal de administración de Power BI está deshabilitado para la siguiente configuración: **Mejora de las respuestas de las API de administración con metadatos detallados**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-test-connection-status-report.png" alt-text="Captura de pantalla de la página del informe de estado de la conexión de prueba.":::

1. Configure un desencadenador de examen. Las opciones disponibles son: **Una sola vez**, **Cada 7 días** y **Cada 30 días**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Captura de pantalla del programador de exámenes de Purview.":::

1. En **Revisar nuevo examen**, seleccione **Guardar y ejecutar** para iniciar el examen.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Captura de pantalla para guardar y ejecutar el origen de Power BI.":::

### <a name="create-and-run-scan-for-cross-tenant-power-bi"></a>Creación y ejecución de un examen para entre inquilinos de Power BI

Para crear y ejecutar un nuevo examen en Azure Purview, ejecute los siguientes cmdlets en la sesión de PowerShell:

   ```powershell
   Set-AzDataCatalogScan -DataSourceName 'pbidatasource' -Name 'pbiscan' -AuthorizationType PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password -IncludePersonalWorkspaces $true -Verbose

   Start-AzDataCatalogScan -DataSourceName 'pbidatasource' -Name 'pbiscan'
   ```

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
