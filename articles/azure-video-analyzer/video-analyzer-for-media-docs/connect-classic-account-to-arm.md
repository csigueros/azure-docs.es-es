---
title: Conexión de una cuenta clásica de Video Analyzer for Media a ARM
description: En este tema se explica cómo conectar una cuenta clásica de pago de Azure Video Analyzer for Media existente a una cuenta basada en ARM.
ms.topic: how-to
ms.author: itnorman
ms.date: 10/19/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 14ab4e2b3c95823359dc1f1f88b8368ce58afef9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312602"
---
# <a name="connect-an-existing-classic-paid-video-analyzer-for-media-account-to-arm-based-account"></a>Conexión de una cuenta clásica de pago de Azure Video Analyzer for Media existente a una cuenta basada en ARM.  

En este artículo se explica cómo conectar una cuenta clásica de pago de Azure Video Analyzer for Media existente a una cuenta basada en Azure Resource Manager (ARM).
En la actualidad, Azure Video Analyzer for Media (anteriormente Video Indexer), es un producto con disponibilidad general que no es un recurso de ARM en Azure.
En este artículo, se analizarán las opciones para conectar la cuenta **existente** de Video Analyzer for Media a [ARM][docs-arm-overview].

## <a name="prerequisites"></a>Prerrequisitos

* Cuenta ilimitada de pago de Video Analyzer for Media (cuenta clásica).
  * Para realizar la acción de conexión a ARM, debe tener permisos de propietario en la cuenta de Video Analyzer for Media.
* Suscripción a Azure.
* Identidad administrada asignada por el usuario (se puede crear a lo largo del flujo).

#### <a name="transition-state-and-recommended-steps-before-connecting-a-classic-account-to-be-arm-based"></a>Estado de transición y pasos recomendados antes de conectar una cuenta clásica para que se base en ARM

En el proceso de conexión, la administración de cuentas está conectada a ARM, lo que desencadenará 30 días de un estado de transición para la cuenta. En ese estado, se puede acceder a una cuenta conectada a través de la API con el token de acceso [generado mediante API Management](https://aka.ms/avam-dev-portal) (forma clásica) o mediante un token de acceso generado con ARM. El estado de transición, que traslada toda la administración de la cuenta para que ARM la administre, deshabilitará la característica de invitar a un usuario desde el portal de Video Analyzer for Media, debido a que [RBAC de Azure][docs-rbac-overview] controlará la administración de la cuenta. Esto hará que todos los usuarios invitados de esta cuenta pierdan su acceso al portal multimedia de la cuenta de Video Analyzer for Media. Por supuesto, esto se puede resolver fácilmente mediante la asignación de roles adecuada a todos estos usuarios mediante RBAC de Azure: [Asignación de RBAC][docs-rbac-assignment]. Solo el propietario de la cuenta, que realizó la acción de conexión, se asignará automáticamente como propietario en la cuenta conectada. Si los usuarios no se agregan mediante RBAC de Azure a la cuenta después de 30 días, perderán el acceso también a través de la API, ya que una vez que finalice el estado de transición, ningún usuario podrá generar un token de acceso válido mediante APIM (forma clásica), sino solo a través de ARM. Convertir RBAC de Azure en la manera exclusiva de administrar el control de acceso basado en roles en la cuenta.

> [!NOTE]
> Si hay usuarios invitados a los que desea quitar su acceso antes del final de los 30 días del estado de transición, debe hacerlo mediante la configuración de la cuenta de Azure Video Analyzer for Media **antes** de conectar la cuenta a ARM. 

## <a name="get-started"></a>Primeros pasos

### <a name="browse-to-video-analyzer-for-media-portal"></a>Vaya al [portal de Video Analyzer for Media](https://aka.ms/vi-portal-link).

1. Inicie sesión mediante su cuenta de AAD.
1. En la barra superior derecha, presione *Cuenta de usuario* para abrir la lista de cuentas del panel lateral.
3. Seleccione la cuenta clásica de Video Analyzer for Media que desea conectar a ARM (las cuentas clásicas estarán etiquetadas con una *etiqueta clásica*).
4. Haga clic en **Configuración**.

  ![account-settings](media/connect-classic-account-to-arm/user-account-settings.png)
   
5. Haga clic en **Conexión a una cuenta basada en ARM**.

  ![connect-button-portal](media/connect-classic-account-to-arm/connect-button.png)

7. Inicio de sesión en Azure Portal
8. Se abrirá la hoja Crear de Video Analyzer for Media.
10. En la sección **Creación de una cuenta de Video Analyzer for Media** escriba los valores necesarios.
    * Si ha seguido los pasos, los campos deben rellenarse automáticamente. Asegúrese de validar los valores.

 ![connect-to-arm](media/connect-classic-account-to-arm/connect-blade-new.png)

 | Nombre | Descripción |
 | ---|---|
 |**Suscripción**| La suscripción contiene actualmente la cuenta clásica y otros recursos relacionados, como Media Services.|
 |**Grupo de recursos**|Seleccione un recurso existente, o bien cree uno. El grupo de recursos debe tener la misma ubicación que la cuenta clásica a la que se está conectando.|
 |**Cuenta de Video Analyzer for Media** (botón de radio)| Seleccione *"Conexión de una cuenta clásica existente".*|
 |**Id. de cuenta existente**| Escriba el identificador de la cuenta clásica existente de Video Analyzer for Media.|
 |**Nombre del recurso**|Escriba el nombre de la nueva cuenta de Video Analyzer for Media. El valor predeterminado será el mismo nombre de la cuenta clásica.|
 |**Ubicación**|La región geográfica se puede cambiar en el proceso de conexión, la cuenta conectada debe permanecer en la misma región. |
 |**Nombre de la cuenta de Media Services**|El nombre de la cuenta de Media Services original que se asoció a la cuenta clásica.|
 |**Identidad administrada asignada por el usuario**|Seleccione una identidad administrada asignada por el usuario o cree una nueva. La cuenta de Video Analyzer for Media la usará para acceder a Media Services. A la identidad administrada asignada por el usuario se le otorgará el rol Colaborador de la cuenta de Media Services.|

1. En la parte inferior del formulario, haga clic en **Revisar y crear**.

### <a name="next-steps"></a>Pasos siguientes

Aprenda a [cargar un vídeo con C#](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/ApiUsage/ArmBased).
  
<!-- links -->
[docs-arm-overview]: ../../azure-resource-manager/management/overview.md
[docs-rbac-overview]: ../../role-based-access-control/overview.md
[docs-rbac-assignment]: ../../role-based-access-control/role-assignments-portal.md
