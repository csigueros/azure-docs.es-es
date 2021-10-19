---
title: Supervisión y funcionamiento de copias de seguridad con el Centro de copias de seguridad
description: En este artículo se explica cómo supervisar y usar las copias de seguridad a gran escala con el Centro de copias de seguridad.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: cab9e710cfe4bf43b0d225d64e8f64b16c09e3a6
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659856"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Supervisión y funcionamiento de copias de seguridad con el Centro de copias de seguridad

Como administrador de copias de seguridad, puede usar el Centro de copias de seguridad como un panel único para supervisar los trabajos y el inventario de copia de seguridad de manera diaria. También puede usar el Centro de copias de seguridad para realizar las operaciones habituales, como responder a solicitudes de copia de seguridad a petición, restaurar copias de seguridad, crear directivas de copia de seguridad, etc.

## <a name="supported-scenarios"></a>Escenarios admitidos

* El Centro de copias de seguridad es compatible actualmente con copias de seguridad de VM de Azure, SQL en VM de Azure, SAP HANA en VM de Azure, Azure Files y Blobs de Azure, Azure Managed Disks y servidor de Azure Database for PostgreSQL.
* Consulte la [matriz de compatibilidad](backup-center-support-matrix.md) para una lista detallada de los escenarios admitidos y no admitidos.

## <a name="backup-instances"></a>Instancias de copia de seguridad

El Centro de copias de seguridad permite realizar búsquedas y detectabilidad sencillas de las instancias de copia de seguridad en todo el conjunto de copias de seguridad.

Seleccionar la pestaña **Instancias de copia de seguridad** en el Centro de copias de seguridad le permite ver detalles de todas las instancias de copia de seguridad a las que tiene acceso.

 Puede ver la información siguiente sobre cada una de las instancias de copia de seguridad:

* Suscripción del origen de datos
* Grupo de recursos del origen de datos
* Punto de recuperación más reciente
* Almacén asociado con la instancia de copia de seguridad
* Estado de protección

 También puede filtrar la lista de instancias de copia de seguridad según los parámetros siguientes:

* Suscripción del origen de datos
* Grupo de recursos del origen de datos
* Ubicación del origen de datos
* Tipo de origen de datos
* Almacén
* Estado de protección
* Etiquetas del origen de datos

Al hacer clic con el botón derecho en cualquiera de los elementos de la cuadrícula, puede realizar acciones en la instancia de copia de seguridad determinada, como navegar al recurso, desencadenar copias de seguridad y restauraciones a petición o detener la copia de seguridad.

![Centro de copias de seguridad: instancias](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Trabajos de copia de seguridad

El Centro de copias de seguridad le permite ver información detallada sobre todos los trabajos que se crearon en el conjunto de copias de seguridad y tomar las medidas adecuadas para los trabajos con errores.

Al seleccionar el elemento de menú **Trabajos de copia de seguridad** del Centro de copias de seguridad, puede ver todos los trabajos. Cada trabajo contiene la información siguiente:

* Instancia de copia de seguridad asociada con el trabajo
* Suscripción del origen de datos
* Grupo de recursos del origen de datos
* Ubicación del origen de datos
* Operación del trabajo
* Estado del trabajo
* Hora de inicio del trabajo
* Duración del trabajo

La selección de un elemento en la cuadrícula le permite ver más detalles sobre el trabajo determinado. Al hacer clic con el botón derecho en un elemento, puede desplazarse hasta el recurso para llevar a cabo las medidas necesarias.

![Centro de copias de seguridad: trabajos](./media/backup-center-monitor-operate/backup-center-jobs.png)

En la pestaña **Trabajos de copia de seguridad**, puede ver trabajos de hasta siete días atrás. Para ver trabajos anteriores, use [Informes de Backup](backup-center-obtain-insights.md).

## <a name="vaults"></a>Almacenes

Seleccionar el elemento de menú **Almacenes** del Centro de copias de seguridad le permite ver una lista de todos los [Almacenes de Recovery Services](backup-azure-recovery-services-vault-overview.md) y los [Almacenes de Backup](backup-vault-overview.md) a los que tiene acceso. Puede filtrar la lista con los parámetros siguientes:

* Suscripción del almacén
* Grupo de recursos del almacén
* Nombre del almacén
* Tipo de origen de datos asociado con la directiva

La selección de cualquier elemento de la lista permite desplazarse a un almacén determinado.

![Centro de copias de seguridad: almacenes](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Directivas de copia de seguridad

El Centro de copias de seguridad permite ver y editar la información clave de cualquiera de las directivas de copia de seguridad.

Al seleccionar el elemento de menú **Directivas de copia de seguridad**, podrá ver todas las directivas que ha creado en el conjunto de copias de seguridad. Puede filtrar la lista por suscripción de almacén, grupo de recursos, tipo de origen de datos y almacén. Al hacer clic con el botón derecho en un elemento de la cuadrícula, puede ver los elementos asociados a esa directiva, editarla o incluso eliminarla, en caso de ser necesario.

![Centro de copias de seguridad: directivas](./media/backup-center-monitor-operate/backup-center-policies.png)


## <a name="resource-centric-views"></a>Vistas centralizadas de recursos

Si su organización hace una copia de seguridad de varios recursos en un almacén común y cada propietario de recursos solo quiere ver información de copia de seguridad de los recursos que posee, puede usar la vista centralizada de recursos en el Centro de Backup. Para usar la vista centralizada de recursos, active la casilla "Mostrar solo información sobre los orígenes de datos a los que tengo acceso". Actualmente, esta opción es compatible con las siguientes pestañas: **Información general**, **Instancias de Backup**, **Trabajos** y **Alertas**. Las cargas de trabajo admitidas son VM de Azure, SQL en VM de Azure, SAP HANA en VM de Azure, blobs de Azure y discos de Azure.

> [!NOTE]
> Los usuarios seguirán necesitando tener los permisos de RBAC en el almacén, incluso si usan la vista centralizada de recursos. El propósito de esta vista es que los usuarios individuales eviten ver la información de los recursos (por ejemplo, las VM ) que no poseen.

## <a name="next-steps"></a>Pasos siguientes

* [Gobernanza del conjunto de copias de seguridad](backup-center-govern-environment.md)
* [Acciones con el Centro de copias de seguridad](backup-center-actions.md)
* [Obtención de conclusiones sobre las copias de seguridad](backup-center-obtain-insights.md)
