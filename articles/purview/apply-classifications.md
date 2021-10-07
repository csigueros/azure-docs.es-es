---
title: Aplicación de clasificaciones en recursos
description: En este documento se describe cómo aplicar clasificaciones en los recursos.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8ec7d456c5bb2eb78c9e1729e38d918c6ac222ee
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210891"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Aplicación de clasificaciones en los recursos de Azure Purview

En este artículo se describe cómo aplicar clasificaciones en recursos.

## <a name="introduction"></a>Introducción

Las clasificaciones pueden ser de tipos de sistema o personalizadas. De forma predeterminada, las clasificaciones del sistema ya están presentes en Purview. Asimismo, las clasificaciones personalizadas se pueden crear en función de un patrón de expresión regular. Recuerde que las clasificaciones se pueden aplicar a los recursos de forma automática o manual.

En este documento se explica cómo aplicar clasificaciones a los datos.

## <a name="prerequisites"></a>Requisitos previos

- Cree clasificaciones personalizadas en función de sus necesidades.
- Configure el examen en los orígenes de datos.

## <a name="apply-classifications"></a>Aplicación de clasificaciones
En Azure Purview, puede aplicar clasificaciones personalizadas o de sistema en un recurso de archivo, tabla o columna. En este artículo se describen los pasos para aplicar manualmente las clasificaciones en los recursos.

### <a name="apply-classification-to-a-file-asset"></a>Aplicación de una clasificación en un recurso de archivo
Azure Purview puede examinar y clasificar automáticamente los archivos de documentación. Por ejemplo, si tiene un archivo denominado *multiple.docx* y tiene un número de id. nacional en su contenido, Azure Purview agrega la clasificación de tipo **Número de identificación nacional de la UE** a la página de detalles del recurso de archivo.

En algunos casos, es posible que quiera agregar manualmente las clasificaciones al recurso de archivo. Si tiene varios archivos agrupados en un conjunto de recursos, agregue las clasificaciones al nivel de conjunto de recursos.

Siga estos pasos para agregar una clasificación personalizada o de sistema a un conjunto de recursos de partición:

1. Busque o examine la partición y vaya a la página de detalles del recurso.

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="Captura de pantalla que muestra la página de detalles del recurso.":::

1. En la pestaña **Información general**, consulte la sección **Clasificaciones** para ver si hay alguna clasificación. Seleccione **Editar**.

1. En la lista desplegable **Clasificaciones**, seleccione las clasificaciones específicas que le interesen. Por ejemplo, **Número de tarjeta de crédito**, que es una clasificación de sistema y **CustomerAccountID**, que es una clasificación personalizada.

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="Captura de pantalla que muestra cómo seleccionar las clasificaciones que se van a agregar a un recurso.":::

1. Seleccione **Guardar**.

1. En la pestaña **Información general**, confirme que las clasificaciones seleccionadas aparecen en la sección **Clasificaciones**.

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="Captura de pantalla que muestra cómo confirmar que las clasificaciones se agregaron a un recurso.":::

### <a name="apply-classification-to-a-table-asset"></a>Aplicación de una clasificación a un recurso de tabla

Cuando Azure Purview examina los orígenes de datos, no asigna automáticamente las clasificaciones a los recursos de tabla. Si quiere que el recurso de tabla tenga una clasificación, debe agregarla manualmente.

Para agregar una clasificación a un recurso de tabla, realice lo siguiente:

1. Busque un recurso de tabla que le interese. Por ejemplo, la tabla **Clientes**.

1. Confirme que no haya ninguna clasificación asignada a la tabla. Seleccione **Editar**.

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="Captura de pantalla que muestra cómo ver y editar las clasificaciones de un recurso de tabla.":::

1. En la lista desplegable **Clasificaciones**, seleccione una o varias clasificaciones. En este ejemplo se usa una clasificación personalizada denominada **CustomerInfo**, pero puede seleccionar cualquier clasificación para este paso.

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="Captura de pantalla que muestra cómo seleccionar las clasificaciones que se van a agregar a un recurso de tabla.":::

1. Para guardar las clasificaciones, seleccione **Guardar**.

1. En la página **Información general**, compruebe que Azure Purview agregó las nuevas clasificaciones.

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="Captura de pantalla que muestra cómo comprobar que las clasificaciones se agregaron a un recurso de tabla.":::

### <a name="add-classification-to-a-column-asset"></a>Agregación de una clasificación a un recurso de columna

Azure Purview examina y agrega automáticamente las clasificaciones a todos los recursos de columna. Sin embargo, si quiere cambiar la clasificación, puede hacerlo en el nivel de columna.

Para agregar una clasificación a una columna:

1. Busque y seleccione el recurso de columna y, a continuación, seleccione **Editar** en la pestaña **Información general**.

1. Seleccione la pestaña **Esquema**.

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="Captura de pantalla que muestra cómo editar el esquema de una columna.":::

1. Identifique las columnas que le interesan y seleccione **Add a classification** (Agregar una clasificación). En este ejemplo se agrega una clasificación de tipo **Contraseñas comunes** a la columna **PasswordHash**.

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="Captura de pantalla que muestra cómo agregar una clasificación a una columna.":::

1. Seleccione **Guardar**.

1. Seleccione la pestaña **Esquema** y confirme que la clasificación se haya agregado a la columna.

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="Captura de pantalla que muestra cómo confirmar que se ha agregado una clasificación a un esquema de columna.":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>Impacto que supone volver a examinar las clasificaciones existentes

Las clasificaciones se aplican la primera vez, en función de cómo se comprueba el conjunto de muestras en los datos y su comparación con el patrón de expresiones regulares establecido. En el momento de volver a realizar el examen, si se aplican clasificaciones nuevas, la columna obtiene clasificaciones adicionales. Las clasificaciones existentes permanecen en la columna y deben quitarse manualmente.

## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre cómo crear una clasificación personalizada, consulte [Creación de una clasificación personalizada](create-a-custom-classification-and-classification-rule.md).