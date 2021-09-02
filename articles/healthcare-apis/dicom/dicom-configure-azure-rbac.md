---
title: 'Configuración de RBAC de Azure para el servicio DICOM: Azure Healthcare APIs'
description: En este artículo se explica cómo configurar RBAC de Azure para el servicio DICOM
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2020
ms.author: aersoy
ms.openlocfilehash: 76d2fa40799d97a7fb2284dbc6abf3ac141552d9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780522"
---
# <a name="configure-azure-rbac-for-the-dicom-service"></a>Configuración de Azure RBAC para el servicio DICOM

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general. 

En este artículo aprenderá a usar el [Control de acceso basado en roles (Azure RBAC) de Azure](../../role-based-access-control/index.yml) para asignar acceso al servicio DICOM. 

## <a name="assign-roles"></a>Asignación de roles

Para conceder acceso al plano de datos DICOM a los usuarios, entidades de servicio o grupos, seleccione la hoja **Control de acceso (IAM)** . Seleccione la pestaña **Asignaciones de roles** y seleccione **+ Agregar**.

[ ![Control de acceso a DICOM.](media/dicom-access-control.png) ](media/dicom-access-control.png#lightbox)


En la selección de **Rol**, busque uno de los roles integrados para el plano de datos de DICOM:

[ ![Incorporación de asignación de roles de RBAC.](media/rbac-add-role-assignment.png) ](media/rbac-add-role-assignment.png#lightbox)

Puede elegir entre:

* Propietario de datos DICOM: acceso total a los datos DICOM.
* Lector de datos DICOM: leer y buscar datos DICOM.

Si estos roles no son suficientes para sus necesidades, puede utilizar Powershell para crear roles personalizados.  Para obtener información sobre cómo crear roles personalizados, vea [Crear un rol personalizado mediante Azure PowerShell](../../role-based-access-control/tutorial-custom-role-powershell.md).

En el cuadro **Seleccionar**, busque un usuario, una entidad de servicio o un grupo al que quiera asignar el rol.

## <a name="caching-behavior"></a>Comportamiento del almacenamiento en caché

El servicio DICOM almacenará en caché las decisiones durante un máximo de cinco minutos. Si concede a un usuario acceso al servicio DICOM al agregarlo a la lista de identificadores de objeto permitidos, o lo quita de la lista, debe dejar hasta cinco minutos para que se propaguen los cambios de permisos.

## <a name="next-steps"></a>Pasos siguientes

En este artículo aprendió a asignar roles de Azure para el plano de datos del servicio DICOM. 
 
>[!div class="nextstepaction"]
>[Información general del servicio DICOM](dicom-services-overview.md)