---
title: Migración de los datos a una cuenta de SQL API de Azure Cosmos DB mediante Striim
description: Aprenda a usar Striim para migrar datos desde una instancia de Oracle Database a una cuenta de SQL API de Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 1826aea977798de29de6f3968b50b9a4578eff69
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114308"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Migración de los datos a una cuenta de SQL API de Azure Cosmos DB mediante Striim
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]
 
La imagen de Striim en Azure Marketplace ofrece un movimiento de datos continuo en tiempo real desde almacenes de datos y bases de datos a Azure. Durante el movimiento de los datos, puede realizar una desnormalización en línea, trasformar los datos y habilitar análisis en tiempo real y escenarios de creación de informes de datos. Es fácil empezar a trabajar con Striim para mover datos empresariales continuamente a SQL API de Azure Cosmos DB. Azure proporciona una oferta de Marketplace que facilita la implementación de Striim y la migración de los datos a Azure Cosmos DB. 

En este artículo se muestra cómo usar Striim para migrar datos desde una instancia de **Oracle Database** a una **cuenta de SQL API de Azure Cosmos DB**.

## <a name="prerequisites"></a>Requisitos previos

* Si no tiene una [suscripción a Azure](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.

* Una instancia de Oracle Database que se ejecute localmente y que contenga algunos datos.

## <a name="deploy-the-striim-marketplace-solution"></a>Implementación de la solución de Marketplace de Striim

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

1. Seleccione **Crear un recurso** y busque **Striim** en Azure Marketplace. Seleccione la primera opción y **Crear**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="Búsqueda del elemento de Marketplace de Striim":::

1. A continuación, escriba las propiedades de configuración de la instancia de Striim. El entorno de Striim se implementa en una máquina virtual. En el panel **Básico**, escriba el **nombre de usuario de máquina virtual**, **Contraseña de máquina virtual** (esta contraseña se usa para SSH en la máquina virtual). Seleccione su **Suscripción**, **Grupo de recursos** y **Detalles de ubicación** allí donde desee implementar Striim. Cuando haya terminado, seleccione **Aceptar**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="Configuración de las opciones básicas para Striim":::

1. En el panel **Striim Cluster settings** (Configuración del clúster de Striim), elija el tipo de implementación de Striim y el tamaño de la máquina virtual.

   |Configuración | Value | Descripción |
   | ---| ---| ---|
   |Tipo de implementación de Striim |Independiente | Striim se puede ejecutar en un tipo de implementación **independiente** o de **clúster**. El modo independiente implementará el servidor Striim en una sola máquina virtual, y lepermite seleccionar el tamaño de las máquinas virtuales dependiendo de su volumen de datos. El modo de clúster implementará el servidor Striim en dos o más máquinas virtuales con el tamaño seleccionado. Los entornos de clúster con más de dos nodos ofrecen conmutación por error y alta disponibilidad automáticas.</br></br> En este tutorial, puede seleccionar la opción Independiente. Use el tamaño "Standard_F4s" predeterminado de la máquina virtual.  | 
   | Nombre del clúster de Striim|    <Striim_cluster_Name>|  Nombre del clúster de Striim.|
   | Contraseña del clúster de Striim|   <Striim_cluster_password>|  Contraseña para el clúster.|

   Una vez que rellene el formulario, seleccione **Aceptar** para continuar.

1. En el panel **Striim access settings** (Configuración de acceso de Striim), configure la **Dirección IP pública** (elija los valores predeterminados), **Domain name for Striim** (Nombre de dominio para Striim) y la **Contraseña de administrador** que desea usar para iniciar sesión en la interfaz de usuario de Striim. Configure una red virtual y una subred (elija los valores predeterminados). Tras rellenar los detalles, seleccione **Aceptar** para continuar.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Configuración de acceso de Striim":::

1. Azure validará la implementación y se asegurará de que todo esté bien; la validación tarda algunos minutos en completarse. Una vez completada la validación, seleccione **Aceptar**.
  
1. Por último, revise las condiciones de uso y seleccione **Crear** para crear su instancia de Striim. 

## <a name="configure-the-source-database"></a>Configuración de la base de datos de origen 

En esta sección, va a configurar la instancia de Oracle Database como origen del movimiento de datos.  Necesitará el [controlador JDBC de Oracle](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) para conectarse a Oracle. Para leer los cambios en su instancia de Oracle Database de origen, puede usar [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) o las [API XStream](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). El controlador JDBC de Oracle tiene que estar presente en classpath de Java de Striim para leer, escribir o conservar datos de la instancia de Oracle Database.

Descargue el controlador [ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) en su máquina local. Más tarde lo instalará en el clúster de Striim.

## <a name="configure-the-target-database"></a>Configuración de la base de datos de destino

En esta sección, va a configurar la cuenta de SQL API de Azure Cosmos DB como destino del movimiento de datos.

1. Cree una [cuenta de SQL API de Azure Cosmos DB](create-cosmosdb-resources-portal.md) mediante Azure Portal.

1. Vaya al panel **Explorador de datos** en su cuenta de Azure Cosmos. Seleccione **Nuevo contenedor** para crear un nuevo contenedor. Supongamos que migra *productos* y datos de *pedidos* desde la instancia de Oracle Database a Azure Cosmos DB. Cree una nueva base de datos denominada **StriimDemo** con un contenedor de nombre **Orders**. Aprovisione el contenedor con **1000 RU** (en este ejemplo se usan 1000 RU, pero debe usar el rendimiento estimado para su carga de trabajo) y **/ORDER_ID** como clave de partición. Estos valores variarán dependiendo de sus datos de origen. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png" alt-text="Creación de una cuenta de API de SQL":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Configuración de Oracle para el flujo de datos de Azure Cosmos DB

1. Ahora, volvamos a Striim. Antes de interactuar con Striim, instale el controlador JDBC de Oracle que descargó anteriormente.

1. Vaya a la instancia de Striim que implementó en Azure Portal. Seleccione el botón **Conectar** en la barra de menús superior y, en la pestaña **SSH**, copie la dirección URL en el campo **Iniciar sesión con la cuenta local de VM**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="Obtención de la dirección URL de SSH":::

1. Abra una nueva ventana de terminal y ejecute el comando SSH que copió desde Azure Portal. En este artículo se usa el terminal en MacOS. Puede seguir las instrucciones similares mediante PuTTY u otro cliente SSH en una máquina Windows. Cuando se le solicite, escriba **sí** para continuar y la **contraseña** que ha establecido para la máquina virtual en el paso anterior.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="Conexión a la máquina virtual de Striim":::

1. Ahora, abra una nueva pestaña de terminal para copiar el archivo **ojdbc8.jar** que descargó anteriormente. Use el siguiente comando SCP para copiar el archivo jar desde su máquina local en la carpeta tmp de la instancia de Striim que se ejecuta en Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="Copia del archivo Jar desde la ubicación de la máquina en Striim":::

1. A continuación, vuelva a la ventana donde usó SSH en la instancia de Striim e inicie sesión como sudo. Mueva el archivo **ojdbc8.jar** del directorio **/tmp** al directorio **lib** de su instancia de Striim con los siguientes comandos:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="Movimiento del archivo Jar a la carpeta lib":::


1. En la misma ventana de terminal, reinicie el servidor Striim ejecutando los siguientes comandos:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim tardará un minuto en iniciarse. Si desea ver el estado, ejecute el siguiente comando: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Ahora, vuelva a Azure y copie la dirección IP pública de su máquina virtual de Striim. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="Copia de la dirección IP de la máquina virtual de Striim":::

1. Para ir a la interfaz de usuario web de Striim, abra una nueva pestaña en un explorador y copie la IP pública seguida de: 9080. Inicie sesión con el nombre de usuario **administrador**, junto con la contraseña de administrador que especificó en Azure Portal.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="Inicio de sesión en Striim":::

1. Ahora llegará a la página principal de Striim. Hay tres paneles diferentes: **Dashboards**, **Apps** y **SourcePreview**. El panel Dashboards le permite mover datos en tiempo real y visualizarlos. En el panel Apps se incluyen sus canalizaciones de streaming de datos o flujos de datos. En el lado derecho de la página se encuentra SourcePreview, donde puede obtener una versión preliminar de sus datos antes de moverlos.

1. Seleccione el panel **Apps**, por ahora nos centraremos en este panel. Existen numerosas aplicaciones de ejemplo que puede usar para aprender sobre Striim. De todas formas, en este artículo creará las suyas propias. Seleccione el botón **Agregar aplicación** en la esquina superior derecha.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="Incorporación de la aplicación Striim":::

1. Hay diversas formas de crear aplicaciones Striim. Seleccione **Iniciar con una plantilla** para empezar con una plantilla existente.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png" alt-text="Inicio de la aplicación con la plantilla":::

1. En el campo **Buscar plantillas** escriba "Cosmos" y seleccione **Destino: Azure Cosmos DB** y, a continuación, seleccione **Oracle CDC a Azure Cosmos DB**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png" alt-text="Selección de Oracle CDC a Cosmos DB":::

1. En la página siguiente, dele un nombre a la aplicación. Puede proporcionar un nombre como **oraToCosmosDB**, después seleccione **Guardar**.

1. A continuación, escriba la configuración de origen de la instancia de origen de Oracle. Escriba un valor para el **Nombre de origen**. El nombre de origen es simplemente una convención de nomenclatura para la aplicación Striim. Puede usar un nombre como **src_onPremOracle**. Escriba los valores para el resto de los parámetros de origen **URL**, **Nombre de usuario** y **Contraseña**, elija **LogMiner** como lector para leer los datos de Oracle. Seleccione **Next** (Siguiente) para continuar.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png" alt-text="Configuración de parámetros de origen":::

1. Striim comprobará el entorno y se asegurará de que puede conectarse a la instancia de Oracle de origen, que tiene los privilegios correctos y que CDC se haya configurado correctamente. Una vez que se validan todos los valores seleccione **Siguiente**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png" alt-text="Validación de los parámetros de origen":::

1. Seleccione las tablas de Oracle Database que desea migrar. Por ejemplo, vamos a elegir la tabla Orders y seleccionar **Siguiente**. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png" alt-text="Selección de las tablas de origen":::

1. Después de seleccionar la tabla de origen, puede realizar operaciones más complicadas, como la asignación y el filtrado. En este caso, solo se creará una réplica de la tabla de origen en Azure Cosmos DB. Seleccione **Siguiente** para configurar el destino

1. Ahora, vamos a configurar el destino:

   * **Target Name** (Nombre de destino): proporcione un nombre descriptivo para el destino. 
   * **Input From** (Origen de la entrada): en la lista desplegable, seleccione el flujo de entrada de la que ha creado en la configuración de origen de Oracle. 
   * **Collections** (Colecciones): especifique las propiedades de configuración de Azure Cosmos DB de destino. La sintaxis de las colecciones es **SourceSchema.SourceTable, TargetDatabase.TargetContainer**. En este ejemplo, el valor sería "SYSTEM.ORDERS, StriimDemo.Orders". 
   * **AccessKey**: el valor PrimaryKey de la cuenta de Azure Cosmos.
   * **ServiceEndpoint**: el URI de la cuenta de Azure Cosmos, que puede encontrarse en la sección **Claves** de Azure Portal. 

   Seleccione **Guardar** y **Siguiente**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png" alt-text="Configuración de los parámetros de destino":::


1. A continuación llegará al diseñador de flujos, donde puede arrastrar y colocar conectores listos para usar para crear las aplicaciones de streaming. No realizará ninguna modificación en el flujo en este momento. Continúe e implemente la aplicación seleccionando el botón **Implementar aplicación**.
 
   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png" alt-text="Implementación de la aplicación":::

1. En la ventana de implementación, puede especificar si desea ejecutar determinadas partes de su aplicación en partes específicas de su topología de implementación. Puesto que estamos realizando la ejecución en una topología de implementación simple mediante Azure, usaremos la opción predeterminada.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png" alt-text="Uso de la opción predeterminada":::

1. Tras la implementación, puede obtener la versión preliminar del flujo para ver todo el flujo de datos. Seleccione el icono de **onda** y el ojo situado junto al mismo. Seleccione el botón **Implementado** en la barra de menús superior e **Iniciar aplicación**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-app.png" alt-text="Inicio de la aplicación":::

1. Mediante el uso de un lector **CDC (captura de datos modificados)** , Striim recogerá solo los nuevos cambios en la base de datos. Si tiene datos que fluyen a través de sus tablas de origen, los verá. Sin embargo, puesto que esta es una tabla de demostración, el origen no está conectado a ninguna aplicación. Si usa un generador de datos de ejemplo, podrá insertar una cadena de eventos en su instancia de Oracle Database.

1. Verá cómo fluyen los datos a través de la plataforma Striim. Striim también recoge todos los metadatos asociados a su tabla, lo que resulta útil a la hora de supervisar los datos y garantizar que estos vayan a parar al destino adecuado.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png" alt-text="Configuración de la canalización CDC":::

1. Por último, iniciemos sesión en Azure y vayamos a la cuenta de Azure Cosmos. Actualice Data Explorer y verá que han llegado esos datos.  

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png" alt-text="Validación de datos migrados en Azure":::

Mediante el uso de la solución de Striim en Azure, puede migrar datos continuamente a Azure Cosmos DB desde diversos orígenes como Oracle, Cassandra, MongoDB y algunos más. Para más información, visite el [sitio web de Striim](https://www.striim.com/), [descargue una prueba gratuita de 30 días de Striim](https://go2.striim.com/download-free-trial) y, para ver si hay algún problema al configurar la ruta de migración con Striim, envíe una [solicitud de soporte técnico](https://go2.striim.com/request-support-striim).

## <a name="next-steps"></a>Pasos siguientes

* ¿Intenta planear la capacidad para una migración a Azure Cosmos DB?
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, obtenga información sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
    * Si conoce las velocidades de solicitud típicas de la carga de trabajo de la base de datos actual, lea sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).

* Si va a migrar datos a SQL API de Azure Cosmos DB, consulte [cómo migrar datos a la cuenta de Cassandra API mediante Striim](../cassandra/migrate-data-striim.md)

* [Supervisión y depuración de los datos con métricas de Azure Cosmos DB](../use-metrics.md)