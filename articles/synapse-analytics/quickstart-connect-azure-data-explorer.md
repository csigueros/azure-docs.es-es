---
title: 'Inicio rápido: Conexión de Azure Data Explorer a un área de trabajo de Azure Synapse Analytics'
description: Conecte un clúster de Azure Data Explorer a un área de trabajo de Azure Synapse Analytics mediante Apache Spark para Azure Synapse Analytics.
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: 9f45bc134c656701ff02beca464ac00fdf222f88
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830210"
---
# <a name="connect-to-azure-data-explorer-using-apache-spark-for-azure-synapse-analytics"></a>Conexión a Azure Data Explorer con Apache Spark para Azure Synapse Analytics

En este artículo se describe cómo acceder a la base de datos de Azure Data Explorer desde Synapse Studio con Apache Spark para Azure Synapse Analytics.

## <a name="prerequisites"></a>Requisitos previos

* [Cree de un clúster y de la base de datos de Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal).
* Debe tener un área de trabajo de Azure Synapse Analytics existente o crear una siguiendo los pasos descritos en [Inicio rápido: Creación de un área de trabajo de Azure Synapse](./quickstart-create-workspace.md).
* Debe tener un grupo de Apache Spark existente o crear uno siguiendo los pasos descritos en [Inicio rápido: Creación de un grupo de Apache Spark (versión preliminar)](./quickstart-create-apache-spark-pool-portal.md).
* [Cree aplicaciones de Azure Active Directory (Azure AD) mediante el aprovisionamiento de una aplicación de Azure AD](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app).
* Conceda a la aplicación de Azure AD acceso a la base de datos siguiendo los pasos descritos en [Administración de permisos de base de datos de Azure Data Explorer](/azure/data-explorer/manage-database-permissions).

## <a name="go-to-synapse-studio"></a>Ir a Synapse Studio

En un área de trabajo de Azure Synapse, seleccione **Iniciar Synapse Studio**. En la página principal de Synapse Studio, seleccione **Datos** para ir a **Data Object Explorer** (Explorador de objetos de datos).

## <a name="connect-an-azure-data-explorer-database-to-an-azure-synapse-workspace"></a>Conexión de una base de datos de Azure Data Explorer a un área de trabajo de Azure Synapse

La conexión de una base de datos de Azure Data Explorer a un área de trabajo se realiza mediante un servicio vinculado. Con un servicio vinculado de Azure Data Explorer, puede examinar y explorar datos y leer y escribir en Apache Spark para Azure Synapse. También puede ejecutar trabajos de integración en una canalización.

En Azure Data Explorer, siga estos pasos para conectar directamente un clúster de Azure Data Explorer:

1. Seleccione el icono **+** junto a **Datos**.
1. Seleccione **Conectar** para conectarse a datos externos.
1. Seleccione **Azure Data Explorer (Kusto)** .
1. Seleccione **Continuar**.
1. Use un nombre descriptivo para designar al servicio vinculado. El nombre aparecerá en Data Object Explorer (Explorador de objetos de datos) y se usará en los entornos de ejecución de Azure Synapse para conectarse a la base de datos.
1. Seleccione el clúster de Azure Data Explorer desde su suscripción o escriba el URI.
1. Escriba el **identificador de la entidad de servicio** y la **clave de la entidad de servicio**. Asegúrese de que esta entidad de servicio tenga acceso de vista en la base de datos para la operación de lectura y acceso de ingesta para la ingesta de datos.
1. Escriba el nombre de la base de datos de Azure Data Explorer.
1. Seleccione **Probar conexión** para asegurarse de que tiene los permisos adecuados
1. Seleccione **Crear**.

    ![Captura de pantalla que muestra un nuevo servicio vinculado.](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > (Opcional) La opción **Probar conexión** no valida el acceso de escritura. Asegúrese de que el identificador de la entidad de servicio tenga acceso de escritura a la base de datos de Azure Data Explorer.

1. Los clústeres y las bases de datos de Azure Data Explorer aparecen en la pestaña **Vinculado** de la sección **Azure Data Explorer**.

    ![Captura de pantalla que muestra la exploración de clústeres.](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

1. Para poder interactuar con el servicio vinculado desde un cuaderno, debe publicarse en el área de trabajo. Haga clic en **Publicar** en la barra de herramientas, revise los cambios pendientes y haga clic **Aceptar**.

    > [!NOTE]
    > En la versión actual, los objetos de base de datos se rellenan en función de los permisos de las cuentas de Azure AD en las bases de datos de Azure Data Explorer. Al ejecutar los cuadernos o los trabajos de integración de Apache Spark, se usará la credencial del servicio de vínculo (por ejemplo, la entidad de servicio).

## <a name="quickly-interact-with-code-generated-actions"></a>Interacción rápida con las acciones generadas por el código

Al hacer clic con el botón derecho en una base de datos o en una tabla, aparece una lista de cuadernos de Spark de ejemplo. Seleccione una opción para leer o escribir datos en Azure Data Explorer o transmitirlos ahí.

[![Captura de pantalla que muestra los nuevos cuadernos de ejemplo.](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

A continuación, se muestra un ejemplo de lectura de datos. Asocie el cuaderno al grupo de Spark y ejecute la celda.

[![Captura de pantalla que muestra un nuevo cuaderno de lectura.](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE]
   > La primera ejecución puede tardar más de tres minutos en iniciar la sesión de Spark, pero las siguientes serán mucho más rápidas.

## <a name="limitations"></a>Limitaciones

Actualmente, el conector de Azure Data Explorer no es compatible con redes virtuales de Azure Synapse.

## <a name="next-steps"></a>Pasos siguientes

* [Código de ejemplo con opciones avanzadas](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Conector de Spark de Azure Data Explorer (Kusto)](https://github.com/Azure/azure-kusto-spark)
