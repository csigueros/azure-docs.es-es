---
title: Administración de modelos DTDL
titleSuffix: Azure Digital Twins
description: Vea cómo crear, editar y eliminar un modelo en Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 8/30/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2e5c137ceb08bd89dc70026639c6191b1c61f42d
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223246"
---
# <a name="manage-azure-digital-twins-models"></a>Administración de modelos de Azure Digital Twins

En este artículo se describe cómo administrar los [modelos](concepts-models.md) de la instancia de Azure Digital Twins. Las operaciones de administración incluyen la carga, validación, recuperación y eliminación de modelos. 

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-developer-interfaces.md](../../includes/digital-twins-developer-interfaces.md)]

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png" alt-text="Captura de pantalla de Azure Digital Twins Explorer en la que se muestra un grafo de modelo de ejemplo." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png":::

## <a name="create-models"></a>Crear modelos

Los modelos de Azure Digital Twins se escriben en DTDL y se guardan como archivos .json. También hay una [extensión de DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) disponible para [Visual Studio Code](https://code.visualstudio.com/), que proporciona validación de sintaxis y otras características para facilitar la escritura de documentos DTDL.

Considere un ejemplo en el que un hospital quiere representar digitalmente sus habitaciones. Cada habitación contiene un dispensador de jabón inteligente para supervisar el lavado de manos y sensores para controlar el tráfico en la habitación.

El primer paso para la solución consiste en crear modelos para representar aspectos del hospital. La habitación de un paciente en este escenario podría describirse de la siguiente manera:

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> Se trata de un cuerpo de ejemplo de un archivo .json en el que se define y se guarda un modelo, que se va a cargar como parte de un proyecto de cliente. La llamada a la API de REST, por otro lado, adopta una matriz de definiciones de modelo como la anterior (que se asigna a un atributo `IEnumerable<string>` en el SDK de .NET). Por lo tanto, para usar este modelo en la API de REST directamente, inclúyalo entre corchetes.

Este modelo define un nombre y un id. único para la habitación del paciente, y propiedades para representar el recuento de visitantes y el estado del lavado de manos. Estos contadores se actualizarán a partir de sensores de movimiento y dispensadores de jabón inteligentes, y se usarán juntos para calcular una propiedad de *porcentaje de lavado de manos*. El modelo también define una relación *hasDevices*, que se usará para conectar cualquier instancia de [Digital Twins](concepts-twins-graph.md) basada este modelo de Habitación con los dispositivos reales.

Siguiendo este método, puede continuar con la definición de modelos para las salas y las zonas del hospital, o bien para todo el hospital.

### <a name="validate-syntax"></a>Validación de la sintaxis

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="upload-models"></a>Carga de modelos

Una vez creados los modelos, puede cargarlos en la instancia de Azure Digital Twins.

Cuando esté listo para cargar un modelo, puede usar el fragmento de código siguiente para el [SDK de .NET](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true):

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

También puede cargar varios modelos en una sola transacción. 

Si usa el SDK, puede cargar varios archivos de modelo con el método `CreateModels` de esta manera:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModels_multi":::

Si usa las [API REST](/rest/api/azure-digitaltwins/) o [CLI de Azure](/cli/azure/dt?view=azure-cli-latest&preserve-view=true), también puede cargar varios modelos colocando varias definiciones de modelo en un único archivo JSON para cargarlos juntos. En este caso, los modelos deben colocarse en una matriz JSON dentro del archivo, como en el ejemplo siguiente:

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Moon.json":::

En la carga, el servicio valida los archivos de modelo.

## <a name="retrieve-models"></a>Recuperación de modelos

Puede mostrar y recuperar los modelos almacenados en la instancia de Azure Digital Twins. 

Existen varias opciones:
* Recuperar un único modelo
* Recuperar todos los modelos
* Recuperar los metadatos y las dependencias de los modelos.

A continuación, se muestran algunas llamadas de ejemplo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Todas las llamadas SDK para recuperar modelos devuelven todos los objetos `DigitalTwinsModelData`. `DigitalTwinsModelData` contiene metadatos sobre el modelo almacenado en la instancia Azure Digital Twins, como el nombre, la DTMI y la fecha de creación del modelo. El objeto `DigitalTwinsModelData` también incluye opcionalmente el propio modelo. Significa que, en función de los parámetros, puede usar las llamadas de recuperación para recuperar solo los metadatos (lo que resulta útil en escenarios en los que se desea mostrar una lista de interfaz de usuario de las herramientas disponibles, por ejemplo) o todo el modelo.

La llamada a `RetrieveModelWithDependencies` devuelve no solo el modelo solicitado, sino también todos los modelos de los que depende el modelo solicitado.

Los modelos no se devuelven necesariamente con el formato de documento exacto con el que se cargaron. Azure Digital Twins solo garantiza que el formato devuelvo será equivalente desde el punto de vista semántico. 

## <a name="update-models"></a>Actualización de modelos

En esta sección se describen las consideraciones y estrategias para actualizar los modelos.

### <a name="before-updating-think-in-the-context-of-your-entire-solution"></a>Antes de actualizar: piense en el contexto de toda la solución.

Antes de realizar actualizaciones en los modelos, se recomienda pensar holísticamente en toda la solución y el impacto de los cambios de modelo que está a punto de realizar. Los modelos de una solución Azure Digital Twins a menudo están interconectados, por lo que es importante tener en cuenta los cambios en cascada en los que la actualización de un modelo requiere actualizar varios otros. La actualización de modelos afectará a los gemelos que usan los modelos y también puede afectar a la entrada y el procesamiento de código, las aplicaciones cliente y los informes automatizados.

Estas son algunas recomendaciones que le ayudarán a administrar las transiciones del modelo sin problemas:
* En lugar de pensar en los modelos individuales, considere la posibilidad de evolucionar todo el conjunto de modelos cuando sea necesario para mantener los modelos y sus relaciones actualizados.
* Trate los modelos como código fuente y adminístrelos en el control de código fuente. Aplique el mismo rigor y la misma atención a los modelos y los cambios de modelo que aplica a otro código de la solución.

Cuando esté listo para continuar con la actualización de los modelos, en el resto de esta sección se describen las estrategias que puede usar para implementar las actualizaciones.

### <a name="strategies-for-updating-models"></a>Estrategias para actualizar modelos

Una vez que se carga un modelo en la instancia de Azure Digital Twins, la interfaz del modelo es inmutable, lo que significa que no hay ninguna "edición" tradicional de los modelos. Azure Digital Twins tampoco permite la recarga del mismo modelo exacto mientras un modelo correspondiente ya esté presente en la instancia.

En su lugar, si desea realizar cambios en un modelo, como actualizar `displayName` o `description`, o agregar y quitar propiedades, deberá reemplazar el modelo original.

Hay dos estrategias entre las que elegir al reemplazar un modelo:
* [Estrategia 1: Cargar una nueva versión del modelo](#strategy-1-upload-new-model-version): cargue el modelo, con un nuevo número de versión, y actualice los gemelos para usar ese nuevo modelo. Las versiones nuevas y anteriores del modelo existirán en la instancia hasta que elimine una.
    - **Use esta estrategia cuando** quiera actualizar solo algunos de los gemelos que usan el modelo, o cuando desee asegurarse de que los gemelos se mantienen conformes con sus modelos y se pueden escribir a través de la transición del modelo.
* [Estrategia 2: Eliminar el modelo antiguo y volver a cargarlo](#strategy-2-delete-old-model-and-reupload): elimine el modelo original y cargue el nuevo modelo con el mismo nombre e id. (valor DTMI) en su lugar. Reemplace completamente el modelo anterior por el nuevo. 
    - **Use esta estrategia cuando** desee actualizar todos los gemelos que usan este modelo a la vez, así como todo el código que reacciona a los modelos. Si la actualización del modelo contiene un cambio importante con la actualización del modelo, los gemelos no serán conformes con sus modelos durante un breve período de tiempo mientras los está transfiriendo del modelo anterior al nuevo, lo que significa que no podrán realizar ninguna actualización hasta que se cargue el nuevo modelo y los gemelos se ajusten a él.

>[!NOTE]
> No se recomienda realizar cambios importantes en los modelos fuera del desarrollo.

Continúe con las secciones siguientes para obtener más información sobre cada opción de estrategia en detalle.

### <a name="strategy-1-upload-new-model-version"></a>Estrategia 1: Cargar una nueva versión del modelo

Esta opción implica crear una nueva versión del modelo y cargarla en la instancia.

Esta operación **no** sobrescribe versiones anteriores del modelo, por lo que varias versiones del modelo coexistirán en la instancia hasta que [las quite](#remove-models). Puesto que coexisten la nueva versión del modelo y la versión anterior, los gemelos pueden usar la nueva versión del modelo o la versión anterior, lo que significa que cargar una nueva versión de un modelo no afecta automáticamente a los gemelos existentes. Los gemelos existentes permanecerán como instancias de la versión anterior del modelo y puede actualizar estos gemelos a la nueva versión del modelo mediante su aplicación de revisiones.

Para usar esta estrategia, siga estos pasos:

#### <a name="1-create-and-upload-new-model-version"></a>1. Cree y cargue una nueva versión del modelo 

Para crear una nueva versión de un modelo existente, empiece con el DTDL del modelo original. Actualice, agregue o quite los campos que quiera cambiar.

A continuación, marque este modelo como una versión más reciente del modelo actualizando el campo `id` del modelo. La última sección del identificador de modelo, después del `;`, representa el número de modelo. Para indicar que esta es una versión más actualizada de este modelo, incremente el número al final del valor `id` en cualquier número mayor que el número de versión actual.

Por ejemplo, si el identificador de modelo anterior tuviera el siguiente aspecto:

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

La versión 2 de este modelo podría tener este aspecto:

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

A continuación, [cargue](#upload-models) la nueva versión del modelo en la instancia. 

Esta versión del modelo estará disponible en su instancia para usarse con gemelos digitales. Esto **no** sobrescribe versiones anteriores del modelo, por lo que varias versiones del modelo coexistirán ahora en la instancia.

#### <a name="2-update-graph-elements-as-needed"></a>2. Actualice los elementos del grafo según sea necesario

A continuación, actualice los **gemelos y las relaciones** de la instancia para utilizar la nueva versión del modelo en lugar de la antigua.

Puede utilizar las siguientes instrucciones para [actualizar gemelos](how-to-manage-twin.md#update-a-digital-twins-model) y [actualizar relaciones](how-to-manage-graph.md#update-relationships). La operación de revisión para actualizar el modelo de un gemelo tendrá un aspecto parecido al siguiente:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

>[!IMPORTANT]
>Cuando actualice gemelos, utilice la **misma revisión** para actualizar los id. de modelo (a la nueva versión) y los campos que deben modificarse para que se ajusten al nuevo modelo.

Es posible que también tenga que actualizar **relaciones** y otros **modelos** de la instancia que hagan referencia a este modelo para que hagan referencia a la nueva versión del modelo. Tendrá que realizar otra operación de actualización de modelos para lograr este propósito, así que vuelva al principio de esta sección y repita el proceso para cualquier otro modelo que necesite actualizar.

#### <a name="3-optional-decommission-or-delete-old-model-version"></a>3. (Opcional) Retire o elimine la versión anterior del modelo

Si no va a utilizar más la versión del modelo antiguo, puede [desconectar](#decommissioning) el modelo antiguo. Esta acción permite que el modelo siga existiendo en la instancia, pero no se puede usar para crear nuevos gemelos digitales.

También puede [eliminar](#deletion) el modelo antiguo por completo si ya no lo quiere en la instancia.

Las secciones vinculadas anteriormente contienen código de ejemplo y consideraciones para retirar y eliminar modelos.

### <a name="strategy-2-delete-old-model-and-reupload"></a>Estrategia 2: Eliminar el modelo antiguo y volver a cargar

En lugar de incrementar la versión de un modelo, puede eliminar un modelo por completo y volver a cargar un modelo editado en la instancia.

Azure Digital Twins no recuerda que el modelo anterior se haya cargado alguna vez, por lo que esta acción será como cargar un modelo completamente nuevo. Los gemelos del gráfico que usan el modelo cambiarán automáticamente a la nueva definición una vez que esté disponible. En función de cómo difiere la nueva definición de la anterior, estos gemelos pueden tener propiedades y relaciones que coincidan con la definición eliminada y no sean válidas con la nueva, por lo que es posible que tenga que aplicarles revisiones para asegurarse de que siguen siendo válidos.

Para usar esta estrategia, siga estos pasos:

### <a name="1-delete-old-model"></a>1. Elimine el modelo anterior

Puesto que Azure Digital Twins no permite dos modelos con el mismo id., empiece por eliminar el modelo original de la instancia. 

>[!NOTE]
> Si tiene otros modelos que dependen de este modelo (mediante herencia o componentes), deberá quitar esas referencias antes de poder eliminar el modelo. Puede actualizar esos modelos dependientes primero para quitar temporalmente las referencias o eliminar los modelos dependientes y volver a cargarlos en un paso posterior.

Use las instrucciones siguientes para [eliminar su modelo original](#deletion). Esta acción dejará a los gemelos que usaban ese modelo temporalmente "huérfanos", ya que ahora usan un modelo que ya no existe. Este estado se reparará en el paso siguiente al volver a cargar el modelo actualizado.

### <a name="2-create-and-upload-new-model"></a>2. Cree y cargue un nuevo modelo

Comience con el DTDL del modelo original. Actualice, agregue o quite los campos que quiera cambiar.

A continuación, [cargue el modelo](#upload-models) a la instancia, como si fuera un nuevo modelo que se carga por primera vez.

### <a name="3-update-graph-elements-as-needed"></a>3. Actualice los elementos del grafo según sea necesario

Ahora que el nuevo modelo se ha cargado en lugar del anterior, los gemelos del grafo comenzarán automáticamente a usar la nueva definición de modelo una vez que expire y se restablezca el almacenamiento en caché en la instancia. **Este proceso puede tardar entre 10 y 15** minutos o más, dependiendo del tamaño del gráfico. Después de eso, las propiedades nuevas y modificadas del modelo deben ser accesibles y las propiedades quitadas ya no serán accesibles.

>[!NOTE]
> Si quitó otros modelos dependientes anteriormente para eliminar el modelo original, vuelva a cargarlos ahora después de restablecer la memoria caché. Si actualizó los modelos dependientes para quitar temporalmente las referencias al modelo original, puede actualizarlos de nuevo para volver a colocar la referencia.

A continuación, actualice **los gemelos y las relaciones** de la instancia para que sus propiedades coincidan con las propiedades definidas por el nuevo modelo. Antes de completar este paso, se pueden leer los gemelos que no coinciden con su modelo, pero no se pueden escribir en ellos. Para obtener más información sobre el estado de los gemelos sin un modelo válido, consulte [Gemelos sin modelos](#after-deletion-twins-without-models).

Hay dos maneras de actualizar gemelos y relaciones para el nuevo modelo para que se puedan escribir de nuevo:
* Aplicar revisiones a los gemelos y las relaciones según sea necesario para que se ajusten al nuevo modelo. Puede utilizar las siguientes instrucciones para [actualizar gemelos](how-to-manage-twin.md#update-a-digital-twin) y [actualizar relaciones](how-to-manage-graph.md#update-relationships).
    - **Si ha agregado propiedades**: no es necesario actualizar los gemelos y las relaciones para que tengan los nuevos valores, ya que los gemelos que no tienen los nuevos valores seguirán siendo gemelos válidos. Puede aplicarles revisiones, pero deberá agregar valores para las nuevas propiedades.
    - **Si ha quitado propiedades**: es necesario aplicar revisiones a los gemelos para quitar las propiedades que ahora no son válidas con el nuevo modelo.
    - **Si ha actualizado propiedades**: es necesario aplicar revisiones a los gemelos para actualizar los valores de las propiedades modificadas para que sean válidos con el nuevo modelo.
* Elimine los gemelos y las relaciones que usan el modelo y vuelva a crearlos. Puede utilizar las siguientes instrucciones para [eliminar gemelos](how-to-manage-twin.md#delete-a-digital-twin) y [volver a crear gemelos](how-to-manage-twin.md#create-a-digital-twin), y para [borrar relaciones](how-to-manage-graph.md#delete-relationships) y [volver a crear relaciones](how-to-manage-graph.md#create-relationships).
    - Es posible que quiera realizar esta operación si está realizando muchos cambios en el modelo y será difícil actualizar los gemelos existentes para que coincidan con él. Sin embargo, volver a crearlos puede ser complicado si tiene muchos gemelos interconectados por muchas relaciones.

## <a name="remove-models"></a>Eliminación de modelos

Los modelos también se pueden quitar del servicio de alguna de estas dos maneras:
* **Retirada**: una vez que se retira un modelo, ya no se puede usar para crear instancias de Digital Twins. Las instancias existentes de Digital Twins que ya usan este modelo no se ven afectadas, por lo que todavía se pueden actualizar con elementos como cambios de propiedades y con la adición o eliminación de relaciones.
* **Eliminación**: esta operación eliminará el modelo definitivamente de la solución. Todas las instancias de Digital Twins que utilicen este modelo dejarán de estar asociadas con cualquier modelo válido, por lo que se tratarán como si no tuvieran ningún modelo. Todavía puede leer estas instancias de Digital Twins, pero no puede actualizarlas hasta que se reasignen a un modelo diferente.

Estas operaciones son características independientes que no se ven afectadas entre sí, aunque se pueden usar juntas para quitar un modelo gradualmente. 

### <a name="decommissioning"></a>Retirada

Para retirar un modelo, puede utilizar el método [DecommissionModel](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.decommissionmodel?view=azure-dotnet&preserve-view=true) del SDK:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

También puede retirar un modelo mediante la llamada API REST [DigitalTwinModels Update](/rest/api/digital-twins/dataplane/models/digitaltwinmodels_update). La propiedad `decommissioned` es la única propiedad que se puede reemplazar por esta llamada API. El documento de revisión JSON tendrá un aspecto parecido al siguiente:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-decommission-model.json":::

El estado de retirada de un modelo se incluye en los registros `ModelData` devueltos por las API de recuperación del modelo.

### <a name="deletion"></a>Eliminación

Puede eliminar todos los modelos de la instancia a la vez o de manera individual.

Para obtener un ejemplo de cómo eliminar todos los modelos al mismo tiempo, consulte el repositorio [Ejemplos de un extremo a otro para Azure Digital Twins](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/CommandLoop.cs) en GitHub. El archivo *CommandLoop.cs* contiene una función `CommandDeleteAllModels` con código para eliminar todos los modelos de la instancia.

Para eliminar un modelo individual, siga las instrucciones y consideraciones del resto de esta sección.

#### <a name="before-deletion-deletion-requirements"></a>Antes de la eliminación: Requisitos para la eliminación

Por lo general, los modelos se pueden eliminar en cualquier momento.

La excepción son los modelos de los que dependen otros modelos, ya sea con una relación `extends` o como componente. Por ejemplo, si un modelo ConferenceRoom extiende un modelo Room y tiene un modelo ACUnit como componente, no puede eliminar Room ni ACUnit hasta que ConferenceRoom elimina dichas referencias correspondientes. 

Para ello, puede actualizar el modelo dependiente para quitar las dependencias o eliminarlo por completo.

#### <a name="during-deletion-deletion-process"></a>Durante la eliminación: Proceso de eliminación

Incluso si un modelo cumple los requisitos para eliminarlo inmediatamente, es posible que quiera seguir unos cuantos pasos para evitar consecuencias no deseadas en las instancias de Digital Twins dependientes. Estos son algunos de los pasos que pueden facilitar la administración del proceso:
1. En primer lugar, retire el modelo.
2. Espere unos minutos para asegurarse de que el servicio ha procesado las solicitudes de creación gemelas de último minuto enviadas antes de la retirada.
3. Consulte las instancias de Digital Twins por modelo para ver todas las instancias de Digital Twins que usan el modelo que ya se ha retirado.
4. Elimine las instancias de Digital Twins si ya no las necesita o aplíqueles una revisión en un nuevo modelo, si es necesario. También puede optar por dejarlas como independientes, en cuyo caso se convierten en instancias de Digital Twins sin modelos una vez que se elimine el modelo. Vea la sección siguiente para conocer las implicaciones de este estado.
5. Espere unos minutos para asegurarse de que los cambios se han aplicado.
6. Eliminación del modelo 

Para eliminar un modelo, puede usar la llamada al SDK [deleteModel:](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.deletemodel?view=azure-dotnet&preserve-view=true).

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

También puede eliminar un modelo con la llamada API REST [DigitalTwinModels Delete](/rest/api/digital-twins/dataplane/models/digitaltwinmodels_delete).

#### <a name="after-deletion-twins-without-models"></a>Después de la eliminación: Instancias de Digital Twins sin modelos

Una vez eliminado un modelo, ahora se considerará que todas las instancias de Digital Twins que usaban dicho modelo ya no tendrán ningún modelo. No hay ninguna consulta que pueda proporcionar una lista de todas las instancias de Digital Twins con este estado, aunque *puede* seguir consultando las instancias de Digital Twins del modelo eliminado para saber qué instancias se ven afectadas.

A continuación, se ofrece información general sobre lo que puede y no puede hacer con instancias de Digital Twins que no tienen ningún modelo.

Cosas que **puede** hacer:
* Consulta de la instancia de Digital Twins.
* Lectura de las propiedades
* Lectura de las relaciones salientes
* Adición y eliminación de las relaciones entrantes (de la misma forma, otras instancias de Digital Twins todavía pueden formar relaciones *con* esta instancia de Digital Twins)
  - El atributo `target` de la definición de la relación todavía puede reflejar el DTMI del modelo eliminado. Una relación sin destino definido también puede funcionar aquí.      
* Eliminar relaciones
* Eliminación de la instancia de Digital Twins

Cosas que **no puede** hacer:
* Editar las relaciones de salida (de la misma forma, las relaciones *de* esta instancia de Digital Twins con otras)
* Modificar propiedades

#### <a name="after-deletion-reuploading-a-model"></a>Después de la eliminación: volver a cargar un modelo

Una vez eliminado un modelo, puede decidir más tarde si cargar un nuevo modelo con el mismo identificador que el que ha eliminado. Esto es lo que sucede en ese caso.
* Desde la perspectiva del almacén de soluciones, esta operación equivale a cargar un modelo completamente nuevo. El servicio no recuerda si el anterior se cargó alguna vez.   
* Si hay otras instancias de Digital Twins en el grafo que hacen referencia al modelo eliminado, dejarán de estar huérfanas; este id. de modelo vuelve a ser válido con la nueva definición. Sin embargo, si la nueva definición del modelo es diferente de la definición del modelo que se eliminó, estas instancias de Digital Twins pueden tener propiedades y relaciones que coincidan con la definición eliminada y que no sean válidas con la nueva.

Azure Digital Twins no impide este estado, por lo que debe asegurarse de revisar detenidamente las instancias de Digital Twins para comprobar que siguen siendo válidas para el cambio de la definición del modelo.

## <a name="next-steps"></a>Pasos siguientes

Vea cómo crear y administrar instancias de Digital Twins basadas en los modelos:
* [Administración de Digital Twins](how-to-manage-twin.md)