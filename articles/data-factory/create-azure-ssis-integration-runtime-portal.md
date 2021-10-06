---
title: Uso de Azure Portal para crear un entorno de ejecución de integración
description: Obtenga información sobre cómo usar Azure Portal para crear un entorno de ejecución de integración de Azure SSIS en Azure Data Factory para que pueda implementar y ejecutar paquetes SSIS en Azure.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 09/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 105e1b6c54f0240fa5ed46a8fe46ca8cdd92fa53
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219411"
---
# <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Uso de Azure Portal para crear un entorno de ejecución de integración

En esta sección, se usa Azure Portal, y en concreto la interfaz de usuario o la aplicación de Data Factory, para crear una instancia de Azure-SSIS IR.

## <a name="create-a-data-factory"></a>Crear una factoría de datos

Para crear la factoría de datos mediante Azure Portal, siga las instrucciones paso a paso que se indican en [Creación de una factoría de datos mediante la interfaz de usuario](./quickstart-create-data-factory-portal.md#create-a-data-factory). Seleccione **Anclar al panel** mientras lo hace, para permitir el acceso rápido después de su creación. 

Después de crear la factoría de datos, abra su página de información general en Azure Portal. Seleccione el icono **Author & monitor**  (Creación y supervisión) para abrir su página de **inicio** en otra pestaña. Ahí puede continuar con la creación de la instancia de Azure-SSIS IR.   

## <a name="provision-an-azure-ssis-integration-runtime"></a>Aprovisionamiento de una instancia de Integration Runtime para la integración de SSIS en Azure

En la página principal, seleccione el icono **Configurar SSIS** para abrir el panel **Configuración de Integration Runtime**.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Captura de pantalla que muestra la página principal de ADF.":::

   El panel **Integration runtime setup** (Configuración de Integration Runtime) tiene tres páginas en las que pueden configurar opciones generales, de implementación y avanzadas respectivamente.

### <a name="general-settings-page"></a>Página General settings (Configuración general)

En la página **General settings** (Configuración general) del panel **Integration runtime setup** (Configuración de Integration Runtime), realice los pasos siguientes.

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png" alt-text="Configuración general":::

1. En **Name** (Nombre), escriba el nombre del entorno de ejecución de integración.

2. En **Description** (Descripción), escriba la descripción del entorno de ejecución de integración.

3. En **Location** (Ubicación), seleccione la ubicación del entorno de ejecución de integración. Se muestran solo las ubicaciones admitidas. Le recomendamos que seleccione la misma ubicación del servidor de bases de datos que va a hospedar SSISDB.

4. En **Node Size** (Tamaño de nodo), seleccione el tamaño de nodo del clúster del entorno de ejecución de integración. Se muestran solo los tamaños de nodo admitidos. Seleccione un tamaño de nodo grande (escalado vertical) si quiere ejecutar muchos paquetes de uso intensivo de proceso o memoria.

   > [!NOTE]
   > Si necesita [aislamiento de proceso](../azure-government/azure-secure-isolation-guidance.md#compute-isolation), seleccione el tamaño de nodo **Standard_E64i_v3**. Este tamaño de nodo representa las máquinas virtuales aisladas que consumen su host físico completo y proporcionan el nivel necesario de aislamiento que requieren determinadas cargas de trabajo, como las de Impacto de nivel 5 (IL5) del Departamento de Defensa de EE. UU.
   
5. En **Node Number** (Número de nodos), seleccione el número de nodos del clúster del entorno de ejecución de integración. Se muestran solo los números de nodos admitidos. Seleccione un clúster de grande con muchos nodos (escalado horizontal), si quiere ejecutar muchos paquetes en paralelo.

6. En **Edition/License** (Edición o licencia), seleccione la edición de SQL Server para el entorno de ejecución de integración: Standard o Enterprise. Seleccione Enterprise si quiere usar características avanzadas en el entorno de ejecución de integración.

7. En **Save Money** (Ahorrar dinero), seleccione la opción Azure Hybrid Benefit (Ventaja híbrida de Azure) para el entorno de ejecución de integración: **Yes** (Sí) o **No**. Seleccione **Yes** (Sí) si quiere que su propia licencia de SQL Server con Software Assurance se beneficie de los ahorros con el uso híbrido.

8. Seleccione **Continuar**.

### <a name="deployment-settings-page"></a>Página Deployment settings (Configuración de implementación)

En la página **Deployment settings** (Configuración de implementación) del panel **Integration runtime setup** (Configuración de Integration Runtime), tiene las opciones para crear los almacenes de paquetes de Azure-SSIS IR o SSISDB.

#### <a name="creating-ssisdb"></a>Creación de SSISDB

En la página **Deployment settings** (Configuración de implementación) del panel **Integration runtime setup** (Configuración de Integration Runtime), si desea implementar los paquetes en SSISDB (Modelo de implementación de proyectos), active la casilla **Create SSIS catalog (SSISDB) hosted by Azure SQL Database server/Managed Instance to store your projects/packages/environments/execution logs** (Crear catálogo de SSIS [SSISDB] hospedado en el servidor de Azure SQL Database/Instancia administrada para almacenar los proyectos/paquetes/entornos/registros de ejecución). De forma alternativa, no necesita crear una instancia de SSISDB ni activar la casilla, si quiere implementar los paquetes en el sistema de archivos, en Azure Files o en una base de datos de SQL Server (MSDB) hospedados por Azure SQL Managed Instance (modelo de implementación de paquetes).

Con independencia del modelo de implementación, active esta casilla de todos modos si quiere usar Agente SQL Server hospedado por Azure SQL Managed Instance para orquestar o programar las ejecuciones de paquetes, ya que está habilitado por SSISDB. Para obtener más información, consulte el artículo [Programación de ejecuciones de paquetes SSIS mediante el agente de Instancia administrada de Azure SQL](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Si activa la casilla, realice los pasos siguientes para traer su propio servidor de bases de datos y hospedar la instancia de SSISDB que se creará y administrará en su nombre.

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png" alt-text="Configuración de implementación de SSISDB":::
   
1. En **Subscription** (Suscripción), seleccione la suscripción de Azure que tiene el servidor de bases de datos que va a hospedar SSISDB. 

1. En **Location** (Ubicación), seleccione la ubicación del servidor de bases de datos que va a hospedar SSISDB. Es recomendable seleccionar la misma ubicación del entorno de ejecución de integración.

1. En **Catalog Database Server Endpoint** (Punto de conexión del servidor de bases de datos de catálogo), seleccione el punto de conexión de su servidor de bases de datos que va a hospedar SSISDB. 
   
   En función del servidor de bases de datos seleccionado, la instancia de SSISDB puede crearse en su nombre como una base de datos única, como parte de un grupo elástico o en una instancia administrada. Se puede acceder a ella en una red pública o mediante la unión a una red virtual. Para obtener asesoramiento acerca de cómo elegir el tipo de servidor de bases de datos que va a hospedar SSISDB, consulte [Comparación entre SQL Database e Instancia administrada de SQL](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

   Si selecciona un servidor de Azure SQL Database con reglas de firewall de IP/puntos de conexión de servicio de red virtual o una instancia administrada con un punto de conexión privado para hospedar SSISDB, o si necesita acceder a los datos locales sin configurar IR autohospedado, debe unir la instancia de Azure-SSIS IR a una red virtual. Para más información, consulte [Unión de una instancia de Azure-SSIS IR a una red virtual](./join-azure-ssis-integration-runtime-virtual-network.md).
      
1. Active la casilla **Use AAD authentication with the system managed identity for Data Factory** (Usar la autenticación de AAD con la identidad administrada por el sistema para Data Factory) o **Use AAD authentication with a user-assigned managed identity for Data Factory** (Usar la autenticación de AAD con una identidad administrada asignada por el usuario para Data Factory) para elegir el método de autenticación de Azure AD para que Azure-SSIS IR acceda al servidor de bases de datos que hospeda SSISDB. No seleccione ninguna de las casillas para elegir el método de autenticación de SQL en su lugar.

   Si activa cualquiera de las casillas, deberá agregar la identidad administrada asignada por el usuario o por el sistema especificada para la factoría de datos a un grupo de Azure AD con permisos de acceso al servidor de bases de datos. Si activa la casilla **Use AAD authentication with a user-assigned managed identity for Data Factory** (Usar la autenticación de AAD con una identidad administrada asignada por el usuario para Data Factory), puede seleccionar las credenciales existentes creadas con las identidades administradas asignadas por el usuario especificadas o crear otras. Para más información, consulte [Habilitar la autenticación de Azure AD para una instancia de Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md).

1. En **Admin Username** (Nombre de usuario de administrador), escriba el nombre de usuario de autenticación de SQL del servidor de bases de datos que hospeda SSISDB. 

1. En **Admin Password** (Contraseña de administrador), escriba la contraseña de la autenticación de SQL del servidor de bases de datos para hospedar SSISDB. 

1. Active la casilla **Use dual standby Azure-SSIS Integration Runtime pair with SSISDB failover** (Usar par de Azure-SSIS Integration Runtime en espera dual) para configurar un par de Azure-SSIS IR en espera dual que funcione en sincronización con el grupo de conmutación por error de Azure SQL Database/Managed Instance para lograr continuidad empresarial y recuperación ante desastres.
   
   Si activa la casilla, escriba un nombre para identificar el par de Azure-SSIS IR principal y secundario en el cuadro de texto **Dual standby pair name** (Nombre de par en espera dual). Debe escribir el mismo nombre de par al crear sus instancias de Azure-SSIS IR principal y secundaria.

   Para más información, consulte [Configuración de Azure-SSIS Integration Runtime para continuidad empresarial y recuperación ante desastres (BCDR)](./configure-bcdr-azure-ssis-integration-runtime.md).

1. En **Catalog Database Service Tier** (Nivel de servicio de bases de datos de catálogo), seleccione el nivel de servicio del servidor de bases de datos para hospedar SSISDB. Seleccione el nivel de servicio Basic, Standard o Premium, o seleccione un nombre de grupo elástico.

Seleccione **Probar conexión** (Probar conexión) cuando corresponda y, si es correcta, seleccione **Continue** (Continuar).

> [!NOTE]
> Si usa el servidor de Azure SQL Database para hospedar SSISDB, los datos se almacenarán de manera predeterminada en un almacenamiento con redundancia geográfica para las copias de seguridad. Si no quiere que los datos se repliquen en otras regiones, siga las instrucciones para la [Configuración de la redundancia del almacenamiento de copia de seguridad mediante PowerShell](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell).
   
#### <a name="creating-azure-ssis-ir-package-stores"></a>Creación de almacenes de paquetes de Azure-SSIS IR

En la página **Deployment settings** (Configuración de implementación) del panel **Integration runtime setup** (Configuración de Integration Runtime), si desea administrar los paquetes implementados en MSDB, el sistema de archivos o Azure Files (Modelo de implementación de paquetes) con almacenes de paquetes de Azure-SSIS IR, active la casilla **Create package stores to manage your packages that are deployed into file system/Azure Files/SQL Server database (MSDB) hosted by Azure SQL Managed Instance** (Crear almacenes de paquetes para administrar los paquetes implementados en el sistema de archivos, en Azure Files o en una base de datos de SQL Server [MSDB] hospedados por Instancia administrada de Azure SQL).
   
Los almacenes de paquetes de Azure-SSIS IR permiten importar, exportar, eliminar y ejecutar paquetes, así como supervisar y detener los paquetes en ejecución mediante SSMS de forma similar al [almacén de paquetes de SSIS heredado](/sql/integration-services/service/package-management-ssis-service). Para más información, vea el artículo [Administración de paquetes SSIS mediante almacenes de paquetes de Azure-SSIS IR](./azure-ssis-integration-runtime-package-store.md).
   
Si activa esta casilla, puede seleccionar **New** (Nuevo) para agregar varios almacenes de paquetes a una instancia de Azure-SSIS IR. Por otro lado, un almacén de paquetes se puede compartir con varias instancias de Azure-SSIS IR.

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png" alt-text="Configuración de implementación para MSDB, un sistema de archivos o Azure Files":::

En el panel **Add package store** (Adición de un almacén de paquetes), siga estos pasos:
   
   1. En **Package store name** (Nombre del almacén de paquetes), escriba el nombre del almacén de paquetes. 

   1. En **Package store linked service** (Servicio vinculado de almacén de paquetes), seleccione el servicio vinculado existente que almacena la información de acceso del sistema de archivos, de Azure Files o de Instancia administrada de Azure SQL, donde se implementan los paquetes, o cree un servicio vinculado nuevo; para ello, seleccione **New** (Nuevo). En el panel **New linked service** (Nuevo servicio vinculado), realice los pasos siguientes.
   
      > [!NOTE]
      > Puede usar servicios vinculados de **Azure File Storage** o **Sistema de archivos** para acceder a Azure Files. Si usa el servicio vinculado **Azure File Storage**, por ahora el almacén de paquetes Azure-SSIS IR solo admite el método de autenticación de tipo **Básico** (no **Clave de cuenta** ni **URI de SAS**).  

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png" alt-text="Configuración de implementación de servicios vinculados":::

      1. En **Name** (Nombre), escriba el nombre del servicio vinculado. 
         
      1. En **Description** (Descripción), escriba la descripción del servicio vinculado. 
         
      1. En **Type** (Tipo), seleccione **Azure File Storage**, **Azure SQL Managed Instance** (Instancia administrada de Azure SQL) o **File System** (Sistema de archivos).

      1. Puede omitir la opción **Connect via integration runtime** (Conectarse a través de IR), ya que siempre se usa su instancia de Azure-SSIS IR para obtener la información de acceso de los almacenes de paquetes.

      1. Si selecciona **Azure File Storage**, seleccione **Basic** (Básico) en **Authentication method** (Método de autenticación) y, después, siga estos pasos. 

         1. En **Account selection method** (Método de selección de cuenta), seleccione **From Azure subscription** (Desde la suscripción de Azure) o **Enter manually** (Especificar manualmente).
         
         1. Si selecciona **From Azure subscription** (Desde la suscripción de Azure), escoja la **suscripción de Azure**, el **nombre de la cuenta de almacenamiento** y el **recurso compartido de archivos** correspondientes.
            
         1. Si selecciona **Enter manually** (Especificar manualmente), escriba `\\<storage account name>.file.core.windows.net\<file share name>` en **Host**, `Azure\<storage account name>` en **Username** (Nombre de usuario), y `<storage account key>` en **Password** (Contraseña), o bien seleccione su instancia de **Azure Key Vault** donde se almacena la contraseña como secreto.

      1. Si selecciona **Azure SQL Managed Instance** (Instancia administrada de Azure SQL), siga estos pasos: 

         1. Seleccione **Connection string** (Cadena de conexión) o elija la instancia de **Azure Key Vault** donde se almacena como secreto.

         1. En caso de seleccionar **Connection string** (Cadena de conexión), realice los siguientes pasos: 
             1. En **Account selection method** (Método de selección de cuenta), si elige **From Azure subscription** (Desde la suscripción de Azure), seleccione la **suscripción de Azure**, el **nombre de servidor**, el **tipo de punto de conexión** y el **nombre de base de datos** pertinentes. Si elige **Enter manually** (Indicar manualmente), siga los pasos que se indican a continuación. 
                1.  En **Fully qualified domain name** (Nombre de dominio completo), escriba `<server name>.<dns prefix>.database.windows.net` o `<server name>.public.<dns prefix>.database.windows.net,3342` como el punto de conexión público o privado (respectivamente) de Instancia administrada de Azure SQL. Si escribe el punto de conexión privado, no se puede usar la **prueba de conexión**, ya que la interfaz de usuario de ADF no puede acceder a ella.

                1. En **Database name** (Nombre de la base de datos), escriba `msdb`.

            1. En **Authentication type** (Tipo de autenticación), seleccione **SQL Authentication** (Autenticación de SQL), **Managed Identity** (Identidad administrada), **Service Principal** (Entidad de servicio) o **User-Assigned Managed Identity** (Identidad administrada asignada por el usuario).

                - Si selecciona **SQL Authentication** (Autenticación de SQL), escriba el **nombre de usuario** y la **contraseña** correspondientes, o bien seleccione la instancia de **Azure Key Vault** donde se almacena la contraseña como secreto.

                -  Si selecciona **Managed Identity** (Identidad administrada), conceda a la identidad administrada por el sistema para ADF acceso a la instancia de Azure SQL Managed Instance.

                - Si selecciona **Service Principal** (Entidad de servicio), escriba el **identificador de entidad de servicio** y la **clave de entidad de servicio** correspondientes, o bien seleccione la instancia de **Azure Key Vault** donde se almacena la clave como secreto.

                -  Si selecciona **User-Assigned Managed Identity** (Identidad administrada asignada por el usuario), conceda a la identidad administrada asignada por el usuario especificada para ADF acceso a la instancia de Azure SQL Managed Instance. A continuación puede seleccionar cualesquiera credenciales existentes creadas con las identidades administradas asignadas por el usuario especificadas o crear otras.

      1. Si selecciona **File system** (Sistema de archivos), escriba en **Host** la ruta de acceso UNC de la carpeta en la que se implementan los paquetes y especifique el **nombre de usuario** y la **contraseña** correspondientes, o bien seleccione la instancia de **Azure Key Vault** donde se almacena la contraseña como secreto.

      1. Seleccione **Test connection** (Prueba de conexión) cuando proceda y, si la prueba se realiza correctamente, seleccione **Create** (Crear).

   1. Los almacenes de paquetes agregados aparecerán en la página **Deployment settings** (Configuración de implementación). Para quitarlas, active sus casillas y, luego, seleccione **Eliminar**.

Seleccione **Test connection** (Probar conexión) cuando corresponda y, si es correcta, seleccione **Continue** (Continuar).

### <a name="advanced-settings-page"></a>Página Advanced settings (Configuración avanzada)

En la página **Advanced settings** (Configuración avanzada) del panel **Integration runtime setup** (Configuración de Integration Runtime), realice los pasos siguientes.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png" alt-text="Configuración avanzada":::

   1. En **Maximum Parallel Executions Per Node** (Número máximo de ejecuciones en paralelo por nodo), seleccione el número máximo de paquetes que se van a ejecutar simultáneamente por nodo en el clúster del entorno de ejecución de integración. Se muestran solo los números de paquetes admitidos. Seleccione un número bajo si quiere usar más de un núcleo para ejecutar un único paquete grande o pesado con un uso intensivo de memoria o proceso. Seleccione un número alto si quiere ejecutar uno o varios paquetes pequeños en un único núcleo.

   1. Active la casilla **Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations** (Personalizar Azure-SSIS Integration Runtime con configuraciones de sistema/instalación de componentes adicionales) para elegir si quiere agregar instalaciones personalizadas estándar/rápidas a Azure-SSIS IR. Para más información, consulte [Instalación personalizada de una instancia de Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

      Si activa esta casilla, haga lo siguiente:

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png" alt-text="Configuración avanzada con instalaciones personalizadas":::
   
      1. En **Custom setup container SAS URI** (URI de SAS del contenedor de instalación personalizada), escriba el URI de SAS del contenedor en el que se almacenan los scripts y los archivos asociados para las instalaciones personalizadas estándar.

      1. En **Express custom setup** (Instalación personalizada rápida), seleccione **Nueva** para abrir el panel **Agregar instalación personalizada rápida** y después seleccione cualquier tipo en el menú desplegable **Express custom setup type** (Tipo de instalación personalizada rápida); por ejemplo, **Run cmdkey command** (Ejecutar comando cmdkey), **Agregar variable de entorno**, **Install licensed component** (Instalar componente con licencia), etc.

         Si selecciona el tipo **Install licensed component** (Instalar componente con licencia), puede seleccionar los componentes integrados de nuestros partners de ISV en el menú desplegable **Component name** (Nombre del componente) y, si fuera necesario, puede escribir la clave de licencia del producto o cargar el archivo de licencia que ha comprado en el cuadro **License key**/**License file** (Clave de licencia/Archivo de licencia).
  
         Las instalaciones rápidas personalizadas que agregue aparecerán en la página **Advanced settings** (Configuración avanzada). Para quitarlas, puede activar las casillas de verificación y seleccionar **Eliminar**.

   1. Seleccione la casilla **Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses** (Seleccionar una red virtual para unir Azure-SSIS Integration Runtime, permitir que ADF cree determinados recursos de red y opcionalmente traer sus direcciones IP públicas propias) para elegir si quiere unir el entorno de ejecución de integración a una red virtual. 

      Active la casilla si usa un servidor de Azure SQL Database con reglas de firewall de IP/puntos de conexión de servicio de red virtual o una instancia administrada con un punto de conexión privado para hospedar SSISDB o si necesita acceder a los datos locales (es decir, tiene orígenes o destinos de datos locales en los paquetes de SSIS) sin configurar un IR autohospedado. Para obtener más información, consulte [Unión de una instancia de Azure-SSIS IR a una red virtual](./join-azure-ssis-integration-runtime-virtual-network.md). 

      Si activa esta casilla, haga lo siguiente:

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="Configuración avanzada con una red virtual":::

      1. En **Subscription** (Suscripción), seleccione la suscripción de Azure que tiene la red virtual.

      1. En **Location** (Ubicación), está seleccionada la misma ubicación del entorno de ejecución de integración.

      1. En **Type** (Tipo), seleccione el tipo de red virtual: clásica o de Azure Resource Manager. Se recomienda seleccionar una red virtual de Azure Resource Manager, puesto que las redes virtuales clásicas dejarán de utilizarse pronto.

      1. En **VNet Name** (Nombre de red virtual), seleccione el nombre de la red virtual. Debe ser la misma que se usa para el servidor de Azure SQL Database con puntos de conexión de servicio de red virtual o instancia administrada con un punto de conexión privado para hospedar SSISDB. O bien, la misma que está conectada a la red local. De lo contrario, puede ser cualquier red virtual para traer sus propias direcciones IP públicas estáticas de Azure-SSIS IR.

      1. En **Subnet Name** (Nombre de subred), seleccione el nombre de la subred en la red virtual. Debe ser la misma que se usa para el servidor de Azure SQL Database con puntos de conexión de servicio de red virtual para hospedar SSISDB. O bien, debe ser una subred diferente de la que se usa en la instancia administrada con un punto de conexión privado para hospedar SSISDB. De lo contrario, puede ser cualquier subred para traer sus propias direcciones IP públicas estáticas de Azure-SSIS IR.

      1. Active la casilla **Bring static public IP addresses for your Azure-SSIS Integration Runtime** (Traer direcciones IP públicas estáticas para Azure-SSIS Integration Runtime) para elegir si quiere traer sus propias direcciones IP públicas estáticas para Azure-SSIS IR, de modo que pueda habilitarlas en el firewall para los orígenes de datos.

         Si activa esta casilla, haga lo siguiente:

         1. En **First static public IP address** (Primera dirección IP pública estática), seleccione la primera dirección IP pública estática que cumpla con los requisitos de su instancia de Azure-SSIS IR. Si no tiene ninguna, haga clic en el vínculo **Crear nueva** para crear direcciones IP públicas estáticas en Azure Portal y, a continuación, haga clic en el botón actualizar aquí para poder seleccionarlas.
      
         1. En **Second static public IP address** (Segunda dirección IP pública estática), seleccione la primera dirección IP pública estática que cumpla con los requisitos de su instancia de Azure-SSIS IR. Si no tiene ninguna, haga clic en el vínculo **Crear nueva** para crear direcciones IP públicas estáticas en Azure Portal y, a continuación, haga clic en el botón actualizar aquí para poder seleccionarlas.

   1. Active la casilla **Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime** (Configurar el entorno de ejecución de integración autohospedado como proxy para su instancia de Azure-SSIS IR) para elegir si quiere configurar IR autohospedado como proxy para su instancia de Azure-SSIS IR. Para obtener más información, consulte [Configuración de un entorno de ejecución de integración autohospedado como proxy](./self-hosted-integration-runtime-proxy-ssis.md). 

      Si activa esta casilla, haga lo siguiente:

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png" alt-text="Configuración avanzada con IR autohospedado":::

      1. En **Self-Hosted Integration Runtime** (Entorno de ejecución de integración autohospedado), seleccione la instancia de IR autohospedado existente como proxy para Azure-SSIS IR.

      1. En **Staging Storage Linked Service** (Servicio vinculado de almacenamiento provisional), seleccione el servicio vinculado de Azure Blob Storage existente o cree uno para el almacenamiento provisional.

      1. En **Ruta de acceso provisional**, especifique un contenedor de blobs en la instancia seleccionada de Azure Blob Storage o deje este campo vacío para usar uno predeterminado para el almacenamiento provisional.

   1. Seleccione **VNet Validation** (Validación de red virtual)  > **Continuar**. 

En la sección **Resumen**, revise todos los valores de aprovisionamiento, marque los vínculos a la documentación recomendada y seleccione **Finalizar** para empezar la creación del entorno de ejecución de integración.

> [!NOTE]
> Aparte del tiempo de configuración personalizada, este proceso debería realizarse en 5 minutos. Sin embargo, Azure-SSIS IR puede tardar entre 20 y 30 minutos en unirse a una red virtual.
>
> Si usa SSISDB, el servicio Data Factory se conectará al servidor de bases de datos para prepararlo. También configura los permisos o los valores de la red virtual, si se especifica, y une la instancia de Azure-SSIS IR a la red virtual.
>
> Al aprovisionar Azure-SSIS IR, también se instalan el acceso redistribuible y el paquete de característica de Azure para SSIS. Estos componentes proporcionan conectividad a archivos de Excel y Access, y a diversos orígenes de datos de Azure, además de a aquellos que ya admiten los componentes integrados. Para obtener más información sobre los componentes integrados o preinstalados, vea el artículo [Componentes integrados y preinstalados en Azure-SSIS Integration Runtime](./built-in-preinstalled-components-ssis-integration-runtime.md). Para obtener más información sobre los componentes adicionales que puede instalar, consulte [Personalización de la instalación en una instancia de Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="connections-pane"></a>Panel Connections (Conexiones)

En el panel **Connections** (Conexiones) del **centro de administración**, cambie a la página **Integration Runtimes** (Entornos de ejecución de integración) y seleccione **Refresh** (Actualizar). 

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png" alt-text="Panel Connections (Conexiones)":::

   Puede editar y volver a configurar su instancia de Azure-SSIS IR; para ello, seleccione su nombre. También puede seleccionar los botones pertinentes para supervisar, iniciar, detener o eliminar su instancia de Azure-SSIS IR; puede generar automáticamente una canalización de ADF con la actividad Ejecutar paquete de SSIS para que se ejecute en su instancia de Azure-SSIS IR; y puede ver el código o la carga JSON de Azure-SSIS IR.  Solo puede editar o eliminar su instancia de Azure-SSIS IR si está detenida.

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Instancias de Integration Runtime de SSIS de Azure en el portal

1. En la interfaz de usuario de Azure Data Factory, cambie a la pestaña **Manage** (Administrar) y, después, a la pestaña **Integration runtimes** (Entornos de ejecución de integración) del panel **Connections** (Conexiones), con el fin de ver los entornos de ejecución de integración existentes en su factoría de datos.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png" alt-text="Visualización de las instancias de IR existentes":::

1. Seleccione **New** (Nuevo) para crear una nueva instancia de Azure-SSIS IR y abrir el panel **Integration runtime setup** (Configuración de Integration Runtime).

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png" alt-text="Integration Runtime a través del menú":::

1. En el panel **Integration runtime setup** (Configuración de Integration Runtime), seleccione el icono **Lift-and-shift existing SSIS packages to execute in Azure** (Migrar mediante lift-and-shift los paquetes de SSIS existentes para ejecutarlos en Azure) y haga clic en **Continue** (Continuar).

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png" alt-text="Especificación del tipo de instancia de Integration Runtime":::

1. Para conocer el resto de pasos de configuración de Azure-SSIS IR, consulte la sección [Aprovisionamiento de una instancia de Azure-SSIS IR](#provision-an-azure-ssis-integration-runtime).
 
## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre cómo aprovisionar Azure-SSIS IR mediante Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md).
- [Más información sobre cómo aprovisionar Azure-SSIS IR mediante una plantilla de Azure Resource Manager](create-azure-ssis-integration-runtime-resource-manager-template.md).
- [Implementación y ejecución de los paquetes SSIS en Azure Data Factory](create-azure-ssis-integration-runtime-deploy-packages.md).

Consulte otros temas sobre Azure-SSIS IR en esta documentación:

- [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información sobre los entornos de ejecución de integración en general, incluido Azure-SSIS IR.
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar y comprender la información acerca de Azure-SSIS IR.
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Azure SSIS IR mediante la adición de más nodos.
- [Implementación, ejecución y supervisión de paquetes de SSIS en Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Conexión a SSISDB en Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Conexión a orígenes de datos locales con autenticación de Windows) 
- [Programación de la ejecución de paquetes en Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
