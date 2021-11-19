---
title: 'Configuración del servicio Azure RBAC for FHIR: API de Azure Healthcare'
description: En este artículo se describe cómo configurar Azure RBAC for FHIR.
author: SteveWohl
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 11/17/2021
ms.author: zxue
ms.openlocfilehash: b43127a483e9935fe2212f85ab730d344815db07
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814848"
---
# <a name="configure-azure-rbac-for-healthcare-apis"></a>Configuración de Azure RBAC para las API de atención sanitaria

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, aprenderá a usar el control de acceso basado en rol [de Azure (RBAC de Azure)](../role-based-access-control/index.yml) para asignar acceso al plano de datos de las API de atención sanitaria. RBAC de Azure es el método preferido para asignar acceso al plano de datos cuando los usuarios del plano de datos se administran en el inquilino de Azure Active Directory asociado a la suscripción de Azure.

Puede completar las asignaciones de roles a través del Azure Portal. Tenga en cuenta que el servicio FHIR y el servicio DICOM han definido distintos roles de aplicación. Agregue o quite uno o varios roles para administrar los controles de acceso de usuario.

## <a name="assign-roles-for-the-fhir-service"></a>Asignación de roles para el servicio FHIR

Para conceder a los usuarios, entidades de servicio o grupos acceso al plano de datos de FHIR, seleccione el servicio FHIR en la Azure Portal. Seleccione **Control de acceso (IAM)** y, a continuación, seleccione la pestaña **Asignaciones de** roles. Seleccione **+Agregar y,** a continuación, **seleccione Agregar asignación de roles.**
 
Si la opción de asignación de roles está atenuada, pida al administrador de la suscripción de Azure que le conceda los permisos para la suscripción o el grupo de recursos, por ejemplo, "Administrador de acceso de usuario". Para más información sobre los roles integrados de Azure, consulte [Roles integrados de Azure.](../role-based-access-control/built-in-roles.md)

[![Asignación de roles de control de acceso. ](fhir/media/rbac/role-assignment.png) ](fhir/media/rbac/role-assignment.png#lightbox)

En la selección Rol, busque uno de los roles integrados para el plano de datos de FHIR, por ejemplo, "Colaborador de datos de FHIR". Puede elegir otros roles a continuación.

* **Lector de datos de FHIR:** puede leer (y buscar) datos de FHIR.
* **Escritor de datos de FHIR:** puede leer, escribir y eliminar datos de FHIR de forma flexible.
* **Exportador de datos de FHIR:** puede leer y exportar ($export operador).
* **Colaborador de datos de FHIR:** puede realizar todas las operaciones del plano de datos.
* **Convertidor de datos de FHIR:** puede usar el convertidor para realizar la conversión de datos

En la **sección Seleccionar,** escriba el nombre de registro de la aplicación cliente. Si se encuentra el nombre, se muestra el nombre de la aplicación. Seleccione el nombre de la aplicación y, a continuación, **seleccione Guardar.** 

Si no se encuentra la aplicación cliente, compruebe el registro de la aplicación para asegurarse de que el nombre es correcto. Asegúrese de que la aplicación cliente se crea en el mismo inquilino en el que se implementa el servicio FHIR en las API de Azure Healthcare (que aquí se denomina servicio FHIR).


[![Seleccione asignación de roles. ](fhir/media/rbac/select-role-assignment.png) ](fhir/media/rbac/select-role-assignment.png#lightbox)

Para comprobar la asignación de roles, seleccione la **pestaña Asignaciones de** roles en la opción de menú Control de acceso **(IAM).**
 
## <a name="assign-roles-for-the-dicom-service"></a>Asignación de roles para el servicio DICOM

Para conceder acceso al plano de datos DICOM a los usuarios, entidades de servicio o grupos, seleccione la hoja **Control de acceso (IAM)** . Seleccione la pestaña **Asignaciones de roles** y seleccione **+ Agregar**.

[ ![Control de acceso a DICOM.](dicom/media/dicom-access-control.png) ](dicom/media/dicom-access-control.png#lightbox)

En la selección de **Rol**, busque uno de los roles integrados para el plano de datos de DICOM:

[ ![Incorporación de asignación de roles de RBAC.](dicom/media/rbac-add-role-assignment.png) ](dicom/media/rbac-add-role-assignment.png#lightbox)

Puede elegir entre:

* Propietario de datos DICOM: acceso total a los datos DICOM.
* Lector de datos DICOM: leer y buscar datos DICOM.

Si estos roles no son suficientes para sus necesidades, puede utilizar Powershell para crear roles personalizados.  Para obtener información sobre cómo crear roles personalizados, vea [Crear un rol personalizado mediante Azure PowerShell](../role-based-access-control/custom-roles-powershell.md).

En el cuadro **Seleccionar**, busque un usuario, una entidad de servicio o un grupo al que quiera asignar el rol.

> [!NOTE]
> Si no puede acceder al servicio FHIR o DICOM en la aplicación u otras herramientas, es posible que tenga que esperar unos minutos más para que la asignación de roles termine de propagarse en el sistema.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a asignar roles de Azure para el servicio FHIR y el servicio DICOM. Para obtener información sobre cómo acceder a las API de atención sanitaria mediante Postman, consulte

- [Acceso mediante Postman](use-postman.md)
- [Acceso mediante el cliente REST](using-rest-client.md)
- [Acceso mediante cURL](using-curl.md)
