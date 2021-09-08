---
title: Creación de parámetros para valores en flujos de trabajo de un solo inquilino
description: Defina parámetros para los valores en los flujos de trabajo que difieren entre entornos de implementación de Azure Logic Apps de un solo inquilino.
services: logic-apps
ms.suite: integration
ms.reviewer: azla
ms.topic: how-to
ms.date: 08/09/2021
ms.openlocfilehash: 32d15d248154c61320fe12074373a6401fe3bdcd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742201"
---
# <a name="create-parameters-to-use-in-workflows-across-environments-in-single-tenant-azure-logic-apps"></a>Creación de parámetros para usar en los flujos de trabajo entre entornos de Azure Logic Apps de un solo inquilino

En Azure Logic Apps, puede abstraer los valores que pueden cambiar en los flujos de trabajo en los entornos de desarrollo, prueba y producción definiendo *parámetros*. Al usar parámetros en su lugar, puede centrarse más en el diseño de los flujos de trabajo e insertar más adelante las variables específicas del entorno.

En este artículo se presenta cómo funcionan los parámetros en el entorno de Azure Logic Apps de un solo inquilino y cómo editar, hacer referencia y administrar variables de entorno mediante la experiencia de parámetros de un solo inquilino.

<a name="parameters-introduction"></a>

## <a name="parameters-in-single-tenant-versus-multi-tenant"></a>Parámetros en un solo inquilino frente a multiinquilino

Si ha trabajado con Azure Logic Apps *multiinquilino*, es posible que conozca bien los parámetros. La parametrización de las entradas de flujos de trabajo en Azure Logic Apps de un solo inquilino funciona de forma similar a Azure Logic Apps multiinquilino, pero con una diferencia muy importante.

Por ejemplo, en el servicio de un solo inquilino y multiinquilino, puede definir parámetros cuando trabaje en el diseñador del flujo de trabajo. Después de definir el parámetro, puede hacer referencia al mismo desde cualquier flujo de trabajo o conexión que se encuentre en el *mismo* recurso de aplicación lógica. Pero, en el servicio *multiinquilino*, después de crear y usar parámetros en el diseñador, se deben definir y establecer las variables de entorno en la plantilla de Resource Manager (plantilla de ARM) y en los archivos de parámetros de plantilla. En este escenario, tendrá que definir y establecer los parámetros *en la implementación* lo que significa que, aunque solo tenga que cambiar una variable, tendrá que volver a implementar la plantilla de ARM de la aplicación lógica.

En cambio, con el servicio *de un solo inquilino*, puede trabajar con variables de entorno tanto en tiempo de ejecución como en la implementación mediante los parámetros *y* la configuración de la aplicación. En Azure Logic Apps de un solo inquilino, la configuración de la aplicación contiene opciones de configuración globales para *todos los flujos de trabajo* de la misma aplicación lógica. Para obtener más información, consulte [Edición de la configuración de host y aplicación para aplicaciones lógicas basadas en un solo inquilino](edit-app-settings-host-settings.md).

Por ejemplo, puede usar la configuración de la aplicación para integrarse con Azure Key Vault y [hacer referencia directamente a cadenas seguras](../app-service/app-service-key-vault-references.md), tales como cadenas de conexión y claves. De forma similar a las plantillas de ARM, donde puede definir variables de entorno en el momento de la implementación, puede definir la configuración de la aplicación dentro de la [definición de flujo de trabajo de la aplicación lógica](/azure/templates/microsoft.logic/workflows). A continuación, puede capturar valores de infraestructura generados dinámicamente, como puntos de conexión, cadenas de almacenamiento, etc.

Pero la configuración de la aplicación tiene límites de tamaño y no se puede hacer referencia a ella desde determinadas áreas de Azure Logic Apps. Los parámetros ofrecen una gama más amplia de casos de uso que la configuración de la aplicación, como la compatibilidad con tamaños grandes del valor y objetos complejos.

Por ejemplo, si usa Visual Studio Code como herramienta de desarrollo local para ejecutar flujos de trabajo localmente, en el proyecto de aplicación lógica, puede definir parámetros mediante el archivo **parameters.json**. Después, puede hacer referencia a cualquier parámetro de este archivo de parámetros desde cualquier flujo de trabajo del archivo **workflow.json** del proyecto o desde cualquier objeto de conexión en el archivo **connections.json** del proyecto. En la lista siguiente se describen algunos casos de uso comunes:

* Tener un archivo de parámetros de prueba que incluya todos los valores que se usan durante las pruebas. Durante la implementación, puede reemplazar el archivo de parámetros de prueba por el archivo de parámetros de producción.

* Parametrizar distintas partes del archivo **connections.json**. A continuación, puede comprobar el archivo **connections.json** en el control de código fuente y, después, administrar todas las conexiones mediante el archivo **parameters.json**.

* Parametrizar objetos complejos, como el objeto JSON `authentication`. Por ejemplo, puede reemplazar el valor del objeto `authentication` por una cadena que contenga una expresión de parámetros única, como `@parameters('api-auth')`.

* Revisar y editar la configuración de la aplicación en el archivo **local.settings.json** del proyecto. A continuación, puede hacer referencia a esta configuración de la aplicación en los parámetros.

> [!NOTE]
> Como recomendación general, considere la posibilidad de usar parámetros, y no la configuración de la aplicación, como la manera predeterminada de parametrizar los valores. De este modo, cuando necesite almacenar claves o cadenas seguras, puede seguir la recomendación para hacer referencia a la configuración de la aplicación a partir de los parámetros. Si quiere, puede usar ambas opciones en la solución mediante parámetros para hacer referencia a la configuración de la aplicación.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene una, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [flujo de trabajo de aplicación lógica en Azure Logic Apps de un solo inquilino](single-tenant-overview-compare.md).

  Si no tiene una aplicación lógica, [créela (Estándar) en Azure Portal](create-single-tenant-workflows-azure-portal.md) o en [Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md).

## <a name="define-use-and-edit-parameters"></a>Definición, uso y edición de parámetros

### <a name="azure-portal"></a>Azure portal

1. En [Azure Portal](https://portal.azure.com), abra la aplicación lógica basada en un solo inquilino. En **Flujos de trabajo**, seleccione y abra el flujo de trabajo en el diseñador.

1. En la barra de herramientas del diseñador, seleccione **Parámetros**.

   ![Captura de pantalla en la que se muestra Azure Portal, el diseñador de flujo de trabajo y la opción "Parámetros" seleccionada en la barra de herramientas del diseñador.](./media/parameterize-workflow-app/portal-designer-select-parameters.png)

1. En el panel **Parámetros** seleccione **Crear parámetro**.

1. Proporcione la información siguiente sobre el parámetro que se creará:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Nombre** | Sí | Nombre del parámetro que se va a crear. |
   | **Tipo** | Sí | Tipo de datos del parámetro, como **Array**, **Bool**, **Float**, **Int**, **Objet** y **String**. <p><p>**Nota**: En flujos de trabajo basados en un solo inquilino, no se admiten tipos de datos seguros, como `securestring` y `secureobject`. |
   | **Valor** | Sí | Valor del parámetro. <p><p>En el caso de Azure Logic Apps de inquilino único, debe especificar el valor del parámetro porque la lógica de flujo de trabajo, la información de conexión y los valores de parámetro no existen en una única ubicación. El diseñador debe poder resolver el valor del parámetro antes de cargarlo. |
   |||

   En el ejemplo siguiente se muestra una definición para un parámetro de cadena:

   ![Captura de pantalla en la que se muestra Azure Portal, el diseñador de flujo de trabajo y el panel "Parámetros" con una definición de parámetro de ejemplo.](./media/parameterize-workflow-app/define-parameter.png)

1. Cuando haya terminado, cierre el panel **Parámetros**, pero asegúrese de guardar el flujo de trabajo para guardar la nueva definición de parámetro.

1. Para hacer referencia al parámetro desde un desencadenador o una acción que se encuentra en cualquier flujo de trabajo de la misma aplicación lógica, siga estos pasos:

   1. En el diseñador, abra el flujo de trabajo que quiera y expanda el desencadenador o la acción.

   1. En la propiedad donde quiere usar el parámetro, haga clic dentro del cuadro de edición de esa propiedad.

   1. En la lista de contenido dinámico que se abre, en **Parámetros**, seleccione el parámetro creado anteriormente, por ejemplo:

      ![Captura de pantalla en la que se muestra una acción de ejemplo con el cursor en el cuadro de edición de propiedades, la lista de contenido dinámico expandida y el parámetro creado previamente seleccionado.](./media/parameterize-workflow-app/reference-parameter.png)

1. Para ver o editar parámetros en la misma aplicación lógica, siga cualquiera de estos dos pasos:

   * Abra cualquier flujo de trabajo en esa aplicación lógica. En el menú del flujo de trabajo, seleccione **Diseñador**. En la barra de herramientas del diseñador, seleccione **Parámetros**.

     Se abre el panel **Parámetros** y se muestran todos los parámetros definidos en los flujos de trabajo de esa aplicación lógica.

   * Para ver o editar en JSON masivo, en el menú principal de la aplicación lógica, seleccione **Parámetros**.

     Se abre la vista de JSON **Parámetros** y se muestran todos los parámetros definidos en los flujos de trabajo de esa aplicación lógica.

### <a name="visual-studio-code"></a>Visual Studio Code

1. En un archivo JSON de nivel raíz de proyecto denominado **parameters.json**, defina *todos* los parámetros y sus valores. Este archivo tiene un objeto que incluye pares *clave-valor*. Cada *clave* es el nombre de cada parámetro, mientras que cada *valor* es la estructura de cada parámetro. Cada estructura debe incluir una declaración `type` y `value`.

   > [!IMPORTANT]
   > El archivo **parameters.json** debe definir e incluir todos los parámetros y sus valores a los que haga referencia o use en otra parte del proyecto, por ejemplo, en las definiciones de flujo de trabajo o las conexiones.

   En el ejemplo siguiente se muestra un archivo de parámetros básicos:

   ```json
   {
        "responseString": { 
            "type": "string", 
            "value": "hello" 
        },
        "functionAuth": { 
            "type": "object", 
            "value": { 
                "type": "QueryString", 
                "name": "Code", 
                "value": "@appsetting('<AzureFunctionsOperation-FunctionAppKey>')" 
            }
        }
    }
    ```

    > [!NOTE]
    > El único tipo de expresión válido en el archivo **parameters.json** es `@appsetting`.

1. Para hacer referencia a parámetros en las entradas de desencadenador o acción, use la expresión `@parameters('<parameter-name>')`.

#### <a name="parameterize-connections-file"></a>Parametrización del archivo de conexiones

Para parametrizar el archivo **connections.json**, reemplace los valores de los literales, como `ConnectionRuntimeUrl`, por una expresión `parameters()` única, como por ejemplo, `@parameters('api-runtimeUrl')`. Los únicos tipos de expresión válidos en el archivo **connections.json** son `@parameters` y `@appsetting`.

> [!IMPORTANT]
>
> Durante el desarrollo, si parametriza el archivo **connections.json**, la experiencia del diseñador se restringe, tanto localmente como en Azure Portal. Si necesita usar el diseñador para desarrollo, en su lugar use un archivo **connections.json** sin parametrizar. A continuación, en las canalizaciones de implementación, reemplace por el archivo con parámetros. 
> El tiempo de ejecución sigue funcionando con parametrización. Las mejoras del diseñador están en desarrollo.

En el ejemplo siguiente se muestra un archivo **connections.json** parametrizado que usa la configuración de la aplicación y los parámetros. Aunque quiere usar parámetros siempre que sea posible, este escenario es una excepción o un caso perimetral en el que se usaría la configuración de la aplicación sobre los parámetros porque la configuración de la aplicación se genera durante la implementación y es más fácil de rellenar dinámicamente en una canalización de desarrollo. En este archivo de ejemplo se usa un parámetro para el objeto de autenticación `blob_auth` complejo y la configuración de la aplicación para los demás valores. En este caso, puede usar un parámetro para el objeto de autenticación, ya que es poco probable que haga referencia al parámetro en el flujo de trabajo:

```json
{
   "serviceProviderConnections": {
      "serviceBus": {
         "parameterValues": {
            "connectionString": "@appsetting('serviceBus_connectionString')"
        },
        "serviceProvider": {
           "id": "/serviceProviders/serviceBus"
        },
        "displayName": "servicebus"
     }
   },
   "managedApiConnections": {
      "azureblob": {
         "api": {
            "id": "/subscriptions/@appsetting('WORKFLOWS_SUBSCRIPTION_ID')/providers/Microsoft.Web/locations/@appsetting('WORKFLOWS_LOCATION_NAME')/managedApis/azureblob"
         },
         "connection": {
            "id": "/subscriptions/@appsetting('WORKFLOWS_SUBSCRIPTION_ID')/resourceGroups/@appsetting('WORKFLOWS_RESOURCE_GROUP_NAME')/providers/Microsoft.Web/connections/azureblob"
         },
         "connectionRuntimeUrl": "@appsetting('BLOB_CONNECTION_RUNTIMEURL')",
         "authentication": "@parameters('blob_auth')"
      }
   }
}
```

## <a name="manage-parameters-files"></a>Administración de archivos de parámetros

Normalmente, debe administrar varias versiones de archivos de parámetros. Es posible que tenga valores de destino para distintos entornos de implementación, como desarrollo, pruebas y producción. La administración de estos archivos de parámetros suele funcionar como la administración de archivos de parámetros de plantilla de ARM. Al realizar la implementación en un entorno específico, se promueve el archivo de parámetros correspondiente, por lo general a través de una canalización para DevOps.

Para reemplazar los archivos de parámetros de forma dinámica mediante la CLI de Azure, ejecute el comando siguiente:

```azurecli
az functionapp deploy --resource-group MyResourceGroup --name MyLogicApp --src-path C:\parameters.json --type static --target-path parameters.json
```

Si tiene un proyecto de aplicación lógica basado en NuGet, tendrá que actualizar el archivo de proyecto ( **&lt;nombre-de-aplicación-lógica&gt;.csproj**) para incluir el archivo de parámetros en la salida de compilación, por ejemplo:

```csproj
<ItemGroup>
  <None Update="parameters.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
  </None>
</ItemGroup>
```

> [!NOTE]
> Actualmente, la capacidad de reemplazar de forma dinámica los archivos de parámetros aún no está disponible en Azure Portal ni en el diseñador de flujos de trabajo.

Para obtener más información sobre cómo configurar las aplicaciones lógicas para implementaciones de DevOps, revise la siguiente documentación:

* [Introducción a la implementación de DevOps para aplicaciones lógicas basadas en un solo inquilino](devops-deployment-single-tenant-azure-logic-apps.md)
* [Configuración de la implementación de DevOps para aplicaciones lógicas basadas en un solo inquilino](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="manage-app-settings"></a>Administración de la configuración de la aplicación

En Azure Logic Apps de un solo inquilino, la configuración de la aplicación contiene opciones de configuración globales para *todos los flujos de trabajo* de la misma aplicación lógica. Al ejecutar flujos de trabajo localmente en Visual Studio Code, se puede acceder a esta configuración de la aplicación como variables de entorno local en el archivo **local.settings.json**. A continuación, puede hacer referencia a esta configuración de la aplicación en los parámetros.

Para agregar, actualizar o eliminar la configuración de la aplicación, seleccione y revise las secciones siguientes para Visual Studio Code, Azure Portal, la CLI de Azure o la plantilla de ARM (Bicep).

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

Para revisar la configuración de la aplicación de la aplicación lógica en Azure Portal, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com/), abra la aplicación lógica basada en un solo inquilino.

1. En el menú de la aplicación lógica, en **Configuración**, seleccione **Configuración**.

1. En la página **Configuración**, en la pestaña **Configuración de la aplicación**, revise la configuración de la aplicación de la aplicación lógica.

1. Para ver todos los valores, seleccione **Mostrar valores**. O bien, para ver un valor único, seleccione ese valor.

Para agregar una configuración nueva, siga estos pasos:

1. En la pestaña **Configuración de la aplicación**, en **Configuración de la aplicación**, seleccione **Nueva configuración la de aplicación**.

1. En **Nombre**, escriba la *clave* o el nombre de la nueva configuración.

1. En **Valor**, escriba el valor de la nueva configuración.

1. Cuando esté listo para crear el nuevo par *clave-valor*, seleccione **Aceptar**.

:::image type="content" source="./media/parameterize-workflow-app/portal-app-settings-values.png" alt-text="Captura de pantalla que muestra Azure Portal y el panel de configuración con los valores y la configuración de la aplicación de una aplicación lógica basada en un solo inquilino" lightbox="./media/parameterize-workflow-app/portal-app-settings-values.png":::.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para revisar la configuración actual de la aplicación mediante la CLI de Azure, ejecute el comando `az logicapp config appsettings list`. Asegúrese de que el comando incluye los parámetros `--name -n` y `--resource-group -g`, por ejemplo:

```azurecli
az logicapp config appsettings list --name MyLogicApp --resource-group MyResourceGroup
```

Para agregar o actualizar una configuración de la aplicación mediante la CLI de Azure, ejecute el comando `az logicapp config appsettings set`. Asegúrese de que el comando incluye los parámetros `--name n` y `--resource-group -g`. Por ejemplo, el comando siguiente crea una configuración con una clave denominada `CUSTOM_LOGIC_APP_SETTING` con un valor de `12345`:

```azurecli
az logicapp config appsettings set --name MyLogicApp --resource-group MyResourceGroup --settings CUSTOM_LOGIC_APP_SETTING=12345 
```

### <a name="resource-manager-or-bicep-template"></a>[Plantilla de Resource Manager o de Bicep](#tab/azure-resource-manager)

Para revisar y definir la configuración de la aplicación en una plantilla de ARM o en una plantilla de Bicep, busque la definición de recursos de la aplicación lógica y actualice el objeto JSON `appSettings`. Para obtener la definición de recursos completa, consulte la [referencia sobre la plantilla de ARM](/azure/templates/microsoft.web/sites).

En este ejemplo se muestra la configuración de archivos para las plantillas de ARM o las plantillas de Bicep:

```json
"appSettings": [
    {
        "name": "string",
        "value": "string"
    },
    {
        "name": "string",
        "value": "string"
    },
    <...>
], 
```

---

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Comparación de las opciones de un solo inquilino, multiinquilino y entorno del servicio de integración para Azure Logic Apps](single-tenant-overview-compare.md)
