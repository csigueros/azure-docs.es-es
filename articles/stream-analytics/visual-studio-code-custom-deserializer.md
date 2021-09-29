---
title: Creación de deserializadores de .NET personalizados para trabajos en la nube de Azure Stream Analytics mediante Visual Studio Code
description: En este tutorial se muestra cómo crear un deserializador de .NET personalizado para un trabajo en la nube de Azure Stream Analytics mediante Visual Studio Code.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 6d5787268cd5f7d25b54f8c7bc63f59c20a8495b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606995"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>Creación de deserializadores de .NET personalizados para Azure Stream Analytics en Visual Studio Code

Azure Stream Analytics tiene [compatibilidad integrada con tres formatos de datos](stream-analytics-parsing-json.md): JSON, CSV y Avro. Con los deserializadores de .NET personalizados, puede leer datos de otros formatos, como el [búfer de protocolo](https://developers.google.com/protocol-buffers/), [Bond](https://github.com/Microsoft/bond) y otros definidos por el usuario para trabajos en la nube.

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Deserializadores de .NET personalizados en Visual Studio Code

Puede crear, probar y depurar un deserializador de .NET personalizado para un trabajo en la nube de Azure Stream Analytics mediante Visual Studio Code.

### <a name="prerequisites"></a>Requisitos previos

* Instale el [SDK de .NET Core](https://dotnet.microsoft.com/download) y reinicie Visual Studio Code.

* Use este [inicio rápido](quick-create-visual-studio-code.md) para aprender a crear un trabajo de Stream Analytics mediante Visual Studio Code.

### <a name="create-a-custom-deserializer"></a>Creación de un deserializador personalizado

1. Abra un terminal y ejecute el siguiente comando para crear una biblioteca de clases de .NET en Visual Studio Code para el deserializador personalizado llamado **ProtobufDeserializer**.

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. Vaya al directorio del proyecto **ProtobufDeserializer** e instale los paquetes NuGet [Microsoft.Azure.StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) y [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/).

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. Agregue la clase [MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) y la clase [MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) al proyecto.

4. Compile el proyecto **ProtobufDeserializer**.

### <a name="add-an-azure-stream-analytics-project"></a>Incorporación de un proyecto de Azure Stream Analytics

1. Abra Visual Studio Code y seleccione **Ctrl+Mayús+P** para abrir la paleta de comandos. A continuación, escriba ASA y seleccione **ASA: Crear nuevo proyecto**. Asígnele el nombre **ProtobufCloudDeserializer**.

### <a name="configure-a-stream-analytics-job"></a>Configuración de un trabajo de Stream Analytics

1. Haga doble clic en **JobConfig.json**. Use las configuraciones predeterminadas, excepto las siguientes:

   |Configuración|Valor sugerido|
   |-------|---------------|
   |Recurso de configuración de almacenamiento global|Selección del origen de datos desde la cuenta actual|
   |Suscripción de configuración de almacenamiento global| < su suscripción >|
   |Cuenta de almacenamiento de la configuración de almacenamiento global| < su cuenta de almacenamiento >|
   |Cuenta de almacenamiento de la configuración CustomCodeStorage|< su cuenta de almacenamiento >|
   |Contenedor de configuración CustomCodeStorage|< su contenedor de almacenamiento >|

2. En la carpeta **Entradas**, abra **input.json**. Seleccione **Add live input** (Agregar entrada en directo), agregue una entrada desde Azure Data Lake Storage Gen2/Blob Storage y elija **Seleccionar en las suscripciones de Azure**. Use las configuraciones predeterminadas, excepto las siguientes:

   |Configuración|Valor sugerido|
   |-------|---------------|
   |Nombre|Entrada|
   |Suscripción|< su suscripción >|
   |Cuenta de almacenamiento|< su cuenta de almacenamiento >|
   |Contenedor|< su contenedor de almacenamiento >|
   |Tipo de serialización|Elija **Personalizado**|
   |SerializationProjectPath|Seleccione **Choose library project path** (Selección de la ruta de acceso de proyecto de biblioteca) de CodeLens y seleccione el proyecto de biblioteca **ProtobufDeserializer** creado en la sección anterior. Seleccione **compilar proyecto** para compilar el proyecto|
   |SerializationClassName|Seleccione **select deserialization class** (seleccionar clase de deserialización) de CodeLens para rellenar automáticamente el nombre de clase y la ruta de acceso del archivo DLL|
   |Class Name (Nombre de clase)|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="Agregue una entrada de deserializador personalizada.":::

3. Agregue la siguiente consulta al archivo **ProtobufCloudDeserializer.asaql**.

   ```sql
   SELECT * FROM Input
   ```

4. Descargue el [archivo de entrada de ejemplo protobuf](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). En la carpeta **Entradas**, haga clic con el botón derecho en **input.json** y seleccione **Agregar entrada local**. A continuación, haga doble clic en **local_input1.json** y use las configuraciones predeterminadas, excepto las siguientes.

   |Configuración|Valor sugerido|
   |-------|---------------|
   |Selección de la ruta de acceso de archivo local|Haga clic en CodeLens para seleccionar < La ruta de acceso del archivo de entrada de protobuf de ejemplo descargado>|

### <a name="execute-the-stream-analytics-job"></a>Ejecución del trabajo de Stream Analytics

1. Abra **ProtobufCloudDeserializer.asaql**, seleccione **Run Locally** (Ejecutar localmente) de CodeLens y, a continuación, elija **Use Local Input** (Usar entrada local) en la lista desplegable.

2. Observe los resultados en la pestaña **Resultados** en la vista de diagrama de trabajo de la derecha. También puede hacer clic en los pasos del diagrama de trabajo para ver el resultado intermedio. Para obtener más detalles, consulte el artículo sobre cómo [depurar localmente mediante el diagrama de trabajo](debug-locally-using-job-diagram-vs-code.md).

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="Compruebe el resultado de ejecución local.":::

Ha implementado correctamente un deserializador personalizado para el trabajo de Stream Analytics. En este tutorial, ha probado el deserializador personalizado localmente con datos de entrada locales. También puede probarlo [mediante la entrada de datos en directo en la nube](visual-studio-code-local-run-live-input.md). Para ejecutar el trabajo en la nube, configuraría correctamente la entrada y la salida. Después, envíe el trabajo a Azure desde Visual Studio Code para ejecutar el trabajo en la nube con el deserializador personalizado recién implementado.

### <a name="debug-your-deserializer"></a>Depuración del deserializador

Puede depurar el deserializador de .NET localmente de la misma manera que el código de .NET estándar. 

1. Agregue puntos de interrupción a la función de .NET.

2. Haga clic en **Ejecutar** desde la barra de actividad de Visual Studio Code y seleccione **crear un archivo launch.json**.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="Cree el archivo de inicio.":::

   Elija **ProtobufCloudDeserializer** y, a continuación, **Azure Stream Analytics** en la lista desplegable.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="Cree el archivo de inicio 2.":::

   Edite el archivo **launch.json** para reemplazar \<ASAScript\>.asaql por ProtobufCloudDeserializer.asaql.
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="Configure el archivo de inicio.":::

3. Pulse **F5** para iniciar la depuración. El programa se detendrá en los puntos de interrupción según lo previsto. Esto funciona para los datos de entrada local y de entrada en directo.

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="Depure el deserializador personalizado.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando no los necesite, elimine el grupo de recursos, el trabajo de streaming y todos los recursos relacionados. La eliminación del trabajo evita la facturación de las unidades de streaming utilizadas por el trabajo. Si piensa utilizar el trabajo en el futuro, puede detenerlo y volver a iniciarlo más adelante cuando sea necesario. Si no va a seguir usando este trabajo, siga estos pasos para eliminar todos los recursos creados en este tutorial:

1. En el menú de la izquierda de Azure Portal, seleccione **Grupos de recursos** y luego el nombre del recurso que creó.  

2. En la página del grupo de recursos, seleccione **Eliminar**, escriba el nombre del recurso que quiere eliminar en el cuadro de texto y, luego, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a implementar un deserializador de .NET personalizado para la serialización de entrada del búfer de protocolo. Para más información sobre la creación de deserializadores personalizados, continúe con el siguiente artículo:

> [!div class="nextstepaction"]
> * [Creación de deserializadores de .NET personalizados para trabajos de Azure Stream Analytics](custom-deserializer-examples.md)
> * [Prueba de los trabajos de Azure Stream Analytics localmente con una entrada activa mediante Visual Studio Code](visual-studio-code-local-run-live-input.md)