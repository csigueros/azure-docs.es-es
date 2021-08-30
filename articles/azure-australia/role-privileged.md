---
title: Control de acceso basado en rol de Azure (Azure RBAC) y Privileged Identity Management
titleSuffix: Azure Australia
description: Guía para implementar el control de acceso basado en rol de Azure (Azure RBAC) y Privileged Identity Management en las regiones de Australia para cumplir los requisitos específicos de las directivas, las normativas y las leyes del Gobierno australiano.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 819d8398395b9cccd4066b216cccd39a8a6fbfcc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/21/2021
ms.locfileid: "117029045"
---
# <a name="azure-role-based-access-control-azure-rbac-and-privileged-identity-management-pim"></a>Control de acceso basado en rol de Azure (Azure RBAC) y Privileged Identity Management (PIM)

La gestión de los privilegios administrativos es un paso crítico para garantizar la seguridad en un entorno de TI. Restringir los privilegios administrativos otorgando los mínimos privilegios es un requisito de [ACSC ISM](https://acsc.gov.au/infosec/ism/index.htm) y forma parte de la lista de recomendaciones de seguridad de [ACSC Essential 8](https://www.acsc.gov.au/infosec/mitigationstrategies.htm).

Microsoft cuenta con un conjunto de controles para implementar el acceso Just-in-Time y Just-Enough-Access en Microsoft Azure. Conocer a fondo estos controles es esencial para adoptar un enfoque de seguridad eficaz en la nube. Esta guía contiene información general sobre los controles y las principales consideraciones de diseño que deben tenerse en cuenta al implementarlos.

## <a name="azure-rbac"></a>Azure RBAC

El control de acceso basado en rol de Azure (Azure RBAC) es fundamental para administrar el acceso a todos los recursos de Microsoft Azure y administrar Azure Active Directory (Azure AD). Azure RBAC puede implementarse junto con una serie de características complementarias disponibles en Azure. Este artículo se centra en la implementación de un sistema RBAC eficaz mediante grupos de administración de Azure, grupos de Azure Active Directory y Azure Privileged Identity Management (PIM).

En líneas generales, la implementación de Azure RBAC requiere tres componentes:

![En el diagrama se muestran los tres componentes necesarios para implementar RBAC, que son la entidad de seguridad, la definición de roles y el ámbito, que se insertan en la asignación de roles.](media/rbac-overview.png)

* **Entidades de seguridad**: una entidad de seguridad puede ser un usuario, un grupo, una [entidad de servicio](../active-directory/develop/app-objects-and-service-principals.md) o una [identidad administrada](../active-directory/managed-identities-azure-resources/overview.md). Los privilegios de las entidades de seguridad deben asignarse utilizando grupos de Azure Active Directory.

* **Definiciones de roles**: una definición de roles, lo que también se denomina "rol", es una colección de permisos. Estos permisos definen las operaciones que pueden realizar las entidades de seguridad asignadas a la definición de roles. Esta funcionalidad está disponible en los roles de recursos de Azure y los roles de administrador de Azure Active Directory. Azure viene con un conjunto de roles integrados (vínculo) que puede ampliarse con roles personalizados.

* **Ámbito**: el ámbito es el conjunto de recursos de Azure a los que se aplica una definición de roles. Los roles de Azure pueden asignarse a recursos de Azure utilizando grupos de administración de Azure.

Estos tres componentes se combinan para conceder a las entidades de seguridad el acceso que se especifica en las definiciones de roles para todos los recursos que se encuentran en el ámbito de los grupos de administración de Azure, lo que se denomina "asignación de roles". Se pueden asignar varias definiciones de roles a una sola entidad de seguridad, al igual que se pueden asignar varias entidades de seguridad a un solo ámbito.

### <a name="azure-active-directory-groups"></a>Grupos de Azure Active Directory

Al asignar privilegios a individuos o equipos, siempre que sea posible, la asignación debe vincularse a un grupo de Azure Active Directory, en lugar de asignarse directamente al usuario en cuestión. Se trata de la misma práctica recomendada que se utilizaba en las implementaciones de Active Directory locales. Siempre que sea posible, deben crearse grupos de Azure Active Directory en cada equipo que complementen la estructura lógica de los grupos de administración de Azure que ha creado.

En un escenario de nube híbrida, los grupos de seguridad locales de Windows Server Active Directory se pueden sincronizar con la instancia de Azure Active Directory. Si ya ha implementado Azure RBAC en el entorno local utilizando estos grupos de seguridad de Windows Server Active Directory, dichos grupos, una vez sincronizados, podrán usarse para implementar Azure RBAC en los recursos de Azure. De lo contrario, el entorno en la nube puede concebirse como un lienzo en blanco en el que diseñar e implementar un plan de administración de privilegios sólido creado en torno a la implementación de Azure Active Directory.

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Roles de recursos de Azure y roles de administrador de Azure Active Directory

Microsoft Azure cuenta con una gran variedad de roles integrados para los [recursos de Azure](../role-based-access-control/built-in-roles.md) y la [administración de Azure Active Directory](../active-directory/roles/permissions-reference.md). Los dos tipos de roles proporcionan acceso pormenorizado a los recursos de Azure o a los administradores de Azure AD. Es importante tener en cuenta que los roles de recursos de Azure no se pueden usar para proporcionar acceso administrativo a Azure AD y que los roles de Azure AD no proporcionan acceso específico a los recursos de Azure.

Algunos ejemplos de los tipos de acceso que se pueden asignar a un recurso de Azure utilizando un rol integrado son:

* Permitir que un usuario administre las máquinas virtuales de una suscripción y que otro usuario administre las redes virtuales
* Permitir que un grupo de DBA administre bases de datos SQL en una suscripción
* Permitir que un usuario administre todos los recursos de un grupo de recursos, como las máquinas virtuales, los sitios web y las subredes
* Permitir que una aplicación acceda a todos los recursos de un grupo de recursos

Algunos ejemplos de los tipos de acceso que se pueden asignar a los administradores de Azure AD son:

* Permitir al personal del departamento de soporte técnico restablecer las contraseñas de los usuarios.
* Permitir que el personal pueda invitar a usuarios externos a una instancia de Azure AD para la colaboración B2B.
* Permitir que el personal administrativo tenga acceso de lectura a los informes de inicio de sesión y auditoría.
* Permitir que el personal pueda administrar todos los usuarios y grupos, incluido el restablecimiento de contraseñas.

Es importante invertir tiempo para conocer la lista completa de acciones permitidas que proporciona un rol integrado para asegurarse de que no se concede acceso indebido. La lista de roles integrados y el acceso que proporcionan cambia constantemente. La lista completa de los roles y sus definiciones pueden verse en la documentación vinculada más arriba o usando el cmdlet de Azure PowerShell:

```PowerShell
Get-AzRoleDefinition
```

```output
Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

o el comando de la CLI de Azure:

```azurecli-interactive
az role definition list
```

```output
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

Si es necesario, también se pueden crear roles de recursos de Azure personalizados. Estos roles personalizados pueden crearse en Azure Portal utilizando PowerShell o la CLI de Azure. Cuando se crean roles personalizados, es fundamental asegurarse de que la finalidad del rol es única y de que ningún rol de un recurso de Azure existente proporciona dicha función. De este modo, resulta menos complejo realizar una administración continuada y se reduce el riesgo de que las entidades de seguridad reciban privilegios innecesarios. Un ejemplo consistiría en crear un rol de recurso de Azure personalizado que se encontrara entre los roles de recursos de Azure integrados, el rol "Colaborador de la máquina virtual" y el rol "Inicio de sesión de administrador de máquina virtual".

El rol personalizado podría basarse en el rol de Colaborador existente, que concede el siguiente acceso:

| Recurso de Azure | Nivel de acceso |
| --- | --- |
| Virtual Machines | Puede realizar tareas de administración, pero no tiene acceso |
| Virtual Network conectado a una máquina virtual | No tiene acceso |
| Storage conectado a una máquina virtual | No tiene acceso |
|

El rol personalizado podría conservar este acceso básico y permitir que los usuarios designados tuvieran algunos privilegios básicos adicionales para modificar la configuración de red de las máquinas virtuales.

Los roles de recursos de Azure también tienen la ventaja de que pueden asignarse a los recursos utilizando los grupos de administración de Azure.

### <a name="azure-management-groups"></a>Grupos de administración de Azure

Los grupos de administración de Azure pueden usarse en una organización para administrar la asignación de roles a todas las suscripciones y sus recursos en un inquilino de Azure. Los grupos de administración de Azure están diseñados para permitirle crear jerarquías de administración; por ejemplo, le permiten asignar la estructura organizativa de forma jerárquica en Azure. Crear unidades de negocio organizativas como entidades lógicas independientes permite aplicar los permisos de una organización en función de los requisitos específicos de cada equipo. Los grupos de administración de Azure pueden usarse para definir una jerarquía de administración de hasta seis niveles de profundidad.

![Grupos de administración](media/management-groups.png)

Los grupos de administración de Azure se asignan a las suscripciones de Azure en un inquilino de Azure. Esto permite a una organización separar los recursos de Azure que pertenecen a unidades de negocio específicas y proporcionar un nivel de control pormenorizado sobre la administración de costos y la asignación de privilegios.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Microsoft ha implementado Just-in-Time (JIT) y Just-Enough-Access (JEA) mediante Azure Privileged Identity Management. Este servicio permite al personal administrativo controlar, administrar y supervisar el acceso con privilegios a los recursos de Azure. PIM permite que las entidades de seguridad se consideren válidas para que el personal administrativo las utilice como un rol, lo que permite a los usuarios solicitar la activación del rol utilizando Azure Portal o cmdlets de PowerShell. De forma predeterminada, la asignación de roles se puede activar durante un período de entre 1 y 72 horas. Si es necesario, el usuario puede solicitar una extensión de su asignación de roles o incluso solicitar una asignación de roles permanente. Si lo desea, puede requerir la autenticación multifactor cuando los usuarios soliciten la activación de sus roles válidos. Una vez que expire el período asignado para la activación de rol, la entidad de seguridad ya no tendrá el acceso con privilegios que le concedía el rol.

El uso de PIM evita los problemas habituales de asignación de privilegios que pueden surgir en entornos que no usan el acceso Just-in-Time o no realizan auditorías rutinarias de asignación de privilegios. Un problema frecuente es la asignación de privilegios elevados que se olvidan y permanecen vigentes una vez completada la tarea que requería estos privilegios. Otro problema es la proliferación de privilegios elevados dentro de un entorno mediante la clonación del acceso asignado a una entidad de seguridad cuando se configuran otras entidades de seguridad similares.

## <a name="key-design-considerations"></a>Consideraciones clave sobre el diseño

Cuando diseñe una estrategia de Azure RBAC con la intención de aplicar la seguridad de privilegios mínimos, debe tener en cuenta los requisitos de seguridad siguientes:

* Las solicitudes de acceso con privilegios deben validarse.
* Los privilegios administrativos están restringidos al mínimo acceso necesario para realizar las tareas especificadas.
* Los privilegios administrativos están restringidos al mínimo período de tiempo necesario para realizar las tareas especificadas.
* Periódicamente, se llevan a cabo revisiones de los privilegios administrativos concedidos.

El proceso de diseño de una estrategia de Azure RBAC requerirá una revisión detallada de las funciones corporativas para comprender la diferencia de acceso entre distintos roles empresariales y el tipo y la frecuencia de trabajo que requieren privilegios elevados. La diferencia de la función entre un Operador de copias de seguridad, un Administrador de seguridad y un Auditor requerirá niveles de acceso diferentes en momentos distintos y con distintos niveles de revisión en curso.

## <a name="validate-requests-for-access"></a>Validación de las solicitudes de acceso

Los privilegios elevados deben aprobarse explícitamente. Para poder hacerlo, debe desarrollarse un proceso de aprobación y contar con el personal adecuado que sea responsable de validar que todas las solicitudes de privilegios adicionales sean legítimas. Privileged Identity Management cuenta con varias opciones para aprobar la asignación de roles. Las solicitudes de activación de roles pueden configurarse para que puedan aprobarse de forma automática o para que estén controladas y requieran que los aprobadores las revisen y aprueben manualmente. Las solicitudes de activación también se pueden configurar para que soliciten que se incluya más información en la solicitud de activación, como los números de vale.

### <a name="restrict-privilege-based-on-duties"></a>Restricción de privilegios en función de las tareas

Restringir el nivel de privilegios concedidos a las entidades de seguridad es fundamental, ya que la asignación excesiva de privilegios es un vector de ataque frecuente en la seguridad de TI. Los tipos de recursos que se administran y los equipos responsables deben evaluarse para poder asignar el nivel mínimo de privilegios necesarios para las tareas diarias. Los privilegios que van más allá de los que son estrictamente necesarios para las tareas diarias solo deben concederse durante el período de tiempo requerido para realizar una tarea específica. Por ejemplo, puede proporcionarse acceso de "Colaborador" al administrador de un cliente y permitirle que solicite permisos de "Propietario" en un recurso de Azure para una tarea específica que requiera temporalmente un acceso de alto nivel.

Esto garantiza que cada administrador solo tiene acceso con privilegios elevados durante el período de tiempo más corto posible. El cumplimiento de estas prácticas reduce la superficie expuesta a ataques general de la infraestructura de TI de las organizaciones.

### <a name="regular-evaluation-of-administrative-privilege"></a>Evaluación periódica de los privilegios administrativos

Es fundamental que las entidades de seguridad de un entorno se auditen de forma rutinaria para garantizar que el nivel de privilegios asignado actualmente es el correcto. Microsoft Azure cuenta con una serie de mecanismos para auditar y evaluar los privilegios asignados a las entidades de seguridad de Azure. Privileged Identity Management permite que el personal administrativo realice periódicamente "revisiones de acceso" de los roles concedidos a las entidades de seguridad. Las revisiones de acceso se pueden llevar a cabo para auditar la asignación de roles de recursos de Azure y de roles administrativos de Azure Active Directory. Las revisiones de acceso pueden configurarse con las siguientes propiedades:

* **Nombre de la revisión y fecha de inicio y finalización**: las revisiones deben configurarse de forma que duren lo suficiente como para que los usuarios designados las completen.

* **Rol que se va a revisar**: cada revisión de acceso se centra en un único rol de Azure.

* **Revisores designados**: hay tres opciones para realizar una revisión. Puede asignar la revisión a otra persona para que la complete, puede hacerlo usted mismo o hacer que cada usuario revise su propio acceso.

* **Requerir a los usuarios que proporcione un motivo de acceso**: se puede solicitar a los usuarios que indiquen un motivo para mantener su nivel de privilegios al completar la revisión de acceso.

El progreso de las revisiones de acceso pendientes puede supervisarse en cualquier momento utilizando un panel de Azure Portal. El acceso al rol que se está revisando permanecerá sin cambios hasta que se complete la revisión de acceso. También se pueden [auditar](../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) todas las activaciones y asignaciones de usuarios de PIM en un período de tiempo especificado.

## <a name="next-steps"></a>Pasos siguientes

Revise el artículo sobre [la supervisión de sistemas en Azure Australia](system-monitor.md).
