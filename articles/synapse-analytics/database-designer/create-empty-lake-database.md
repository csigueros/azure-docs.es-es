---
title: Creación de una base de datos de lago vacía
description: Aprenda a crear una base de datos de lago vacía en Azure Synapse Analytics que se pueda agregar fácilmente.
author: aamerril
ms.author: aamerril
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 2cff956fa8507ec1dcbf8e3ab3576d19ecebf526
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060819"
---
# <a name="how-to-create-an-empty-lake-database"></a>Cómo crear de una base de datos de lago vacía

En este artículo, aprenderá a crear una [base de datos de lago](./concepts-lake-database.md) vacía en Azure Synapse Analytics mediante el diseñador de bases de datos. El diseñador de bases de datos le permite crear e implementar fácilmente una base de datos sin tener que escribir código. 

## <a name="prerequisites"></a>Prerrequisitos

- Se requieren al menos permisos de rol de usuario de Synapse para explorar una plantilla de base de datos de lago desde la Galería.
- Se requieren permisos de administrador, colaborador o de publicador de artefactos de Synapse en el área de trabajo de Synapse para crear una base de datos de lago.
- Los permisos de colaborador de datos de blobs de almacenamiento son necesarios en el lago de datos.

## <a name="create-lake-database-from-database-template"></a>Creación de una base de datos de lago a partir de una plantilla de base de datos
1. Desde el centro de **Inicio** del área de trabajo de Azure Synapse Analytics, seleccione la pestaña **Datos** que tiene a la izquierda. Se abrirá la pestaña **Datos** y verá la lista de bases de datos que ya existen en el área de trabajo.
2. Mantenga el puntero sobre el botón **+** y, a continuación, seleccione **Base de datos de lago (versión preliminar)** .
![Captura de pantalla que muestra la creación de una base de datos de lago vacía](./media/create-empty-lake-database/create-empty-lakedb.png)
3. La pestaña del diseñador de bases de datos se abrirá con una base de datos vacía.
4. El diseñador de bases de datos tiene la **Propiedades** que deben configurarse en la parte derecha.
    - **Nombre**: asigne un nombre a la aplicación base de datos. Los nombres no se pueden editar después de publicar la base de datos, por lo que debe asegurarse de que el nombre que elija sea correcto.
    - **Descripción**: proporcionar una descripción para la base de datos es opcional, pero esto permite a los usuarios comprender el propósito de la base de datos.
    - **Configuración de almacenamiento de la base de datos**: esta sección contiene la información de almacenamiento predeterminada para las tablas de la base de datos. Este valor predeterminado se aplica a cada tabla de la base de datos, a menos que se invalide en la propia tabla.
    - **Servicio vinculado**: es el servicio vinculado predeterminado que se usa para almacenar los datos en Azure Data Lake Storage.  Se mostrará el servicio vinculado predeterminado asociado al área de trabajo de Synapse, pero puede cambiar el **servicio vinculado** a cualquier cuenta de almacenamiento de ADLS que quiera. 
    - **Carpeta de entrada**: se usa para establecer la ruta de acceso predeterminada del contenedor y la carpeta de ese servicio vinculado mediante el explorador de archivos.
    - **Formato de datos**: las bases de datos de lago en Azure Synapse admiten Parquet y texto delimitado como formatos de almacenamiento de datos.

> [!NOTE]
> Siempre puede invalidar la configuración de almacenamiento predeterminada tabla por tabla, y el valor predeterminado seguirá siendo personalizable. Si no está seguro de qué elegir, puede volver más adelante.
 
5. Para agregar una tabla a la base de datos, seleccione el botón **+ Tabla**. 
    - El valor **Personalizado** agregará una nueva tabla al lienzo.
    - La opción **Desde la plantilla** abrirá la galería y podrá seleccionar una plantilla de base de datos que se usará al agregar una nueva tabla. Para obtener más información, consulte [Creación de una base de datos de lago a partir de una plantilla de base de datos](./create-lake-database-from-lake-database-templates.md).
    - La opción **Desde el lago de datos** le permite importar un esquema de tabla con datos que ya están en el lago.
6. seleccione **Personalizada**. Aparecerá una nueva tabla en el lienzo denominada Table_1.
7. A continuación, puede personalizar Table_1, incluidos el nombre de la tabla, la descripción, la configuración de almacenamiento, las columnas y las relaciones. Para obtener más información, consulte [Modificar una base de datos de lago](./modify-lake-database.md).
8. Agregue una nueva tabla del lago de datos seleccionando **+ Tabla** y, a continuación, **Desde el lago de datos**.
9. Aparecerá el panel **Crear tabla externa a partir del lago de datos**. Rellene el panel con los detalles siguientes y seleccione **Continuar**.
    - **Nombre de tabla externa**: es el nombre que quiere darle a la tabla que está creando.
    - **Servicio vinculado**: es el servicio vinculado que contiene la ubicación de Azure Data Lake Storage donde reside el archivo de datos.
    - **Archivo o carpeta de entrada**: use el explorador de archivos para navegar y seleccionar un archivo en su lago, para crear una tabla con él.
![Captura de pantalla que muestra las opciones en el panel Crear tabla externa a partir del lago de datos](./media/create-empty-lake-database/create-from-lake.png)
    - En la siguiente pantalla, Azure Synapse obtendrá una vista previa del archivo y detectará el esquema.
    - Accederá a la página **Nueva tabla externa** donde puede actualizar cualquier configuración relacionada con el formato de datos y **Vista previa de datos** para verificar si Synapse identificó el archivo correctamente.
    - Cuando esté satisfecho con la configuración, seleccione **Crear**.
    - Se agregará una nueva tabla con el nombre seleccionado al lienzo y la sección **Configuración de Storage para la tabla** mostrará el archivo que especificó.
    
10. Ahora que tiene la base de datos personalizada, es el momento de publicarla. Si usa la integración de Git con el área de trabajo de Synapse, debe confirmar los cambios y combinarlos en la rama de colaboración. [Obtenga más información sobre el control de código fuente en Azure Synapse](././cicd/../../cicd/source-control.md). Si usa el modo en directo de Synapse, puede seleccionar "publicar".
    - La base de datos se validará para ver si hay errores antes de su publicación. Los errores encontrados se mostrarán en la pestaña de notificaciones y tendrán instrucciones sobre cómo solucionar el error.
    
       ![Captura de pantalla del panel de validación que muestra los errores de validación en la base de datos](./media/create-empty-lake-database/validation-error.png)
    - La publicación creará el esquema de base de datos en el Metastore de Azure Synapse. Después de la publicación, los objetos de la base de datos y la tabla serán visibles para otros servicios de Azure y permitirán que los metadatos de la base de datos fluyan a aplicaciones como Power BI o Purview.

11. Ya ha creado una base de datos de lago vacía en Azure Synapse y le ha agregado tablas mediante las opciones **Personalización** y **Desde el lago de datos**.

## <a name="next-steps"></a>Pasos siguientes

Siga explorando las funcionalidades del diseñador de bases de datos mediante los vínculos siguientes. 
- [Modificación de una base de datos de lago](./modify-lake-database.md)
- [Más información sobre las bases de datos de lago](./concepts-lake-database.md)
- [Creación de una base de datos de lago a partir de una plantilla de base de datos de lago](./create-lake-database-from-lake-database-templates.md)
