---
title: Conectar y administrar áreas de trabajo de Azure Synapse Analytics
description: En esta guía se describe cómo conectarse a áreas de trabajo de Azure Synapse Analytics en Azure Purview y cómo usar las características de Purview para examinar y administrar el origen de área de trabajo de Azure Synapse Analytics.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: ed76730cd37cf903c77b0893b546ab824e7ddff3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010923"
---
# <a name="connect-to-and-manage-azure-synapse-analytics-workspaces-in-azure-purview"></a>Conexión y administración de áreas de trabajo de Azure Synapse Analytics en Azure Purview

En este artículo se describe cómo registrar áreas de trabajo Azure Synapse Analytics y cómo autenticarse e interactuar con áreas de trabajo de Azure Synapse Analytics en Azure Purview. Para obtener más información sobre Azure Purview, consulte el [artículo de introducción](overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register) | [Sí](#scan)| [Sí](#scan) | [Sí](#scan)| [Sí](#scan)| No| [Sí](how-to-lineage-azure-synapse-analytics.md)|


<!-- 4. Prerequisites
Required. Add any relevant/source-specific prerequisites for connecting with this source. Authentication/Registration should be covered by the sections below and does not need to be covered here.
-->

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

## <a name="register"></a>Register

En esta sección se describe cómo registrar áreas de trabajo de Azure Synapse Analytics en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Autenticación para registro

Solo los usuarios con al menos un rol de *Lector* en el área de trabajo de Azure Synapse que también sean *administradores de orígenes de datos* en Azure Purview pueden realizar este paso.

### <a name="steps-to-register"></a>Pasos para registrarse

1. Vaya a la cuenta de Azure Purview.
1. En el panel izquierdo, seleccione **Dispositivos**.
1. Seleccione **Registrar**.
1. En **Registrar orígenes**, seleccione **Azure Synapse Analytics (varios)** .
1. Seleccione **Continuar**.

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Captura de pantalla de una selección de orígenes en Azure Purview, incluidos Azure Synapse Analytics.":::

1. En la página **Registrar orígenes (Azure Synapse Analytics)** , haga lo siguiente:

    a. Escriba un **nombre** con el que se muestre el origen de datos en el catálogo.  
    b. También puede seleccionar una **suscripción** por la que filtrar.  
    c. En la lista desplegable **Nombre del área de trabajo**, seleccione el área de trabajo con la que está trabajando.  
    d. En las listas desplegables de puntos de conexión, los puntos de conexión SQL se rellenan automáticamente en función de la selección del área de trabajo.  
    e. En la lista desplegable **Seleccionar una colección**, elija la colección con la que está trabajando o, opcionalmente, cree una nueva.  
    f. Seleccione **Registrar** para registrar el origen de datos.

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Captura de pantalla de la página &quot;Registrar orígenes (Azure Synapse Analytics)&quot; para especificar detalles sobre el Azure Synapse origen.":::

## <a name="scan"></a>Examinar

Siga los pasos que se indican a continuación para áreas de trabajo de Azure Synapse Analytics para identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

Primero deberá configurar la autenticación para enumerar los recursos [dedicados](#authentication-for-enumerating-dedicated-sql-database-resources) o [sin servidor](#authentication-for-enumerating-serverless-sql-database-resources). Esto permitirá a Purview enumerar los recursos de área de trabajo y realizar exámenes dentro de ámbito.

Posteriormente, deberá [aplicar permisos para examinar el contenido del área de trabajo](#apply-permissions-to-scan-the-contents-of-the-workspace).

### <a name="authentication-for-enumerating-dedicated-sql-database-resources"></a>Autenticación para enumerar recursos de base de datos SQL dedicados

1. En Azure Portal, vaya al recurso de área de trabajo de Azure Synapse.  
1. En el panel izquierdo, seleccione  **Control de acceso (IAM)** .

   > [!NOTE]
   > Debe ser *propietario* o *administrador de acceso de usuarios* para agregar un rol al grupo de recursos.

1. Seleccione el botón **Agregar**.
1. Establezca el rol **Lector** y escriba el nombre de la cuenta de Azure Purview, que representa su identidad de servicio administrada (MSI).
1. Haga clic en **Guardar** para finalizar la asignación del rol.

> [!NOTE]
> Si planea registrar y examinar varias áreas de trabajo de Azure Synapse en su cuenta de Azure Purview, también puede asignar el rol desde un nivel superior, como un grupo de recursos o una suscripción.

### <a name="authentication-for-enumerating-serverless-sql-database-resources"></a>Autenticación para enumerar recursos de base de datos SQL sin servidor

Hay tres lugares en los que deberá establecer la autenticación para permitir que Purview enumere los recursos de base de datos SQL sin servidor: el área de trabajo de Synapse, el almacenamiento asociado y las bases de datos sin servidor. Los pasos siguientes establecerán permisos para los tres.

1. En Azure Portal, vaya al recurso de área de trabajo de Azure Synapse.  
1. En el panel izquierdo, seleccione  **Control de acceso (IAM)** . 

   > [!NOTE]
   > Debe ser *propietario* o *administrador de acceso de usuarios* para agregar un rol al grupo de recursos.
   
1. Seleccione el botón **Agregar**.   
1. Establezca el rol **Lector** y escriba el nombre de la cuenta de Azure Purview, que representa su identidad de servicio administrada (MSI).
1. Haga clic en **Guardar** para finalizar la asignación del rol.
1. En Azure Portal, vaya al **grupo de recursos** o a la **suscripción** donde se encuentre el área de trabajo de Azure Synapse.
1. En el panel izquierdo, seleccione  **Control de acceso (IAM)** . 

   > [!NOTE]
   > Debe ser *propietario* o *administrador de acceso de usuarios* para agregar un rol a los campos **Grupo de recursos** o **Suscripción**. 

1. Seleccione el botón **Agregar**. 
1. Establezca el rol **Lector de datos de Storage Blob** y escriba el nombre de la cuenta de Azure Purview (que representa su MSI) en el cuadro **Seleccionar**. 
1. Haga clic en **Guardar** para finalizar la asignación del rol.
1. Vaya al área de trabajo de Azure Synapse y abra el Synapse Studio.
1. Seleccione la pestaña **Datos** del menú de la izquierda.
1. Seleccione los puntos suspensivos ( **...** ) junto a una de las bases de datos y, a continuación, inicie un nuevo SQL script.
1. Agregue el MSI de la cuenta de Azure Purview (representado mediante el nombre de la cuenta) en las bases de datos SQL sin servidor ejecutando el siguiente comando en el script de SQL:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ```

### <a name="apply-permissions-to-scan-the-contents-of-the-workspace"></a>Aplicación de permisos para examinar el contenido del área de trabajo

Puede configurar la autenticación para un origen de Azure Synapse de dos maneras:

- Uso de una identidad administrada
- Uso de una entidad de servicio

> [!IMPORTANT]
> Estos pasos para las bases de datos sin servidor **no** se aplican a las bases de datos replicadas. Actualmente en Synapse, las bases de datos sin servidor que se replican desde bases de datos de Spark son de solo lectura. Haga clic [aquí](../synapse-analytics/sql/resources-self-help-sql-on-demand.md#operation-is-not-allowed-for-a-replicated-database) para obtener más información.

> [!NOTE]
> Debe configurar la autenticación en cada base de datos SQL dedicada que esté contenida en el área de trabajo de Azure Synapse que tenga intención de registrar y examinar. Los permisos que se mencionan en las secciones siguientes para la base de datos SQL sin servidor se aplican a todas las bases de datos dentro del área de trabajo. Es decir, tendrá que configurar la autenticación solo una vez.

#### <a name="use-a-managed-identity-for-dedicated-sql-databases"></a>Uso de una identidad administrada para bases de datos SQL dedicadas

1. Vaya al área de trabajo de Azure Synapse.
1. Vaya a la sección **Datos** y busque una de las bases de datos SQL dedicadas.
1. Seleccione los puntos suspensivos ( **...** ) junto a ella y, a continuación, inicie un nuevo SQL script.

   > [!NOTE]
   > Para ejecutar los comandos en el procedimiento siguiente, debe ser *administrador de Azure Synapse* en el área de trabajo. Para obtener más información sobre los permisos de Azure Synapse Analytics, consulte [Configuración del control de acceso para el área de Azure Synapse de trabajo](../synapse-analytics/security/how-to-set-up-access-control.md).

1. Agregue el MSI de la cuenta de Azure Purview (representado mediante el nombre de la cuenta) como **db_datareader** en la base de datos SQL dedicada ejecutando el siguiente comando en el script de SQL:

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [PurviewAccountName]
    GO
    ```

#### <a name="use-a-managed-identity-for-serverless-sql-databases"></a>Uso de una identidad administrada para bases de datos SQL sin servidor

1. Vaya al área de trabajo de Azure Synapse.
1. Vaya a la sección **Datos** y siga los pasos siguientes para **cada base de datos que quiera examinar**.
1. Seleccione los puntos suspensivos ( **...** ) junto a la base de datos y, a continuación, inicie un nuevo SQL script.
1. Agregue el MSI de la cuenta de Azure Purview (representado mediante el nombre de la cuenta) como **db_datareader** en las bases de datos SQL sin servidor ejecutando el siguiente comando en el script de SQL:
    ```sql
    CREATE USER [PurviewAccountName] FOR LOGIN [PurviewAccountName];
    ALTER ROLE db_datareader ADD MEMBER [PurviewAccountName]; 
    ```

#### <a name="grant-permission-to-use-credentials-for-external-tables"></a>Concesión de permiso para usar credenciales para tablas externas

Si el área de trabajo de Azure Synapse tiene tablas externas, a la identidad administrada de Azure Purview se le debe conceder el permiso Referencias en las credenciales con ámbito de tabla externa. Con el permiso Referencias, Azure Purview puede leer datos de tablas externas.

```sql
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::[scoped_credential] TO [PurviewAccountName];
```

#### <a name="use-a-service-principal-for-dedicated-sql-databases"></a>Uso de una entidad de servicio para bases de datos SQL dedicadas

> [!NOTE]
> Primero debe configurar una nueva *credencial* del tipo de *entidad de servicio* siguiendo las instrucciones de [Credenciales para la autenticación de origen en Azure Purview](manage-credentials.md).

1. Vaya al **área de trabajo de Azure Synapse**.
1. Vaya a la sección **Datos** y busque una de las bases de datos SQL dedicadas.
1. Seleccione los puntos suspensivos ( **...** ) junto a ella y, a continuación, inicie un nuevo SQL script.
1. Agregue el **identificador de la entidad de servicio** como **db_datareader** en la base de datos SQL dedicada ejecutando el siguiente comando en el script de SQL:

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [ServicePrincipalID]
    GO
    ```

> [!NOTE]
> Repita el paso anterior para todas las bases de datos SQL dedicadas en el área de trabajo de Synapse. 

#### <a name="use-a-service-principal-for-serverless-sql-databases"></a>Uso de una entidad de servicio para bases de datos SQL sin servidor

1. Vaya al área de trabajo de Azure Synapse.
1. Vaya a la sección **Datos** y busque una de las bases de datos SQL sin servidor.
1. Seleccione los puntos suspensivos ( **...** ) junto a ella y, a continuación, inicie un nuevo SQL script.
1. Agregue el **identificador de la entidad de servicio** para bases de datos SQL sin servidor ejecutando el siguiente comando en el script de SQL:
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ```
    
1. Agregue el **identificador de la entidad de servicio** como **db_datareader** para las bases de datos SQL sin servidor que desee examinar. ejecutando el siguiente comando en el script de SQL:
   ```sql
    CREATE USER [ServicePrincipalID] FOR LOGIN [ServicePrincipalID];
    ALTER ROLE db_datareader ADD MEMBER [ServicePrincipalID]; 
    ```

### <a name="set-up-azure-synapse-workspace-firewall-access"></a>Configuración del acceso al firewall de área de trabajo de Azure Synapse

1. En Azure Portal, vaya al área de trabajo de Azure Synapse. 

1. En el panel izquierdo, seleccione **Firewalls**.

1. En **Permitir que los servicios y recursos de Azure accedan a esta área de trabajo**, seleccione **Activado**.

1. Seleccione **Guardar**.

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. Seleccione la pestaña **Mapa de datos** en el panel izquierdo de [Purview Studio](https://web.purview.azure.com/resource/).

1. Seleccione el origen de datos que ha registrado.

1. Seleccione **Ver detalles** y, a continuación, seleccione la pestaña **Nuevo examen**. También puede usar el **icono de acción rápida Escanear** en el mosaico de origen.

1. En el panel de detalles **Examen**, en el cuadro **Nombre**, escriba un nombre para este examen.
1. En la lista desplegable **Tipo**, seleccione los tipos de recursos que quiere examinar dentro de este origen. **SQL Database** es el único tipo que se admite actualmente en un área de trabajo de Azure Synapse.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Captura de pantalla del panel de detalles del origen de Azure Synapse.":::

1. En la lista desplegable **Credencial**, seleccione la credencial para conectarse a los recursos del origen de datos. 
  
1. En cada tipo puede optar por examinar todos los recursos o un subconjunto de ellos por nombre.

1.  Haga clic en **Continuar** para seguir. 

1.  Seleccione un **conjunto de reglas de examen** de tipo **Azure Synapse SQL**. También puede crear conjuntos de reglas de examen insertados.

1. Elija el desencadenador del examen. Puede programarlo para que se ejecute **semanalmente, mensualmente** o **una vez**.

1. Revise el examen y seleccione **Guardar** para completar la configuración.  

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
