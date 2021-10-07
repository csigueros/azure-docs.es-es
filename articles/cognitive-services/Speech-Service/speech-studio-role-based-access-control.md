---
title: 'Control de acceso basado en rol en Speech Studio: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Aprenda a asignar roles de acceso al servicio de voz a través de Speech Studio.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: pafarley
ms.openlocfilehash: 72d2acd25381af654fb098d81a7cdc4ec0bbce22
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699780"
---
# <a name="azure-role-based-access-control-in-speech-studio"></a>Control de acceso basado en rol de Azure en Speech Studio 

Speech Studio admite el control de acceso basado en rol de Azure (Azure RBAC), un sistema de autorización que permite administrar el acceso individual a los recursos de Azure. Con Azure RBAC, puede asignar diferentes niveles de permisos para las operaciones de Speech Studio a distintos miembros del equipo. Para obtener más información sobre Azure RBAC, consulte la [documentación de Azure RBAC](/azure/role-based-access-control/overview).

## <a name="prerequisites"></a>Requisitos previos

* Debe haber iniciado sesión en Speech Studio con su cuenta de Azure y el recurso de voz. Consulte la [Introducción a Speech Studio](speech-studio-overview.md).

## <a name="manage-role-assignments-for-speech-resources"></a>Administración de asignaciones de roles para recursos de voz

Para conceder acceso a un recurso de voz de Azure, agregue una asignación de roles a través de la herramienta RBAC de Azure en Azure Portal. 

En cuestión de minutos, al destino se le asignará el rol seleccionado en el ámbito seleccionado. Para obtener ayuda con estos pasos, consulte [Asignación de roles de Azure mediante Azure Portal](/azure/role-based-access-control/role-assignments-portal?tabs=current).

## <a name="supported-built-in-roles-in-speech-studio"></a>Roles integrados admitidos en Speech Studio

Una definición de roles es una colección de permisos. Use los siguientes roles integrados recomendados si no tiene ningún requisito personalizado único para los permisos:

| **Rol integrado** | **Permiso para enumerar claves de recursos** | **Permiso para operaciones de Habla personalizada** | **Permiso para operaciones de Voz personalizada**| **Permiso para otras funcionalidades** |
| ---| ---| ---| ---| --|
|**Propietario** |Yes |Acceso total a los proyectos, como el permiso para crear, editar o eliminar proyectos, datos, modelos o puntos de conexión |Acceso total a los proyectos, como el permiso para crear, editar o eliminar proyectos, datos, modelos o puntos de conexión |Acceso total |
|**Colaborador** |Yes |Acceso total a los proyectos, como el permiso para crear, editar o eliminar proyectos, datos, modelos o puntos de conexión |Acceso total a los proyectos, como el permiso para crear, editar o eliminar proyectos, datos, modelos o puntos de conexión |Acceso total |
|**Colaboradores de Cognitive Services** |Yes |Acceso total a los proyectos, como el permiso para crear, editar o eliminar proyectos, datos, modelos o puntos de conexión |Acceso total a los proyectos, como el permiso para crear, editar o eliminar proyectos, datos, modelos o puntos de conexión |Acceso total |
|**Usuarios de Cognitive Services** |Yes |Acceso total a los proyectos, como el permiso para crear, editar o eliminar proyectos, datos, modelos o puntos de conexión |Acceso total a los proyectos, como el permiso para crear, editar o eliminar proyectos, datos, modelos o puntos de conexión |Acceso total |
|**Colaborador de Voz de Cognitive Services** |No |Acceso total a los proyectos, como el permiso para crear, editar o eliminar proyectos, datos, modelos o puntos de conexión |Acceso total a los proyectos, como el permiso para crear, editar o eliminar proyectos, datos, modelos o puntos de conexión |Acceso total |
|**Usuario de Voz de Cognitive Services** |No |Puede ver los proyectos, conjuntos de datos, modelos o puntos de conexión; no puede crear, editar, eliminar |Puede ver los proyectos, conjuntos de datos, modelos o puntos de conexión; no puede crear, editar, eliminar |Acceso total |
|**Lector de datos de Cognitive Services (versión preliminar)** |No |Puede ver los proyectos, conjuntos de datos, modelos o puntos de conexión; no puede crear, editar, eliminar |Puede ver los proyectos, conjuntos de datos, modelos o puntos de conexión; no puede crear, editar, eliminar |Acceso total |

Como alternativa, puede [crear sus propios roles personalizados](/azure/role-based-access-control/custom-roles). Por ejemplo, podría crear un rol personalizado con el permiso para cargar conjuntos de datos de voz personalizados, pero sin la capacidad de implementar un modelo de voz personalizado en un punto de conexión.

> [!NOTE]
> Speech Studio admite la autenticación basada en claves. Los roles que tienen permiso para enumerar claves de recursos (`Microsoft.CognitiveServices/accounts/listKeys/action`) se autenticarán primero con una clave de recurso y tendrán total acceso a las operaciones de Speech Studio, siempre y cuando la autenticación de clave esté habilitada en Azure Portal. Si el administrador del servicio deshabilita la autenticación de clave, esos roles perderán todo el acceso a Studio.

> [!NOTE]
> Un recurso se podría asignar o heredar con varios roles y el nivel final de acceso a este recurso es una combinación de todos los permisos de los roles del nivel de operación.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre [Cifrado de datos en reposo del servicio de voz](/azure/cognitive-services/speech-service/speech-encryption-of-data-at-rest).