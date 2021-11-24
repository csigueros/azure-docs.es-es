---
title: Escalabilidad automática de instancias de Apache Spark
description: Uso de la característica de escalabilidad automática de Azure Synapse para escalar automáticamente instancias de Apache Spark
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 03/31/2020
ms.openlocfilehash: 7ca093294cb1782da5adeb02888696b38f57de4c
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400117"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Escalabilidad automática de grupos de Apache Spark de Azure Synapse Analytics

La característica de escalabilidad automática de grupos de Apache Spark para Azure Synapse Analytics escala y reduce verticalmente de forma automática el número de nodos de una instancia de clúster. Durante la creación de un nuevo grupo de Apache Spark para Azure Synapse Analytics, se puede establecer un número mínimo y máximo de nodos cuando se selecciona la escalabilidad automática. La escalabilidad automática luego supervisa los requisitos de recursos de la carga y escala o reduce verticalmente el número de nodos. El uso de esta característica no tiene un cargo adicional.

## <a name="metrics-monitoring"></a>Supervisión de métricas

La escalabilidad automática supervisa continuamente la instancia de Spark y recopila las métricas siguientes:

|Métrica|Descripción|
|---|---|
|Total de CPU pendiente|Número total de núcleos necesarios para iniciar la ejecución de todos los trabajos pendientes.|
|Total de memoria pendiente|Memoria total (en MB) necesaria para iniciar la ejecución de todos los nodos pendientes.|
|Total de CPU libre|La suma de todos los núcleos sin usar en los nodos activos.|
|Total de memoria libre|La suma de la memoria sin usar (en MB) en los nodos activos.|
|Memoria usada por nodo|Carga en un nodo. Un nodo donde se usan 10 GB de memoria se considera bajo más carga que un trabajo con 2 GB de memoria usada.|

Las métricas anteriores se comprueban cada 30 segundos. La escalabilidad automática toma decisiones de escalar o reducir verticalmente según estas métricas.

## <a name="load-based-scale-conditions"></a>Condiciones de escalado basado en la carga

Cuando se detectan las condiciones siguientes, Escalabilidad automática emite una solicitud de escalado:

|Escalabilidad vertical|Reducción vertical|
|---|---|
|El total de CPU pendiente es mayor que el total de CPU libre durante más de 1 minuto.|El total de CPU pendiente es menor que el total de CPU libre durante más de 2 minutos.|
|El total de memoria pendiente es mayor que el total de memoria libre durante más de 1 minuto.|El total de memoria pendiente es menor que el total de memoria libre durante más de 2 minutos.|

Para la escalabilidad vertical, el servicio de escalabilidad automática de Azure Synapse calcula cuántos nodos nuevos se necesitan para cumplir con los requisitos actuales de CPU y memoria y, luego, emite una solicitud de escalabilidad vertical para agregar el número de nodos necesario.

Para la reducción vertical, en función del número de ejecutores, los patrones de aplicación por nodo, la CPU actual y los requisitos de memoria, la escalabilidad automática emite una solicitud para quitar un número determinado de nodos. El servicio detecta también qué nodos son candidatos para la eliminación en función de la ejecución del trabajo actual. En primer lugar, la operación de reducción vertical retira los nodos y, luego, los quita del clúster.

## <a name="get-started"></a>Introducción

### <a name="create-a-serverless-apache-spark-pool-with-autoscaling"></a>Creación de un grupo de Apache Spark sin servidor con la escalabilidad automática

Para habilitar la característica de escalabilidad automática, complete estos pasos como parte del proceso de creación de grupos normal:

1. En la pestaña **Datos básicos**, marque la casilla **Habilitar escalabilidad automática**.
1. Escriba los valores deseados para estas propiedades:  

    * Número **mínimo** de nodos.
    * Número **máximo** de nodos.

El número inicial de nodos será el mínimo. Este valor define el tamaño inicial de la instancia durante su creación. El número mínimo de nodos no puede ser inferior a tres.

También puede habilitar la asignación dinámica de ejecutores en escenarios donde los requisitos del ejecutor sean muy diferentes en las fases de un trabajo de Spark o el volumen de datos procesados fluctúe con el tiempo. Habilitando la asignación dinámica de ejecutores, podemos usar la capacidad según sea necesario.

Si se habilita durante la creación de un grupo de Spark, es posible establecer el número mínimo y máximo de nodos, sujeto a los límites de los nodos disponibles. Estos valores se restablecen a los predeterminados con cada sesión que se crea dentro del grupo.

Apache Spark permite configurar la asignación dinámica de ejecutores mediante el código que se muestra a continuación:

```
    %%configure -f
    {
        "conf" : {
            "spark.dynamicAllocation.maxExecutors" : "6",
            "spark.dynamicAllocation.enable": "true",
            "spark.dynamicAllocation.minExecutors": "2"
     }
    }
```
Los valores predeterminados que se especifican a través del código invalidan los valores establecidos a través de la interfaz de usuario.

Al habilitar la asignación dinámica, los ejecutores se escalan o reducen verticalmente en función del uso que se hace de ellos. De este modo, se garantiza que se aprovisionen de acuerdo con las necesidades del trabajo que se está ejecutando.

## <a name="best-practices"></a>Procedimientos recomendados

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Consideración de la latencia de las operaciones de escalado o reducción verticales

Una operación de escalabilidad puede tardar entre 1 y 5 minutos en completarse.

### <a name="prepare-for-scaling-down"></a>Preparación para la reducción vertical

Durante el proceso de reducción vertical de instancias, la escalabilidad automática pondrá los nodos en estado de retirada para que no se puedan iniciar nuevos ejecutores en ese nodo.

Los trabajos en ejecución se seguirán ejecutando y se completarán. Los trabajos pendientes esperarán ser programados como normales con menos nodos disponibles.

## <a name="next-steps"></a>Pasos siguientes

Guía de inicio rápido para configurar un nuevo grupo de Spark [Creación de un grupo de Spark](../quickstart-create-apache-spark-pool-portal.md)
