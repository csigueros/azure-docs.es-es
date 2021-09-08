---
title: Guía para el ajuste de tamaño
description: Planee el tamaño de una implementación de los servicios de datos habilitados para Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 6ac2397823dffd2c31cbe534c8fea1886e84558d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733494"
---
# <a name="sizing-guidance"></a>Guía para el ajuste de tamaño


## <a name="overview-of-sizing-guidance"></a>Introducción a la guía para el ajuste de tamaño

Al planear la implementación de los servicios de datos de Azure Arc, debe planear la cantidad correcta de recursos de proceso, memoria y almacenamiento que se necesitan para ejecutar el controlador de datos de Azure Arc y para la cantidad de instancias administradas de SQL y grupos de servidores de Hiperescala de PostgreSQL.  Como los servicios de datos habilitados para Azure Arc se implementan en Kubernetes, tiene la flexibilidad de agregar capacidad adicional al clúster de Kubernetes a lo largo del tiempo mediante la incorporación de almacenamiento o nodos de ejecución adicionales.  En esta guía se proporcionan instrucciones sobre los requisitos mínimos, además de instrucciones sobre los tamaños recomendados para algunos requisitos comunes.

## <a name="general-sizing-requirements"></a>Requisitos generales de tamaño

> [!NOTE]
> Si no está familiarizado con los conceptos que aparecen en este artículo, puede leer más sobre la [gobernanza de los recursos de Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) y la [notación de tamaño de Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

El número de núcleos debe ser un valor entero mayor o igual que uno.

Al usar la CLI de Azure (az) para la implementación, se deben especificar los valores de memoria como una potencia de dos; es decir, con los sufijos: Ki, Mi o Gi.

Los valores límite siempre deben ser mayores que el valor de la solicitud, si se especifica.

Los valores límite de los núcleos son la métrica facturable en la instancia administrada de SQL y los grupos de servidores de Hiperescala de PostgreSQL.

## <a name="minimum-deployment-requirements"></a>Requisitos mínimos de implementación

Una implementación de servicios de datos habilitados para Azure Arc de tamaño mínimo se debe considerar como el controlador de datos de Azure Arc más una instancia administrada de SQL más un grupo de servidores Hiperescala de PostgreSQL con dos nodos de trabajo.  En el caso de esta configuración, necesita al menos 16 GB de RAM y 4 núcleos de capacidad _disponible_ en el clúster de Kubernetes.  Debe asegurarse de que tiene un tamaño mínimo de nodo de Kubernetes de 8 GB de memoria RAM y 4 núcleos y una capacidad total de 16 GB de RAM disponible entre todos los nodos de Kubernetes.  Por ejemplo, podría tener 1 nodo con 32 GB de memoria RAM y 4 núcleos o bien tener 2 nodos con 16 GB de memoria RAM y 4 núcleos cada uno.

Para detalles sobre el ajuste de tamaño, consulte el artículo [storage-configuration](storage-configuration.md).

## <a name="data-controller-sizing-details"></a>Detalles sobre el ajuste de tamaño del controlador de datos

El controlador de datos es una colección de pods que se implementan en el clúster de Kubernetes para proporcionar una API, el servicio de controlador, el programa previo y las bases de datos y paneles de supervisión.  En esta tabla se describen los valores predeterminados para las solicitudes y límites de memoria y CPU.

|Nombre del pod|Solicitud de CPU|Solicitud de memoria|Límite de CPU|Límite de memoria|Notas|
|---|---|---|---|---|---|
|**bootstrapper**|100 m|100 Mi|200 m|200 Mi||
|**control**|400 m|2 Gi|1800 m|2 Gi||
|**controldb**|200 m|3 Gi|800 m|6 Gi||
|**logsdb**|200 m|1600 Mi|2|1600 Mi||
|**logsui**|100 m|500 Mi|2|2 Gi||
|**metricsdb**|200 m|800 Mi|400 m|2 Gi||
|**metricsdc**|100 m|200 Mi|200 m|300 Mi|Metricsdc es un DaemonSet que se crea en cada uno de los nodos de Kubernetes del clúster.  En esta tabla, los números son _por nodo_. Si establece allowNodeMetricsCollection = false en el archivo del perfil de implementación antes de crear el controlador de datos, no se creará el DaemonSet metricsdc.|
|**metricsui**|20 m|200 Mi|500 m|200 Mi||

Puede invalidad la configuración predeterminada de los pods controldb y control en el archivo de perfil de implementación o en el archivo YAML de datacontroller.  Ejemplo:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

Para detalles sobre el ajuste de tamaño, consulte el artículo [storage-configuration](storage-configuration.md).

## <a name="sql-managed-instance-sizing-details"></a>Detalles de ajuste de tamaño de la instancia administrada de SQL

Cada instancia administrada de SQL debe tener las siguientes solicitudes y límites de recursos mínimos:

|Nivel de servicio|Uso general|Crítico para la empresa (versión preliminar)|
|---|---|---|
|Solicitud de CPU|Mínimo: 1; Máximo: 24; Valor predeterminado: 2|Mínimo: 1; Máximo: ilimitado; Valor predeterminado: 4|
|Límite de CPU|Mínimo: 1; Máximo: 24; Valor predeterminado: 2|Mínimo: 1; Máximo: ilimitado; Valor predeterminado: 4|
|Solicitud de memoria|Mínimo: 2 Gi; Máximo: 128 Gi; Valor predeterminado: 4 Gi|Mínimo: 2 Gi; Máximo: ilimitado; Valor predeterminado: 4 Gi|
|Límite de memoria|Mínimo: 2 Gi; Máximo: 128 Gi; Valor predeterminado: 4 Gi|Mínimo: 2 Gi; Máximo: ilimitado; Valor predeterminado: 4 Gi|

Cada pod de instancia administrada de SQL que se crea tiene tres contenedores:

|Nombre del contenedor|Solicitud de CPU|Solicitud de memoria|Límite de CPU|Límite de memoria|Notas|
|---|---|---|---|---|---|
|fluentbit|100 m|100 Mi|No especificado|No especificado|Las solicitudes de recursos de contenedor de fluentbit son _adicionales a_ las solicitudes especificadas para la instancia administrada de SQL.|
|arc-sqlmi|Especificado o no especificado por el usuario.|Especificado o no especificado por el usuario.|Especificado o no especificado por el usuario.|Especificado o no especificado por el usuario.|
|collectd|No especificado|No especificado|No especificado|No especificado|

El tamaño de volumen predeterminado para todos los volúmenes persistentes es 5 Gi.

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>Detalles de ajuste de tamaño del grupo de servidores de Hiperescala de PostgreSQL

Cada nodo del grupo de servidores de Hiperescala de PostgreSQL debe tener estas solicitudes de recursos mínimas:
- Memoria: 256 Mi
- Núcleos: 1

Cada pod de trabajo o de coordinación del grupo de servidores de Hiperescala de PostgreSQL que se crea tiene tres contenedores:

|Nombre del contenedor|Solicitud de CPU|Solicitud de memoria|Límite de CPU|Límite de memoria|Notas|
|---|---|---|---|---|---|
|fluentbit|100 m|100 Mi|No especificado|No especificado|Las solicitudes de recursos de contenedor de fluentbit son _adicionales a_ las solicitudes especificadas para los nodos del grupo de servidores de Hiperescala de PostgreSQL.|
|postgres|Especificado o no especificado por el usuario.|Especificado por el usuario o 256 Mi (valor predeterminado).|Especificado o no especificado por el usuario.|Especificado o no especificado por el usuario.||
|telegraf|No especificado|No especificado|No especificado|No especificado||

## <a name="cumulative-sizing"></a>Ajuste de tamaño acumulado

El tamaño total necesario de un entorno para los servicios de datos habilitados para Azure Arc es principalmente una función del número y el tamaño de las instancias de base de datos que se van a crear.  Puede ser difícil predecir con anticipación el tamaño total sabiendo que el número de instancias crecerá y disminuirá y que la cantidad de recursos necesarios para cada instancia de base de datos cambiará.

El tamaño de línea base de un entorno de servicios de datos habilitados para Azure Arc determinado es el tamaño del controlador de datos, que requiere 4 núcleos y 16 GB de RAM.  Desde ahí, puede agregar el total acumulado de núcleos y memoria que se requieren para las instancias de base de datos.  En el caso de la instancia administrada de SQL, la cantidad de pods es igual al número de instancias administradas de SQL que se crean.  En el caso de los grupos de servidores de Hiperescala de PostgreSQL, la cantidad de pods es igual al número de nodos de trabajo más uno para el nodo de coordinación.  Por ejemplo, si tiene un grupo de servidores de PostgreSQL con 3 nodos de trabajo, el número total de pods será 4.

Además de los núcleos y la memoria que solicita para cada instancia de base de datos, debe sumar 250 m de núcleos y 250 Mi de RAM para los contenedores del agente.

A continuación se muestra un cálculo de ajuste de tamaño de ejemplo.

Requisitos:

- **"SQL1"** : 1 instancia administrada de SQL con 16 GB de RAM, 4 núcleos
- **"SQL2"** : 1 instancia administrada de SQL con 256 GB de RAM, 16 núcleos
- **"Postgres1"** : 1 grupo de servidores de Hiperescala de PostgreSQL con 4 nodos de trabajo a 12 GB de RAM, 4 núcleos

Cálculos de ajuste de tamaño:

- El tamaño de "SQL1" es: 1 pod * ([16 Gi de RAM, 4 núcleos] + [250 Mi de RAM, 250 m núcleos]) para los agentes por pod = 16,25 Gi de RAM, 4,25 núcleos.
- El tamaño de "SQL2" es: 1 pod * ([256 Gi de RAM, 16 núcleos] + [250 Mi de RAM, 250 m núcleos]) para los agentes por pod = 256,25 Gi de RAM, 16,25 núcleos.
- El tamaño total de SQL 1 y SQL 2 es: (16,25 GB + 256,25 Gi) = 272,5 GB de RAM, (4,25 núcleos + 16,25 núcleos) = 20,5 núcleos.

- El tamaño de "Postgres1" es: (4 pods de trabajo + 1 pod de coordinación) * ([12 GB de RAM, 4 núcleos] + [250 Mi de RAM, 250 m núcleos]) para los agentes por pod = 61,25 GB de RAM, 21,25 núcleos.

- La capacidad total que se necesita para las instancias de base de datos es: 272,5 GB de RAM, 20,5 núcleos para SQL + 61,25 GB de RAM, 21,25 núcleos para Hiperescala de PostgreSQL = 333,75 GB de RAM, 42,5 núcleos.

- La capacidad total que se necesita para las instancias de base de datos más el controlador de datos es: 333,75 GB de RAM, 42,5 núcleos para las instancias de base de datos + 16 GB de RAM, 4 núcleos para el controlador de datos = 349,75 GB de RAM, 46,5 núcleos.

Para detalles sobre el ajuste de tamaño, consulte el artículo [storage-configuration](storage-configuration.md).

## <a name="other-considerations"></a>Otras consideraciones

Tenga en cuenta que la solicitud de tamaño de una instancia de base de datos determinada para núcleos o RAM no puede superar la capacidad disponible de los nodos de Kubernetes en el clúster.  Por ejemplo, si el nodo de Kubernetes más grande que tiene en el clúster de Kubernetes tiene 256 GB de RAM y 24 núcleos, no podrá crear una instancia de base de datos con una solicitud de 512 GB de RAM y 48 núcleos.  

Se recomienda mantener al menos el 25 % de la capacidad disponible en todos los nodos de Kubernetes para permitir que Kubernetes programe de manera eficaz los pods que se van a crear y para permitir el escalado elástico, las actualizaciones graduales de los nodos de Kubernetes y el crecimiento a largo plazo a petición.

En los cálculos de dimensionamiento, no olvide sumar los requisitos de recursos de los pods del sistema de Kubernetes y cualquier otra carga de trabajo que pueda estar compartiendo capacidad con los servicios de datos habilitados para Azure Arc en el mismo clúster de Kubernetes.

Para mantener la alta disponibilidad durante el mantenimiento planeado y la continuidad ante desastres, debe planear que al menos uno de los nodos de Kubernetes en el clúster no estará disponible en un momento determinado.  Kubernetes intentará volver a programar los pods que se ejecutaban en un nodo determinado que se retiró para realizar mantenimiento o debido a un error.  Si no hay ninguna capacidad disponible en el resto de los nodos, estos pods no se volverán a programar para su creación hasta que haya capacidad disponible de nuevo.  Tenga especial cuidado con las instancias de base de datos de gran tamaño.  Por ejemplo, si solo hay un nodo de Kubernetes lo suficientemente grande como para cumplir con los requisitos de recursos de una instancia de base de datos de gran tamaño y ese nodo presenta un error, Kubernetes no podrá programar el pod de dicha instancia de base de datos en otro nodo de Kubernetes.

Tenga en cuenta los [límites máximos para un tamaño de clúster de Kubernetes](https://kubernetes.io/docs/setup/best-practices/cluster-large/).

Es posible que el administrador de Kubernetes haya configurado [cuotas de recursos](https://kubernetes.io/docs/concepts/policy/resource-quotas/) en su espacio de nombre o proyecto.  Téngalos en cuenta al planear los tamaños de instancia de base de datos.
