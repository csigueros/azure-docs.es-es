---
title: Configuración del Control de acceso basado en roles (Azure RBAC) de Azure para Azure API for FHIR
description: En este artículo se explica cómo configurar RBAC de Azure para el plano de datos de Azure API for FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.reviewer: dseven
ms.openlocfilehash: 124dec2ec765628f08bdad18f4fd12d30141d5c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780294"
---
# <a name="configure-azure-rbac-for-fhir"></a>Configuración de RBAC de Azure para FHIR 

En este artículo aprenderá a usar el [Control de acceso basado en roles (Azure RBAC) de Azure](../../role-based-access-control/index.yml) para asignar acceso al plano de datos de Azure API for FHIR. RBAC de Azure es el método preferido para asignar acceso al plano de datos cuando los usuarios del plano de datos se administran en el inquilino de Azure Active Directory asociado a la suscripción de Azure. Si usa un inquilino de Azure Active Directory externo, vea la [referencia de asignación de RBAC local](configure-local-rbac.md).

## <a name="confirm-azure-rbac-mode"></a>Confirmación del modo RBAC de Azure

Para usar RBAC de Azure, Azure API for FHIR debe estar configurado para usar el inquilino de la suscripción de Azure para el plano de datos y no debe haber ningún identificador de objeto de identidad asignado. Puede comprobar la configuración si revisa la hoja **Autenticación** de Azure API for FHIR:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="Confirmación del modo RBAC de Azure":::

**Autoridad** debe establecerse en el inquilino de Azure Active Directory asociado a la suscripción y no debe haber ningún GUID en el cuadro con la etiqueta **Identificadores de objeto permitidos**. Se observa además que el cuadro está deshabilitado y que hay una etiqueta que indica que debe usarse RBAC de Azure para asignar roles de plano de datos.

## <a name="assign-roles"></a>Asignación de roles

Para conceder acceso a los usuarios, las entidades de servicio o los grupos al plano de datos de FHIR, haga clic en **Control de acceso (IAM)** , **Asignaciones de roles** y luego **+ Agregar**:

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="Agregar asignación de roles de Azure":::

En la selección de **Rol**, busque uno de los roles integrados para el plano de datos de FHIR:

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="Roles de datos de FHIR integrados":::

Puede elegir entre:

* Lector de datos de FHIR: puede leer (y buscar) datos de FHIR.
* Escritor de datos de FHIR: puede leer, escribir y eliminar datos de FHIR de forma temporal.
* Exportador de datos de FHIR: puede leer y exportar datos (operador `$export`).
* Colaborador de datos de FHIR: puede realizar todas las operaciones de plano de datos.

En el cuadro **Seleccionar**, busque un usuario, una entidad de servicio o un grupo al que quiera asignar el rol.

>[!Note]
>Asegúrese de que se ha completado el registro de la aplicación cliente. Consulte los detalles sobre el [registro de aplicaciones](register-confidential-azure-ad-client-app.md). Si se usa el tipo de concesión de código de autorización de OAuth 2.0, conceda el mismo rol de aplicación FHIR al usuario. Si se usa el tipo de concesión de credenciales de cliente de OAuth 2.0, este paso no es necesario.

## <a name="caching-behavior"></a>Comportamiento del almacenamiento en caché

Azure API for FHIR almacena en caché las decisiones durante un máximo de 5 minutos. Si concede a un usuario acceso al servidor de FHIR al agregarlo a la lista de identificadores de objeto permitidos, o lo quita de la lista, debe dejar hasta cinco minutos para que se propaguen los cambios de permisos.

## <a name="next-steps"></a>Pasos siguientes

En este artículo aprendió a asignar roles de Azure para el plano de datos de FHIR. Para obtener más información sobre la configuración adicional para Azure API for FHIR:
 
>[!div class="nextstepaction"]
>[Configuración adicional de Azure API for FHIR](azure-api-for-fhir-additional-settings.md)
