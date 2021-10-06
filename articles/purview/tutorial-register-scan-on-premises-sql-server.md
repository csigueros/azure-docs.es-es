---
title: 'Tutorial: Registro y examen de una instancia local de SQL Server'
description: En este tutorial, se describe cómo registrar una instancia local de SQL Server en Purview y examinar el servidor mediante un entorno de ejecución de integración autohospedado.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/27/2021
ms.custom: template-tutorial
ms.openlocfilehash: 0054b41fdf12efa4bbd3f1bf34e66023b7ea2d75
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213283"
---
# <a name="tutorial-register-and-scan-an-on-premises-sql-server"></a>Tutorial: Registro y examen de una instancia local de SQL Server

Azure Purview está diseñado para conectarse a orígenes de datos para ayudarle a administrar los datos confidenciales, simplificar la detección de datos y garantizar el uso correcto. Purview puede conectarse a orígenes en todo el entorno, incluidas varias nubes y entornos locales. En este escenario, usará un entorno de ejecución de integración autohospedado para conectarse a los datos de una instancia local de SQL Server. A continuación, usará Azure Purview para examinar y clasificar los datos.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Iniciar sesión en Purview Studio.
> * Crear una colección en Azure Purview.
> * Cree una instancia de Integration Runtime autohospedada.
> * Almacenar credenciales en Azure Key Vault.
> * Registrar una instancia local de SQL Server en Purview.
> * Examinar la instancia de SQL Server.
> * Examinar el catálogo de datos para ver los recursos de la instancia de SQL Server.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una instancia activa de [Azure Key Vault](../key-vault/certificates/quick-create-portal.md#create-a-vault).
- Una cuenta de Azure Purview. Si aún no tiene una, puede [seguir nuestra guía de inicio rápido para crear una](create-catalog-portal.md).
- Una [instancia local de SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads).

## <a name="sign-in-to-purview-studio"></a>Inicio de sesión en Purview Studio

Para interactuar con Purview, se conectará a [Purview Studio](https://web.purview.azure.com/resource/) mediante Azure Portal. Para encontrar Studio, vaya al recurso de Purview en [Azure Portal](https://portal.azure.com) y seleccione el icono **Abrir Purview Studio** en la página de información general.

:::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/open-purview-studio.png" alt-text="Captura de pantalla de la ventana de Purview en Azure Portal, con el botón Purview Studio resaltado." border="true":::

## <a name="create-a-collection"></a>Creación de una colección

Las colecciones de Azure Purview se usan para organizar los recursos y orígenes en una jerarquía personalizada para la organización y la detectabilidad. También son la herramienta utilizada para administrar el acceso en Purview. En este tutorial, crearemos una colección para hospedar el origen SQL Server y todos sus recursos. En este tutorial no se trata la información sobre la asignación de permisos a otros usuarios, por lo que, para más información, puede seguir nuestra guía [Control de acceso en Azure Purview](catalog-permissions.md).

### <a name="check-permissions"></a>Compruebe los permisos.

Para crear y administrar colecciones en Purview, deberá ser un **Administrador de colecciones** en Purview. Podemos comprobar estos permisos en [Purview Studio](use-purview-studio.md).

1. Seleccione **Data Map > Collections** (Mapa de datos > Colecciones) en el panel de la izquierda para abrir la página de administración de colecciones.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/find-collections.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la pestaña Colecciones seleccionada." border="true":::

1. Seleccione la colección raíz. La colección raíz es la principal de la lista de colecciones y tendrá el mismo nombre que el recurso de Purview. En el ejemplo siguiente, se llama Cuenta de Purview.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/select-root-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la colección raíz resaltada." border="true":::

1. Seleccione **Asignaciones de roles** en la ventana de colecciones.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/role-assignments.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la pestaña Asignaciones de roles resaltada." border="true":::

1. Para crear una colección, deberá estar en la lista de administradores de colecciones en las asignaciones de roles. Si creó el recurso de Purview, debería aparecer como administrador de colecciones en la colección raíz. Si no es así, deberá ponerse en contacto con el administrador de colecciones para que le conceda permiso.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/collection-admins.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la sección de administradores de colecciones resaltada." border="true":::

### <a name="create-the-collection"></a>Creación de la colección

1. Seleccione **+ Agregar una colección**. De nuevo, solo los [administradores de colecciones](#check-permissions) pueden administrar colecciones.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/select-add-a-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, en la que se muestra la ventana de la nueva colección, con los botones para agregar una colección resaltados." border="true":::

1. En el panel derecho, escriba el nombre y la descripción de la colección. Si es necesario, también puede agregar usuarios o grupos como administradores de colecciones a la nueva colección.
1. Seleccione **Crear**.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/create-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, en la que se muestra la ventana de la nueva colección, con un nombre para mostrar y administradores de colecciones seleccionados, así como el botón Crear resaltado." border="true":::

1. La información de la nueva colección se reflejará en la página.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/created-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, en la que se muestra la ventana de colección recién creada." border="true":::

## <a name="create-a-self-hosted-integration-runtime"></a>Creación de una instancia de Integration Runtime autohospedada

El entorno de ejecución de integración autohospedado (SHIR) es la infraestructura de proceso utilizada por Purview para conectarse a los orígenes de datos locales. El SHIR se descarga e instala en una máquina de la misma red que el origen de datos local.

En este tutorial, se da por supuesto que la máquina donde va a instalar el entorno de ejecución de integración autohospedado puede establecer conexiones de red a Internet. Esta conexión permite al SHIR comunicarse entre el origen y Azure Purview. Si la máquina tiene un firewall restringido, o si desea proteger el firewall, examine los [requisitos de red del entorno de ejecución de integración autohospedado](manage-integration-runtimes.md#networking-requirements).

1. En la página principal de Purview Studio, seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.

1. En **Source management** (Administración de orígenes) en el panel izquierdo, seleccione **Integration runtimes** (Entornos de ejecución de integración) y, a continuación, seleccione **+ New** (+ Nuevo).

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-integration-runtime.png" alt-text="Selección del botón Entornos de ejecución de integración.":::

1. En la página **Integration runtime setup** (Configuración de Integration Runtime), seleccione **Self-Hosted** (Autohospedado) para crear un entorno de ejecución de integración autohospedado y, luego, seleccione **Continuar** (Continuar).

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-self-hosted-ir.png" alt-text="Cree un nuevo entorno de ejecución de integración autohospedado.":::

1. Escriba un nombre para el entorno de ejecución de integración y seleccione Create (Crear).

1. En la página **Integration Runtime settings** (Parámetros de Integration Runtime), siga los pasos descritos en la sección **Manual setup** (Instalación manual). Tendrá que descargar el entorno de ejecución de integración desde el sitio de descarga en una máquina virtual o máquina física que se encuentre en la misma red que la instancia local de SQL Server. Para obtener información sobre el tipo de máquina necesaria, puede seguir nuestra guía [Creación y administración de un entorno de ejecución de integración autohospedado](manage-integration-runtimes.md#prerequisites).

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/integration-runtime-settings.png" alt-text="Obtener la clave.":::

   - Copie y pegue la clave de autenticación.

   - Descargue el entorno de ejecución de integración autohospedado desde [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) en una máquina Windows local. Ejecute al programa de instalación. Se admiten versiones del entorno de ejecución de integración autohospedado, como 5.4.7803.1 y 5.6.7795.1. 

   - En la página **Registro de Integration Runtime (autohospedado)** , pegue una de las dos claves guardadas anteriormente y seleccione **Registrar**.

     :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/register-integration-runtime.png" alt-text="Introducir la clave.":::

   - En la página **Nuevo nodo de Integration Runtime (autohospedado)** , seleccione **Finalizar**.

1. Verá la siguiente ventana cuando el entorno de ejecución de integración autohospedado se haya registrado correctamente:

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/successfully-registered.png" alt-text="Se ha registrado correctamente.":::

## <a name="set-up-sql-authentication"></a>Configuración de la autenticación de SQL

Solo hay una manera de configurar la autenticación para la instancia local de SQL Server:

- Autenticación de SQL

### <a name="sql-authentication"></a>Autenticación SQL

La cuenta de SQL debe tener acceso a la base de datos **maestra**. El motivo es que `sys.databases` está en la base de datos. El examen de Purview debe enumerar `sys.databases` para buscar todas las instancias de SQL en el servidor.

#### <a name="create-a-new-login-and-user"></a>Creación de un nuevo inicio de sesión y un usuario

Si quiere crear un nuevo inicio de sesión y un usuario para poder examinar el servidor de SQL Server, siga estos pasos:

> [!Note]
> Todos los pasos siguientes se pueden ejecutar con el código que se proporciona [aquí](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql).

1. Vaya a SQL Server Management Studio (SSMS), conéctese al servidor, vaya a Seguridad, haga clic con el botón derecho en Inicio de sesión y cree un nuevo inicio de sesión. Asegúrese de seleccionar la autenticación de SQL.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Creación de un nuevo inicio de sesión y un usuario.":::

1. Seleccione Roles de servidor en el panel de navegación izquierdo y asegúrese de que está asignado el rol público.

1. Seleccione sucesivamente Asignación de usuarios en el panel de navegación izquierdo, luego, todas las bases de datos del mapa y, seguidamente, el rol de base de datos **db_datareader**.

1. Seleccione **Aceptar** para guardar.

1. Vuelva al usuario que creó; para ello, seleccione y mantenga (o haga clic con el botón derecho) y seleccione **Propiedades**. Escriba una contraseña nueva y confírmela. Seleccione "Especificar la contraseña anterior" y escríbala. **Es necesario cambiar la contraseña en cuanto cree un nuevo inicio de sesión.**

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/change-password.png" alt-text="cambiar contraseña.":::

#### <a name="create-a-key-vault-credential"></a>Creación de una credencial de Key Vault

1. Vaya al almacén de claves en Azure Portal. Seleccione **Configuración > Secretos**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-secrets.png" alt-text="Seleccionar secretos en el menú de la izquierda":::

1. Seleccione **Generar/Importar**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-generate-import.png" alt-text="Seleccionar Generar/Importar en el menú superior.":::

1. Para las opciones de carga, seleccione **Manual** y escriba el **nombre** y el **valor** como la *contraseña* del inicio de sesión de SQL Server. Asegúrese de que **Habilitado** esté establecido en **Sí**. Si establece una fecha de activación y expiración, asegúrese de que la fecha de hoy esté entre las dos o no podrá usar la credencial.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/create-credential-secret.png" alt-text="Agregar valores a la credencial del almacén de claves.":::

1. Seleccione **Crear** para completar la acción.
1. En [Azure Purview Studio](#sign-in-to-purview-studio),vaya a la página **Management** (Administración) en el menú izquierdo.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-management.png" alt-text="Selección de la página de administración en el menú de la izquierda.":::

1. Seleccione la página **Credentials** (Credenciales).

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-credentials.png" alt-text="El botón Credenciales de la página de administración está resaltado.":::

1. En la página **Credentials** (Credenciales), seleccione **Manage Key Vault connections** (Administrar conexiones de Key Vault).

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/manage-key-vault-connections.png" alt-text="Administración de conexiones de Azure Key Vault.":::

1. Seleccione **+ New** (+ Nuevo) en la página Manage Key Vault connections (Administrar conexiones de Key Vault).

1. Rellene la información necesaria y seleccione **Create** (Crear).

1. Confirme que el almacén de claves se ha asociado correctamente a la cuenta de Azure Purview, como se muestra en este ejemplo:

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/view-kv-connections.png" alt-text="Vista de conexiones de Azure Key Vault para confirmar.":::

1. Cree una nueva credencial para SQL Server; para ello, seleccione **+ New** (+ Nuevo).

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-new.png" alt-text="Seleccionar +Nuevo para crear una credencial.":::

1. Proporcione la información necesaria. Seleccione el **método de autenticación** y una **conexión a Key Vault** desde la que seleccionar un secreto.

1. Una vez rellenados todos los detalles, seleccione **Create** (Crear).

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/new-credential.png" alt-text="Nueva credencial.":::

1. Compruebe que la nueva credencial aparece en la vista de lista y que está preparada para usarse.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/view-credentials.png" alt-text="Visualización de la credencial.":::

## <a name="register-sql-server"></a>Registro de SQL Server

1. Vaya a la cuenta de Purview en [Azure Portal](https://portal.azure.com) y seleccione [Purview Studio](#sign-in-to-purview-studio).

1. En el panel de navegación izquierdo de Orígenes y análisis, seleccione **Entornos de ejecución de integración**. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no está configurado, siga los pasos que se mencionan [aquí](manage-integration-runtimes.md) para crear un entorno de ejecución de integración autohospedado para realizar exámenes en una máquina virtual de Azure o local que tenga acceso a la red local.

1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.

1. Seleccione **Registrar**.

1. Seleccione **SQL Server** y, a continuación, **Continuar**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Configure el origen de datos de SQL.":::

1. Proporcione un nombre descriptivo y un punto de conexión de servidor y, a continuación, seleccione **Finalizar** para registrar el origen de datos. Si, por ejemplo, el FQDN de SQL Server es **foobar.database.windows.net**, escriba *foobar* como punto de conexión del servidor.

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. Seleccione la pestaña **Mapa de datos** en el panel izquierdo de Purview Studio.

1. Seleccione el origen de SQL Server que ha registrado.

1. Seleccione **Nuevo análisis**

1. Seleccione la credencial para conectarse al origen de datos.

1. Elija los elementos adecuados de la lista para limitar el ámbito del examen a tablas específicas.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/on-premises-sql-scope-your-scan.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen. Puede elegir entre los valores predeterminados del sistema, los conjuntos de reglas personalizadas existentes o la creación de un conjunto de reglas en línea.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/on-premises-sql-scan-rule-set.png" alt-text="Conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación o ejecutar el examen una vez.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/trigger-scan.png" alt-text="trigger":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando este origen de Purview o SQL más adelante, puede seguir los pasos siguientes para eliminar el entorno de ejecución de integración, la credencial de SQL y los recursos de Purview.

### <a name="remove-shir-from-purview"></a>Eliminación del SHIR de Purview

1. En la página principal de [Purview Studio](https://web.purview.azure.com/resource/), seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.

1. En **Source management** (Administración de orígenes) en el panel izquierdo, seleccione **Integration runtimes** (Entornos de ejecución de integración).

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-integration-runtime.png" alt-text="Selección del botón Entornos de ejecución de integración.":::

1. Active la casilla situada junto al entorno de ejecución de integración y, a continuación, seleccione el botón **Delete** (Eliminar).

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/delete-integration-runtime.png" alt-text="Casilla situada junto al entorno de ejecución de integración y botón para eliminar resaltados.":::

1. Seleccione **Confirm** (Confirmar) en la ventana siguiente para confirmar la eliminación.

1. La ventana se actualizará automáticamente y ya no debería ver el SHIR en la lista Integration Runtimes (Entornos de ejecución de integración).

### <a name="uninstall-self-hosted-integration-runtime"></a>Desinstalación del entorno de ejecución de integración autohospedado

1. Inicie sesión en la máquina en la que está instalado el entorno de ejecución de integración autohospedado.
1. Abra el panel de control y, en *Desinstalar un programa*, busque "Microsoft Integration Runtime".

1. Desinstale el entorno de ejecución de integración existente.

> [!IMPORTANT] 
> En el proceso siguiente, seleccione Sí. No mantenga los datos durante el proceso de desinstalación.

:::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-yes.png" alt-text="Captura de pantalla del botón &quot;Sí&quot; para eliminar todos los datos de usuario del entorno de ejecución de integración.":::

### <a name="remove-sql-credentials"></a>Eliminación de las credenciales de SQL

1. Vaya a [Azure Portal](https://portal.azure.com) y vaya al recurso de Key Vault donde ha almacenado las credenciales de Purview.

1. En el menú de la izquierda, en **Configuración**, seleccione **Secretos**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-secrets.png" alt-text="Seleccionar Secretos en el menú de la izquierda en Azure Key Vault.":::

1. Seleccione el secreto de la credencial de SQL Server que creó para este tutorial.
1. Seleccionar **Eliminar**

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-delete-credential.png" alt-text="Eliminar secreto desde el menú superior del secreto de Azure Key Vault.":::

1. Seleccione **Sí** para eliminar permanentemente el recurso.

### <a name="delete-purview-account"></a>Eliminación de la cuenta de Purview

Si desea eliminar la cuenta de Purview después de completar este tutorial, siga estos pasos.

1. Vaya a [Azure Portal](https://portal.azure.com) y vaya a la cuenta de Purview.

1. Seleccione el botón **Delete** (Eliminar) en la parte superior de la página.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-delete.png" alt-text="El botón Eliminar de la página de la cuenta de Purview de Azure Portal está seleccionado.":::

1. Una vez completado el proceso, recibirá una notificación en Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Uso de las API REST de Purview](tutorial-using-rest-apis.md)
