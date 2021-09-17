---
title: Detalles del grupo de Apache Spark
description: Introducción a los tamaños y configuraciones del grupo de Apache Spark en Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 08/19/2021
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: c674813aee7702e9887e909e4a8baf7ace074a2c
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597650"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Configuraciones del grupo de Apache Spark en Azure Synapse Analytics

Un grupo de Spark es un conjunto de metadatos que define los requisitos de recursos de proceso y las características de comportamiento asociadas cuando se crean instancias de una instancia de Spark. Estas características incluyen, entre otras, el nombre, el número de nodos, el tamaño del nodo, el comportamiento de escalado y el período de vida. Un grupo de Spark no consume ningún recurso por sí mismo. La creación de grupos de Spark no conlleva ningún costo. Solo se incurre en cargos una vez que se ejecuta un trabajo de Spark en el grupo de Spark de destino y se crean instancias de la instancia de Spark a petición.

Puede consultar cómo crear un grupo de Spark y ver todas sus propiedades en [Introducción a los grupos de Spark en Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md).

## <a name="isolated-compute"></a>Proceso aislado

La opción de proceso aislado reporta una mayor seguridad a los recursos de proceso de Spark procedentes de servicios que no son de confianza, para lo cual dedica el recurso de proceso físico a un solo cliente.
Esta opción es la que mejor funciona en cargas de trabajo que requieren un alto grado de aislamiento de otros clientes por motivos como, por ejemplo, el cumplimiento normativo y de requisitos legales.  
La opción de proceso aislado solamente está disponible con el tamaño de nodo XXXGrande (80 vCPU/504 GB) y en las regiones indicadas abajo.  La opción de proceso aislado se puede habilitar o deshabilitar después de la creación del grupo, aunque puede que sea necesario reiniciar la instancia.  Si tiene previsto habilitar esta característica en el futuro, asegúrese de que el área de trabajo de Synapse se crea en una región donde se admitan procesos aislados.

* Este de EE. UU.
* Oeste de EE. UU. 2
* Centro-sur de EE. UU.
* US Gov: Arizona
* US Gov - Virginia

## <a name="nodes"></a>Nodos

La instancia del grupo de Apache Spark consta de un nodo principal y dos o más nodos de trabajo con un mínimo de tres nodos en una instancia de Spark.  El nodo principal ejecuta servicios de administración adicionales, como Livy, Yarn Resource Manager, Zookeeper y el controlador de Spark.  Todos los nodos ejecutan servicios como Node Agent y Yarn Node Manager. Todos los nodos de trabajo ejecutan el servicio Spark Executor.

## <a name="node-sizes"></a>Tamaño de nodo

Un grupo de Spark puede definirse con tamaños de nodo que van desde un nodo de ejecución pequeño con 8 núcleos virtuales y 64 GB de memoria hasta un nodo de ejecución XXGrande con 64 núcleos virtuales y 432 GB de memoria por nodo. Los tamaños de nodo se pueden modificar después de que se cree el grupo, aunque es posible que sea necesario reiniciar la instancia.

|Size | vCore | Memoria|
|-----|------|-------|
|Small|4|32 GB|
|Media|8|64 GB|
|grande|16|128 GB|
|XGrande|32|256 GB|
|XXGrande|64|432 GB|
|XXXGrande (proceso aislado)|80|504 GB|

## <a name="autoscale"></a>Escalado automático

Los grupos de Apache Spark proporcionan la capacidad de escalar y reducir verticalmente los recursos de proceso en función de la cantidad de actividad.  Cuando se habilita la característica de escalabilidad automática, puede establecer el número mínimo y máximo de nodos que se van a escalar.
Cuando la característica de escalabilidad automática está deshabilitada, el número de nodos establecido permanecerá fijo.  Esta configuración se puede modificar después de que se cree el grupo, aunque es posible que sea necesario reiniciar la instancia.

## <a name="automatic-pause"></a>Pausa automática

La característica de pausa automática libera recursos después de un período de inactividad establecido, lo que reduce el costo general de un grupo de Apache Spark.  El número de minutos de inactividad se puede configurar una vez que se habilita esta característica.  La característica de pausa automática es independiente de la característica de escalabilidad automática. Los recursos se pueden pausar si la escalabilidad automática está habilitada o deshabilitada.  Esta configuración se puede modificar después de que se cree el grupo, aunque es posible que sea necesario reiniciar la instancia.

## <a name="next-steps"></a>Pasos siguientes

* [Azure Synapse Analytics](../index.yml)
* [Documentación de Apache Spark](https://spark.apache.org/docs/2.4.5/)