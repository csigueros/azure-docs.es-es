---
title: 'ML Studio (clásico): Exportación y eliminación de sus datos: Azure'
description: Los datos integrados almacenados por Machine Learning Studio (clásico) están disponibles para su exportación y eliminación en Azure Portal y también mediante API REST autenticadas. Se puede acceder a los datos de telemetría en el Portal de privacidad de Azure. Este artículo le muestra cómo.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 47ca53ac4fa6aa30ae67fdaf9be3cf6eb3f489bb
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112581510"
---
# <a name="export-and-delete-in-product-user-data-from-machine-learning-studio-classic"></a>Exportación y eliminación de datos de usuario integrados desde Machine Learning Studio (clásico)

**SE APLICA A:**  ![Se aplica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clásico)   ![No se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)




Puede eliminar o exportar los datos integrados almacenados por Machine Learning Studio (clásico) mediante Azure Portal, la interfaz de Studio (clásico), PowerShell y las API REST autenticadas. En este artículo se indica cómo hacerlo. 

Se puede acceder a los datos de telemetría mediante el portal de privacidad de Azure. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>¿Qué tipo de datos de usuario recopila Studio (clásico)?

Para este servicio, los datos de usuario constan de información acerca de los usuarios autorizados a acceder a las áreas de trabajo y de los registros de telemetría de las interacciones del usuario con el servicio.

Hay dos tipos de datos de usuario en Machine Learning Studio (clásico):
- **Datos de la cuenta personal:** identificadores de cuenta y direcciones de correo electrónico asociados a una cuenta.
- **Datos del cliente:** los datos que se han cargado para analizar.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Tipos de cuenta de Studio (clásico) y cómo se almacenan los datos

Hay tres tipos de cuentas en Machine Learning Studio (clásico). El tipo de cuenta que posea determina cómo se almacenan los datos y cómo se pueden eliminar o exportar.

- Un **área de trabajo de invitado** es una cuenta gratuita y anónima. Puede registrarse sin proporcionar credenciales, como una dirección de correo electrónico o una contraseña.
    -  Los datos se purgan después de que expira el área de trabajo de invitado.
    - Los usuarios invitados pueden exportar los datos de cliente mediante la interfaz de usuario, las API REST o el paquete de PowerShell.
- Un **área de trabajo gratuita** es una cuenta gratuita en la que inicia sesión con las credenciales de una cuenta Microsoft: una dirección de correo electrónico y una contraseña.
    - Puede exportar y eliminar los datos personales y de cliente, que están sujetos a solicitudes de derechos del interesado (DSR).
    - Puede exportar los datos de cliente mediante la interfaz de usuario, las API REST o el paquete de PowerShell.
    - En el caso de las áreas de trabajo gratuitas que no usan cuentas de Azure AD, los datos de telemetría se pueden exportar mediante el Portal de privacidad.
    - Al eliminar el área de trabajo, se eliminan todos los datos de cliente personales.
- Un **área de trabajo estándar** es una cuenta de pago a la que se accede con credenciales de inicio de sesión.
    - Puede exportar y eliminar los datos personales y de cliente, que están sujetos a solicitudes de DSR.
    - Puede acceder a los datos en el Portal de privacidad de Azure
    - Puede exportar los datos personales y de cliente mediante la interfaz de usuario, las API REST o el paquete de PowerShell.
    - Puede eliminar los datos en Azure Portal.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Eliminación de los datos del área de trabajo en Studio (clásico) 

### <a name="delete-individual-assets"></a>Eliminación de recursos individuales

Los usuarios pueden eliminar recursos de un área de trabajo; para ello, selecciónelos y, a continuación, seleccione el botón Eliminar.

![Eliminación de recursos en Machine Learning Studio (clásico)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Eliminación de un área de trabajo completa

Los usuarios también pueden eliminar todo el área de trabajo:
- Área de trabajo de pago: eliminar mediante Azure Portal.
- Área de trabajo gratuita: use el botón Eliminar en el panel **Configuración**.

![Eliminación de un área de trabajo gratuita en Machine Learning Studio (clásico)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Exportación de datos de Studio (clásico) con PowerShell
Use PowerShell para exportar toda la información a un formato portátil desde Machine Learning Studio (clásico) mediante comandos. Para obtener información, vea el artículo [Módulo de PowerShell para Machine Learning Studio (clásico)](powershell-module.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener documentación sobre los servicios web y el plan de compromiso de facturación, vea [Referencia de la API REST de Machine Learning Studio (clásico)](/rest/api/machinelearning/).