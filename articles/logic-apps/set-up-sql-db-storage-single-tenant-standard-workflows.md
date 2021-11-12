---
title: Configuración del almacenamiento de SQL para los flujos de trabajo de aplicaciones lógicas estándar
description: Configure una base de datos SQL para almacenar los artefactos, los estados y el historial de ejecución del flujo de trabajo de aplicación lógica (estándar) en una instancia de Azure Logic Apps de inquilino único.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5dd6f2d024055a75e350bfb5b0eee0e26c115193
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475813"
---
# <a name="set-up-sql-database-storage-for-standard-logic-apps-in-single-tenant-azure-logic-apps-preview"></a>Configuración del almacenamiento de base de datos SQL para aplicaciones lógicas estándar en instancias de Azure Logic Apps de inquilino único (versión preliminar)

> [!IMPORTANT]
> Esta funcionalidad está en versión preliminar y está sujeta a las [Condiciones de uso complementarias para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Al elegir el tipo de recurso **Logic App (Standard)** (Aplicación lógica [estándar]) para crear flujos de trabajo que se ejecutan en instancias de Azure Logic Apps de inquilino único, App Service Environment v3 o fuera de Azure, también debe crear una cuenta de Azure Storage para guardar los artefactos, los estados y los datos en tiempo de ejecución relacionados con el flujo de trabajo. Sin embargo, si quiere mayor flexibilidad y control sobre el entorno de tiempo de ejecución, el rendimiento, el escalado y la administración de los flujos de trabajo de aplicación lógica, puede usar el proveedor de almacenamiento de SQL en lugar de Azure Storage para las transacciones de almacenamiento relacionadas con ellos.

En este artículo se proporciona información general sobre los motivos de usar el almacenamiento de SQL como proveedor de almacenamiento principal de Azure Logic Apps frente a la alternativa de Azure Storage, y se muestra cómo configurar SQL para el uso de almacenamiento, ya sea durante la creación de aplicaciones lógicas en Azure Portal o durante la implementación de la aplicación lógica desde Visual Studio Code.

Si no conoce las aplicaciones lógicas, consulte la siguiente documentación:

- [¿Qué es Azure Logic Apps?](logic-apps-overview.md)
- [Comparación de las opciones de un solo inquilino, multiinquilino y entorno del servicio de integración para Azure Logic Apps](single-tenant-overview-compare.md)

<a name="why-sql"></a>

## <a name="why-use-sql"></a>Por qué usar SQL

Como opción de almacenamiento alternativa disponible para instancias de Azure Logic Apps de inquilino único, SQL proporciona las siguientes ventajas:

| Prestación | Descripción |
|---------|-------------|
| **Portabilidad** | SQL tiene muchos factores de forma, como máquinas virtuales, plataforma como servicio (PaaS) y contenedores. Puede ejecutar bases de datos SQL en cualquier lugar en el que quiera ejecutar flujos de trabajo de aplicación lógica. |
| **Control** | SQL ofrece un control pormenorizado sobre el rendimiento y el escalado de la base de datos durante períodos concretos o con cargas de trabajo específicas. Los precios de SQL se basan en el uso y el rendimiento de la CPU, de forma que resulta más sencillo predecir los costos que con Azure Storage, donde estos dependen de cada operación. |
| **Usar los recursos existentes** | Si está familiarizado con las herramientas de Microsoft, puede usar sus recursos en integraciones modernas con SQL. Tiene la posibilidad de reutilizar los recursos en implementaciones locales tradicionales e implementaciones modernas en la nube con las ventajas híbridas de Azure. SQL también proporciona herramientas sólidas y muy compatibles, como SQL Server Management Studio (SSMS), interfaces de línea de comandos y SDK. |
| **Cumplimiento normativo** | SQL ofrece más opciones que Azure Storage para realizar copias de seguridad, restauraciones, conmutación por error y crear redundancias. Puede aplicar al almacenamiento de su aplicación lógica los mismos mecanismos de nivel empresarial que los de otras aplicaciones empresariales. |
|||

<a name="when-use-sql"></a>

## <a name="when-to-use-sql"></a>Cuándo usar SQL

En la tabla siguiente se describen algunos motivos por los que podría querer usar SQL:

| Escenario | Recomendación del proveedor de almacenamiento |
|----------|----------------------------|
| Quiere ejecutar flujos de trabajo de aplicación lógica en Azure con un mayor control sobre el rendimiento del almacenamiento. | Use SQL como proveedor de almacenamiento, ya que Azure Storage no proporciona herramientas para optimizar el rendimiento. |
| Quiere ejecutar flujos de trabajo de aplicación lógica de forma local, lo que puede hacer con [aplicaciones lógicas habilitadas para Azure Arc](azure-arc-enabled-logic-apps-overview.md). | Use SQL como proveedor de almacenamiento para poder elegir dónde hospedar la base de datos SQL, por ejemplo, de forma local en una máquina virtual, un contenedor o en un entorno de varias nubes. Considere la posibilidad de ejecutar los flujos de trabajo de aplicación lógica cerca de los sistemas que quiera integrar, o de reducir la dependencia de la nube.   |
| Quiere costos de almacenamiento predecibles. | Use SQL como proveedor de almacenamiento cuando quiera un mayor control sobre los costos de escalado. Los costos de SQL son por proceso y operación de entrada/salida por segundo (IOPS). Los costos de Azure Storage se basan en el número de operaciones, lo que puede funcionar mejor con cargas de trabajo pequeñas que se escalen hasta cero. |
| Prefiere usar SQL en lugar de Azure Storage. | SQL es un ecosistema conocido y confiable que puede usar para aplicar la misma gobernanza y administración en las operaciones en segundo plano de las aplicaciones lógicas. |
| Quiere reutilizar los entornos de SQL existentes. | Use SQL como proveedor de almacenamiento si ya posee licencias de SQL que quiera reutilizar o modernizar en la nube. También podría interesarle usar las ventajas híbridas de Azure para las integraciones de aplicaciones lógicas. |
| Todo lo demás | Use Azure Storage como proveedor de almacenamiento predeterminado. |
|||

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta de Azure y una suscripción activa. Si no tiene una,  [regístrese para obtener una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Un entorno de SQL que se usará con la aplicación lógica. Sin embargo, antes de configurar el entorno, realice los pasos siguientes:

  1. Cree una instancia de SQL Server.

     Los tipos admitidos son  [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads), [base de datos de Azure SQL](https://azure.microsoft.com/products/azure-sql/database/), [Azure SQL Managed Instance](https://azure.microsoft.com/products/azure-sql/managed-instance/), entre otros.

     - Si la instancia de SQL Server se hospeda en Azure mediante uno de los tipos admitidos, asegúrese de configurar los permisos siguientes:

       1. En [Azure portal](https://portal.azure.com), vaya al recurso de SQL Server.

       1. En el menú de navegación del recurso, en **Seguridad**, seleccione **Firewalls y redes virtuales**.

       1. En el panel que se abre, en **Permitir que los servicios y recursos de Azure accedan a este servidor**, seleccione **Sí**.

       1. Guarde los cambios.

     - Si la instancia de SQL Server no está hospedada en Azure, asegúrese de que los firewalls o la configuración de red del servidor permitan que los servicios y los recursos de Azure accedan al servidor y la base de datos.

     - Si usa SQL Express para el desarrollo local, conéctese a la instancia con nombre predeterminada `localhost\SQLExpress`.

  1. Cree o use una base de datos existente.

     Debe tener una base de datos que se pueda usar para poder configurar el proveedor de almacenamiento de SQL.

  1. Ahora puede seguir los [pasos para configurar el entorno de SQL](#set-up-sql-environment) que se describen en este artículo.

- Si el desarrollo es local, [Visual Studio Code](https://code.visualstudio.com/Download) instalado en el equipo local.

  > [!NOTE]
  > Asegúrese de instalar la [versión más reciente de Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) para garantizar la compatibilidad con SQL mediante la versión de Microsoft Installer (MSI), que es `func-cli-X.X.XXXX-x*.msi`. Para más información sobre los requisitos de instalación de Visual Studio Code, consulte [Creación de un flujo de trabajo de integración con Azure Logic Apps (estándar) de inquilino único en Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md).

<a name="set-up-sql-environment"></a>

## <a name="set-up-your-sql-environment"></a>Configuración del entorno de SQL

1. Antes de configurar el proveedor de almacenamiento de SQL, lleve a cabo los pasos necesarios de la sección [Requisitos previos](#prerequisites).

1. Configure los permisos de la instancia de SQL Server.

   Actualmente, el proveedor de almacenamiento de SQL admite la autenticación de SQL en cadenas de conexión. También puede usar la autenticación de Windows para el desarrollo y las pruebas locales. En este momento, no está disponible la compatibilidad con Azure Active Directory (Azure AD) y las identidades administradas.

   Debe usar una identidad que tenga permisos para crear y administrar artefactos relacionados con el flujo de trabajo en la base de datos SQL de destino. Por ejemplo, un administrador tiene todos los permisos necesarios para crear y administrar estos artefactos. En la lista siguiente se describen los artefactos que el entorno de ejecución de Azure Logic Apps de inquilino único intenta crear mediante la cadena de conexión de SQL que se proporciona. Asegúrese de que la identidad usada en la cadena de conexión de SQL tenga los permisos necesarios para crear los artefactos siguientes:

   - Cree y elimine los esquemas siguientes:  `dt`,  `dc` y  `dq`.
   - Agregue, modifique y elimine tablas de estos esquemas.
   - Agregue, modifique y elimine tipos de tabla definidos por el usuario en estos esquemas.

   Para más información sobre los permisos de destino, consulte [Permisos de SQL Server en el Motor de base de datos](/sql/relational-databases/security/permissions-database-engine).

1. Conéctese a SQL.

   - Asegúrese de que la base de datos SQL permita el acceso necesario para el desarrollo.

   - Si usa una base de datos de Azure SQL, complete los siguientes requisitos:

     - Para el desarrollo y las pruebas locales, permita explícitamente conexiones desde la dirección IP de su equipo local. Puede  [establecer sus reglas de firewall de IP en Azure SQL Server](../azure-sql/database/network-access-controls-overview.md#ip-firewall-rules).

     - En [Azure Portal](https://portal.azure.com), permita que el recurso de aplicación lógica acceda a la base de datos SQL con una cadena de conexión proporcionada, para lo que será necesario  [permitir los servicios de Azure](../azure-sql/database/network-access-controls-overview.md#allow-azure-services).

     - Configure cualquier otro  [control de acceso a la red de la base de datos SQL](../azure-sql/database/network-access-controls-overview.md)  que sea necesario para su escenario.

   - Si usa Azure SQL Managed Instance, permita que los servicios de Azure (`logicapp`) se [conecten a la base de datos SQL mediante puntos de conexión públicos protegidos](../azure-sql/managed-instance/public-endpoint-overview.md).

<a name="set-up-sql-logic-app-creation-azure-portal"></a>

## <a name="set-up-sql-during-creation-in-the-azure-portal"></a>Configuración de SQL durante la creación en Azure Portal

Al crear la aplicación lógica con el tipo de recurso **Logic App (Standard)** (Aplicación lógica [estándar]) en Azure, puede configurar SQL como proveedor de almacenamiento.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con su cuenta de Azure.

1. En el cuadro de búsqueda de Azure Portal, escriba `logic apps` y seleccione **Logic Apps**.

   ![Captura de pantalla que muestra el cuadro de búsqueda de Azure Portal con el término de búsqueda "aplicaciones lógicas" y la categoría "Logic Apps" seleccionada.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/find-logic-app-resource-template.png)

1. En la página **Logic Apps**, seleccione en **Agregar**.

1. En la página **Crear aplicación lógica**, en la pestaña **Aspectos básicos**, proporcione la siguiente información sobre el recurso de aplicación lógica:

   | Propiedad | Obligatorio | Value | Descripción |
   |----------|----------|-------|-------------|
   | **Suscripción** | Sí | <*Azure-subscription-name*> | La suscripción de Azure que se usa para la aplicación lógica. |
   | **Grupo de recursos** | Sí | <*nombre del grupo de recursos de Azure*> | El grupo de recursos de Azure en el que se crea la aplicación lógica y los recursos relacionados. El nombre del recurso debe ser único entre las regiones y solo puede contener letras, números, guiones ( **-** ), caracteres de subrayado ( **_** ), paréntesis ( **()** ) y puntos ( **.** ). <p><p>En este ejemplo se crea un grupo de recursos denominado `Fabrikam-Workflows-RG`. |
   | **Tipo** | Sí | **Estándar** | Este tipo de recurso de aplicación lógica se ejecuta en el entorno de Azure Logic Apps de un solo inquilino y usa el [modelo de uso, facturación y precios Estándar](logic-apps-pricing.md#standard-pricing). |
   | **Nombre de la aplicación lógica** | Sí | <*nombre-de-la-aplicación-lógica*> | Nombre que se va a usar para la aplicación lógica. El nombre del recurso debe ser único entre las regiones y solo puede contener letras, números, guiones ( **-** ), caracteres de subrayado ( **_** ), paréntesis ( **()** ) y puntos ( **.** ). <p><p>En este ejemplo se crea una aplicación lógica denominada `Fabrikam-Workflows`. <p><p>**Nota**: El nombre de la aplicación lógica obtiene automáticamente el sufijo, `.azurewebsites.net`, porque el recurso **Aplicación lógica (estándar)** está basado en el entorno de ejecución de Azure Logic Apps de inquilino único, que usa el modelo de extensibilidad de Azure Functions y se hospeda como una extensión en el sistema en tiempo de ejecución de Azure Functions. Azure Functions usa la misma convención de nomenclatura de aplicaciones. |
   | **Publicar** | Sí | <*entorno-de-implementación*> | El destino de implementación de la aplicación lógica. De forma predeterminada, se selecciona **Flujo de trabajo** para la implementación en Azure Logic Apps de un solo inquilino. Azure crea un recurso de aplicación lógica vacío donde tiene que agregar el primer flujo de trabajo. <p><p>**Nota**: Actualmente, la opción **Contenedor de Docker** requiere una [*ubicación personalizada*](../azure-arc/kubernetes/conceptual-custom-locations.md) en un clúster de Kubernetes habilitado para Azure Arc, que puede utilizar con [instancias de Logic Apps habilitadas para Azure Arc (versión preliminar)](azure-arc-enabled-logic-apps-overview.md). Las ubicaciones del recurso de la aplicación lógica, la ubicación personalizada y el clúster deben tener los mismos valores. |
   | **Región** | Sí | <*Azure-region*> | Ubicación que se usará para crear el grupo de recursos y los recursos. En este ejemplo se implementa la aplicación lógica de ejemplo en Azure y se usa **Oeste de EE. UU.** <p>- Si seleccionó **Contenedor de Docker**, seleccione la ubicación personalizada. <p>- Para implementar en un recurso de [ASEv3,](../app-service/environment/overview.md) que debe existir primero, seleccione ese recurso de entorno en la lista **Región**. |
   |||||

   En el ejemplo siguiente se muestra la página **Crear aplicación lógica** con la pestaña **Aspectos básicos**:

   ![Captura de pantalla que muestra Azure Portal y la página "Crear aplicación lógica" con la pestaña "Aspectos básicos".](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-logic-app-resource-portal.png)

1. Cuando esté listo, seleccione **Siguiente: Hospedaje**. A continuación, en la pestaña **Hosting**, proporcione la información siguiente acerca de la solución de almacenamiento y el plan de hospedaje que se usará para la aplicación lógica.

   | Propiedad | Obligatorio | Value | Descripción |
   |----------|----------|-------|-------------|
   | **Tipo de almacenamiento** | Sí | **SQL y Azure Storage** | Tipo de almacenamiento que quiere usar para los artefactos y los datos relacionados con el flujo de trabajo. <p><p>- Si ha seleccionado previamente una ubicación personalizada como región, seleccione **SQL**. <p><p>- Si ha seleccionado previamente una región de Azure o una ubicación de ASEv3, seleccione **SQL and Azure Storage** (SQL y Azure Storage). <p><p>**Nota**: Si va a realizar la implementación en una región de Azure, también necesita una cuenta de Azure Storage, que se usa para completar el hospedaje puntual de la configuración de la aplicación lógica en la plataforma Azure Logic Apps. La definición, el estado, el historial de ejecución y otros artefactos en tiempo de ejecución del flujo de trabajo se almacenan en la base de datos SQL. <p><p>En el caso de implementaciones en una ubicación personalizada hospedada en un clúster de Azure Arc, solo necesita SQL como proveedor de almacenamiento. |
   | **Cuenta de almacenamiento** | Sí | <*Azure-storage-account-name*> | La [cuenta de Azure Storage](../storage/common/storage-account-overview.md) que se usará para transacciones de almacenamiento. <p><p>Este nombre de recurso debe ser único en todas las regiones y tener de 3 a 24 caracteres (solo números y letras minúsculas). Seleccione una cuenta existente o cree una nueva. <p><p>En este ejemplo se crea una cuenta de almacenamiento denominada `fabrikamstorageacct`. |
   | **Cadena de conexión de SQL** | Sí | <*sql-connection-string*> | Cadena de conexión de SQL, que actualmente solo admite la autenticación de SQL, no la autenticación de OAuth ni la autenticación de identidad administrada. <p><p>**Nota**: Asegúrese de especificar una cadena de conexión correcta porque Azure Portal no la validará automáticamente. |
   | **Tipo de plan** | Sí | <*plan-de-hospedaje*> | Plan de hospedaje que se usará para implementar la aplicación lógica. <p><p>Para más información, revise [Planes de hospedaje y planes de tarifa](logic-apps-pricing.md#standard-pricing). |
   | **Plan de Windows** | Sí | <*nombre-de-plan*> | El nombre de plan que se usará. Seleccione el nombre de un plan existente o proporcione un nombre para un plan nuevo. <p><p>Este ejemplo usa el nombre de `Fabrikam-Service-Plan`. |
   | **SKU y tamaño** | Sí | <*plan-de-tarifa*> | [Plan de tarifa](../app-service/overview-hosting-plans.md) que se usará para la aplicación lógica. La selección afecta a los precios, al proceso, a la memoria y al almacenamiento que se usan en la aplicación lógica y los flujos de trabajo. <p><p>Para cambiar el plan de tarifa predeterminado, seleccione **Cambiar tamaño**. Después, puede seleccionar otros planes de tarifa, en función de la carga de trabajo que necesite. <p><p>Para más información, revise [Planes de hospedaje y planes de tarifa](logic-apps-pricing.md#standard-pricing). |
   |||||

   En el ejemplo siguiente se muestra la página **Crear aplicación lógica** con la pestaña **Hospedaje**:

   ![Captura de pantalla que muestra Azure Portal y la página "Crear aplicación lógica" con la pestaña "Hospedaje".](./media/set-up-sql-db-storage-single-tenant-standard-workflows/set-up-sql-storage-details.png)

1. Termine el resto de los pasos de creación que se indican en [Creación de un flujo de trabajo de integración con Azure Logic Apps (estándar) de inquilino único en Azure Portal](create-single-tenant-workflows-azure-portal.md).

Cuando haya terminado, el nuevo recurso y flujo de trabajo de aplicación lógica estarán activos en Azure y usarán la base de datos SQL como proveedor de almacenamiento.

<a name="set-up-sql-local-dev"></a>

## <a name="set-up-sql-for-local-development-in-visual-studio-code"></a>Configuración de SQL para el desarrollo local en Visual Studio Code

En los pasos siguientes se muestra cómo configurar SQL como proveedor de almacenamiento para el desarrollo y las pruebas locales en Visual Studio Code:

1. Configure el entorno de desarrollo para que funcione con una instancia de Azure Logic Apps de inquilino único.

   1. Para trabajar en Visual Studio Code con la extensión de Azure Logic Apps (estándar), debe cumplir los [requisitos previos](create-single-tenant-workflows-visual-studio-code.md#prerequisites).

   1. [Configure Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#set-up) para que funcione con la extensión de Azure Logic Apps (estándar).

   1. En Visual Studio Code, [conéctese a su cuenta de Azure](create-single-tenant-workflows-visual-studio-code.md#connect-azure-account) y [cree un proyecto de aplicación lógica en blanco](create-single-tenant-workflows-visual-studio-code.md#create-project).

1. En Visual Studio Code, abra el panel de exploración, si aún no está abierto.

1. En este panel, en la raíz del proyecto de aplicación lógica, mueva el puntero del ratón sobre cualquier área en blanco debajo de todos los archivos y carpetas, abra el menú contextual y seleccione **Use SQL storage for your Logic App project** (Usar el almacenamiento de SQL en el proyecto de aplicación lógica).

   ![Captura de pantalla que muestra Visual Studio Code, el panel de exploración y el puntero del mouse en la raíz del proyecto del área en blanco, el menú contextual abierto y la opción "Use SQL storage for your Logic App project" (Usar el almacenamiento de SQL en el proyecto de aplicación lógica) seleccionada.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/use-sql-storage-logic-app-project.png)

1. Cuando se le solicite, escriba la cadena de conexión de SQL. Puede optar por usar una instancia local de SQL Express o cualquier otra base de datos SQL que tenga.

   ![Captura de pantalla que muestra Visual Studio Code y la solicitud de la cadena de conexión de SQL.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-sql-connection-string.png)

   Después de la confirmación, Visual Studio Code crea la siguiente configuración en el archivo **local.settings.json** del proyecto. Puede actualizar esta configuración en cualquier momento.  

   ![Captura de pantalla que muestra Visual Studio Code, el proyecto de aplicación lógica y el archivo "local.settings.json" abierto con la opción de cadena de conexión de SQL.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/local-settings-json-file.png)

<a name="set-up-sql-logic-app-deployment-visual-studio-code"></a>

## <a name="set-up-sql-during-deployment-from-visual-studio-code"></a>Configuración de SQL durante la implementación desde Visual Studio Code

Puede publicar directamente el proyecto de aplicación lógica de Visual Studio Code en Azure. Esta acción implementa el proyecto de aplicación lógica con el tipo de recurso  **Logic App (Standard)**   (Aplicación lógica [estándar]).

- Si va a publicar el proyecto como un nuevo recurso **Logic App (Standard)** (Aplicación lógica [estándar]) en Azure y quiere usar SQL como proveedor de almacenamiento, especifique la cadena de conexión de SQL al publicar la aplicación. Para ver los pasos completos, consulte [Configuración de SQL para la implementación de nuevas aplicaciones lógicas](#deploy-new-logic-app-visual-studio-code).

- Si ya ha configurado SQL, puede publicar el proyecto de aplicación lógica en un recurso **Logic App (Standard)** (Aplicación lógica [estándar]) ya implementado en Azure. Esta acción sobrescribe la aplicación lógica existente.

  > [!NOTE]
  > SQL Express local no funcionará con aplicaciones lógicas implementadas y hospedadas en Azure.

<a name="deploy-new-logic-app-visual-studio-code"></a>

### <a name="set-up-sql-for-new-logic-app-standard-resource-deployment"></a>Configuración de SQL para la implementación de nuevos recursos de aplicación lógica estándar

1. En la barra de actividad de Visual Studio Code, seleccione el icono de Azure.

1. En la barra de herramientas del panel  **Azure: Logic Apps (Standard)**   (Azure: Logic Apps [estándar]), seleccione  **Deploy to Logic App** (Implementar en la aplicación lógica).

   ![Captura de pantalla que muestra el panel "Azure: Logic Apps (Standard)" (Azure: Logic Apps [estándar]) y el icono "Deploy to Logic App" (Implementar en la aplicación lógica) seleccionados.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/deploy-to-logic-app.png)

1. Si se le solicita, seleccione la suscripción de Azure que se va a usar para la implementación de la aplicación lógica.

1. En la lista que abre Visual Studio Code, asegúrese de seleccionar la opción avanzada **Create new Logic App (Standard) in Azure Advanced** (Crear nueva instancia de Logic Apps [estándar] en Azure avanzado). De lo contrario, no se le pedirá que configure SQL.

   ![Captura de pantalla que muestra la opción de implementación "Create new Logic App (Standard) in Azure Advanced" (Crear nueva instancia de Logic Apps [estándar] en Azure avanzado) seleccionada.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-create-logic-app-advanced.png)

1. Cuando se le pida, proporcione un nombre globalmente único para la nueva aplicación lógica, que es el nombre que se usará para el recurso  **Logic App (Standard)**   (Aplicación lógica [estándar]). En este ejemplo se usa  `Fabrikam-Workflows-App`.

   ![Captura de pantalla que muestra la solicitud de un nombre único global para la aplicación lógica.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-logic-app-name.png)

1. Seleccione una ubicación para la aplicación lógica. También puede empezar a escribir para filtrar la lista.

   - Para realizar la implementación en Azure, seleccione la región de Azure preferida. Si anteriormente creó un recurso App Service Environment v3 (ASEv3) y quiere implementarlo allí, seleccione su ASEv3.

   - Para realizar la implementación en Logic Apps habilitado para Azure Arc, seleccione la ubicación personalizada configurada previamente.

   En el ejemplo siguiente se muestra la lista de ubicaciones filtrada por **Oeste de EE. UU**.

   ![Captura de pantalla que muestra la solicitud de seleccionar una ubicación de implementación con regiones de Azure disponibles y una ubicación personalizada para implementaciones de Azure Arc.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-location.png)

1. Seleccione el tipo de plan de hospedaje para la nueva aplicación lógica.

   1. Si seleccionó una instancia de ASEv3 como ubicación de la aplicación, elija **Plan de App Service** y, luego, seleccione el recurso ASEv3. En caso contrario, seleccione **Workflow Standard** (Flujo de trabajo estándar).

      ![Captura de pantalla que muestra la solicitud de selección de "Workflow Standard" (Flujo de trabajo estándar) o "Plan de App Service".](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-hosting-plan.png)

   1. Cree un nombre para el plan o seleccione uno existente.

      En este ejemplo se selecciona  **Crear nuevo plan de App Service**, ya que no hay planes existentes disponibles.

      ![Captura de pantalla que muestra la solicitud de creación de un nombre para el plan de hospedaje con la opción "Crear nuevo plan de App Service" seleccionada.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-app-service-plan.png)

1. Proporcione un nombre para el plan de hospedaje y, a continuación, seleccione un plan de tarifa para el plan seleccionado.

   Para más información, revise [Planes de hospedaje y planes de tarifa](logic-apps-pricing.md#standard-pricing).

1. Cuando se le pida un grupo de recursos de Azure para la implementación, para un mejor rendimiento, seleccione el mismo grupo de recursos que el del proyecto.

   > [!NOTE]
   > Aunque puede crear o usar un grupo de recursos diferente, esto podría afectar al rendimiento. Si crea o elige otro grupo de recursos, pero cancela después de que aparezca el mensaje de confirmación, la implementación también se cancelará.

1. Cuando se le pida que seleccione una cuenta de almacenamiento para la aplicación lógica, elija una de las siguientes opciones:

   - Si seleccionó previamente una ubicación personalizada, elija la opción **SQL**.

   - Si quiere realizar la implementación en Azure, seleccione la opción **SQL and Azure Storage** (SQL y Azure Storage).

     > [!NOTE]
     > Esta opción solo es necesaria para las implementaciones de Azure. En Azure, se requiere Azure Storage para realizar un hospedaje puntual de la configuración de la aplicación lógica en la plataforma Azure Logic Apps. El estado del flujo de trabajo en curso, el historial de ejecución y otros artefactos en tiempo de ejecución se almacenan en la base de datos SQL.
     >
     > En el caso de implementaciones en una ubicación personalizada hospedada en un clúster de Azure Arc, solo necesita SQL como proveedor de almacenamiento.  

1. Cuando se le pida, seleccione **Crear una nueva cuenta de almacenamiento** o elija una cuenta de almacenamiento existente, si dispone de una.

   ![Captura de pantalla que muestra el panel "Azure: Logic Apps (estándar)" y un mensaje para crear o seleccionar una cuenta de almacenamiento.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-storage.png)

1. Cuando se le solicite que confirme el almacenamiento de SQL; seleccione **Sí**. Cuando se le solicite la cadena de conexión, escriba la cadena de conexión de SQL.

   > [!NOTE]
   > Asegúrese de escribir una cadena de conexión correcta, ya que Visual Studio Code no la validará automáticamente.

   ![Captura de pantalla que muestra Visual Studio Code y la solicitud de la cadena de conexión de SQL.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-sql-connection-string.png)

1. Finalice el resto de los pasos de implementación que se indican en [Publicación en un nuevo recurso de aplicación lógica estándar](create-single-tenant-workflows-visual-studio-code.md#publish-new-logic-app).

Cuando haya terminado, el nuevo recurso y flujo de trabajo de aplicación lógica estarán activos en Azure y usarán la base de datos SQL como proveedor de almacenamiento.

## <a name="validate-deployments"></a>Validación de implementaciones

Después de implementar el recurso **Logic App (Standard)** (Aplicación lógica [estándar]) en Azure, puede comprobar si la configuración es correcta:

1. En [Azure Portal](https://portal.azure.com), abra el recurso de aplicación lógica.

1. En el menú de navegación del recurso, en **Configuración**, seleccione **Configuración**.

1. En el panel **Configuración**, en **Configuración de la aplicación**, busque la configuración de aplicación **Workflows.Sql.ConnectionString** y confirme que aparece la cadena de conexión de SQL y que es correcta.

1. En el entorno de SQL, confirme que las tablas de SQL se crearon con el nombre de esquema que empieza por "dt" y "dq".

Por ejemplo, en la captura de pantalla siguiente se muestran las tablas que el entorno de ejecución de Azure Logic Apps de inquilino único creó para un recurso de aplicación lógica con un único flujo de trabajo:

![Captura de pantalla que muestra las tablas de SQL creadas por el entorno de ejecución de Azure Logic Apps de inquilino único.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/runtime-created-tables-sql.png)

El servicio Azure Logic Apps de inquilino único también crea tipos de tabla definidos por el usuario. Por ejemplo, en la captura de pantalla siguiente se muestran los tipos de tabla definidos por el usuario que el entorno de ejecución de Azure Logic Apps de inquilino único creó para un recurso de aplicación lógica con un único flujo de trabajo:

![Captura de pantalla que muestra los tipos de tabla de SQL definidos por el usuario creados por el entorno de ejecución de Azure Logic Apps de inquilino único.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/runtime-created-user-defined-tables-sql.png)

## <a name="next-steps"></a>Pasos siguientes

- [Creación de un flujo de trabajo de integración con Azure Logic Apps (estándar) de inquilino único en Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md)
- [Edición de la configuración de host y aplicación para aplicaciones lógicas en Azure Logic Apps de inquilino único](edit-app-settings-host-settings.md)
