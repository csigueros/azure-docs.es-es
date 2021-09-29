---
title: Copia de archivos nuevos y modificados según LastModifiedDate
description: Aprenda a usar una plantilla de solución para copiar archivos nuevos y cambiados por LastModifiedDate con Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 601dcaa3ea402f8f6b8b8c0664b8a47cfb5f4359
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743526"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Copia de archivos nuevos y cambiados por LastModifiedDate con Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En este artículo se describe una plantilla de solución que puede usar para copiar archivos nuevos y cambiados solo por LastModifiedDate desde un almacén basado en archivos en un almacén de destino. 

## <a name="about-this-solution-template"></a>Acerca de esta plantilla de solución

Esta plantilla selecciona primero los archivos nuevos y cambiados solo por sus atributos **LastModifiedDate** y, a continuación, copia esos archivos seleccionados desde el almacén de origen de datos en el almacén de destino de datos.

La plantilla contiene una actividad:
- **Copia** para copiar archivos nuevos y cambiados solo por LastModifiedDate desde un almacén de archivos en un almacén de destino.

La plantilla define seis parámetros:
-  *FolderPath_Source* es la ruta de acceso a la carpeta donde puede leer los archivos del almacén de origen. Debe reemplazar el valor predeterminado por su propia ruta de acceso a la carpeta.
-  *Directory_Source* es la ruta de acceso a la subcarpeta donde puede leer los archivos del almacén de origen. Debe reemplazar el valor predeterminado por su propia ruta de acceso a la subcarpeta.
-  *FolderPath_Destination* es la ruta de acceso a la carpeta donde desea copiar archivos en el almacén de destino. Debe reemplazar el valor predeterminado por su propia ruta de acceso a la carpeta.
-  *Directory_Destination* es la ruta de acceso a la subcarpeta donde quiere copiar archivos en el almacén de destino. Debe reemplazar el valor predeterminado por su propia ruta de acceso a la subcarpeta.
-  *LastModified_From* se usa para seleccionar los archivos cuyo atributo LastModifiedDate es posterior o igual a este valor datetime.  Para seleccionar solo los archivos nuevos, los cuales no se han copiado la última vez, este valor datetime puede ser la hora a la que se desencadenó la canalización la última vez. Puede reemplazar el valor predeterminado '2019-02-01T00:00:00Z' por su LastModifiedDate esperado en la zona horaria UTC. 
-  *LastModified_To* se usa para seleccionar los archivos cuyo atributo LastModifiedDate es anterior a este valor datetime. Para seleccionar solo los archivos nuevos, los cuales no se han copiado la última vez, este valor datetime puede ser la hora actual.  Puede reemplazar el valor predeterminado '2019-02-01T00:00:00Z' por su LastModifiedDate esperado en la zona horaria UTC. 

## <a name="how-to-use-this-solution-template"></a>Uso de esta plantilla de solución

1. Vaya a la plantilla **Copia de archivos nuevos por LastModifiedDate**. Cree una **nueva** conexión con el almacén de almacenamiento de origen. El almacén de almacenamiento de origen es desde donde se van a copiar los archivos.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png" alt-text="Creación de una nueva conexión con el origen":::
    
2. Cree una **nueva** conexión con el almacén de destino. El almacén de destino es donde se van a copiar los archivos. 

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png" alt-text="Creación de una nueva conexión con el destino":::

3. Seleccione **Usar esta plantilla**.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png" alt-text="Uso de esta plantilla":::
    
4. Verá la canalización disponible en el panel, como se muestra en el ejemplo siguiente:

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png" alt-text="Visualización de la canalización":::

5. Seleccione **Depurar**, escriba el valor de los **parámetros** y seleccione **Finalizar**.  En la imagen que se muestra a continuación, establecemos los parámetros de la siguiente manera.
   - **FolderPath_Source** = sourcefolder
   - **Directory_Source** = subfolder
   - **FolderPath_Destination** = destinationfolder
   - **Directory_Destination** = subfolder
   - **LastModified_From** =  2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    En el ejemplo se indica que los archivos que se han modificado por última vez en el intervalo de tiempo (**2019-02-01T00:00:00Z** y **2019-03-01T00:00:00Z**) se copiarán desde la ruta de acceso de origen **sourcefolder/subfolder** en una ruta de acceso de destino **destinationfolder/subfolder**.  Puede reemplazarlos por sus propios parámetros.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png" alt-text="Ejecución de la canalización":::

6. Revise el resultado. Verá solo los archivos modificados por última vez en el intervalo configurado. Este se ha copiado en el almacén de destino.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png" alt-text="Revisión del resultado":::
    
7. Ahora puede agregar un desencadenador de ventanas de saltos de tamaño constante para automatizar esta canalización para que siempre pueda copiar archivos nuevos y cambiados solo por LastModifiedDate de forma periódica.  Seleccione **Agregar desencadenador** y luego **Nuevo/Editar**.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png" alt-text="Captura de pantalla que resalta la opción de menú New/Edit (Nuevo/Editar) que aparece al seleccionar Add trigger (Agregar desencadenador).":::
    
8. En la ventana **Agregar desencadenadores**, seleccione **+ Nuevo**.

9. Seleccione **Ventana de saltos de tamaño constante** para el tipo de desencadenador, establezca **Cada 15 minutos** como periodicidad (puede cambiarla por cualquier intervalo de tiempo). Seleccione **Sí** en la casilla activada y, a continuación, seleccione **Aceptar**.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png" alt-text="Creación de un desencadenador"::: 
    
10. Establezca el valor de los **parámetros de ejecución de desencadenador** de la manera siguiente y seleccione **Finalizar**.
    - **FolderPath_Source** = **sourcefolder**.  Se puede reemplazar por su carpeta en el almacén de datos de origen.
    - **Directory_Source** = **subfolder**.  Se puede reemplazar por su subcarpeta en el almacén de datos de origen.
    - **FolderPath_Destination** = **destinationfolder**.  Se puede reemplazar por su carpeta en el almacén de datos de destino.
    - **Directory_Destination** = **subfolder**.  Se puede reemplazar por su subcarpeta en el almacén de datos de destino.
    - **LastModified_From** =   **\@trigger().outputs.windowStartTime**.  Es una variable del sistema del desencadenador que determina la hora a la que se desencadenó la canalización por última vez.
    - **LastModified_To** =  **\@trigger().outputs.windowEndTime**.  Es una variable del sistema del desencadenador que determina la hora a la que se desencadena la canalización esta vez.
    
    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png" alt-text="Parámetros de entrada":::
    
11. Seleccione **Publish All** (Publicar todo).
    
    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png" alt-text="Publicar todo":::

12. Cree nuevos archivos en su carpeta de origen del almacén de origen de datos.  Ahora espera a que la canalización se desencadene automáticamente y solo se copiarán los nuevos archivos en el almacén de destino.

13. Seleccione la pestaña **Supervisar** en el panel de navegación izquierdo y espere unos 15 minutos si la periodicidad del desencadenador se ha establecido en cada 15 minutos. 

14. Revise el resultado. Verá que su canalización se desencadenará automáticamente cada 15 minutos y solo se copiarán los archivos nuevos o cambiados del almacén de origen en el almacén de destino en cada ejecución de canalización.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png" alt-text="Captura de pantalla que muestra los resultados devueltos al desencadenar la canalización.":::
    
## <a name="next-steps"></a>Pasos siguientes

- [Introducción al servicio Azure Data Factory](introduction.md)
