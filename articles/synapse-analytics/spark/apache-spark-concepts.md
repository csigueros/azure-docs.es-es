---
title: Conceptos básicos de Apache Spark
description: Introducción a los conceptos básicos de Apache Spark en Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 1ca0fd208ed739118708630a80b4829ce7519704
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715746"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Conceptos básicos de Apache Spark en Azure Synapse Analytics

Apache Spark es una plataforma de procesamiento paralelo que admite el procesamiento en memoria para mejorar el rendimiento de aplicaciones de análisis de macrodatos. Apache Spark en Azure Synapse Analytics es una de las implementaciones de Microsoft de Apache Spark en la nube. 

Azure Synapse facilita la creación y configuración de funcionalidades de Spark en Azure. Azure Synapse proporciona una implementación diferente de las funcionalidades de Spark que se documentan aquí.

## <a name="spark-pools"></a>Grupos de Spark

Se crea un grupo de Apache Spark sin servidor en Azure Portal. Es la definición de un grupo de Spark que, cuando se crean instancias, se utiliza para crear una instancia de Spark que procesa datos. Cuando se crea un grupo de Spark, solo existe como metadatos; no se consumen, ejecutan ni cobran recursos. Un grupo de Spark tiene una serie de propiedades que controlan las características de una instancia de Spark. Estas características incluyen, entre otras, el nombre, el tamaño, el comportamiento de escalado y el período de vida.

Dado que no hay ningún costo de recursos asociado a la creación de grupos de Spark, se puede crear cualquier cantidad de ellos con cualquier número de configuraciones diferentes. Los permisos también se pueden aplicar a los grupos de Spark, lo que permite a los usuarios acceder a algunos y a otros no.

Un procedimiento recomendado consiste en crear grupos de Spark más pequeños que se puedan usar para el desarrollo y la depuración y, después, otros más grandes para ejecutar cargas de trabajo de producción.

Puede consultar cómo crear un grupo de Spark y ver todas sus propiedades en [Introducción a los grupos de Spark en Azure Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md).

## <a name="spark-instances"></a>Instancias de Spark

Las instancias de Spark se crean al conectarse a un grupo de Spark, crear una sesión y ejecutar un trabajo. Como varios usuarios pueden acceder a un solo grupo de Spark, se crea una nueva instancia de Spark para cada usuario que se conecta. 

Cuando se envía un segundo trabajo, si hay capacidad en el grupo, la instancia de Spark existente también tiene capacidad. A continuación, la instancia existente procesará el trabajo. De lo contrario, si la capacidad está disponible en el nivel de grupo, se creará una nueva instancia de Spark.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1

- Va a crear un grupo de Spark llamado SP1. Tiene un tamaño de clúster fijo de 20 nodos.
- También va a enviar un trabajo de Notebook, J1, que usa 10 nodos, y a crear una instancia de Spark, SI1, para procesar el trabajo.
- Ahora envía otro trabajo, J2, que usa 10 nodos porque todavía hay capacidad en el grupo y la instancia, J2, la procesa SI1.
- Si J2 hubiera solicitado 11 nodos, no habría habido capacidad en SP1 ni en SI1. En este caso, si J2 procede de un cuaderno, se rechazará el trabajo. Si J2 procede de un trabajo por lotes, se pondrá en cola.

### <a name="example-2"></a>Ejemplo 2

- Crea una llamada a un grupo de Spark, SP2. Este tiene un escalado automático habilitado de 10 a 20 nodos.
- También va a enviar un trabajo de Notebook, J1, que usa 10 nodos, y a crear una instancia de Spark, SI1, para procesar el trabajo.
- Ahora envía otro trabajo, J2, que usa 10 nodos porque todavía hay capacidad en el grupo y la instancia crece automáticamente hasta los 20 nodos y procesa a J2.

### <a name="example-3"></a>Ejemplo 3

- Va a crear un grupo de Spark llamado SP1. Tiene un tamaño de clúster fijo de 20 nodos.
- También va a enviar un trabajo de Notebook, J1, que usa 10 nodos, y a crear una instancia de Spark, SI1, para procesar el trabajo.
- Otro usuario, U2, envía un trabajo, J3, que usa 10 nodos y una nueva instancia de Spark, SI2, se crea para procesar el trabajo.
- Ahora va a enviar otro trabajo, J2, que usa 10 nodos porque todavía hay capacidad en el grupo y la instancia, J2, la procesa SI1.

## <a name="quotas-and-resource-constraints-in-apache-spark-for-azure-synapse"></a>Cuotas y restricciones de recursos en Apache Spark para Azure Synapse

### <a name="workspace-level"></a>Nivel de área de trabajo

Cada área de trabajo de Azure Synapse incluye una cuota predeterminada de núcleos virtuales que se puede usar para Spark. La cuota se divide entre la cuota de usuario y la cuota de flujo de trabajo para que ninguno de los patrones de uso utilice los núcleos virtuales del área de trabajo. La cuota es diferente según el tipo de suscripción, pero es simétrica entre el usuario y el flujo de entrada. Sin embargo, si solicita más núcleos virtuales de los que quedan en el área de trabajo, obtendrá el siguiente error:

```console
Failed to start session: [User] MAXIMUM_WORKSPACE_CAPACITY_EXCEEDED
Your Spark job requested 480 vcores.
However, the workspace only has xxx vcores available out of quota of yyy vcores.
Try reducing the numbers of vcores requested or increasing your vcore quota. Click here for more information - https://go.microsoft.com/fwlink/?linkid=213499
```

El vínculo del mensaje apunta a este artículo.

En el siguiente artículo se describe cómo solicitar un aumento en la cuota del área de trabajo del núcleo virtual.

- Seleccione "Azure Synapse Analytics" como el tipo de servicio.
- En la ventana detalles de la cuota, seleccione Apache Spark (núcleo virtual) por área de trabajo.

[Solicitud de un aumento de la cuota estándar desde Ayuda y soporte técnico](../../azure-portal/supportability/per-vm-quota-requests.md)

### <a name="spark-pool-level"></a>Nivel de grupo de Spark

Al definir un grupo de Spark, se define de forma eficaz una cuota por usuario para ese grupo, si se ejecutan varios cuadernos o trabajos, o una combinación de dos, es posible agotar la cuota del grupo. Si lo hace, se generará un mensaje de error similar al siguiente:

```console
Failed to start session: Your Spark job requested xx vcores.
However, the pool is consuming yy vcores out of available zz vcores.Try ending the running job(s) in the pool, reducing the numbers of vcores requested, increasing the pool maximum size or using another pool
```

Para solucionar este problema, debe reducir el uso de los recursos del grupo antes de enviar una nueva solicitud de recursos mediante la ejecución de un cuaderno o un trabajo.

## <a name="next-steps"></a>Pasos siguientes

- [Azure Synapse Analytics](../index.yml)
- [Documentación de Apache Spark](https://spark.apache.org/docs/2.4.5/)