---
title: Cómo registrar y examinar áreas de trabajo de Azure Synapse Analytics
description: Obtenga información sobre cómo examinar un área de trabajo de Azure Synapse en su catálogo de datos de Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8a7b23089e9b17e35b56b04991c76b37baedf231
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207799"
---
# <a name="register-and-scan-azure-synapse-analytics-workspaces"></a>Registro y examen de áreas de trabajo de Azure Synapse Analytics

En este artículo se describe cómo registrar un área de trabajo de Azure Synapse Analytics en Azure Purview y cómo configurar un examen de esta.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Los exámenes de áreas de trabajo de Azure Synapse Analytics permiten capturar metadatos y esquemas con relación a bases de datos SQL dedicadas y sin servidor contenidas dentro de ellos. Los exámenes de áreas de trabajo también clasifican los datos automáticamente en función de las reglas de clasificación personalizadas y del sistema.

## <a name="prerequisites"></a>Requisitos previos

- Antes de registrar los orígenes de datos, cree una cuenta de Azure Purview. Para obtener más información, consulte [Guía de inicio rápido: Creación de una cuenta de Azure Purview](create-catalog-portal.md).
- Debe ser administrador de orígenes de datos de Azure Purview.
- Configure la autenticación tal como se explica en las secciones siguientes.

## <a name="register-and-scan-an-azure-synapse-workspace"></a>Registro y examen de áreas de trabajo de Azure Synapse

> [!IMPORTANT]
> Para examinar el área de trabajo correctamente, siga los pasos y aplique los permisos exactamente como se describen en las secciones siguientes.

### <a name="step-1-register-your-source"></a>**Paso 1**: Registrar los dispositivos

> [!NOTE]
> Solo los usuarios con al menos un rol *Lector* en el área de Azure Synapse que también sean *administradores de orígenes de datos* en Azure Purview pueden realizar este paso.

Para registrar un nuevo origen de Azure Synapse en el catálogo de datos, haga lo siguiente:

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


### <a name="step-2-apply-permissions-to-enumerate-the-contents-of-the-azure-synapse-workspace"></a>**Paso 2**: Aplicar permisos para enumerar el contenido del área de trabajo de Azure Synapse

#### <a name="set-up-authentication-for-enumerating-dedicated-sql-database-resources"></a>Configuración de la autenticación para enumerar recursos de bases de datos SQL dedicadas

1. En Azure Portal, vaya al recurso de área de trabajo de Azure Synapse.  
1. En el panel izquierdo, seleccione  **Control de acceso (IAM)** . 

   > [!NOTE]
   > Debe ser *propietario* o *administrador de acceso de usuarios* para agregar un rol al grupo de recursos.
   
1. Seleccione el botón **Agregar**.   
1. Establezca el rol **Lector** y escriba el nombre de la cuenta de Azure Purview, que representa su identidad de servicio administrada (MSI).
1. Haga clic en **Guardar** para finalizar la asignación del rol.

> [!NOTE]
> Si planea registrar y examinar varias áreas de trabajo de Azure Synapse en su cuenta de Azure Purview, también puede asignar el rol desde un nivel superior, como un grupo de recursos o una suscripción. 

#### <a name="set-up-authentication-for-enumerating-serverless-sql-database-resources"></a>Configuración de la autenticación para enumerar recursos de bases de datos SQL sin servidor

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

### <a name="step-3-apply-permissions-to-scan-the-contents-of-the-workspace"></a>**Paso 3**: Aplicar permisos para examinar el contenido del área de trabajo

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

### <a name="step-4-set-up-azure-synapse-workspace-firewall-access"></a>**Paso 4**: Configurar el acceso al firewall del área de trabajo de Azure Synapse

1. En Azure Portal, vaya al área de trabajo de Azure Synapse. 

1. En el panel izquierdo, seleccione **Firewalls**.

1. En **Permitir que los servicios y recursos de Azure accedan a esta área de trabajo**, seleccione **Activado**.

1. Seleccione **Guardar**.

### <a name="step-5-set-up-a-scan-on-the-workspace"></a>**Paso 5**: Configurar un examen en el área de trabajo

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

#### <a name="view-your-scans-and-scan-runs"></a>Visualización de los exámenes y las ejecuciones de exámenes

1. Para ver los detalles del origen, haga clic en **Ver detalles** en el icono de la sección de Orígenes. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Captura de pantalla de la Azure Synapse Analytics detalles del origen."::: 

1. Para ver los detalles de la ejecución del examen, vaya a la página **Detalles del examen**.

    * La **barra de estado** muestra un breve resumen sobre el estado de ejecución de los recursos secundarios. El estado se muestra en el examen de nivel de área de trabajo.  
    * El color verde indica una ejecución de examen correcta, el rojo, una ejecución de examen con errores, y el gris, que la ejecución del examen todavía está en curso.  
    * Puede ver información más pormenorizada sobre las ejecuciones de examen seleccionándolas.

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Captura de pantalla de la página de detalles del examen de Azure Synapse Analytics." lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

    * Puede ver un resumen de las ejecuciones de exámenes recientes con errores en la parte inferior de la **página de detalles del origen**. De nuevo, puede ver información más pormenorizada sobre las ejecuciones de examen seleccionándolas.

#### <a name="manage-your-scans"></a>Administración de exámenes

Para editar, eliminar o cancelar un examen, haga lo siguiente:

1. Vaya al centro de administración. En la sección **Orígenes y exámenes**, seleccione **Orígenes de datos** y, a continuación, el origen de datos que desea administrar.

1. Seleccione el examen que desea administrar y, a continuación, seleccione **Editar**.

   - Seleccione **Eliminar** para eliminar el examen.
   - Si se está ejecutando un examen, también puede cancelarlo.

## <a name="next-steps"></a>Pasos siguientes

- [Navegación por el catálogo de datos de Azure Purview](how-to-browse-catalog.md)
- [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)   
