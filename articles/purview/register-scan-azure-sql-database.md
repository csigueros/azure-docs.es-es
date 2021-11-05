---
title: Registro y examen de Azure SQL DB
description: En este artículo se describe el proceso para registrar una base de datos de Azure SQL en Azure Purview, incluidas instrucciones para autenticarse e interactuar con el origen de Azure SQL DB.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: c2ed5a0a65bd4d10620b0c72dbb5efb2aac4152f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131062005"
---
# <a name="connect-to-azure-sql-database-in-azure-purview"></a>Conexión a Azure SQL Database en Azure Purview

En este artículo se describe el proceso para registrar un origen de datos de Azure SQL en Azure Purview, incluidas instrucciones para autenticarse e interactuar con el origen de base de datos de Azure SQL.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register) | [Sí](#scan)|[Sí](#scan) | [Sí](#scan)|[Sí](#scan)| No |[Linaje de Data Factory](how-to-link-azure-data-factory.md)|

### <a name="known-limitations"></a>Restricciones conocidas

* Azure Purview no admite más de 300 columnas en la pestaña Esquema y mostrará el mensaje "Additional-Columns-Truncated" (Columnas adicionales truncadas).

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

## <a name="register"></a>Register

Esta sección le permitirá registrar el origen de datos de Azure SQL DB y configurar un mecanismo de autenticación adecuado para garantizar un examen correcto del origen de datos.

### <a name="steps-to-register"></a>Pasos para registrarse

Es importante registrar el origen de datos en Azure Purview antes de configurar un examen para el origen de datos.

1. Vaya a [Azure Portal](https://portal.azure.com), vaya a la página **Cuentas de Purview** y seleccione su _cuenta de Purview_.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-purview-acct.png" alt-text="Captura de pantalla que muestra la cuenta de Purview usada para registrar el origen de datos":::

1. Abra **Purview Studio** y vaya hasta **Mapa de datos**.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-open-purview-studio.png" alt-text="Captura de pantalla que navega al vínculo Orígenes en el Mapa de datos":::

1. Cree la [Jerarquía de colecciones](./quickstart-create-collection.md) mediante el menú **Colecciones** y asigne permisos a las subcolecciones individuales, según sea necesario.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-collections.png" alt-text="Captura de pantalla que muestra el menú de recopilación para asignar permisos de control de acceso a la jerarquía de recopilación":::

1. Vaya a la colección adecuada en el menú **Orígenes** y seleccione el icono **Registrar** para registrar una nueva instancia de Azure SQL DB.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-data-source.png" alt-text="Captura de pantalla que muestra la colección usada para registrar el origen de datos":::

1. Seleccione el origen de datos de **Azure SQL Database** y, a continuación, **Continuar**.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-select-ds.png" alt-text="Captura de pantalla que permite seleccionar el origen de datos":::

1. Asigne un **Nombre** adecuado al origen de datos, seleccione la **suscripción de Azure** correspondiente, el **nombre de servidor** de SQL Server y la **colección**, y seleccione **Aplicar**.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-ds-details.png" alt-text="Captura de pantalla que muestra los detalles que debe escribir para registrar el origen de datos":::

1. La base de datos de Azure SQL Server se mostrará en la colección seleccionada.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-ds-collections.png" alt-text="Captura de pantalla que muestra el origen de datos asignado a la colección para iniciar el examen":::

## <a name="scan"></a>Examinar

### <a name="authentication-for-a-scan"></a>Autenticación para un examen

Para tener acceso para examinar el origen de datos, es necesario configurar un método de autenticación en Azure SQL Database.
Se admiten las siguientes opciones:

* **Autenticación de SQL**

* **Identidad administrada**: en cuanto se crea la cuenta de Azure Purview, se crea automáticamente una **identidad administrada** por el sistema en el inquilino de Azure AD. Según el tipo de recurso, se requieren asignaciones de roles RBAC específicas para que la MSI de Azure Purview realice los exámenes.
Si usa la identidad administrada, la cuenta de Purview tiene la suya propia, que es básicamente el nombre que le diera a la cuenta de Purview al crearla.

* **Entidad de servicio**: En este método, puede crear una nueva o usar una entidad de servicio existente en el inquilino de Azure Active Directory.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Configuración de la autenticación de Azure AD en la cuenta de la base de datos

La entidad de servicio o la identidad administrada debe tener permiso para obtener los metadatos de la base de datos, los esquemas y las tablas. También debe ser capaz de consultar las tablas para realizar el muestreo para la clasificación.

- [Configuración y administración de la autenticación de Azure AD con Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Debe crear un usuario de Azure AD en Azure SQL Database con la identidad administrada de Purview exacta o su propia entidad de servicio siguiendo el tutorial [Cree el usuario de la entidad de servicio en Azure SQL Database](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database). Tiene que asignar el permiso adecuado (por ejemplo, `db_datareader`) a la identidad. Ejemplo de la sintaxis de SQL para crear el usuario y conceder el permiso:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [Username]
    GO
    ```

    > [!Note]
    > `Username` es la entidad de servicio propia o la identidad administrada de Purview. Puede leer más información sobre [roles fijos de base de datos y sus funcionalidades](/sql/relational-databases/security/authentication-access/database-level-roles#fixed-database-roles).

#### <a name="using-sql-authentication-for-scanning"></a>Uso de la autenticación de SQL para el examen

> [!Note]
> Solo pueden crear inicios de sesión el inicio de sesión de entidad de seguridad a nivel de servidor (creado por el proceso de aprovisionamiento) o los miembros del rol de base de datos `loginmanager` en la base de datos maestra. Unos **15 minutos** después de conceder el permiso, la cuenta de Purview debería tener los permisos adecuados para poder examinar los recursos.

Puede seguir las instrucciones de [CREAR INICIO DE SESIÓN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) para crear un inicio de sesión de Azure SQL Database si aún no tiene uno. En los pasos siguientes necesitará un **nombre de usuario** y una **contraseña**.

1. Vaya al almacén de claves en Azure Portal.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault.png" alt-text="Captura de pantalla que muestra el almacén de claves":::

1. Seleccione **Configuración > Secretos** y, después, **+ Generar/Importar**.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para generar un secreto":::

1. Escriba el **nombre** y el **valor** como la *contraseña* de la instancia de Azure SQL Database.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret-sql.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para especificar los valores del secreto de SQL":::

1. Seleccione **Crear** para completar la acción.

1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

1. Por último, [cree una nueva credencial](manage-credentials.md#create-a-new-credential) mediante la clave para configurar el examen.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-credentials.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para configurar las credenciales":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault-options.png" alt-text="Captura de pantalla en la que se muestra el almacén de claves para crear un secreto":::

#### <a name="using-managed-identity-for-scanning"></a>Uso de la identidad administrada para examinar

Es importante conceder a la cuenta de Purview el permiso para examinar la instancia de Azure SQL DB. Puede Agregar la MSI del catálogo en el nivel de suscripción, grupo de recursos o recurso, en función de para qué desea que tenga permisos de examen.

> [!Note] 
> Debe ser propietario de la suscripción para poder agregar una identidad administrada a un recurso de Azure.

1. En [Azure Portal](https://portal.azure.com), busque la suscripción, el grupo de recursos o el recurso (por ejemplo, una instancia de Azure SQL Database) que desea permitir que el catálogo examine.

1. Seleccione **Control de acceso (IAM)** en el panel de navegación izquierdo y, después, seleccione **+ Agregar** --> **Agregar asignación de roles**.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sql-ds.png" alt-text="Captura de pantalla que muestra la base de datos de Azure SQL":::

1. Establezca el **Rol** en **Lector** y escriba el _nombre de la cuenta de Azure Purview_ en el cuadro de entrada **Seleccionar**. A continuación, seleccione **Save** (Guardar) para dar esta asignación de rol a su cuenta de Purview.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-access-managed-identity.png" alt-text="Captura de pantalla que muestra los detalles para asignar permisos para la cuenta de Purview":::

#### <a name="using-service-principal-for-scanning"></a>Uso de la entidad de servicio para examinar

##### <a name="creating-a-new-service-principal"></a>Creación de una nueva entidad de servicio

Si tiene que [crear una nueva entidad de servicio](./create-service-principal-azure.md), es necesario registrar una aplicación en el inquilino de Azure AD y proporcionar acceso a la entidad de servicio en los orígenes de datos. El administrador global de Azure AD u otros roles, como el administrador de aplicaciones, pueden realizar esta operación.

##### <a name="getting-the-service-principals-application-id"></a>Obtención del identificador de aplicación de la entidad de servicio

1. Copie el **Id. de aplicación (cliente)** presente en la **Información general** de la [_Entidad de servicio_](./create-service-principal-azure.md) ya creada.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp-appln-id.png" alt-text="Captura de pantalla que muestra el identificador de aplicación (cliente) de la entidad de servicio":::

##### <a name="granting-the-service-principal-access-to-your-azure-sql-database"></a>Concesión de acceso a la entidad de servicio a Azure SQL Database

1. Vaya al almacén de claves en Azure Portal.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault.png" alt-text="Captura de pantalla que muestra el almacén de claves para el que agregar un secreto para la entidad de servicio":::

1. Seleccione **Configuración > Secretos** y, después, **+ Generar/Importar**.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para generar un secreto para la entidad de servicio":::

1. Introduzca el **nombre** que quiera y el **valor** como el **secreto de cliente** de su entidad de servicio.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-create-secret.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para especificar los valores del secreto":::

1. Seleccione **Crear** para completar la acción.

1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

1. Por último, [cree una nueva credencial](manage-credentials.md#create-a-new-credential) mediante la clave para configurar el examen.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-credentials.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para agregar credenciales para la entidad de servicio":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp-cred.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para crear un secreto para la entidad de servicio":::

### <a name="firewall-settings"></a>Configuración de firewall

Si el servidor de bases de datos tiene habilitado un firewall, deberá actualizarlo para permitir el acceso de una de estas dos maneras:

1. Permitir las conexiones de Azure a través del firewall.
1. Instalar un entorno de ejecución de integración autohospedado y darle acceso a través del firewall.

#### <a name="allow-azure-connections"></a>Permitir conexiones de Azure

La habilitación de las conexiones de Azure permitirá que Azure Purview llegue al servidor y se conecte sin actualizar el propio firewall. Puede seguir la guía de procedimientos para las [Conexiones desde dentro de Azure](../azure-sql/database/firewall-configure.md#connections-from-inside-azure).

1. Vaya a la cuenta de la base de datos.
1. En la página **Información general**, seleccione el nombre del servidor.
1. Seleccione **Security > Firewalls and virtual networks** (Seguridad > Firewalls y redes virtuales).
1. Seleccione **Sí** en **Permitir que los servicios y recursos de Azure accedan a este servidor**
:::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-firewall.png" alt-text="Permitir que los servicios y recursos de Azure accedan a este servidor" border="true":::.

#### <a name="self-hosted-integration-runtime"></a>Entorno de ejecución de integración autohospedado

Se puede instalar un entorno de ejecución de integración autohospedado (SHIR) en una máquina para conectarse con un recurso de una red privada.

1. [Cree e instale un entorno de ejecución de integración autohospedado](./manage-integration-runtimes.md) en una máquina personal o en una máquina dentro de la misma red virtual que el servidor de bases de datos.
1. Compruebe el firewall del servidor de bases de datos para confirmar que la máquina con el SHIR tiene acceso a través del firewall. Agregue la dirección IP de la máquina si aún no tiene acceso.
1. Si la instancia de Azure SQL Server está detrás de un punto de conexión privado o en una red virtual, puede usar un [punto de conexión privado de ingesta](catalog-private-link-ingestion.md#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources) para garantizar el aislamiento de red de un extremo a otro.

### <a name="creating-the-scan"></a>Creación del examen

1. Abra la **cuenta de Purview** y seleccione **Abrir Purview Studio**.
1. Vaya a **Mapa de datos** --> **Orígenes** para ver la jerarquía de colecciones.
1. Seleccione el icono **Nuevo examen** en la instancia de **Azure SQL DB** registrada anteriormente.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-new-scan.png" alt-text="Captura de pantalla que muestra la pantalla para crear un nuevo examen":::

#### <a name="if-using-sql-authentication"></a>Uso de la autenticación de SQL

1. Proporcione un **Nombre** para el examen, seleccione **Método de selección de base de datos** como _Escribir manualmente_, escriba el **nombre de la base de datos** y la **credencial** creada anteriormente, elija la colección adecuada para el examen y seleccione **Probar conexión** para validar la conexión. Cuando la validación sea correcta, seleccione **Continuar**.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sql-auth.png" alt-text="Captura de pantalla que muestra la opción de autenticación de SQL para el examen":::

#### <a name="if-using-managed-identity"></a>Si se utiliza una identidad administrada

1. Proporcione un **Nombre** para el examen, seleccione **Purview MSI** en **Credencial**, elija la colección adecuada para el examen.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-managed-id.png" alt-text="Captura de pantalla que muestra la opción de identidad administrada para ejecutar el examen":::

1. Seleccione **Test connection** (Probar conexión). Tras una conexión correcta, seleccione **Continuar**.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-test.png" alt-text="Captura de pantalla que permite que la opción de identidad administrada ejecute el examen":::

#### <a name="if-using-service-principal"></a>Si se utiliza una entidad de servicio

1. Proporcione un **Nombre** para el examen, seleccione la colección adecuada para el examen y seleccione el menú desplegable **Credencial** para seleccionar la credencial creada anteriormente.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp.png" alt-text="Captura de pantalla que muestra la opción para que la entidad de servicio habilite el examen":::

1. Seleccione **Test connection** (Probar conexión). Tras una conexión correcta, seleccione **Continuar**.

### <a name="scoping-and-running-the-scan"></a>Ámbito y ejecución del examen

1. Para limitar el ámbito del examen a carpetas y subcarpetas específicas, puede elegir los elementos adecuados en la lista.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scope-scan.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen. Puede elegir entre los valores predeterminados del sistema, los conjuntos de reglas personalizadas existentes o la creación de un conjunto de reglas en línea.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-rule-set.png" alt-text="Conjunto de reglas de examen":::

1. Si crea un nuevo _conjunto de reglas de examen_

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-new-scan-rule-set.png" alt-text="Nuevo conjunto de reglas de examen":::

1. Puede seleccionar las **reglas de clasificación** que se incluirán en la regla de examen.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-classification.png" alt-text="Reglas de clasificación del conjunto de reglas de examen":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sel-scan-rule.png" alt-text="Selección del conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación o ejecutar el examen una vez.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-trigger.png" alt-text="desencadenador del examen":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-review-scan.png" alt-text="revisión del examen":::

### <a name="view-scan"></a>Visualización del examen

1. Vaya al _origen de datos_ en la _Colección_ y seleccione **Ver detalles** para comprobar el estado del examen.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-view-scan.png" alt-text="visualización del examen":::

1. Los detalles del examen indican el progreso del examen en el **Estado de la última ejecución** y el número de recursos _examinados_ y _clasificados_.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-view-scan-details.png" alt-text="Visualización de los detalles del examen":::

1. El **Estado de la última ejecución** se actualizará a **En curso** y, posteriormente, a **Completado** una vez que todo el examen se haya ejecutado correctamente.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-complete.png" alt-text="visualización del examen completado":::

### <a name="manage-scan"></a>Administración del examen

Los exámenes se pueden administrar o ejecutar de nuevo al finalizar.

1. Seleccione el **Nombre del examen** para administrar el examen.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-manage scan.png" alt-text="administración del examen":::

1. Puede volver a _ejecutar el examen_, _editarlo_ o _eliminarlo_.  

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-manage-scan-options.png" alt-text="administración de las opciones del examen":::

1. Puede volver a _ejecutar un examen incremental_ o un _examen completo_.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-full-inc.png" alt-text="examen completo o incremental":::

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
