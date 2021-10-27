---
title: Plantilla de ETL para un modelo de datos del sector minorista
description: En este tutorial se proporcionan los pasos para usar la plantilla del modelo de datos del sector minorista con los datos del ejemplo de Adventure Works.
author: kromerm
ms.author: aamerril
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 10/18/2021
ms.openlocfilehash: 888a6c78572940dd69ef2161fc1ded979259d63f
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181559"
---
# <a name="adventureworks-template-documentation"></a>Documentación de la plantilla de AdventureWorks

En este documento se explica cómo configurar y usar la plantilla de canalización de AdventureWorks de Microsoft para empezar a explorar el conjunto de datos de AdventureWorks mediante Azure Synapse Analytics y la plantilla de base de datos Retail.

## <a name="overview"></a>Información general
AdventureWorks es un distribuidor ficticio de equipamiento deportivo que se usa para la demostración de aplicaciones de Microsoft. En este caso, se utiliza como ejemplo de cómo usar Synapse Pipelines para asignar datos de minoristas a la plantilla de base de datos Retail para realizar un análisis más detallado en Azure Synapse.

## <a name="prerequisites"></a>Requisitos previos

* **Suscripción de Azure**. Si no tiene una suscripción a Azure, cree una [cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de empezar.
* **Área de trabajo de Azure Synapse**. [Cree un área de trabajo de Azure Synapse](../storage/common/storage-account-create.md) si no tiene ninguna aún.

## <a name="find-the-template"></a>Búsqueda de la plantilla

Siga estos pasos para encontrar la plantilla.

1. Vaya al área de trabajo de Synapse. En la página principal, seleccione **Aprender** y, después, **Examinar la galería**. Se abre la galería de Synapse. Puede buscar conjuntos de datos, scripts y canalizaciones, entre otros, para instalar en el área de trabajo. 

1. Seleccione **Canalizaciones** y filtre los resultados con la palabra clave "AdventureWorks".

1. Seleccione la plantilla **AdventureWorks** y, a continuación, seleccione **Continuar**.

Con estos pasos, se abre la página de información general de la plantilla.

## <a name="configure-the-template"></a>Configuración de la plantilla
El diseño de la plantilla hace que esta requiera una configuración mínima. En la página de información general de la plantilla, puede ver una vista previa de la configuración inicial de la canalización y hacer clic en **Abrir canalización** para crear los recursos en su propia área de trabajo. Recibirá una notificación de que los 31 recursos de la plantilla se han creado y puede revisarlos antes de confirmarlos o publicarlos. Encontrará los componentes de la plantilla siguientes:

* 17 canalizaciones: están programadas para garantizar que los datos se cargan correctamente en las tablas de destino e incluyen una canalización por tabla de origen más las de programación.
* 14 flujos de datos: contienen la lógica que se carga desde el sistema de origen y lleva los datos a la base de datos de destino.

Si tiene el conjunto de datos AdventureWorks cargado en otra base de datos, puede actualizar los orígenes del flujo de datos para que apunten a ese conjunto. De lo contrario, realice los pasos siguientes para crear una base de datos de origen y de destino que coincida con el esquema definido en la plantilla.


## <a name="dataset-and-sourcetarget-models"></a>Conjunto de datos y modelos de origen/destino
El conjunto de datos AdventureWorks en formato Excel puede descargarse desde este [sitio de GitHub](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/AdventureWorks%20Data.zip). Además, puede acceder a la [definición de esquema para las bases de datos de origen y de destino](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/AdventureWorksSchemas.xlsx). Use el diseñador de bases de datos de Synapse y vuelva a crear las bases de datos de origen y de destino con el esquema de Excel que descargó anteriormente. Para obtener más información sobre el diseñador de bases de datos, consulte esta [documentación](https://aka.ms/SynapseDatabaseDesignerDocumentation).

En las bases de datos creadas, asegúrese de que los flujos de datos apuntan a las tablas correctas mediante la edición de las listas desplegables en la configuración de origen y receptor de la base de datos del área de trabajo. Para cargar los datos en el modelo de origen, coloque los archivos .csv que se proporcionan en el conjunto de datos de ejemplo en las carpetas correctas que se especifican en las tablas. Una vez hecho esto, solo es necesario ejecutar las canalizaciones.

## <a name="troubleshoot-the-pipelines"></a>Solución de problemas de las canalizaciones
Si la canalización no se ejecuta correctamente, deben comprobarse algunos aspectos principales para ver si hay errores.

* Esquema del conjunto de datos. Asegúrese de que la configuración de datos de los archivos .csv sea precisa. Si ha incluido encabezados de fila, asegúrese de que la opción correspondiente esté seleccionada en la tabla de base de datos.
* Orígenes de flujos de datos. Si ha usado nombres de tabla o columna distintos a los proporcionados en el esquema de ejemplo, deberá recorrer los flujos de datos para comprobar que las columnas se hayan asignado correctamente.
* Receptor del flujo de datos. Las configuraciones de formato de los datos y el esquema de la base de datos de destino deberán coincidir con la plantilla de flujo de datos. Al igual que anteriormente, si se ha realizado algún cambio, deberán alinearse esos elementos.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre los [flujos de datos de asignación](concepts-data-flow-overview.md).
* Obtenga más información sobre las [plantillas de canalización](solution-templates-introduction.md).
