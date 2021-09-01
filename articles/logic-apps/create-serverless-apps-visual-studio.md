---
title: Creación de una aplicación sin servidor de ejemplo con Visual Studio
description: Con una plantilla de inicio rápido de Azure, cree, implemente y administre una aplicación sin servidor de ejemplo con Azure Logic Apps y Azure Functions en Visual Studio.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 07/15/2021
ms.openlocfilehash: df395fcffc1a7e675921f2ff397d488c301c703a
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114363907"
---
# <a name="create-an-example-serverless-app-with-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Creación de una aplicación sin servidor de ejemplo con Azure Logic Apps y Azure Functions en Visual Studio

Puede crear, compilar e implementar rápidamente aplicaciones "sin servidor" basadas en la nube mediante los servicios y funcionalidades de Azure, como Azure Logic Apps y Azure Functions. Cuando se usa Azure Logic Apps, puede crear flujos de trabajo de forma rápida y sencilla mediante enfoques con poco código o sin código para simplificar la orquestación de tareas combinadas. Puede integrar diferentes servicios, en la nube, locales o híbridos, sin codificar esas interacciones, tener que mantener el código de adherencia o aprender nuevas API o especificaciones. Cuando se usa Azure Functions, puede acelerar el desarrollo mediante un modelo controlado por eventos. Puede usar desencadenadores que respondan a eventos ejecutando automáticamente su propio código. Puede usar enlaces para integrar sin problemas otros servicios.

En este artículo, se muestra cómo crear una aplicación sin servidor de ejemplo que se ejecuta en Azure multiinquilino mediante una plantilla de inicio rápido de Azure. La plantilla crea un proyecto de grupo de recursos de Azure que incluye una plantilla de implementación de Azure Resource Manager. Esta plantilla define un recurso de aplicación lógica básica en el que un flujo de trabajo predefinido incluye una llamada a una función de Azure definida por el usuario. La definición del flujo de trabajo incluye los siguientes componentes:

* Un desencadenador de solicitud que recibe solicitudes HTTP. Para iniciar este desencadenador, envíe una solicitud a la dirección URL del desencadenador.
* Una acción de Azure Functions que llama a una función de Azure que puede definir más adelante.
* Una acción de respuesta que devuelve una respuesta HTTP que contiene el resultado de la función.

Para más información, consulte los artículos siguientes:

* [Informática sin servidor: una introducción a las tecnologías sin servidor](https://azure.microsoft.com/overview/serverless-computing/)
* [Acerca de Azure Logic Apps](logic-apps-overview.md)
* [Acerca de Azure Functions](../azure-functions/functions-overview.md)
* [Sin servidor de Azure: Información general para la creación de soluciones y aplicaciones basadas en la nube con Azure Logic Apps y Azure Functions](logic-apps-serverless-overview.md)

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si no tiene una suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Descargue e instale las siguientes herramientas, si aún no las tiene:

  * [Visual Studio 2019, 2017 o 2015 (Community u otra edición)](https://aka.ms/download-visual-studio). Este inicio rápido usa Visual Studio Community 2019, que es gratuito.

    > [!IMPORTANT]
    > Al instalar Visual Studio 2019 o 2017, asegúrese de seleccionar la carga de trabajo **Desarrollo de Azure**.

  * [Microsoft Azure SDK para .NET (versión 2.9.1 o posterior)](https://azure.microsoft.com/downloads/). Más información sobre [Azure SDK for .NET](/dotnet/azure/intro).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Herramientas de Azure Logic Apps para la versión de Visual Studio que utilice. Puede [aprender a instalar esta extensión desde Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions) o puede descargar las versiones respectivas de las herramientas de Azure Logic Apps desde Visual Studio Marketplace:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)
    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)
    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    > [!IMPORTANT]
    > Asegúrese de reiniciar Visual Studio después de finalizar la instalación.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools), para que pueda depurar localmente la función de Azure. Para más información, consulte [Uso de Azure Functions Core Tools](../azure-functions/functions-run-local.md).

* Acceso a Internet mientras se usa el diseñador de flujos de trabajo insertado.

  El diseñador requiere una conexión a Internet para crear recursos en Azure y leer las propiedades y los datos de los [conectores administrados](../connectors/managed.md) del flujo de trabajo. Por ejemplo, si usa el conector de SQL, el diseñador comprueba en la instancia del servidor si hay propiedades predeterminadas y propiedades personalizadas disponibles.

## <a name="create-a-resource-group-project"></a>Creación de un proyecto de grupo de recursos

Para empezar, cree un proyecto de grupo de recursos de Azure como contenedor para la aplicación sin servidor. En Azure, un *grupo de recursos* es una colección lógica que se usa para organizar los recursos de toda la aplicación. A continuación, puede administrar e implementar estos recursos como un único recurso. Para una aplicación sin servidor de Azure, el grupo de recursos incluye recursos de Azure Logic Apps *y* Azure Functions. Para más información, consulte [Terminología de Resource Manager](../azure-resource-manager/management/overview.md#terminology).

1. Abra Visual Studio e inicie sesión con su cuenta de Azure, si se le solicita.

1. En la ventana de inicio que se abre, seleccione **Crear un nuevo proyecto**.

   ![Captura de pantalla que muestra la ventana de inicio de Visual Studio con la opción "Crear un nuevo proyecto" seleccionada.](./media/create-serverless-apps-visual-studio/start-window.png)

1. Si la ventana de inicio no se abre, en el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.

   ![Captura de pantalla que muestra el menú "Archivo" abierto con las opciones de menú "Nuevo" y "Proyecto" seleccionadas.](./media/create-serverless-apps-visual-studio/create-new-project-visual-studio.png)

1. Una vez que se abra la ventana **Crear un nuevo proyecto**, en el cuadro de búsqueda, seleccione la plantilla de proyecto **Grupo de recursos de Azure** para C# o Visual Basic. Cuando esté listo, seleccione **Siguiente**. Este ejemplo continúa con C#.

   ![Captura de pantalla que muestra la ventana "Crear un nuevo proyecto" y el cuadro de búsqueda con "grupo de recursos" junto con la plantilla de proyecto "Grupo de recursos de Azure" seleccionada.](./media/create-serverless-apps-visual-studio/start-window-find-project-template.png)

1. Una vez que se abra la ventana **Configurar el nuevo proyecto**, proporcione información sobre el proyecto, como el nombre. Seleccione **Crear** cuando haya terminado.

   ![Captura de pantalla que muestra la ventana "Configurar el nuevo proyecto" y los detalles del proyecto.](./media/create-serverless-apps-visual-studio/start-window-create-new-project-details.png)

1. Cuando se abra la ventana **Seleccionar plantilla de Azure**, en la lista **Mostrar plantillas de esta ubicación**, seleccione **Inicio rápido de Azure (github.com/Azure/azure-quickstart-templates)** como ubicación de la plantilla.

1. En el cuadro de búsqueda, escriba `logic-app-and-function-app`. En los resultados, seleccione la plantilla llamada **quickstarts\microsoft.logic\logic-app-and-function-app**. Cuando finalice, seleccione **Aceptar**.

   ![Captura de pantalla que muestra la ventana "Seleccionar plantilla de Azure" con "Inicio rápido de Azure" seleccionado como ubicación de la plantilla y "quickstarts\microsoft.logic\logic-app-and-function-app" seleccionada.](./media/create-serverless-apps-visual-studio/select-template.png)

   Visual Studio crea el proyecto de grupo de recursos, incluido el contenedor de la solución del proyecto.

   ![Captura de pantalla que muestra el proyecto y la solución creados.](./media/create-serverless-apps-visual-studio/create-serverless-solution.png)

1. Después, implemente la solución en Azure.

   > [!IMPORTANT]
   > Asegúrese de completar el paso de implementación. De lo contrario, no puede abrir, revisar ni editar el flujo de trabajo de la aplicación lógica mediante el diseñador de Visual Studio.

## <a name="deploy-your-solution"></a>Implementación de la solución

Para poder abrir la aplicación lógica mediante el diseñador de Visual Studio, debe implementar la aplicación en Azure. A continuación, el diseñador puede crear las conexiones a los servicios y recursos usados en el flujo de trabajo de la aplicación lógica.

1. En el Explorador de soluciones, desde el menú contextual del proyecto, seleccione **Implementar** > **Nuevo**.

   ![Captura de pantalla que muestra el Explorador de soluciones con el menú contextual del proyecto abierto, el menú "Implementar" abierto y la opción "Nuevo" seleccionada.](./media/create-serverless-apps-visual-studio/deploy.png)

1. Una vez que se abra la ventana **Implementar en grupo de recursos**, siga estos pasos para proporcionar la información de implementación:

   1. Después de que la ventana detecte la suscripción actual, confirme la suscripción de Azure o seleccione otra suscripción si lo desea.

   1. Cree un nuevo grupo de recursos de Azure. En la lista **Grupo de recursos**, seleccione **Crear nuevo**.

      ![Captura de pantalla que muestra la ventana "Implementar en grupo de recursos" con la opción "Crear nuevo" seleccionada.](./media/create-serverless-apps-visual-studio/create-resource-group-start.png)

   1. Una vez que se abra la ventana **Crear grupo de recursos**, proporcione la siguiente información:

      | Propiedad | Descripción |
      |----------|-------------|
      | Nombre de grupo de recursos | Nombre que se asigna al grupo de recursos |
      | Ubicación del grupo de recursos | Región del centro de datos de Azure para hospedar el recurso de aplicación lógica |
      |||

      En este ejemplo, se continúa con la creación de un grupo de recursos en la región Oeste de EE. UU.

      ![Captura de pantalla que muestra la ventana "Crear grupo de recursos" con información del nuevo grupo de recursos.](./media/create-serverless-apps-visual-studio/create-resource-group.png)

   1. Termine de crear e implementar la solución, por ejemplo:

      ![Configuración de implementación](./media/create-serverless-apps-visual-studio/deploy-to-resource-group.png)

1. Si aparece la ventana **Editar parámetros**, especifique los nombres de recurso que va a usar para el recurso de aplicación lógica y el recurso de aplicación de funciones implementados y, a continuación, guarde los cambios.

   > [!IMPORTANT]
   > Asegúrese de usar nombres únicos globalmente para la aplicación lógica y la aplicación de funciones.

   ![Captura de pantalla que muestra la ventana "Editar parámetros" con nombres para el recurso de aplicación lógica y el recurso de la aplicación de funciones.](./media/create-serverless-apps-visual-studio/logic-function-app-name-parameters.png)

   Cuando Visual Studio inicia la implementación en el grupo de recursos, el estado de implementación de la solución aparece en la ventana **Salida** de Visual Studio. Una vez finalizada la implementación, la aplicación lógica está activa en Azure Portal.

## <a name="open-and-edit-your-deployed-logic-app"></a>Apertura y edición de la aplicación lógica implementada

1. En el Explorador de soluciones, desde el menú contextual del archivo **azuredeploy.json**, seleccione **Open With Logic App Designer** (Abrir con el Diseñador de aplicación lógica).

   ![Captura de pantalla que muestra el menú contextual de "azuredeploy.json" con la opción "Abrir con el Diseñador de aplicación lógica" seleccionada.](./media/create-serverless-apps-visual-studio/open-designer.png)

   > [!TIP]
   > Si no tiene este comando en Visual Studio 2019, compruebe que tiene las actualizaciones más recientes de Visual Studio.

1. Una vez que se abra el diseñador de flujos de trabajo, puede continuar editando el flujo de trabajo o agregando pasos. Cuando haya terminado, recuerde guardar los cambios en el archivo **azuredeploy.json**.

   ![Captura de pantalla que muestra el flujo de trabajo de la aplicación lógica en el diseñador.](./media/create-serverless-apps-visual-studio/opened-logic-app-workflow.png)

## <a name="create-an-azure-functions-project"></a>Creación de un proyecto de Azure Functions

Para crear una función de Azure basada en C# desde la solución, cree un proyecto de biblioteca de clases de C# según las instrucciones que se indican en [Inicio rápido: Creación de la primera función de C# en Azure mediante Visual Studio](../azure-functions/functions-create-your-first-function-visual-studio.md). De lo contrario, para crear el proyecto y la función de Azure Functions con otros lenguajes, siga los siguientes inicios rápidos:

* [Inicio rápido: Creación de una función de Java con Visual Studio Code](../azure-functions/create-first-function-vs-code-java.md)
* [Inicio rápido: Creación de una función de JavaScript con Visual Studio Code](../azure-functions/create-first-function-vs-code-node.md)
* [Inicio rápido: Creación de una función de PowerShell en Azure mediante Visual Studio Code](../azure-functions/create-first-function-vs-code-powershell.md)
* [Inicio rápido: Creación de una función en Azure con Python mediante Visual Studio Code](../azure-functions/create-first-function-vs-code-python.md)
* [Inicio rápido: Creación de una función en Azure con TypeScript mediante Visual Studio Code](../azure-functions/create-first-function-vs-code-typescript.md)

## <a name="deploy-functions-from-visual-studio"></a>Implementación de funciones desde Visual Studio

La plantilla de implementación de la solución puede implementar cualquier función de Azure que exista en la solución desde el repositorio de Git especificado por las variables del archivo **azuredeploy.json**. Si crea el proyecto de Functions en la solución, puede comprobarlo en el control de código fuente de Git (por ejemplo, GitHub o Azure DevOps) y luego actualizar la variable `repo` en el archivo **azuredeploy.json** para que la plantilla implemente la función de Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Administración de aplicaciones lógicas y visualización del historial de ejecuciones

Si tiene recursos de aplicaciones lógicas ya implementados en Azure, puede editarlas, administrarlas, ver el historial de ejecución y deshabilitarlas desde Visual Studio. Para más información, consulte [Administración de aplicaciones lógicas con Visual Studio](manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Pasos siguientes

* Para otro ejemplo con Azure Logic Apps y Azure Functions, pruebe [Tutorial: Automatización de tareas para procesar correos electrónicos mediante Azure Logic Apps, Azure Functions y Azure Storage](tutorial-process-email-attachments-workflow.md).