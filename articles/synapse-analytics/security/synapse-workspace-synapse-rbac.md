---
title: Control de acceso basado en rol de Synapse
description: En este artículo se explica el control de acceso basado en rol en Azure Synapse Analytics
author: meenalsri
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.openlocfilehash: e2a49fb9de3c4f63edc483724769c5c726d3a63c
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081859"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>¿Qué es el control de acceso basado en rol de Synapse?

El control de acceso basado en rol de Synapse amplía las funcionalidades del [control de acceso basado en rol de Azure](../../role-based-access-control/overview.md) para las áreas de trabajo de Synapse y su contenido. 

El control de acceso basado en rol de Azure se utiliza para administrar quién puede crear, actualizar o eliminar el área de trabajo de Synapse y sus grupos de SQL, grupos de Apache Spark y entornos de ejecución de integración.

El control de acceso basado en rol de Synapse se utiliza para administrar quién puede:
- Publicar artefactos de código y enumerar o acceder a artefactos de código publicados. 
- Ejecutar código en grupos de Apache Spark y entornos de ejecución de integración.
- Acceder a servicios vinculados (datos) protegidos por credenciales. 
- Supervisar o cancelar la ejecución de trabajos, examinar la salida de trabajos y los registros de ejecución.  

>[!Note]
>Aunque el control de acceso basado en rol de Synapse se usa para administrar el acceso a los scripts de SQL publicados, solo proporciona un control de acceso limitado a los grupos de SQL sin servidor y dedicados. El acceso a los grupos de SQL se controla principalmente mediante la seguridad de SQL.

## <a name="what-can-i-do-with-synapse-rbac"></a>¿Qué se puede hacer con el control de acceso basado en rol de Azure?

Estos son algunos ejemplos de lo que puede hacer con el control de acceso basado en rol de Synapse:
  - Permite a los usuarios publicar los cambios realizados en los cuadernos y trabajos de Apache Spark en el servicio en directo.
  - Permite a los usuarios ejecutar y cancelar cuadernos y trabajos de Spark en un grupo de Apache Spark concreto.
  - Permite a los usuarios utilizar credenciales específicas para que puedan ejecutar canalizaciones protegidas por la identidad del sistema del área de trabajo, así como acceder a los datos de los servicios vinculados protegidos con credenciales. 
  - Permite a los administradores administrar, supervisar y cancelar la ejecución de trabajos en grupos de Spark concretos.    

## <a name="how-synapse-rbac-works"></a>Funcionamiento del control de acceso basado en rol de Synapse
Al igual que el control de acceso basado en rol de Azure, el de Synapse funciona mediante la creación de asignaciones de roles. Una asignación de roles consta de tres elementos: una entidad de seguridad, una definición de roles y un ámbito.  

### <a name="security-principals"></a>Entidades de seguridad

Una _entidad de seguridad_ es un usuario, grupo, entidad de seguridad o identidad administrada.

### <a name="roles"></a>Roles
 
Un _rol_ es una colección de permisos o acciones que se pueden realizar en tipos de recursos o tipos de artefactos concretos.

Synapse proporciona roles integrados que definen colecciones de acciones que se ajustan a las necesidades de diferentes roles:
- Los administradores pueden obtener acceso completo para crear y configurar un área de trabajo. 
- Los desarrolladores pueden crear, actualizar y depurar scripts de SQL, cuadernos, canalizaciones y flujos de datos, pero no pueden publicar o ejecutar este código en recursos/datos de proceso de producción.
- Los operadores pueden supervisar y administrar el estado del sistema, los registros de ejecución y revisión de las aplicaciones, sin acceder al código o a los resultados de la ejecución.
- El personal de seguridad puede administrar y configurar puntos de conexión sin tener acceso al código, a los recursos de proceso o a los datos.

[Obtenga más información](./synapse-workspace-synapse-rbac-roles.md) sobre los roles de Synapse integrados. 

### <a name="scopes"></a>Ámbitos

Los _ámbitos_ definen los recursos o artefactos a los que se aplica el acceso.  Synapse admite ámbitos jerárquicos.  Los permisos concedidos en un ámbito de nivel superior los heredan los objetos de un nivel inferior.  En el control de acceso basado en rol de Synapse, el ámbito de nivel superior es un área de trabajo.  La asignación de un rol con un ámbito de área de trabajo concede permisos a todos los objetos aplicables del área de trabajo.  

Estos son los ámbitos admitidos actualmente dentro de un área de trabajo: Grupo de Apache Spark, entorno de ejecución de integración, servicio vinculado y credencial. 

El acceso a los artefactos de código se concede con el ámbito del área de trabajo.  La concesión de acceso a colecciones de artefactos dentro de un área de trabajo se admitirá en una versión posterior.

## <a name="resolving-role-assignments-to-determine-permissions"></a>Resolución de asignaciones de roles para determinar los permisos

Una asignación de roles concede a la entidad de seguridad los permisos definidos por el rol en el ámbito especificado.

El control de acceso basado en rol de Synapse es un modelo aditivo como Azure RBAC. Se pueden asignar varios roles a una sola entidad de seguridad y a ámbitos diferentes. Al calcular los permisos de una entidad de seguridad, el sistema considera todos los roles asignados a la entidad y a los grupos que incluyen directa o indirectamente la entidad.  También tiene en cuenta el ámbito de cada asignación al determinar los permisos que se aplican.  

## <a name="enforcing-assigned-permissions"></a>Exigencia de permisos asignados

En Synapse Studio, es posible que algunas opciones o botones concretos estén atenuados o que se devuelva un error de permisos al intentar realizar una acción sin los permisos necesarios. 

Si una opción o un botón están deshabilitados, al pasar el mouse sobre ellos se muestra información sobre herramientas con el permiso necesario.  Póngase en contacto con un administrador de Synapse para asignar un rol que conceda el permiso necesario. [Aquí](./synapse-workspace-synapse-rbac-roles.md) puede ver los roles que proporcionan acciones específicas.

## <a name="who-can-assign-synapse-rbac-roles"></a>¿Quién puede asignar roles del control de acceso basado en rol de Synapse?

Los roles del control de acceso basado en rol de Synapse solo puede asignarlos un administrador de Synapse.  Los administradores de Synapse en el nivel de área de trabajo pueden conceder acceso en cualquier ámbito.  Los administradores de Synapse en un ámbito de nivel inferior solo pueden conceder acceso a ese ámbito. 

Cuando se crea un área de trabajo, al creador se le asigna automáticamente el rol Administrador de Synapse en el ámbito del área de trabajo.   

## <a name="where-do-i-manage-synapse-rbac"></a>¿Dónde se administra el control de acceso basado en rol de Synapse?

El control de acceso basado en rol de Synapse se administra desde Synapse Studio mediante las herramientas de control de acceso en el centro de administración. 

## <a name="next-steps"></a>Pasos siguientes

Conozca los [roles del control de acceso basado en rol de Synapse](./synapse-workspace-synapse-rbac-roles.md) integrados.

Aprenda a [examinar las asignaciones de roles del control de acceso basado en rol de Synapse](./how-to-review-synapse-rbac-role-assignments.md) en un área de trabajo.

Aprenda a [asignar roles de Synapse RBAC](./how-to-manage-synapse-rbac-role-assignments.md)