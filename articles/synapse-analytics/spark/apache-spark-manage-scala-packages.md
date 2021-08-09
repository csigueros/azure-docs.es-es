---
title: Administración de bibliotecas de Scala y Java para Apache Spark
description: Obtenga información sobre cómo agregar y administrar bibliotecas de Scala y Java en Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: bed5c6b1b1cf21de0105e0eeb2096d359177c115
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2021
ms.locfileid: "109815007"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>Administración de paquetes de Scala y Java para Apache Spark en Azure Synapse Analytics

Las bibliotecas proporcionan código reutilizable que se puede incluir en los programas o proyectos. 

Es posible que tenga que actualizar el entorno de grupo sin servidor de Apache Spark por varios motivos. Por ejemplo:
- Si se ha publicado una nueva versión de una de sus dependencias principales.
- Si necesita un paquete adicional para entrenar su modelo de aprendizaje automático o preparar sus datos.
- Si ha encontrado un paquete mejor y ya no necesita el paquete anterior.

A fin de que el código de terceros o de compilación local esté disponible para las aplicaciones, puede instalar una biblioteca en uno de los grupos de Apache Spark sin servidor o en una sesión de cuaderno. En este artículo se explica cómo administrar paquetes de Scala y Java.

## <a name="default-installation"></a>Instalación predeterminada
Apache Spark en Azure Synapse Analytics tiene un conjunto completo de bibliotecas para tareas comunes de ingeniería de datos, preparación de datos, aprendizaje automático y visualización de datos. Puede encontrar la lista de bibliotecas completas en [Compatibilidad con las versiones de Apache Spark](apache-spark-version-support.md). 

Cuando se inicie una instancia de Spark, estas bibliotecas se incluirán automáticamente. Se pueden agregar paquetes adicionales de Scala o Java en el nivel de grupo o de sesión de Spark.

## <a name="workspace-packages"></a>Paquetes de área de trabajo
Los paquetes de área de trabajo pueden ser archivos JAR personalizados o privados. Puede cargar estos paquetes en su área de trabajo y después asignarlos a un grupo de Spark específico.

Para agregar paquetes de área de trabajo:
1. Vaya a la pestaña **Administrar** > **Paquetes de área de trabajo**.
2. Cargue sus archivos jar mediante el selector de archivos.
3. Una vez cargados en el área de trabajo de Azure Synapse, puede agregar estos archivos jar a un grupo de Apache Spark específico.

![Captura de pantalla en la que se resaltan los paquetes de área de trabajo.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Visualización de paquetes de área de trabajo")

## <a name="pool-libraries"></a>Bibliotecas de grupos
Una vez que haya identificado los paquetes de Scala y Java que le gustaría usar para la aplicación Spark, puede instalarlos en un grupo de Spark. Las bibliotecas de nivel de grupo están disponibles para todos los cuadernos y todos los trabajos que se ejecutan en el grupo.

Puede actualizar las bibliotecas de grupos de Spark desde Synapse Studio o Azure Portal. donde puede seleccionar las bibliotecas de área de trabajo que se van a instalar. 

Después de guardar los cambios, un trabajo de Spark ejecutará la instalación y almacenará en caché el entorno resultante para reutilizarlo más adelante. Una vez completado el trabajo, los nuevos trabajos o sesiones de cuaderno de Spark usarán las bibliotecas de grupos actualizadas. 

> [!IMPORTANT]
> - Si el paquete que va a instalar es de gran tamaño o tarda mucho tiempo en instalarse, afectará al tiempo de inicio de la instancia de Spark.
> - No se admite la modificación de la versión de PySpark, Python, Scala/Java, .NET o Spark.

#### <a name="manage-packages-from-synapse-studio-or-azure-portal"></a>Administración de paquetes desde Synapse Studio o Azure Portal
Las bibliotecas de grupos de Spark se pueden administrar desde Synapse Studio o Azure Portal. 

Para actualizar o agregar bibliotecas a un grupo de Spark:
1. Navegue hasta el área de trabajo de Azure Synapse Analytics desde Azure Portal.

    Si va a actualizar desde **Azure Portal**:

    - En la sección **Synapse resources** (Recursos de Synapse), seleccione la pestaña **Apache Spark pools** y seleccione un grupo de Spark en la lista.
     
    - Seleccione **Paquetes** en la sección **Configuración** del grupo de Spark.
  
    ![Captura de pantalla que resalta el botón de carga de un archivo de configuración de entorno.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Adición de bibliotecas de Python")
   
    Si va a actualizar desde **Synapse Studio**:
    - Seleccione **Administrar** en el panel de navegación principal y, a continuación, seleccione **Grupos de Apache Spark**.

    - Seleccione la sección **Paquetes** de un grupo de Spark específico.
    ![Captura de pantalla en la que se resalta la opción para cargar un archivo de configuración de entorno en Synapse Studio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Adición de bibliotecas de Python desde Synapse Studio")
   
2. Si quiere agregar archivos JAR, vaya a la sección **Paquetes de área de trabajo** para agregarlos al grupo. 
3. Después de guardar los cambios, se desencadenará un trabajo del sistema para instalar y almacenar en caché las bibliotecas especificadas. Este proceso ayuda a reducir el tiempo general de inicio de sesión. 
4. Una vez que el trabajo se haya completado correctamente, todas las sesiones nuevas seleccionarán las bibliotecas de grupos actualizadas.

> [!IMPORTANT]
> Al seleccionar la opción para **forzar configuración nueva**, finalizarán todas las sesiones actuales del grupo de Spark seleccionado. Una vez finalizadas las sesiones, tendrá que esperar a que el grupo se reinicie. 
>
> Si esta opción no está seleccionada, tendrá que esperar a que la sesión de Spark actual finalice o detenerla manualmente. Una vez finalizada la sesión, deberá dejar que el grupo se reinicie.

#### <a name="track-installation-progress-preview"></a>Seguimiento del progreso de la instalación (versión preliminar)
Cada vez que se actualiza un grupo con un nuevo conjunto de bibliotecas, se inicia un trabajo de Spark reservado para el sistema. Este trabajo de Spark ayuda a supervisar el estado de la instalación de las bibliotecas. Si se produce un error en la instalación debido a conflictos de la biblioteca u otros problemas, el grupo de Spark volverá a su estado anterior o predeterminado. 

Además, los usuarios pueden inspeccionar los registros de instalación para identificar conflictos de dependencia o ver qué bibliotecas se instalaron durante la actualización del grupo.

Para consultar estos registros:
1. Vaya a la lista de aplicaciones de Spark en la pestaña **Supervisar**. 
2. Seleccione el trabajo del sistema de la aplicación de Spark que corresponde a la actualización del grupo. Estos trabajos del sistema se ejecutan con el nombre *SystemReservedJob-LibraryManagement*.
   ![Captura de pantalla que resalta el trabajo de biblioteca reservado para el sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Visualización del trabajo de biblioteca del sistema")
3. Cambie para ver los registros del **controlador** y de **stdout**. 
4. En los resultados, verá los registros relacionados con la instalación de los paquetes.
    ![Captura de pantalla que resalta los resultados del trabajo de biblioteca reservado para el sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Visualización del progreso del trabajo de biblioteca del sistema")

## <a name="session-scoped-libraries"></a>Bibliotecas de ámbito de sesión 
Además de las bibliotecas de nivel de grupo, también puede especificar bibliotecas de ámbito de sesión al comienzo de una sesión de cuaderno.  Las bibliotecas de ámbito de sesión permiten especificar y usar paquetes JAR exclusivamente en una sesión de cuaderno. 

Cuando se usan bibliotecas de ámbito de sesión, es importante tener en cuenta lo siguiente:
   - Al instalar las bibliotecas de ámbito de sesión, solo el cuaderno actual tiene acceso a las bibliotecas especificadas. 
   - Estas bibliotecas no afectarán a otras sesiones o trabajos que usen el mismo grupo de Spark. 
   - Las bibliotecas se instalan sobre las bibliotecas del entorno de ejecución base y de nivel de grupo. 
   - Las bibliotecas de cuadernos tienen la prioridad más alta.

Para especificar paquetes de Java o Scala de ámbito de sesión, puede usar la opción ```%%configure```:

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

Se recomienda ejecutar el comando %%configure al principio del cuaderno. Puede consultar este [documento](https://github.com/cloudera/livy#request-body) para obtener la lista completa de parámetros válidos.

## <a name="next-steps"></a>Pasos siguientes
- Visualización de las bibliotecas predeterminadas: [Compatibilidad de las versiones de Azure Spark](apache-spark-version-support.md)
- [Solución de errores de instalación de biblioteca](apache-spark-troubleshoot-library-errors.md)
