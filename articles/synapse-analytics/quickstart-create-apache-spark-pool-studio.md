---
title: 'Inicio rápido: Creación de un grupo de Apache Spark sin servidor mediante Synapse Studio'
description: Siga los pasos de esta guía para crear un grupo de Apache Spark sin servidor mediante Synapse Studio.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 10/16/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 14d604a5e15d7ca33cb9b73a41f867016599087d
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401544"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-using-synapse-studio"></a>Inicio rápido: Creación de un grupo de Apache Spark sin servidor mediante Synapse Studio

Azure Synapse Analytics ofrece varios motores de análisis que ayudan a ingerir, transformar, modelar, analizar y servir datos. Los grupos de Apache Spark ofrecen funcionalidades de proceso de macrodatos de código abierto. Después de crear un grupo de Apache Spark en el área de trabajo de Synapse, los datos se pueden cargar, modelar, procesar y servir para obtener información.  

En este inicio rápido se describen los pasos necesarios para crear un grupo de Apache Spark en un área de trabajo de Azure Synapse mediante Synapse Studio.

> [!IMPORTANT]
> La facturación de las instancias de Spark se prorratea por minuto, tanto si se usan como si no. Asegúrese de cerrar la instancia de Spark después de que haya terminado de usarla, o configure un breve tiempo de espera. Para obtener más información, consulte la sección **Limpieza de recursos** de este artículo.

Si no tiene una suscripción a Azure, [cree una cuenta gratuita antes de empezar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Requisitos previos

- Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
- [Área de trabajo de Synapse](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en el [Portal de Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Desplazamiento al área de trabajo de Synapse

1. Vaya al área de trabajo de Synapse en la que se va a crear el grupo de Apache Spark. Para ello, escriba el nombre del servicio (o el nombre del recurso directamente) en la barra de búsqueda.

    ![Barra de búsqueda de Azure Portal en la que se ha escrito "Áreas de trabajo de Synapse".](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. En la lista de áreas de trabajo, escriba el nombre (o una parte del nombre) del área que desea abrir. En este ejemplo, se usará un área de trabajo denominado **contosoanalytics**.

    ![Lista de áreas de trabajo de Synapse filtradas en la que se muestran las que contienen el nombre Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Inicio de Synapse Studio 

En la información general del área de trabajo, seleccione la **dirección URL web del área de trabajo**  para abrir Synapse Studio.

![Información general del área de trabajo de Synapse en Azure Portal con la opción Iniciar Synapse Studio resaltada.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-the-apache-spark-pool-in-synapse-studio"></a>Creación del grupo de Apache Spark en Synapse Studio

1. En la página principal de Synapse Studio, vaya a **Management Hub** (Centro de administración) en el panel de navegación de la izquierda, para lo que debe seleccionar el icono **Manage** (Administrar).

    ![Página principal de Synapse Studio con la sección Management Hub (Centro de administración) resaltada.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Una vez que esté en Management Hub (Centro de administración), vaya a la sección **Apache Spark pools** (Grupos de Apache Spark) para ver la lista actual de grupos de Apache Spark que están disponibles en el área de trabajo.
    
    ![Centro de administración de Synapse Studio con la navegación por los grupos de Apache Spark seleccionada](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-22.png)

1. Seleccione **+ New** (+Nuevo) y aparecerá el nuevo Asistente para crear grupos de Apache Spark. 

1. Escriba la siguiente información en la pestaña **Datos básicos**.

    | Configuración | Valor sugerido | Descripción |
    | :------ | :-------------- | :---------- |
    | **Nombre del grupo de Apache Spark** | contosospark | Este es el nombre que tendrá el grupo de Apache Spark. |
    | **Tamaño del nodo** | Pequeño (4 vCPU/32 GB) | Establézcalo en el menor tamaño para reducir los costos de este artículo de inicio rápido |
    | **Autoscale** | Disabled | En este inicio rápido no se necesitará la escalabilidad automática |
    | **Número de nodos** | 8 | Use un tamaño pequeño para limitar los costos en este inicio rápido|
    | **Asignación dinámica de ejecutores** |  Disabled | Esta configuración se asigna a la propiedad de asignación dinámica de la configuración de Spark para la asignación de ejecutores de aplicaciones de Spark. En este inicio rápido no se necesitará la escalabilidad automática.|
    
    ![Aspectos básicos del nuevo grupo de Apache Spark en Synapse Studio](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-24.png)
    
    > [!IMPORTANT]
    > Tenga en cuenta que existen limitaciones específicas para los nombres que pueden usar los grupos de Apache Spark. Los nombres solo deben contener letras o números, deben tener 15 caracteres o menos, deben comenzar con una letra, no contener palabras reservadas y ser únicos en el área de trabajo.

1. En la pestaña siguiente (Configuración adicional), deje todas las configuraciones en sus valores predeterminados.

1. Por ahora no vamos a agregar etiquetas, así que seleccione **Review + create** (Revisar y crear).

1. En la pestaña **Review + create** (Revisar y crear), asegúrese de que los datos son correctos, en función de lo que se especificó anteriormente, y haga clic en **Create** (Crear). 

    ![Creación de un nuevo grupo de Apache Spark en Synapse Studio](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-26.png)

1. El grupo de Apache Spark iniciará el proceso de aprovisionamiento.

1. Una vez que se haya completado el aprovisionamiento, el nuevo grupo de Apache Spark aparecerá en la lista.
    
    ![Lista del nuevo grupo de Apache Spark en Synapse Studio](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-28.png)

## <a name="clean-up-apache-spark-pool-resources-using-synapse-studio"></a>Eliminación de recursos de un grupo de Apache Spark mediante Synapse Studio

Siga los pasos que se indican a continuación para eliminar el grupo de Apache Spark del área de trabajo mediante Synapse Studio.
> [!WARNING]
> Al eliminar un grupo de Apache Spark, se quitará también el motor de análisis del área de trabajo, por lo que no será posible conectarse al grupo y todas las consultas, canalizaciones y cuadernos que usen este grupo de Spark dejarán de funcionar.

Si está seguro de que quiere eliminar el grupo de Apache Spark, haga lo siguiente:

1. Vaya a los grupos de Apache Spark en el centro de administración de Synapse Studio.
1. Seleccione los puntos suspensivos que aparecen al lado del grupo de Apache que se va a eliminar (en este caso, **contosospark**) para mostrar los comandos del grupo de Apache Spark.

    ![Lista de grupos de Apache Spark, con el grupo creado recientemente seleccionado.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-29.png)

1. Presione **Eliminar**.
1. Confirme la eliminación y presione el botón **Eliminar**.
1. Cuando el proceso se complete correctamente, el grupo de Apache Spark dejará de aparecer en los recursos del área de trabajo. 

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Quickstart: Creación de un grupo de Apache Spark (versión preliminar) en Synapse Analytics mediante herramientas web](quickstart-apache-spark-notebook.md).
- Consulte [Quickstart: Creación de un grupo de Apache Spark (versión preliminar)](quickstart-create-apache-spark-pool-portal.md).
