---
title: Creación de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc desde Azure Portal
description: Creación de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc desde Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 382e4e855b0a4925cfaae2f7746587a7ab2fd951
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750319"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-the-azure-portal"></a>Creación de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc desde Azure Portal

En este documento se describen los pasos para crear un grupo de servidores de Hiperescala de PostgreSQL en Azure Arc desde Azure Portal.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Introducción
Si ya está familiarizado con los temas siguientes, puede omitir este párrafo.
Hay temas importantes que puede que le interese leer antes de continuar con la creación:
- [Información general sobre los servicios de datos habilitados para Azure Arc](overview.md)
- [Modos de conectividad y requisitos](connectivity.md)
- [Conceptos de almacenamiento de Kubernetes y configuración de almacenamiento](storage-configuration.md)
- [Modelo de recursos de Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Si prefiere probar las cosas sin aprovisionar un entorno completo por su cuenta, empiece a trabajar rápidamente con [Azure Arc JumpStart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) en Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o en una máquina virtual de Azure.


## <a name="deploy-an-arc-data-controller-configured-to-use-the-direct-connectivity-mode"></a>Implementación de un controlador de datos de Arc configurado para usar el modo de conectividad directa

Requisito: antes de implementar un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc que se controlará desde Azure Portal, primero debe implementar un controlador de datos de Azure Arc configurado para usar el modo de conectividad *Directa*.
Para implementar un controlador de datos de Arc, complete las instrucciones de estos artículos:
1. [Implementación del controlador de datos: modo de conexión directa (requisitos previos)](create-data-controller-direct-prerequisites.md)
1. [Implementación del controlador de datos de Azure Arc en modo de conexión directa desde Azure Portal](create-data-controller-direct-azure-portal.md)


## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Paso preliminar y temporal solo para usuarios de OpenShift
Implemente este paso antes de pasar al siguiente. Para implementar el grupo de servidores Hiperescala de PostgreSQL en Red Hat OpenShift en un proyecto distinto del predeterminado, debe ejecutar los comandos siguientes en el clúster para actualizar las restricciones de seguridad. Este comando concede los privilegios necesarios a las cuentas de servicio que ejecutarán el grupo de servidores Hiperescala de PostgreSQL. La restricción de contexto de seguridad (SCC) arc-data-scc es la que ha agregado al implementar el controlador de datos de Azure Arc.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Server-group-name es el nombre del grupo de servidores que se va a crear en el siguiente paso.**

Para obtener más detalles sobre las SCC en OpenShift, consulte la [documentación de OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html). 

Siga con el paso siguiente.

## <a name="deploy-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-the-azure-portal"></a>Implementación de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc desde Azure Portal

Para implementar y controlar un grupo de servidores de Hiperescala de Postgres habilitada para Azure Arc desde Azure Portal debe implementarlo en un controlador de datos de Arc configurado para usar el modo de conectividad *Directa*. 

> [!IMPORTANT]
> No puede controlar un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc desde Azure Portal si lo ha implementado en un controlador de datos de Azure Arc configurado para usar el modo de conectividad *Indirecta*. 

Después de implementar un controlador de datos de Arc habilitado para el modo de conectividad directa, puede elegir una de las tres opciones siguientes para implementar un grupo de servidores de Hiperescala de Postgres habilitada para Azure Arc:

### <a name="option-1-deploy-from-the-azure-marketplace"></a>Opción 1: Implementación desde Azure Marketplace
1. Abra un explorador en la dirección URL siguiente [https://portal.azure.com](https://portal.azure.com)
2. En la ventana de búsqueda de la parte superior de la página, busque "*azure arc postgres*" en Azure Market Place y seleccione **Grupos de servidores de Azure Database for PostgreSQL: Azure Arc**.
3. En la página que se abre, haga clic en **+ Crear** en la esquina superior izquierda. 
4. Rellene el formulario como si fuera a implementar otro recurso de Azure.

### <a name="option-2-deploy-from-the-azure-database-for-postgresql-deployment-option-page"></a>Opción 2: Implementación desde la página de la opción de implementación de Azure Database for PostgreSQL
1. Abra un explorador en la dirección URL siguiente https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer.
2. Haga clic en el icono de la parte inferior derecha. Se titula: Hiperescala de PostgreSQL habilitada para Azure Arc (versión preliminar).
3. Rellene el formulario como si fuera a implementar otro recurso de Azure.

### <a name="option-3-deploy-from-the-azure-arc-center"></a>Opción 3: Implementación desde el centro de Azure Arc
1. Abra un explorador en la dirección URL siguiente https://ms.portal.azure.com/#blade/Microsoft_Azure_HybridCompute/AzureArcCenterBlade/overview
1. En el centro de la página, haga clic en [Implementar] en el icono titulado *Implemente los servicios de Azure* y, a continuación, haga clic en [Implementar] en el icono titulado Hiperescala de PostgreSQL (versión preliminar).
2. o bien, en el panel de navegación de la izquierda de la página, en la sección Servicios, haga clic en [Hiperescala de PostgreSQL (versión preliminar)] y, a continuación, haga clic en [+ Crear] en la parte superior izquierda del panel.


#### <a name="important-parameters-you-should-consider"></a>Parámetros importantes que debe tener en cuenta:

- **el número de nodos de trabajo** que quiere implementar para escalar horizontalmente y lograr mejores rendimientos; Antes de continuar, lea los [conceptos sobre Hiperescala de Postgres](concepts-distributed-postgres-hyperscale.md). En la tabla siguiente se indica el intervalo de valores admitidos y la forma de implementación de Postgres que obtiene con ellos. Por ejemplo, si quiere implementar un grupo de servidores con 2 nodos de trabajo, indique 2. Se crean tres pods, uno para el nodo o la instancia de coordinación y dos para los nodos o las instancias de trabajo (uno para cada uno de los trabajos).



|Necesita:   |Forma del grupo de servidores que se va a implementar   |Número de nodos de trabajo que se deben indicar   |Nota   |
|---|---|---|---|
|Un formulario de Postgres escalado horizontalmente para satisfacer las necesidades de escalabilidad de las aplicaciones.   |3 o más instancias de Postgres, 1 es coordinador, n son trabajos con n >=2.   |n, con n>=2.   |Se carga la extensión Citus que proporciona la funcionalidad Hiperescala.   |
|Un formulario básico de Hiperescala de Postgres para que realice la validación funcional de la aplicación con un costo mínimo. No es válido para la validación de rendimiento y escalabilidad. Para ello, debe usar el tipo de implementaciones descritas anteriormente.   |1 instancia de Postgres que es coordinador y trabajo.   |0 y agregue Citus a la lista de extensiones que se cargarán.   |Se carga la extensión Citus que proporciona la funcionalidad Hiperescala.   |
|Una instancia sencilla de Postgres que está lista para escalar horizontalmente cuando la necesite.   |1 instancia de Postgres. Todavía no es consciente de la semántica para el coordinador y el trabajo. Para escalarla horizontalmente después de la implementación, edite la configuración, aumente el número de nodos de trabajo y distribuya los datos.   |0   |La extensión Citus que proporciona la funcionalidad Hiperescala está presente en la implementación, pero aún no está cargada.   |
|   |   |   |   |

Aunque indica que 1 trabajo funciona, no se recomienda su uso. Esta implementación no le proporcionará mucho valor. Con ella, se obtienen 2 instancias de Postgres: 1 coordinador y 1 trabajador. Con esta configuración, realmente no se escalan horizontalmente los datos desde que se implementa un único trabajo. Por lo tanto, no verá un mayor nivel de rendimiento y escalabilidad. Quitaremos la compatibilidad de esta implementación en una versión futura.

- **las clases de almacenamiento** que quiere que use el grupo de servidores. Es importante que establezca la clase de almacenamiento justo en el momento de implementar un grupo de servidores, ya que no se puede cambiar después. Si va a cambiar la clase de almacenamiento después de la implementación, deberá extraer los datos, eliminar el grupo de servidores, crear otro grupo de servidores e importar los datos. Puede especificar las clases de almacenamiento que se usarán para los datos, los registros y las copias de seguridad. De forma predeterminada, si no indica las clases de almacenamiento, se usarán las clases de almacenamiento del controlador de datos.
    - Para establecer la clase de almacenamiento para los datos, indique el parámetro `--storage-class-data` o `-scd` seguido del nombre de la clase de almacenamiento.
    - Para establecer la clase de almacenamiento para los registros, indique el parámetro `--storage-class-logs` o `-scl` seguido del nombre de la clase de almacenamiento.
    - Para establecer la clase de almacenamiento para las copias de seguridad: en esta versión preliminar de Hiperescala de PostgreSQL habilitada para Azure Arc hay dos maneras de establecer las clases de almacenamiento, en función de los tipos de operaciones de copia de seguridad o restauración que quiera realizar. Estamos trabajando para simplificar esta experiencia. Indicará una clase de almacenamiento o un montaje de notificación de volumen. Un montaje de notificación de volumen es un par formado por una notificación de volumen persistente existente (en el mismo espacio de nombres) y el tipo de volumen (y metadatos opcionales según el tipo de volumen) separados por dos puntos. El volumen persistente se montará en cada pod del grupo de servidores de PostgreSQL.
        - Si quiere planear solo restauraciones completas de bases de datos, establezca el parámetro `--storage-class-backups` o `-scb` seguido del nombre de la clase de almacenamiento.
        - Si planea realizar restauraciones completas de la base de datos y restauraciones a un momento dado, establezca el parámetro `--volume-claim-mounts` o `--volume-claim-mounts` seguido del nombre de una notificación de volumen y un tipo de volumen.


## <a name="next-steps"></a>Pasos siguientes

- Conéctese a la instancia de Hiperescala de PostgreSQL habilitada para Azure Arc: lea [Obtención de puntos de conexión y cadenas de conexión](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
- Lea los conceptos y las guías paso a paso de Hiperescala de Azure Database for PostgreSQL para distribuir los datos entre varios nodos de Hiperescala de PostgreSQL y poder beneficiarse de posibles mejores rendimientos:
    * [Nodos y tablas](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinar el tipo de aplicación](../../postgresql/concepts-hyperscale-app-type.md)
    * [Elección de una columna de distribución](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocación de tabla](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribución y modificación de tablas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Diseño de una base de datos multiinquilino](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Diseño de un panel de análisis en tiempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* En los documentos anteriores, omita las secciones **Inicio de sesión en Azure Portal** y **Creación de una instancia de Azure Database for PostgreSQL: Hiperescala (Citus)** . Implemente los pasos restantes en la implementación de Azure Arc. Esas secciones son específicas de Hiperescala (Citus) de Azure Database for PostgreSQL que se ofrece como un servicio PaaS en la nube de Azure, pero las demás partes de los documentos se aplican directamente a Hiperescala de PostgreSQL habilitada para Azure Arc.

- [Escalado horizontal del grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc](scale-out-in-postgresql-hyperscale-server-group.md)
- [Conceptos de almacenamiento de Kubernetes y configuración de almacenamiento](storage-configuration.md)
- [Expansión de notificaciones de volúmenes persistentes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Modelo de recursos de Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)


