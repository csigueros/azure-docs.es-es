---
title: Introducción al control de acceso del área de trabajo de Azure Synapse
description: En este artículo se describen los mecanismos que se usan para controlar el acceso a las áreas de trabajo de Synapse, así como a los recursos y artefactos de código que contienen.
services: synapse-analytics
author: meenalsri
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 11/02/2021
ms.author: mesrivas
ms.reviewer: wiassaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 39eba039a8f7b381e998be3d8bfe5cd213217ab3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013429"
---
# <a name="azure-synapse-access-control"></a>Control de acceso de Azure Synapse 

En este artículo, se proporciona información general acerca de los mecanismos disponibles para controlar el acceso a los datos y recursos de proceso de Azure Synapse.  

## <a name="overview"></a>Información general

Azure Synapse proporciona un sistema de control de acceso completo y detallado que integra: 
- **Roles de Azure** para la administración de los recursos y el acceso a los datos en el almacenamiento. 
- **Roles de Synapse** para administrar el acceso en vivo al código y a la ejecución. 
- **Roles de SQL** para el acceso al plano de datos para los datos de grupos de SQL. 
- **Permisos de Git** para el control del código fuente, lo que incluye la integración continua y el soporte técnico de la implementación.  

Los roles de Azure Synapse proporcionan conjuntos de permisos que se pueden aplicar en ámbitos diferentes. Esta granularidad facilita la concesión del acceso apropiado a administradores, desarrolladores, personal de seguridad y operadores para procesar recursos y datos.

Para simplificar el control de acceso se pueden usar grupos de seguridad que estén alineados con los roles de trabajo de las personas. Para administrar el acceso solo hay que agregar y quitar usuarios de los grupos de seguridad adecuados.

## <a name="access-control-elements"></a>Elementos del control de acceso

### <a name="create-and-manage-azure-synapse-compute-resources"></a>Creación y administración de recursos de proceso de Azure Synapse

Los roles de Azure se usan para controlar la administración de: 
- Grupos de SQL dedicados
- Grupos de Data Explorer
- Grupos de Apache Spark
- Entornos de ejecución de integración

Para *crear* estos recursos, es preciso tener los roles Propietario o Colaborador de Azure en el grupo de recursos. Por su parte, para *administrarlos* una vez creados, es preciso tener los roles Propietario o Colaborador de Azure en el grupo de recursos o en los recursos individuales. 

### <a name="develop-and-execute-code-in-azure-synapse"></a>Desarrollo y ejecución de código en Azure Synapse 

Synapse admite dos modelos de desarrollo.

- **Desarrollo en vivo de Synapse**. El código se desarrolla y depura en Synapse Studio y, después, se **publica** para guardarlo y ejecutarlo.  El servicio Synapse es la única fuente confiable para la edición y ejecución de código.  Al cerrar Synapse Studio los trabajos que no se hayan publicado se pierden.  
- **Desarrollo habilitado para Git**. El código se desarrolla y depura en Synapse Studio y los cambios se **envían** a una rama de trabajo de un repositorio de Git. Todo el trabajo de una o varias ramas se integra en una rama de colaboración, desde donde se **publica** en el servicio. El repositorio de Git es la única fuente confiable para la edición de código, mientras que el servicio lo es para su ejecución. Los cambios se deben enviar al repositorio de Git, o bien se deben publicar en el servicio antes de cerrar Synapse Studio. [Aquí encontrará más información](../cicd/continuous-integration-delivery.md) sobre el uso de Synapse Analytics con Git.

En ambos modelos de desarrollo, cualquier usuario con acceso a Synapse Studio puede crear artefactos de código. Sin embargo, para publicarlos en el servicio, leer los publicados, enviar los cambios a Git, ejecutar código y acceder a datos vinculados protegidos mediante credenciales se necesitan permisos adicionales.

### <a name="azure-synapse-roles"></a>Roles de Azure Synapse

Los roles de Azure Synapse se usan para realizar un control del acceso al servicio Synapse que le permite: 
- Mostrar los artefactos de código publicados. 
- Publicar artefactos de código, servicios vinculados y definiciones de credenciales.
- Ejecutar código o canalizaciones que usan recursos de proceso de Synapse.
- Ejecutar código o canalizaciones que acceden a datos vinculados protegidos mediante credenciales.
- Ver las salidas asociadas con artefactos de código publicados
- Supervisar el estado del recurso de proceso y ver registros en tiempo de ejecución.

Los roles de Azure Synapse se pueden asignar al ámbito del área de trabajo o a ámbitos pormenorizados, con el fin de limitar los permisos concedidos a recursos de Azure Synapse concretos.

### <a name="git-permissions"></a>Permisos de Git

Al usar el desarrollo habilitado para Git en el modo Git, necesita permisos de Git además de los roles de usuario o RBAC (control de acceso basado en rol) de Synapse para leer artefactos de código, incluidas las definiciones de credenciales y el servicio vinculado. Para confirmar los cambios en los artefactos de código en modo Git, necesita permisos de Git, el rol Colaborador de Azure (RBAC de Azure) en el área de trabajo y el rol Editor de artefactos de Synapse (RBAC de Synapse).

   
### <a name="access-data-in-sql"></a>Acceso a datos en SQL

Cuando se trabaja con grupos de SQL dedicados y sin servidor, el acceso al plano de datos se controla mediante permisos de SQL. 

El creador de un área de trabajo se asigna como administrador de Active Directory al área de trabajo. Tras su creación, este rol se puede asignar a otro usuario o grupo de seguridad en Azure Portal.

**Grupos de SQL sin servidor**: a los administradores de Synapse se les conceden los permisos de `db_owner` (`DBO`) en el grupo de SQL sin servidor, "Built-in". Para conceder a otros usuarios acceso a grupos de SQL sin servidor, los administradores de Synapse necesitan ejecutar scripts de SQL en todos los grupos sin servidor.  

**Grupos de SQL dedicados**: los administradores de Synapse tienen acceso total a los datos de grupos de SQL dedicados y la capacidad de conceder acceso a otros usuarios. Los administradores de Synapse también pueden realizar actividades de configuración y mantenimiento en grupos dedicados, excepto para quitar bases de datos. el permiso de administrador de Active Directory se le concede al creador del área de trabajo y al MSI del área de trabajo.  El permiso para acceder a grupos de SQL dedicados no se concede de forma automática. Para conceder a otros usuarios o grupos acceso a grupos de SQL dedicados, el administrador de Active Directory o el administrador de Synapse debe ejecutar scripts de SQL en cada grupo de SQL dedicado.

Para ver ejemplos de scripts de SQL para conceder permisos de SQL en grupos de SQL, consulte el artículo sobre la [configuración de Synapse Access Control](./how-to-set-up-access-control.md).  

### <a name="accessing-data-in-data-explorer-pools"></a>Acceso a los datos de grupos de Data Explorer

Cuando se trabaja con grupos de Data Explorer, el acceso al plano de datos se controla mediante permisos de Data Explorer. A los administradores de Synapse se les conceden permisos `All Database admin` en grupos de Data Explorer. Para conceder a otros usuarios o grupos acceso a los grupos de Data Explorer, los administradores de Synapse deben hacer referencia a [Administración de roles de seguridad](/azure/data-explorer/kusto/management/security-roles?context=/azure/synapse-analytics/context/context). Para obtener más información sobre el acceso al plano de datos, consulte [Introducción al control de acceso de Data Explorer](/azure/data-explorer/kusto/management/access-control/index?context=/azure/synapse-analytics/context/context).


 ### <a name="accessing-system-managed-data-in-storage"></a>Acceso a datos administrados por el sistema en el almacenamiento

Tanto los grupos de SQL sin servidor como las tablas de Apache Spark almacenan sus datos en un contenedor ADLS Gen2 asociado con el área de trabajo. Las bibliotecas de Apache Spark instaladas por el usuario también se administran en la misma cuenta de almacenamiento. Para habilitar estos casos de uso, tanto a los usuarios como al MSI del área de trabajo se les debe conceder acceso de **colaborador de datos de Storage Blob** a este contenedor de almacenamiento de ADLS Gen2.  

## <a name="using-security-groups-as-a-best-practice"></a>Uso de grupos de seguridad como procedimiento recomendado

Para simplificar la administración del control de acceso se pueden usar grupos de seguridad para asignar roles a individuos y grupos. Se pueden crear grupos de seguridad para crear un espejo de los roles o funciones de trabajo de su organización que necesiten acceder a los recursos o artefactos de Synapse.  A estos grupos de seguridad basados en roles se les puede asignar uno o varios roles de Azure, roles de Synapse, permisos de SQL o permisos de Git. Si los grupos de seguridad se han elegido correctamente, es fácil asignar a los usuarios los permisos necesarios, para lo que hay que agregarlos al grupo de seguridad adecuado. 

>[!Note]
>Si se usan grupos de seguridad para administrar el acceso, Azure Active Directory genera una mayor latencia antes de que los cambios surtan efecto. 

## <a name="access-control-enforcement-in-synapse-studio"></a>Aplicación del control de acceso en Synapse Studio

Synapse Studio se comportará de forma diferente en función de los permisos y del modo actual:
- **Modo activo de Synapse:** Synapse Studio impedirá que vea contenido publicado, que publique contenido o que realice otras acciones sin tener el permiso requerido.  En algunos casos, se impedirá crear aquellos artefactos de código que no se puedan usar o guardar. 
- **Modo Git:** si tiene permisos de Git que le permiten confirmar cambios en la rama actual, se permitirá la acción de confirmación si tiene permiso para publicar cambios en el servicio activo (rol Editor de artefactos de Synapse) y en el rol Colaborador de Azure en el área de trabajo.  

En algunos casos, se le permite crear artefactos de código, incluso sin permiso para publicar o enviar. De esta forma, puede ejecutar código (con los permisos de ejecución necesarios). [Más información](./synapse-workspace-understand-what-role-you-need.md) sobre los roles necesarios para las tareas comunes. 

Si una característica está deshabilitada en Synapse Studio, un cuadro de información sobre herramientas indicará el permiso requerido. Use la [guía de roles RBAC de Synapse](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them) para consultar cuál es el rol necesario para proporcionar el permiso que falta.


## <a name="next-steps"></a>Pasos siguientes

- Más información acerca de [Synapse RBAC](./synapse-workspace-synapse-rbac.md).
- Más información acerca de los [roles de Synapse RBAC](./synapse-workspace-synapse-rbac-roles.md).
- Aprenda a [configurar el control de acceso](./how-to-set-up-access-control.md) de un área de trabajo de Synapse mediante grupos de seguridad.
- Aprenda a [examinar asignaciones de roles RBAC de Synapse](./how-to-review-synapse-rbac-role-assignments.md)
- Aprenda a [administrar asignaciones de roles RBAC de Synapse](./how-to-manage-synapse-rbac-role-assignments.md)
