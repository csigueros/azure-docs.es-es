---
title: Administración de paquetes
description: Obtenga información sobre cómo agregar y administrar bibliotecas que usa Apache Spark en Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 4ad54fe95bc646d7ed3c96ad901ae2ba1337ab7b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251345"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Administración de bibliotecas para Apache Spark en Azure Synapse Analytics
Las bibliotecas proporcionan código reutilizable que se puede incluir en los programas o proyectos. 

Es posible que tenga que actualizar el entorno de grupo sin servidor de Apache Spark por varios motivos. Por ejemplo:
- Si se ha publicado una nueva versión de una de sus dependencias principales.
- Si necesita un paquete adicional para entrenar su modelo de aprendizaje automático o preparar sus datos.
- Si ha encontrado un paquete mejor y ya no necesita el paquete anterior.
- Si su equipo ha creado un paquete personalizado y necesita que esté disponible en el grupo de Apache Spark.

A fin de que el código de terceros o de compilación local esté disponible para las aplicaciones, puede instalar una biblioteca en uno de los grupos de Apache Spark sin servidor o en una sesión de cuaderno.
  
## <a name="default-installation"></a>Instalación predeterminada
Apache Spark en Azure Synapse Analytics tiene una instalación completa de Anaconda y otras bibliotecas adicionales. Puede encontrar la lista de bibliotecas completas en [Compatibilidad con las versiones de Apache Spark](apache-spark-version-support.md). 

Cuando se inicie una instancia de Spark, estas bibliotecas se incluirán automáticamente. Se pueden agregar paquetes adicionales en el nivel de sesión o de grupo de Spark.

## <a name="workspace-packages"></a>Paquetes de área de trabajo
A la hora de desarrollar aplicaciones o modelos personalizados, su equipo puede desarrollar varios artefactos de código, como archivos JAR o wheel, para empaquetar el código. 

En Azure Synapse, los paquetes de área de trabajo pueden ser archivos JAR o wheel personalizados o privados. Puede cargar estos paquetes en su área de trabajo y después asignarlos a un grupo de Spark específico. Una vez asignados, estos paquetes de área de trabajo se instalan automáticamente en todas las sesiones del grupo de Spark.

Para obtener más información sobre cómo administrar bibliotecas de área de trabajo, visite las siguientes guías paso a paso:

- [Paquetes de área de trabajo de Python:](./apache-spark-manage-python-packages.md#install-wheel-files) cargue archivos wheel de Python como paquetes de área de trabajo y, después, agregue estos paquetes a grupos de Apache Spark sin servidor específicos.
- [Paquetes de área de trabajo de Scala/Java:](./apache-spark-manage-scala-packages.md#workspace-packages) cargue archivos JAR de Scala y Java como paquetes de área de trabajo y, después, agregue estos paquetes a grupos de Apache Spark sin servidor específicos.

## <a name="pool-packages"></a>Paquetes de grupo
En algunos casos, es posible que quiera estandarizar el conjunto de paquetes que se usan en un grupo específico de Apache Spark. Esta estandarización puede ser útil si varias personas de su equipo instalan normalmente los mismos paquetes. 

Con las funcionalidades de administración de grupos de Azure Synapse Analytics, puede configurar el conjunto predeterminado de bibliotecas que le gustaría instalar en un grupo específico de Apache Spark sin servidor. Estas bibliotecas se instalan sobre el [entorno de ejecución base](./apache-spark-version-support.md). 

Actualmente, la administración de grupos solo es compatible con Python. En Python, los grupos de Synapse Spark usan Conda para instalar y administrar las dependencias de paquetes de Python. A la hora de especificar las bibliotecas de nivel de grupo, ahora puede proporcionar un archivo requirements.txt o un archivo environment.yml. Este archivo de configuración del entorno se usa cada vez que se crea una instancia de Spark desde ese grupo de Spark. 

Para obtener más información sobre estas funcionalidades, consulta la documentación sobre la [administración de grupos de Python](./apache-spark-manage-python-packages.md#pool-libraries).

> [!IMPORTANT]
> - Si el paquete que va a instalar es de gran tamaño o tarda mucho tiempo en instalarse, afectará al tiempo de inicio de la instancia de Spark.
> - No se admite la modificación de la versión de PySpark, Python, Scala/Java, .NET o Spark.
> - No se admite la instalación de paquetes desde PyPI en áreas de trabajo habilitadas para DEP.

## <a name="session-scoped-packages"></a>Paquetes de ámbito de sesión
A menudo, al realizar análisis de datos interactivos o aprendizaje automático, es posible que quiera probar paquetes más recientes o que necesite paquetes que ya no están disponibles en el grupo de Apache Spark. En lugar de actualizar la configuración del grupo, los usuarios ahora pueden usar paquetes de ámbito de sesión para agregar, administrar y actualizar dependencias de sesión.

Los paquetes de ámbito de sesión permiten a los usuarios definir las dependencias de paquetes al inicio de la sesión. Cuando se instala un paquete de ámbito de sesión, solo la sesión actual tiene acceso a los paquetes especificados. Como resultado, estos paquetes de ámbito de sesión no afectarán a otras sesiones u otros trabajos que usen el mismo grupo de Apache Spark. Además, las bibliotecas se instalan sobre el entorno de ejecución base y los paquetes de nivel de grupo. 

Para obtener más información sobre cómo administrar paquetes de ámbito de sesión, consulte las siguientes guías paso a paso:

- [Paquetes de sesión de Python:](./apache-spark-manage-python-packages.md) al inicio de una sesión, proporcione un archivo *environment.yml* de Conda para instalar paquetes de Python adicionales desde repositorios populares. 
- [Paquetes de sesión de Scala/Java:](./apache-spark-manage-scala-packages.md) al inicio de la sesión, proporcione una lista de los archivos JAR que se van a instalar mediante `%%configure`.

## <a name="next-steps"></a>Pasos siguientes
- Visualización de las bibliotecas predeterminadas: [Compatibilidad de las versiones de Azure Spark](apache-spark-version-support.md)
