---
title: Machine Learning Services en Azure SQL Managed Instance
description: En este artículo se proporciona información general acerca de Machine Learning Services en Instancia administrada de Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: mathoma, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: 0d24da8044cc4306f65f235a85092593610e33f7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751223"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Machine Learning Services en Azure SQL Managed Instance

Machine Learning Services es una característica de Instancia administrada de Azure SQL que proporciona aprendizaje automático en la base de datos, con compatibilidad con scripts de Python y R. Incluye paquetes de R y de Python de Microsoft para el análisis predictivos de alto rendimiento y el aprendizaje automático. Los datos relacionales pueden usarse en scripts mediante procedimientos almacenados, scripts de T-SQL que contienen instrucciones en R o Python o código R o Python que contiene T-SQL.

## <a name="what-is-machine-learning-services"></a>¿Qué es Machine Learning Services?

Machine Learning Services en Instancia administrada de Azure SQL permite ejecutar scripts de Python y R en la base de datos. Se puede usar para preparar y limpiar los datos, realizar ingeniería de características, y entrenar, evaluar e implementar modelos de aprendizaje automático en una base de datos. La característica ejecuta los scripts donde residen los datos y elimina la transferencia de los datos a otro servidor a través de la red.

Use Machine Learning Services con compatibilidad con R/Python en Instancia administrada de Azure SQL para:

- **Ejecutar scripts de R y Python para la preparación de datos y el procesamiento de datos de uso general**: ahora puede traer scripts de R/Python a Instancia administrada de Azure SQL donde residen los datos, en lugar de tener que trasladar los datos a otro servidor para ejecutar scripts de R y Python. Ya no será necesario el movimiento de datos, con lo que se evitarán problemas relacionados con la latencia, la seguridad y el cumplimiento.

- **Entrenar modelos de aprendizaje automático en la base de datos**: puede entrenar modelos con cualquier algoritmo de código abierto. Puede escalar fácilmente el entrenamiento en todo el conjunto de datos, en lugar de recurrir a conjuntos de datos de ejemplo extraídos de la base de datos.

- **Implementar los modelos y los scripts en producción en procedimientos almacenados**: los scripts y los modelos entrenados se pueden operacionalizar simplemente mediante su inserción en procedimientos almacenados de T-SQL. Las aplicaciones que se conectan a Instancia administrada de Azure SQL pueden aprovechar las predicciones y la inteligencia de estos modelos mediante una llamada a un procedimiento almacenado. También puede usar la función de PREDICT nativa de T-SQL para operacionalizar modelos con el fin de obtener una puntuación rápida en escenarios de puntuación en tiempo real sumamente simultáneos.

Machine Learning Services incluye las distribuciones base de Python y R. Se pueden instalar y usar marcos y paquetes de código abierto, como PyTorch, TensorFlow y scikit-learn, además de los paquetes de Microsoft [revoscalepy](/sql/machine-learning/python/ref-py-revoscalepy) y [microsoftml](/sql/machine-learning/python/ref-py-microsoftml) para Python, y [RevoScaleR](/sql/machine-learning/r/ref-r-revoscaler), [MicrosoftML](/sql/machine-learning/r/ref-r-microsoftml), [olapr](/sql/machine-learning/r/ref-r-olapr) y [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils) para R.

## <a name="how-to-enable-machine-learning-services"></a>Habilitación de Machine Learning Services

Para habilitar Machine Learning Services en Azure SQL Managed Instance, habilite la extensibilidad con los siguientes comandos SQL (SQL Managed Instance se reiniciará y no estará disponible durante unos segundos):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Para más información sobre cómo afecta este comando a los recursos de SQL Managed Instance, consulte [Gobernanza de recursos](machine-learning-services-differences.md#resource-governance).

### <a name="enable-machine-learning-services-in-a-failover-group"></a>Habilitación de Machine Learning Services en un grupo de conmutación por error

En un [grupo de conmutación por error](failover-group-add-instance-tutorial.md), las bases de datos del sistema no se replican en la instancia secundaria (consulte [Limitaciones de los grupos de conmutación por error](../database/auto-failover-group-overview.md#limitations-of-failover-groups) para más información).

Si la instancia administrada que está usando forma parte de un grupo de conmutación por error, haga lo siguiente:

- Ejecute los comandos `sp_configure` y `RECONFIGURE` en cada instancia del grupo de conmutación por error para habilitar Machine Learning Services.

- Instale las bibliotecas de R/Python en una base de datos de usuario en lugar de en la base de datos maestra.

## <a name="next-steps"></a>Pasos siguientes

- Consulte las [principales diferencias con Machine Learning Services en SQL Server](machine-learning-services-differences.md).
- Para obtener información sobre cómo usar Python en Machine Learning Services, vea [Ejecución de scripts de Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Para obtener información sobre cómo usar R en Machine Learning Services, vea [Ejecución de scripts de R](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Para obtener más información sobre el aprendizaje automático en otras plataformas de SQL, consulte la [documentación del aprendizaje automático de SQL](/sql/machine-learning/index).
