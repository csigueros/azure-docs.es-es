---
title: Transformación con Azure Databricks
description: Aprenda a usar una plantilla de solución para transformar datos mediante un cuaderno de Databricks en Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: dc01ba10922901e5c3558e26b0f2ab2718ea34e3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743589"
---
# <a name="transformation-with-azure-databricks"></a>Transformación con Azure Databricks

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En este tutorial, creará una canalización de un extremo a otro que contenga las actividades **Validation** (Validación), **Copy data** (Copiar datos) y **Notebook** (Cuaderno) en Azure Data Factory.

- **Validation**: garantiza que el conjunto de datos de origen está listo para el consumo de bajada antes de desencadenar el trabajo de copia y análisis.

- **Copy Data**: duplica el conjunto de datos de origen en el almacenamiento receptor, que se monta como DBFS en el cuaderno de Azure Databricks. De esta manera, Spark puede consumirlo directamente.

- **Notebook**: desencadena el cuaderno de Databricks que transforma el conjunto de datos. También agrega el conjunto de datos a una carpeta procesada o a Azure Synapse Analytics.

Por motivos de simplicidad, la plantilla de este tutorial no crea un desencadenador programado. Puede agregar uno si es necesario.

:::image type="content" source="media/solution-template-Databricks-notebook/pipeline-example.png" alt-text="Diagrama de la canalización":::

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta de Azure Blob Storage con un contenedor llamado `sinkdata` para su uso como receptor.

  Anote el nombre de la cuenta de almacenamiento, el nombre del contenedor y la clave de acceso. Los necesitará más adelante en la plantilla.

- Un área de trabajo de Azure Databricks.

## <a name="import-a-notebook-for-transformation"></a>Importación de un cuaderno para Transformation (Transformación)

Para importar un cuaderno de **Transformation** al área de trabajo de Databricks:

1. Inicie sesión en el área de trabajo de Azure Databricks y, a continuación, seleccione **Importar**.
       :::image type="content" source="media/solution-template-Databricks-notebook/import-notebook.png" alt-text="Comando de menú para importar un área de trabajo":::
   La ruta de acceso al área de trabajo puede ser diferente de la que se muestra, pero recuérdela más adelante.
1. Seleccione **Importar desde: URL**. En el cuadro de texto, escriba `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`.

   :::image type="content" source="media/solution-template-Databricks-notebook/import-from-url.png" alt-text="Selecciones para importar un cuaderno":::

1. Ahora vamos a actualizar el cuaderno de **Transformation** con la información de la conexión de almacenamiento.

   En el cuaderno importado, vaya a **command 5** (comando 5), como se muestra en el siguiente fragmento de código.

   - Reemplace `<storage name>` y `<access key>` por su propia información de conexión de almacenamiento.
   - Use la cuenta de almacenamiento con el contenedor `sinkdata`.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Genere un **token de acceso de Databricks** para Data Factory para acceder a Databricks.
   1. En el área de trabajo de Databricks, seleccione el icono de su perfil de usuario en la esquina superior derecha.
   1. Seleccione **Configuración de usuario**.
    :::image type="content" source="media/solution-template-Databricks-notebook/user-setting.png" alt-text="Comando de menú para la configuración del usuario":::
   1. Seleccione **Generar nuevo token** en la pestaña **Tokens de acceso**.
   1. Seleccione **Generar**.

    :::image type="content" source="media/solution-template-Databricks-notebook/generate-new-token.png" alt-text="Botón &quot;Generate&quot;":::

   *Guarde el de token de acceso* para usarlo más adelante en la creación de un servicio vinculado de Databricks. El token de acceso será similar a `dapi32db32cbb4w6eee18b7d87e45exxxxxx`.

## <a name="how-to-use-this-template"></a>Uso de esta plantilla

1. Vaya a la plantilla **Transformation with Azure Databricks** (Transformación con Azure Databricks) y cree nuevos servicios vinculados para las siguientes conexiones.

   :::image type="content" source="media/solution-template-Databricks-notebook/connections-preview.png" alt-text="Configuración de conexiones":::

    - **Conexión al blob de origen**: para acceder a los datos de origen.

       Puede usar el almacenamiento de blobs público, que contiene los archivos de origen para este ejercicio. Tome como referencia la siguiente captura de pantalla para la configuración. Use la siguiente **dirección URL de SAS** para conectarse al almacenamiento de origen (acceso de solo lectura):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        :::image type="content" source="media/solution-template-Databricks-notebook/source-blob-connection.png" alt-text="Selecciones para el método de autenticación y la dirección URL de SAS":::

    - **Conexión al blob de destino**: para almacenar los datos copiados.

       En la ventana **Nuevo servicio vinculado**, seleccione su blob de almacenamiento receptor.

       :::image type="content" source="media/solution-template-Databricks-notebook/destination-blob-connection.png" alt-text="Blob de almacenamiento receptor como un nuevo servicio vinculado":::

    - **Azure Databricks**: para conectarse al clúster de Databricks.

        Cree un servicio vinculado de Databricks con la clave de acceso que ha generado anteriormente. Puede seleccionar un *clúster interactivo* si tiene uno. En este ejemplo se usa la opción **New job cluster** (Nuevo clúster de trabajo).

        :::image type="content" source="media/solution-template-Databricks-notebook/databricks-connection.png" alt-text="Selecciones para conectarse al clúster":::

1. Seleccione **Usar esta plantilla**. Verá una canalización creada.

    :::image type="content" source="media/solution-template-Databricks-notebook/new-pipeline.png" alt-text="Crear una canalización":::

## <a name="pipeline-introduction-and-configuration"></a>Introducción y configuración de canalizaciones

En la nueva canalización, la mayoría de las opciones se han configurado automáticamente con los valores predeterminados. Revise las configuraciones de la canalización y realice los cambios necesarios.

1. En la actividad **Validation** llamada **Availability flag** (Marca de disponibilidad), compruebe que el valor del **conjunto de datos** de origen sea el conjunto `SourceAvailabilityDataset` que ha creado anteriormente.

   :::image type="content" source="media/solution-template-Databricks-notebook/validation-settings.png" alt-text="Valor del conjunto de datos de origen":::

1. En la actividad **Copy data** llamada **file-to-blob** (de archivo a blob), compruebe las pestañas **Source** (Origen) y **Sink** (Receptor). Cambie la configuración si es necesario.

   - Pestaña **Source** (Origen) Pestaña :::image type="content" source="media/solution-template-Databricks-notebook/copy-source-settings.png" alt-text="Source":::

   - Pestaña **Sink** (Receptor) Pestaña :::image type="content" source="media/solution-template-Databricks-notebook/copy-sink-settings.png" alt-text="Sink":::

1. En la actividad **Notebook** llamada **Transformation**, revise y actualice las rutas de acceso y la configuración según sea necesario.

   El campo **Databricks linked service** (Servicio vinculado de Databricks) debería estar rellenado previamente con el valor de un paso anterior, como se muestra a continuación: :::image type="content" source="media/solution-template-Databricks-notebook/notebook-activity.png" alt-text="Valor rellenado para el servicio vinculado de Databricks":::

   Para comprobar la configuración de **Notebook**:
  
    1. Seleccione la pestaña **Configuración**. En **Notebook path** (Ruta de acceso del cuaderno), compruebe que la ruta de acceso predeterminada es correcta. Es posible que necesite examinar y elegir la ruta de acceso correcta del cuaderno.

       :::image type="content" source="media/solution-template-Databricks-notebook/notebook-settings.png" alt-text="Ruta de acceso del cuaderno":::

    1. Expanda el selector **Base Parameters** (Parámetros base) y compruebe que los parámetros coinciden con lo que se muestra en la siguiente captura de pantalla. Estos parámetros se pasan al cuaderno de Databricks desde Data Factory.

       :::image type="content" source="media/solution-template-Databricks-notebook/base-parameters.png" alt-text="Parámetros base":::

1. Compruebe que los **parámetros de canalización** coinciden con los que aparecen en la siguiente captura de pantalla: :::image type="content" source="media/solution-template-Databricks-notebook/pipeline-parameters.png" alt-text="Parámetros de canalización":::

1. Conéctese a sus conjuntos de datos.

    >[!NOTE]
    >En los conjuntos de archivos de siguientes, la ruta de acceso del archivo se ha especificado automáticamente en la plantilla. Si se requiere algún cambio, asegúrese de especificar la ruta de acceso para el **contenedor** y el **directorio** en caso de error de conexión.

   - **SourceAvailabilityDataset**: para comprobar si los datos de origen están disponibles.

     :::image type="content" source="media/solution-template-Databricks-notebook/source-availability-dataset.png" alt-text="Selecciones para el servicio vinculado y la ruta de acceso al archivo de SourceAvailabilityDataset":::

   - **SourceFilesDataset**: para acceder a los datos de origen.

       :::image type="content" source="media/solution-template-Databricks-notebook/source-file-dataset.png" alt-text="Selecciones para el servicio vinculado y la ruta de acceso al archivo de SourceFilesDataset":::

   - **DestinationFilesDataset**: para copiar los datos en la ubicación de destinos del receptor. Use los valores siguientes:

     - **Linked service** -  (Servicio vinculado): `sinkBlob_LS`, creado en un paso anterior.

     - **File path** -  (Ruta de acceso del archivo): `sinkdata/staged_sink`.

       :::image type="content" source="media/solution-template-Databricks-notebook/destination-dataset.png" alt-text="Selecciones para el servicio vinculado y la ruta de acceso al archivo de DestinationFilesDataset":::

1. Seleccione **Debug** (Depurar) para ejecutar la canalización. Puede consultar el vínculo a los registros de Databricks si quiere obtener registros de Spark más detallados.

    :::image type="content" source="media/solution-template-Databricks-notebook/pipeline-run-output.png" alt-text="Vínculo a los registros de Databricks desde la salida":::

    También puede comprobar el archivo de datos mediante el Explorador de Azure Storage.

    > [!NOTE]
    > Para establecer una correlación con ejecuciones de canalización de Data Factory, en este ejemplo se agrega el identificador de ejecución de la canalización de la factoría de datos a la carpeta de salida. Esto ayuda a hacer un seguimiento de los archivos generados por cada ejecución.
    > :::image type="content" source="media/solution-template-Databricks-notebook/verify-data-files.png" alt-text="Identificador de ejecución de la canalización anexado":::

## <a name="next-steps"></a>Pasos siguientes

- [Introducción al servicio Azure Data Factory](introduction.md)
