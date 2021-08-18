---
title: 'Introducción al control de versiones: Azure HDInsight'
description: Obtenga información sobre cómo funciona el control de versiones en Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: b7b23a1d7e549d5e1e5b712d2290722158d49f38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722292"
---
# <a name="how-versioning-works-in-hdinsight"></a>Funcionamiento del control de versiones en Azure HDInsight

El servicio HDInsight tiene dos componentes principales: un proveedor de recursos y componentes de Apache Hadoop que se implementan en un clúster. 

## <a name="hdinsight-resource-provider"></a>Proveedor de recursos de HDInsight

Los recursos de Azure están disponibles mediante un proveedor de recursos. El proveedor de recursos de HDInsight es responsable de la creación, administración y eliminación de clústeres.

## <a name="hdinsight-images"></a>Imágenes de HDInsight

HDInsight usa imágenes para reunir componentes de software de código abierto (OSS) que se pueden implementar en un clúster. Estas imágenes contienen el sistema operativo Ubuntu de base y los componentes principales como Spark, Hadoop, Kafka, HBase o Hive.

## <a name="versioning-in-hdinsight"></a>Control de versiones en HDInsight

Microsoft actualiza periódicamente las imágenes y el proveedor de recursos de HDInsight para incluir nuevas mejoras y características.

Se puede crear una nueva versión de HDInsight cuando se cumplen una o varias de las siguientes condiciones:

- Cambios o actualizaciones importantes de la funcionalidad del proveedor de recursos de HDInsight
- Versiones principales de los componentes de software de código abierto
- Cambios importantes en el sistema operativo Ubuntu

Se crea una nueva versión de la imagen cuando se cumplen una o varias de las siguientes condiciones:

- Versiones principales o secundarias y actualizaciones de los componentes de software de código abierto
- Revisiones o correcciones de un componente de la imagen

## <a name="next-steps"></a>Pasos siguientes

- [Versiones de Azure HDInsight](./hdinsight-component-versioning.md)
