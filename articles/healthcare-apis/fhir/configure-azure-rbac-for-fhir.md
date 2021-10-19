---
title: 'Configuración del servicio RBAC de Azure para FHIR: API de Azure Healthcare'
description: En este artículo se describe cómo configurar Azure RBAC for FHIR.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 09/10/2021
ms.author: zxue
ms.openlocfilehash: ff2a488a7ed8a693f23b533242748eed400bd802
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782502"
---
# <a name="configure-azure-rbac-for-the-fhir-service"></a>Configuración de RBAC de Azure para el servicio FHIR

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, aprenderá a usar el control de acceso basado en rol [de Azure (RBAC de Azure)](../../role-based-access-control/index.yml) para asignar acceso al plano de datos de las API de atención sanitaria. RBAC de Azure es el método preferido para asignar acceso al plano de datos cuando los usuarios del plano de datos se administran en el inquilino de Azure Active Directory asociado a la suscripción de Azure. 

## <a name="assign-roles"></a>Asignación de roles

Para conceder a los usuarios, entidades de servicio o grupos acceso al plano de datos de FHIR, seleccione el servicio FHIR en el Azure Portal. Seleccione **Control de acceso (IAM)** y, a continuación, seleccione la pestaña **Asignaciones de** roles. Seleccione **+Agregar y,** a continuación, **seleccione Agregar asignación de roles.**
 
Si la opción de asignación de roles está atenuada, pida al administrador de la suscripción de Azure que le conceda los permisos para la suscripción o el grupo de recursos, por ejemplo, "Administrador de acceso de usuario". Para más información sobre los roles integrados de Azure, consulte [Roles integrados de Azure.](../../role-based-access-control/built-in-roles.md)

[![Asignación de roles de control de acceso. ](media/rbac/role-assignment.png) ](media/rbac/role-assignment.png#lightbox)

En la selección Rol, busque uno de los roles integrados para el plano de datos de FHIR, por ejemplo, "Colaborador de datos de FHIR". Puede elegir otros roles a continuación.

* **Lector de datos de FHIR:** puede leer (y buscar) datos de FHIR.
* **FHIR Data Writer:** puede leer, escribir y eliminar datos de FHIR de forma flexible.
* **Exportador de datos de FHIR:** puede leer y exportar ($export operador).
* **Colaborador de datos de FHIR:** puede realizar todas las operaciones del plano de datos.
* **Convertidor de datos de FHIR:** puede usar el convertidor para realizar la conversión de datos

En la **sección Seleccionar,** escriba el nombre de registro de la aplicación cliente. Si se encuentra el nombre, se muestra el nombre de la aplicación. Seleccione el nombre de la aplicación y, a continuación, **seleccione Guardar.** 

Si no se encuentra la aplicación cliente, compruebe el registro de la aplicación para asegurarse de que el nombre es correcto. Asegúrese de que la aplicación cliente se crea en el mismo inquilino en el que está implementado el servicio FHIR en las API de Azure Healthcare (aquí denominado servicio FHIR).


[![Seleccione asignación de roles. ](media/rbac/select-role-assignment.png) ](media/rbac/select-role-assignment.png#lightbox)

Para comprobar la asignación de roles, seleccione la **pestaña Asignaciones de roles** en la opción de menú Control de acceso **(IAM).**
 

> [!NOTE]
> La asignación de roles puede tardar unos minutos en propagarse en el sistema. Si no puede acceder al servicio FHIR en la aplicación u otras herramientas de prueba, puede esperar unos minutos. Además, compruebe que ha concedido los permisos user_impersonation a las API de Azure Healthcare en el registro de la aplicación.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a asignar roles de Azure para el plano de datos de FHIR. Para obtener información sobre cómo acceder al servicio FHIR mediante Postman, consulte

>[!div class="nextstepaction"]
>[Acceso al servicio FHIR con Postman](../use-postman.md)