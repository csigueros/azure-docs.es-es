---
title: Cree una base de datos de lago en Azure Synapse a partir de una plantilla de base de datos.
description: Aprenda a explorar, personalizar y crear una base de datos de lago a partir de una plantilla de base de datos.
author: aamerril
ms.author: aamerril
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 6051b8d36067846429a20b396ba39cabd70c1774
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017413"
---
# <a name="how-to-create-a-lake-database-from-database-templates"></a>Cómo: Crear una base de datos de lago a partir de una plantilla de base de datos de lago

En este artículo, aprenderá a usar las plantillas de base de Azure Synapse para simplificar la creación de una [base de datos de lago](./concepts-lake-database.md). El uso de plantillas de bases de datos proporciona a su base de datos una rica información semántica, lo que permite a los usuarios finales de los datos comprender más fácilmente qué datos están disponibles y cómo usarlos.

## <a name="prerequisites"></a>Prerrequisitos

- Se requieren al menos permisos de rol de usuario de Synapse para explorar una plantilla de base de datos de lago desde la Galería.
- Se requieren permisos de administrador o colaborador de Synapse en el área de trabajo de Synapse para crear una base de datos de lago.
- Los permisos de colaborador de datos de blobs de almacenamiento son necesarios en el lago de datos.

## <a name="create-lake-database-from-database-template"></a>Creación de una base de datos de lago a partir de una plantilla de base de datos

1. En el centro **Inicio** del área de trabajo de Azure Synapse Analytics, seleccione **Centro de conocimientos** y, a continuación, **Examinar galería**. Llegará a la pestaña **Plantillas de base de datos de lago.**
2. En la categoría **Plantillas de base de datos de lago** se enumeran las plantillas de base de datos estandarizadas disponibles para un sector específico.
   1. También puede visitar la pestaña **Plantillas de base de datos de Lake** en el centro de **datos**, **+** Agregar nuevo recurso, menú **Examinar galería**.
3. Seleccione el sector que le interesa (por ejemplo, **Retail)** y seleccione **Continuar** para ir a la exploración del modelo de datos.
4. Llegará al lienzo de la base de datos y podrá explorar las tablas disponibles en la plantilla. De forma predeterminada, el lienzo mostrará una selección de las tablas más usadas en esa plantilla. El lienzo tiene varias herramientas que le ayudarán a navegar por el diagrama de relación de entidad.
    - **Hacer zoom de ajuste** para ajustar todas las tablas del lienzo en el área de visualización
    - **Aumentar el zoom** para acercar el lienzo
    - **Disminuir el zoom** para alejar el lienzo
    - **Control deslizante de zoom** para controlar el nivel de zoom
    - **Vista previa de zoom** para proporcionar una vista previa del lienzo
    - **Expandir todo**/**Contraer todo** para ver más o menos columnas dentro de una tabla del lienzo
    - **Borrar lienzo** para borrar todas las tablas de la ![página exploración del lienzo, que muestra tablas y controles de ejemplo.](./media/create-lake-database-from-lake-database-template/canvas-overview.png)

5. A la izquierda, verá una lista de carpetas que contienen los elementos de la plantilla que puede agregar al lienzo. Hay varios controles que le ayudarán.
    - **Cuadro de búsqueda** para buscar tablas basadas en un término. El término se buscará en las tablas, columnas y descripciones de la plantilla.
    - **Filtrar tablas** para seleccionar secciones enteras del modelo que se explorarán
      - Las **plantillas de Enterprise** seleccionan tablas de cada **área de negocio** para mostrar un "lo mejor de".
      - **Las plantillas de área de negocio** seleccionan tablas principalmente de un **área de negocio** único, pero también incluyen tablas relacionadas de otras **áreas de negocio.**
    - **Las áreas de negocio** son carpetas que contienen tablas relacionadas con esa construcción empresarial. Por ejemplo, Previsión y presupuesto contiene tablas relacionadas con la administración de presupuestos.
    - Puede expandir las carpetas de área de negocio para ver las tablas y activar la casilla para agregarlas al lienzo. 
    - Las tablas seleccionadas se pueden quitar mediante la casilla.

6. Puede seleccionar una tabla del lienzo. Se abre el panel de propiedades de la tabla con las pestañas General, Columnas y Relaciones.
    - La pestaña General tiene información sobre la tabla, como su nombre y descripción.
    - La pestaña Columnas tiene los detalles sobre todas las columnas que la contienen, como los nombres de columna y los tipos de datos.
    - En la pestaña Relaciones se enumeran las relaciones entrantes y salientes de la tabla con otras tablas del lienzo.
    
7. Para agregar rápidamente tablas relacionadas con las tablas del lienzo, seleccione las elipses a la derecha del nombre de la tabla y, a continuación, seleccione **Agregar tablas relacionadas**. Todas las tablas con relaciones existentes se agregan al lienzo.

8. Una vez que el lienzo tenga todas las tablas que cumplen sus requisitos, seleccione **Crear base de datos** para continuar con la creación de la base de datos de lago. La nueva base de datos se mostrará en el diseñador de bases de datos y podrá personalizarla según sus necesidades empresariales. 

9. El diseñador de bases de datos tiene más **Propiedades** que deben configurarse en la parte derecha.
    - **Nombre**: asigne un nombre a la base de datos. Los nombres no se pueden editar después de publicar la base de datos, por lo que debe asegurarse de que el nombre que elija sea correcto.
    - **Descripción**: proporcionar una descripción para la base de datos es opcional, pero esto permite a los usuarios comprender el propósito de la base de datos.
    - **La configuración de almacenamiento de la base de datos** es una sección que contiene la información de almacenamiento predeterminada para las tablas de la base de datos. Este valor predeterminado se aplica a cada tabla de la base de datos, a menos que se invalide en la propia tabla.
    - **El servicio vinculado** es el servicio vinculado predeterminado que se usa para almacenar los datos en Azure Data Lake Storage. Se mostrará el servicio vinculado predeterminado asociado al área de trabajo de Synapse, pero puede cambiar el **servicio vinculado** a cualquier cuenta de almacenamiento de ADLS que quiera. 
    - **La carpeta de entrada** se usa para establecer la ruta de acceso predeterminada del contenedor y la carpeta de ese servicio vinculado mediante el explorador de archivos.
    - **Formato de datos**: las bases de datos de lago en Azure Synapse admiten Parquet y texto delimitado como formatos de almacenamiento de datos.
> [!NOTE]
> Siempre puede invalidar la configuración de almacenamiento predeterminada tabla por tabla, y el valor predeterminado seguirá siendo personalizable. Si no está seguro de qué elegir, puede volver más adelante.
 
![Captura de pantalla que muestra el diseñador de bases de datos con el panel de propiedades abierto](./media/create-lake-database-from-lake-database-template/designer-overview.png)


10. Puede empezar a personalizar las tablas, columnas y relaciones heredadas de la plantilla de base de datos. También puede agregar tablas personalizadas, columnas y relaciones según sea necesario en la base de datos. Para obtener más información sobre cómo modificar una base de datos de lago, consulte [Modificar una base de datos de lago.](./modify-lake-database.md)

11. Ahora que tiene la base de datos personalizada, es el momento de publicarla. Si usa la integración de Git con el área de trabajo de Synapse, debe confirmar los cambios y combinarlos en la rama de colaboración. [Obtenga más información sobre el control de código fuente en Azure Synapse](././cicd/../../cicd/source-control.md). Si usa el modo en directo de Synapse, puede seleccionar **Publicar**.
     - La base de datos se validará para ver si hay errores antes de su publicación. Los errores encontrados se mostrarán en la pestaña de notificaciones y tendrán instrucciones sobre cómo solucionar el error.
      
       ![Captura de pantalla del panel de validación que muestra los errores de validación en la base de datos](./media/create-lake-database-from-lake-database-template/validation-error.png)
     - La publicación creará el esquema de base de datos en el Metastore de Azure Synapse. Después de la publicación, los objetos de la base de datos y la tabla serán visibles para otros servicios de Azure y permitirán que los metadatos de la base de datos fluyan a aplicaciones como Power BI o Purview.

12.  Ahora ha creado una base de datos de lago mediante una plantilla de base de datos de lago en Azure Synapse. 

## <a name="next-steps"></a>Pasos siguientes

Siga explorando las funcionalidades del diseñador de bases de datos mediante los vínculos siguientes. 
- [Modificación de una base de datos de lago](./modify-lake-database.md)
- [Más información sobre las bases de datos de lago](./concepts-lake-database.md)
