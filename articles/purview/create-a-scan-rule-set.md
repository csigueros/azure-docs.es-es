---
title: Creación de un conjunto de reglas de examen
description: Cree un conjunto de reglas de examen en Azure Purview para examinar rápidamente los orígenes de datos de su organización.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 0b9175a2795ac926c7adf93dc81f84ff7b5a4472
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207389"
---
# <a name="create-a-scan-rule-set"></a>Creación de un conjunto de reglas de examen

En el catálogo de Azure Purview, puede crear conjuntos de reglas de examen para que pueda examinar rápidamente los orígenes de datos de su organización.

Un conjunto de reglas de examen es un contenedor para agrupar un conjunto de reglas de examen y que así pueda asociarlas fácilmente a un examen. Por ejemplo, puede crear un conjunto de reglas de examen predeterminado para cada uno de los tipos de orígenes de datos y, a continuación, usar estos conjuntos de reglas de examen de forma predeterminada en todos los exámenes de la empresa. También puede proporcionar a los usuarios los permisos adecuados para crear otros conjuntos de reglas de examen con diferentes configuraciones en función de las necesidades de la empresa.

## <a name="steps-to-create-a-scan-rule-set"></a>Pasos para crear un conjunto de reglas de examen

Para crear un conjunto de reglas de examen:

1. En Azure [Purview Studio](https://web.purview.azure.com/resource/), seleccione **Data Map** (Mapa de datos).

1. Seleccione **Scan rule sets** (Conjuntos de reglas de examen) en el panel izquierdo y, a continuación, seleccione **Nuevo**.

1. En la página **New scan rule set** (Nuevo conjunto de reglas de examen), seleccione los orígenes de datos que admite el examinador del catálogo en la lista desplegable **Tipo de origen**. Puede crear un conjunto de reglas de examen para cada tipo de origen de datos que quiera examinar.

1. Escriba el **nombre** del conjunto de reglas de examen. La longitud máxima del mismo es de 63 caracteres, sin espacios. Si lo desea, escriba una **descripción**. La longitud máxima es de 256 caracteres.

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="Captura de pantalla que muestra la página del conjunto de reglas de examen." border="true":::

1. Seleccione **Continuar**.

   Aparecerá la página **Seleccionar tipos de archivo**. Tenga en cuenta que las opciones de tipo de archivo de esta página varían en función del tipo de origen de datos que eligió en la página anterior. Todos los tipos de archivo están habilitados de forma predeterminada.

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="Captura de pantalla que muestra la página Seleccionar tipos de archivo.":::

   La selección de **Tipos de archivos de documento** en esta página le permite incluir o excluir los siguientes tipos de archivos de Office: .doc, .docm, .docx, .dot, .odp, .ods, .odt, .pdf, .pot, .pps, .ppsx, .ppt, .pptm, .pptx, .xlc, .xls, .xlsb, .xlsm, .xlsx y .xlt.

1. Habilite o deshabilite un icono de tipo de archivo activando o desactivando la casilla correspondiente. Si elige un origen de datos de tipo Data Lake (por ejemplo, Azure Data Lake Storage Gen2 o Azure Blob), habilite los tipos de archivo según los cuales se debe extraer y clasificar el esquema.

1. Para determinados tipos de orígenes de datos, también puede [Crear un tipo de archivo personalizado](#create-a-custom-file-type).

1. Seleccione **Continuar**.

   Aparecerá la página **Select classification rules** (Seleccionar reglas de clasificación). En esta página se muestran las **Reglas del sistema** seleccionadas, las **Reglas personalizadas** y el número total de reglas de clasificación seleccionadas. De forma predeterminada, se seleccionan todas las casillas de **Reglas del sistema**.

1. En el caso de las reglas que quiera incluir o excluir, puede seleccionar o desactivar las casillas de **reglas del sistema** de forma global y en función de la categoría.

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="Captura de pantalla que muestra la página para seleccionar las reglas de clasificación.":::

1. Puede expandir el nodo de categoría y seleccionar o desactivar las casillas individuales. Por ejemplo, si la regla **Argentina.DNI Number** tiene muchos falsos positivos, puede desactivar esa casilla específica.

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="Captura de pantalla que muestra cómo seleccionar las reglas del sistema.":::

1. Haga clic en **Crear** para terminar de crear el conjunto de reglas de examen.

### <a name="create-a-custom-file-type"></a>Creación de un tipo de archivo personalizado

Azure Purview le permite agregar una extensión personalizada y la definición de un delimitador de columna personalizado en un conjunto de reglas de examen.

Para crear un tipo de archivo personalizado:

1. Siga los pasos del 1 al 5 en la sección [Pasos para crear un conjunto de reglas de examen](#steps-to-create-a-scan-rule-set) o edite un conjunto de reglas de examen existente.

1. En la página **Seleccionar tipos de archivo**, seleccione **Nuevo tipo de archivo** para crear un nuevo tipo de archivo personalizado.

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="Captura de pantalla que muestra cómo seleccionar un nuevo tipo de archivo en la página para seleccionar los tipos de archivo.":::

1. Escriba una **Extensión de archivo** y una **Descripción** opcional.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="Captura de pantalla que muestra la página del nuevo tipo de archivo personalizado." border="true":::

1. Seleccione una de las opciones siguientes en **File contents within** (Contenido del archivo en) para especificar el tipo de contenido del archivo:

   - Seleccione **Delimitador personalizado** y escriba su propio **Delimitador personalizado** (un solo carácter).

   - Seleccione **System File Type** (Tipo de archivo del sistema) y elija un tipo de archivo del sistema (por ejemplo, XML) en la lista desplegable **System File Type** (Tipo de archivo del sistema).

1. Seleccione **Crear** para guardar el archivo personalizado.

   El sistema vuelve a la página **Seleccionar tipos de archivo** e inserta el nuevo tipo de archivo personalizado como un nuevo icono.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="Captura de pantalla que muestra el icono del nuevo tipo de archivo personalizado en la página para seleccionar los tipos de archivo.":::

1. Seleccione **Editar** en el icono del nuevo tipo de archivo si quiere cambiarlo o eliminarlo.

1. Seleccione **Continuar** para terminar de configurar el conjunto de reglas de examen.

## <a name="system-scan-rule-sets"></a>Conjuntos de reglas de examen del sistema

Microsoft se encarga de definir los conjuntos de reglas de examen del sistema y se crean automáticamente para cada catálogo de Azure Purview. Cada conjunto de reglas de examen del sistema está asociado a un tipo de origen de datos específico. Al crear un examen, puede asociarlo a un conjunto de reglas de examen del sistema. Cada vez que Microsoft realiza una actualización en estos conjuntos de reglas del sistema, puede actualizarlos en el catálogo y aplicar la actualización en todos los exámenes asociados al conjunto.

1. Para ver la lista de conjuntos de reglas de examen del sistema, seleccione **Conjuntos de reglas de examen** en el **Centro de administración** y elija la pestaña **Sistema**.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="Captura de pantalla que muestra la lista de conjuntos de reglas de examen del sistema." lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. Cada conjunto de reglas de examen del sistema tiene un **nombre**, un **tipo de origen** y una **versión**. Si selecciona el número de versión de un conjunto de reglas de examen en la columna **Versión**, verá las reglas asociadas a la versión actual y las versiones anteriores (si las hay).

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="Captura de pantalla que muestra la página del conjunto de reglas de examen del sistema." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. Si hay disponible una actualización para un conjunto de reglas de examen del sistema, puede seleccionar **Actualizar** en la columna **Versión**. En la página de la regla de examen del sistema, elija una versión de la lista desplegable **Select a new version to update** (Seleccionar una nueva versión para actualizar). La página proporciona una lista de reglas de clasificación del sistema asociadas a la nueva versión y a la versión actual.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="Captura de pantalla que muestra cómo cambiar la versión de un conjunto de reglas de examen del sistema." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>Asociación de un examen con un conjunto de reglas de examen del sistema

Al [crear un examen](tutorial-scan-data.md#scan-data-into-the-catalog), puede asociarlo a un conjunto de reglas de examen del sistema tal como se indica:

1. En la página **Select a scan rule set** (Seleccionar un conjunto de reglas de examen), seleccione el conjunto de reglas de examen del sistema que necesite.

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="Captura de pantalla que muestra cómo seleccionar un conjunto de reglas de examen del sistema para realizar un examen." lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. Seleccione **Continuar** y, a continuación, elija **Guardar y ejecutar**.
