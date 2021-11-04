---
title: 'Inicio rápido: Creación de un grupo de Apache Spark para GPU sin servidor'
description: Cree un grupo de Apache Spark para GPU sin servidor mediante Azure Portal siguiendo los pasos descritos en esta guía.
services: synapse-analytics
author: Niharikadutta
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 10/18/2021
ms.author: nidutta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 439e8b70c20bc07a431b17afdca84690c3cbd61e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093438"
---
# <a name="quickstart-create-an-apache-spark-gpu-enabled-pool-in-azure-synapse-analytics-using-the-azure-portal"></a>Inicio rápido: Creación de un grupo de Apache Spark habilitado para GPU en Azure Synapse Analytics mediante Azure Portal

Un grupo de Apache Spark proporciona funcionalidades de proceso de macrodatos de código abierto donde los datos se pueden cargar, modelar, procesar y distribuir para obtener información de análisis más rápida. Synapse ofrece ahora la posibilidad de crear grupos de Apache Spark que usan unidades de procesamiento gráfico (GPU) en el back-end para ejecutar las cargas de trabajo de Spark en las GPU y así acelerar el procesamiento.

En este inicio rápido, aprenderá a usar Azure Portal para crear un grupo de Apache Spark habilitado para GPU en un área de trabajo de Azure Synapse Analytics.

> [!NOTE]
> Los grupos de Azure Synapse habilitados para GPU están actualmente en versión preliminar pública.

Si no tiene una suscripción a Azure, [cree una cuenta gratuita antes de empezar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerrequisitos

- Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
- [Área de trabajo de Synapse Analytics](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en el [Portal de Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Desplazamiento al área de trabajo de Synapse 
1. Vaya al área de trabajo de Synapse en la que se va a crear el grupo de Apache Spark. Para ello, escriba el nombre del servicio (o el nombre del recurso directamente) en la barra de búsqueda.
![Barra de búsqueda de Azure Portal en la que se ha escrito "Áreas de trabajo de Synapse".](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
2. En la lista de áreas de trabajo, escriba el nombre (o una parte del nombre) del área que desea abrir. En este ejemplo, se usará un área de trabajo denominado **contosoanalytics**.
![Lista de áreas de trabajo de Synapse filtradas en la que se muestran las que contienen el nombre Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)


## <a name="create-new-azure-synapse-gpu-enabled-pool"></a>Creación de un grupo de Azure Synapse habilitado para GPU

1. En el área de trabajo de Synapse en la que desee crear el grupo de Apache Spark, seleccione **New Apache Spark pool** (Nuevo grupo de Apache Spark) en la barra superior.
    ![Información general del área de trabajo de Synapse con un cuadro rojo alrededor del comando para crear un nuevo grupo de Apache Spark](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-01.png)
2. En la pestaña **Datos básicos**, escriba la siguientes información:

    |Configuración | Valor sugerido | Descripción |
    | :------ | :-------------- | :---------- |
    | **Nombre del grupo de Apache Spark** | Un nombre de grupo válido. | Este es el nombre que tendrá el grupo de Apache Spark. |
    | **Familia de tamaños de nodo** | Acelerado por hardware | Elija Hardware Accelerated (Acelerado por hardware) en el menú desplegable. |
    | **Tamaño del nodo** | Grande (16 vCPU/110 GB/1 GPU) | Establézcalo en el menor tamaño para reducir los costos de este artículo de inicio rápido |
    | **Autoscale** | Disabled | En este inicio rápido no se necesita la escalabilidad automática |
    | **Número de nodos** | 3 | Use un tamaño pequeño para limitar los costos en este inicio rápido |


    ![Flujo de creación del grupo de Apache Spark: pestaña de datos básicos.](media/quickstart-create-apache-spark-pool/create-spark-gpu-pool-portal-01.png)
    > [!IMPORTANT]
    > Tenga en cuenta que existen limitaciones específicas para los nombres que pueden usar los grupos de Apache Spark. Los nombres solo deben contener letras o números, deben tener 15 caracteres o menos, deben comenzar con una letra, no contener palabras reservadas y ser únicos en el área de trabajo.

3. Seleccione **Siguiente: Configuración adicional** y examine la configuración predeterminada. No modifique ninguna configuración predeterminada. Observe que los grupos de GPU **solo se pueden crear con Apache Spark 3.1**.
    ![Captura de pantalla que muestra la página "Create Apache Spark pool" (Crear grupo de Apache Spark) con la pestaña "Additional settings"(Configuración adicional) seleccionada.](media/quickstart-create-apache-spark-pool/create-spark-gpu-pool-portal-02.png)

4. Seleccione **Siguiente: etiquetas**. No agregue ninguna etiqueta.

    ![Flujo de creación del grupo de Apache Spark: pestaña de configuración adicional.](media/quickstart-create-apache-spark-pool/create-spark-pool-03-tags.png)

5. Seleccione **Revisar + crear**.

6. Asegúrese de que los detalles son correctos en función de lo que se especificó anteriormente y seleccione **Create** (Crear).
    ![Flujo de creación del grupo de SQL: pestaña de revisión de la configuración.](media/quickstart-create-apache-spark-pool/create-spark-gpu-pool-portal-03.png)

7. Llegados a este punto, se iniciará el flujo de aprovisionamiento de recursos, que mandará una indicación al finalizar.
    ![Captura de pantalla que muestra la página "Overview" (Información general) con un mensaje que indica que se ha completado la implementación.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-06.png)

8. Una vez finalizado el aprovisionamiento, al volver al área de trabajo se mostrará una nueva entrada para el grupo de Azure Synapse habilitado para GPU recién creado.
    ![Flujo de creación del grupo de Apache Spark: aprovisionamiento de recursos.](media/quickstart-create-apache-spark-pool/create-spark-gpu-pool-portal-04.png)

9. En este momento, no hay ningún recurso en ejecución, sin cargos por Spark, y ha creado metadatos sobre las instancias de Spark que desea crear.

## <a name="clean-up-resources"></a>Limpieza de recursos

Siga los pasos que se indican a continuación para eliminar el grupo de Apache Spark del área de trabajo.
> [!WARNING]
> Al eliminar un grupo de Apache Spark, se quitará el motor de análisis del área de trabajo. Ya no será posible conectarse al grupo de SQL y todas las consultas, canalizaciones y cuadernos que usen este grupo de Apache Spark dejarán de funcionar.

Si está seguro de que quiere eliminar el grupo de Apache Spark, haga lo siguiente:

1. Vaya a la hoja Grupos de Apache Spark en el área de trabajo.
2. Seleccione el grupo de Apache Spark que se va a eliminar (en este caso, **contosospark**).
3. Presione **Eliminar**.

 ![Lista de grupos de Apache Spark, con el grupo creado recientemente seleccionado.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-08.png)

4. Confirme la eliminación y presione el botón **Eliminar**.

 ![Cuadro de diálogo de confirmación para eliminar el grupo de Apache Spark seleccionado.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-10.png)

5. Cuando el proceso se complete correctamente, el grupo de Apache Spark dejará de aparecer en los recursos del área de trabajo.

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Inicio rápido: Creación de un cuaderno de Apache Spark para ejecutarse en un grupo de GPU](spark/apache-spark-rapids-gpu.md).
