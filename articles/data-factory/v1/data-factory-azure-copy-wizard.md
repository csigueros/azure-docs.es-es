---
title: Asistente para copia de Azure Data Factory
description: Obtenga información sobre cómo utilizar el Asistente para copia de Azure Data Factory para copiar datos de orígenes de datos admitidos en receptores.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 82ad8dbec999b1e65eb5b9d866eacaf95c8647d2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649594"
---
# <a name="azure-data-factory-copy-wizard"></a>Asistente para copia de Azure Data Factory

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. 

El Asistente para copia de Azure Data Factory simplifica el proceso de ingesta de datos, que normalmente es el primer paso en un escenario de integración de datos de un extremo a otro. Para aprender el uso del Asistente para copia de Azure Data Factory no es preciso conocer las definiciones de JSON de servicios vinculados, conjuntos de datos y canalizaciones. El asistente crea automáticamente una canalización para copiar datos del origen de datos seleccionado al destino seleccionado. Además, el Asistente para copia le ayuda a validar los datos que se ingieren en el momento de la creación. Esto le permite ahorrar tiempo, especialmente cuando ingiere datos por primera vez desde el origen de datos. Si quiere iniciar el Asistente para copia, haga clic en el icono **Copiar datos** de la página principal de Data Factory.

:::image type="content" source="./media/data-factory-copy-wizard/copy-data-wizard.png" alt-text="Asistente para copia":::

## <a name="designed-for-big-data"></a>Diseñado para macrodatos
Este asistente permite mover fácilmente los datos de una gran variedad de orígenes a distintos destinos en cuestión de minutos. Después de pasar por el asistente, se crea automáticamente una canalización con una actividad de copia, junto con las entidades de Data Factory dependientes (servicios vinculados y conjuntos de datos). No se requieren más pasos para crear la canalización.   

:::image type="content" source="./media/data-factory-copy-wizard/select-data-source-page.png" alt-text="Selección de origen de datos":::

> [!NOTE]
> Para ver instrucciones detalladas para crear una canalización de ejemplo para copiar datos desde un blob de Azure a una tabla de una base de datos de Azure SQL, consulte el [tutorial del Asistente para copia](data-factory-copy-data-wizard-tutorial.md).

El asistente está diseñado pensando en los macrodatos desde el principio, con compatibilidad para diversos datos y tipos de objeto. Puede crear canalizaciones de Data Factory que trasladen cientos de carpetas, archivos o tablas. El asistente admite vista previa de datos automática, captura y asignación de esquema, y filtrado de datos.

## <a name="automatic-data-preview"></a>Vista previa de datos automática
Puede obtener una vista previa de la parte de los datos del origen de datos seleccionado para validar si los datos son los que desea copiar. Además, si los datos de origen están en un archivo de texto, el Asistente para copia analiza dicho archivo para obtener más información sobre el esquema y los delimitadores de columna y fila automáticamente.

:::image type="content" source="./media/data-factory-copy-wizard/file-format-settings.png" alt-text="Configuración del formato de archivo":::

## <a name="schema-capture-and-mapping"></a>Captura y asignación de esquema
En algunos casos, es posible que el esquema de datos de entrada no coincida con el esquema de datos de salida. En este escenario, es preciso asignar columnas del esquema de origen a columnas del esquema de destino.

> [!TIP]
> Al copiar datos desde SQL Server o Azure SQL Database en Azure Synapse Analytics, si la tabla no se encuentra en el almacén de destino, Data Factory podrá crear la tabla automáticamente mediante el esquema del origen. Obtenga más información en [Movimiento de datos hacia y desde Azure Synapse Analytics mediante Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).

Use una lista desplegable para seleccionar una columna del esquema de origen para asignar a una columna en el esquema de destino. El Asistente para copia intenta entender el patrón para la asignación de columnas. Se aplica el mismo patrón para el resto de las columnas, por lo que no debe seleccionar cada una de las columnas individualmente para completar la asignación de esquema. Si lo prefiere, puede invalidar estas asignaciones mediante el uso de las listas desplegables para asignar las columnas una por una. El patrón se vuelve más preciso a medida que asigna más columnas. El Asistente para copia actualiza el patrón constantemente y, en última instancia, alcanza el patrón correcto para la asignación de columnas que desea conseguir.     

:::image type="content" source="./media/data-factory-copy-wizard/schema-mapping.png" alt-text="Asignación de esquemas":::

## <a name="filtering-data"></a>Filtrado de datos
Puede filtrar los datos de origen para seleccionar solo aquellos que deben copiarse en el almacén de datos receptor. El filtrado reduce el volumen de los datos se copian en el almacén de datos del receptor, por lo que mejora el rendimiento de la operación de copia. Proporciona una manera flexible de filtrar los datos de una base de datos relacional mediante el lenguaje de consulta SQL, o archivos en una carpeta de blobs de Azure mediante [Funciones y variables de Data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrado de datos de una base de datos
La siguiente captura de pantalla muestra una consulta SQL con la función `Text.Format` y la variable `WindowStart`.

:::image type="content" source="./media/data-factory-copy-wizard/validate-expressions.png" alt-text="Validación de expresiones":::

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrar datos en una carpeta de blobs de Azure
Puede usar variables en la ruta de acceso de la carpeta para copiar datos desde una carpeta que se determina en el runtime según las [variables del sistema](data-factory-functions-variables.md#data-factory-system-variables). Estas son las variables que se admiten: **{year}** , **{month}** , **{day}** , **{hour}** , **{minute}** y **{custom}** . Por ejemplo: carpetaDeEntrada/{year}/{month}/{day}.

Supongamos que tiene carpetas de entrada con el siguiente formato:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Haga clic en el botón **Examinar** de **Archivo o carpeta**, vaya a una de estas carpetas (por ejemplo, 2016->03->01->02) y haga clic en **Elegir**. En el cuadro de texto, debería aparecer `2016/03/01/02`. Sustituya **2016** por **{year}** , **03** por **{month}** , **01** por **{day}** y **02** por **{hour}** y presione la tecla **Tabulación**. Aparecerán listas desplegables en las que podrá seleccionar el formato de estas cuatro variables:

:::image type="content" source="./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png" alt-text="Uso de variables del sistema":::   

Como se muestra en la siguiente captura de pantalla, también puede usar una variable **personalizada** y cualquier [cadena de formato admitida](/dotnet/standard/base-types/custom-date-and-time-format-strings). Para seleccionar una carpeta con esa estructura, utilice primero el botón **Examinar** . A continuación, reemplace un valor por **{custom}** y presione la tecla **Tabulación** para ver el cuadro de texto donde puede escribir la cadena de formato.     

:::image type="content" source="./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png" alt-text="Uso de la variable personalizada":::

## <a name="scheduling-options"></a>Opciones de programación
Puede ejecutar la operación de copia una vez o de forma periódica (cada hora, día, etc.). Estas opciones se pueden utilizar para los distintos conectores en los diferentes entornos, ya sean locales y en la nube, así como para la copia del escritorio local.

Una operación de copia única permite el movimiento de datos desde un origen a un destino solamente una vez. Se aplica a los datos de cualquier tamaño y en cualquier formato admitido. La copia programada permite copiar datos con una frecuencia prescrita. Puede utilizar las opciones avanzadas (como el reintento, el tiempo de espera y las alertas) para configurar la copia programada.

:::image type="content" source="./media/data-factory-copy-wizard/scheduling-properties.png" alt-text="Propiedades de programación":::

## <a name="troubleshooting"></a>Solución de problemas

En esta sección se exploran métodos comunes de solución de problemas del Asistente para copia en Azure Data Factory.

> [!NOTE] 
> Estas sugerencias para la solución de problemas se aplican al Asistente para copia de la versión 1 de Data Factory. En Data Factory v2, consulte la guía de solución de problemas en [Solución de problemas con de Azure Data Factory Studio](../data-factory-ux-troubleshoot-guide.md).

### <a name="error-code-unable-to-validate-in-copy-wizard"></a>Código de error: No se puede validar en el Asistente para copia

- **Síntomas**: en el primer paso del Asistente para copia, aparece el mensaje de advertencia "No se puede validar".
- **Causas**: Esto puede ocurrir si todas las cookies de terceros están deshabilitadas.
- **Solución:** 
    - Use Internet Explorer o Microsoft Edge.
    - Si usa el explorador Chrome, siga las instrucciones que se indican a continuación para agregar una excepción de cookies para *microsoftonline.com* y *windows.net*.
        1.  Abra el explorador Chrome.
        2.  Haga clic en la llave inglesa o en las tres líneas a la derecha (Personalizar y controlar Google Chrome).
        3.  Haga clic en **Configuración**.
        4.  Busque **Cookies** o vaya a **Privacidad** en Configuración avanzada.
        5.  Seleccione **Configuración de contenido**.    
        6.  Las cookies deben estar establecidas en **Permitir que se almacenen datos locales (recomendado)** .
        7.  Haga clic en **Administrar excepciones**. En **Patrón de nombre de host** escriba lo siguiente y asegúrese de que **Permitir** sea el comportamiento establecido.
            - login.microsoftonline.com
            - login.windows.net
        8.  Cierre el explorador y vuelva a abrirlo.
    - Si usa el explorador Firefox, siga las instrucciones que se indican a continuación para agregar excepciones de cookies.
        1. En el menú de Firefox, vaya a **Herramientas** > **Opciones**.
        2. En **Privacidad** > **Historial**, es posible que el valor actual sea **Usar una configuración personalizada para el historial**.
        3. En **Aceptar cookies de terceros**, es posible que el valor actual sea **Nunca**. En tal caso, debe hacer clic en **Excepciones** a la derecha para agregar los sitios siguientes.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Cierre el explorador y vuelva a abrirlo. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Código de error: No se puede abrir la página de inicio de sesión e introducir la contraseña

- **Síntomas**: El Asistente para copia le redirige a la página de inicio de sesión, pero la página no se muestra correctamente.
- **Causas**: Este problema puede producirse si ha cambiado el entorno de red de una red de oficina a una red doméstica. Hay algunas cachés en exploradores. 
- **Solución:** 
    1.  Cierre el explorador e inténtelo de nuevo. Si el problema persiste, vaya al paso siguiente.   
    2.  Si usa Internet Explorer, intente abrirla en modo privado (presione "Ctrl" + "Mayús" + "P"). Si usa Chrome, intente abrirla en modo de incógnito (presione "Ctrl" + "Mayús" + "N"). Si el problema persiste, vaya al paso siguiente. 
    3.  Pruebe con otro explorador. 


## <a name="next-steps"></a>Pasos siguientes
Si quiere ver un tutorial rápido sobre el uso del Asistente para copia de Data Factory a fin de crear una canalización con una actividad de copia, consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md).