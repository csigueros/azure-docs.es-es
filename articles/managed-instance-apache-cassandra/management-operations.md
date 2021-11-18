---
title: Operaciones de administración en Azure Managed Instance for Apache Cassandra
description: Obtenga información sobre las operaciones de administración compatibles con Azure Managed Instance for Apache Cassandra. También se explica la separación de responsabilidades entre el equipo de soporte técnico de Azure y los clientes al mantener clústeres independientes e híbridos.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 53e454ba84747c0e5fdeb009bc9f53e51480be9d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715271"
---
# <a name="management-operations-in-azure-managed-instance-for-apache-cassandra"></a>Operaciones de administración en Azure Managed Instance for Apache Cassandra

Azure Managed Instance for Apache Cassandra proporciona operaciones de implementación y escalado automatizadas para los centros de datos administrados de código abierto de Apache Cassandra. En este artículo se definen las operaciones de administración y las características que proporciona el servicio. También se explica la separación de responsabilidades entre el equipo de soporte técnico de Azure y los clientes al mantener clústeres independientes e [híbridos](configure-hybrid-cluster.md).

## <a name="patching"></a>Aplicación de revisiones

* Las revisiones de nivel de sistema operativo se realizan automáticamente con una frecuencia de aproximadamente 2 semanas.

* Las revisiones de nivel de software de Apache Cassandra se realizan cuando se identifican vulnerabilidades de seguridad. Tenga en cuenta que la frecuencia de aplicación de las revisiones puede variar.

* Durante la aplicación de revisiones, las máquinas se reinician de bastidor en bastidor. No debería experimentar ninguna degradación en el lado de la aplicación, siempre y cuando **no se esté utilizando la opción de cuórum ALL** Y el factor de replicación sea **3 o superior**.

* La versión de Apache Cassandra tiene el formato `X.Y.Z`. Puede controlar la implementación de las versiones principales (X) y secundarias (Y) manualmente a través de las herramientas de servicio. Mientras que las revisiones de Cassandra (Z) que pueden ser necesarias para esa combinación de versión principal y secundaria, estas se realizan automáticamente.  

## <a name="maintenance"></a>Mantenimiento

* El servicio ejecuta automáticamente la [reparación de Nodetool](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/tools/toolsRepair.html) mediante la herramienta [Reaper](http://cassandra-reaper.io/). Esta herramienta se ejecuta una vez cada semana. Puede deshabilitarla si usa su propio servicio para realizar una [implementación híbrida](configure-hybrid-cluster.md).

* La supervisión del estado del nodo consta de:

  * Supervisar activamente la pertenencia de cada nodo en el anillo de Cassandra.
  * Supervisión activa de máquinas virtuales para identificar y corregir problemas con Azure, Virtual Machines, Storage, Linux y el software de soporte técnico.

* Recuerde que usted es el responsable de los problemas de uso de la CPU, el disco y la red, que pueden surgir debido al uso de Cassandra. Algunos ejemplos de estos problemas son:

  * Operaciones de consulta ineficaces.
  * Rendimiento que supera la capacidad.
  * Ingesta de datos que superan la capacidad de almacenamiento.
  * Valores de configuración de espacio de claves incorrectos.
  * Modelo de datos deficiente o estrategia de claves de partición.

## <a name="security"></a>Seguridad

Azure Managed Instance for Apache Cassandra proporciona muchos controles y características de seguridad explícitos integrados:

* Imágenes de máquinas virtuales Linux con una cadena de suministro controlada.
* Supervisión común de vulnerabilidades y exposición (CVE) en el nivel de sistema operativo.
* Rotación de certificados para el software Apache Cassandra y Prometheus hospedados en la instancia de Virtual Machines administrada.
* Examen de vulnerabilidades activo.
* Examen de virus activo.
* Prácticas de codificación seguras.

## <a name="hybrid-support"></a>Compatibilidad híbrida

Cuando se configura un clúster [híbrido](configure-hybrid-cluster.md), las operaciones de Reaper automatizadas que se ejecutan en el servicio beneficiarán a todo el clúster. Esto incluye los centros de datos que no aprovisione el servicio. A parte de esto, recuerde que es su responsabilidad mantener el centro de datos local u hospedado externamente.

## <a name="next-steps"></a>Pasos siguientes

Comience con uno de nuestros inicios rápidos:
* [Creación de un clúster de instancia administrada desde Azure Portal](create-cluster-portal.md)
* [Implementación de un clúster de Apache Spark administrado con Azure Databricks](deploy-cluster-databricks.md)
* [Administración de recursos de Azure Managed Instance for Apache Cassandra mediante la CLI de Azure](manage-resources-cli.md)
