---
author: baanders
description: archivo de inclusión que describe cómo configurar una función de Azure para que funcione con Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/14/2021
ms.author: baanders
ms.openlocfilehash: 6aabec311df33c1d08d12b48117d9763ccf1761d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748599"
---
Puede configurar el acceso de seguridad para la aplicación de funciones mediante la CLI de Azure o Azure Portal. Siga los pasos de su opción preferida.

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [digital-twins-configure-function-app-cli.md](digital-twins-configure-function-app-cli.md)]

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Realice los pasos siguientes en [Azure Portal](https://portal.azure.com/).

#### <a name="assign-an-access-role"></a>Asignación de un rol de acceso

[!INCLUDE [digital-twins-permissions-required.md](digital-twins-permissions-required.md)]

Una identidad administrada asignada por el sistema permite que los recursos de Azure se autentiquen en servicios en la nube (por ejemplo, Azure Key Vault) sin almacenar las credenciales en el código. Después de habilitar la identidad administrada asignada por el sistema, se pueden conceder todos los permisos necesarios a través del control de acceso basado en rol de Azure. 

El ciclo de vida de este tipo de identidad administrada está ligado al ciclo de vida de este recurso. Además, cada recurso puede tener solo una identidad administrada asignada por el sistema.

1. Busque la aplicación de funciones en [Azure Portal](https://portal.azure.com/); para ello, escriba su nombre en el cuadro de búsqueda. Seleccione la aplicación en los resultados. 

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/portal-search-for-function-app.png" alt-text="Captura de pantalla de Azure Portal.El nombre de la aplicación de funciones aparece en la barra de búsqueda del portal y el resultado de la búsqueda está resaltado.":::

1. En la página de la aplicación de funciones, en el menú de la izquierda, seleccione __Identidad__ para trabajar con una identidad administrada para la función. En la página __Asignado por el sistema__, compruebe que el __Estado__ está establecido en **Activado**. Si no es así, establézcalo ahora y **Guarde** el cambio.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/verify-system-managed-identity.png" alt-text="Captura de pantalla de Azure Portal. En la página Identidad de la aplicación de funciones, la opción Estado está establecida en Activado." lightbox="../articles/digital-twins/media/includes/azure-functions/verify-system-managed-identity.png":::

1. Seleccione __Asignaciones de roles de Azure__.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-1.png" alt-text="Captura de pantalla del Azure Portal. En la página Identidad de la función de Azure, en Permisos, está resaltado el botón Asignaciones de roles de Azure." lightbox="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-1.png":::

    Seleccione __+ Agregar asignación de roles (versión preliminar)__ .

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-2.png" alt-text="Captura de pantalla de Azure Portal. En la página Asignaciones de roles de Azure, el botón Agregar asignación de roles está resaltado." lightbox="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-2.png":::

1. En la página __Agregar asignación de roles (versión preliminar)__ , seleccione los siguientes valores:

    * **Ámbito**: _Grupo de recursos_.
    * **Suscripción**: Seleccione su suscripción a Azure.
    * **Grupo de recursos**: seleccione el grupo de recursos.
    * **Rol**: _Propietario de datos de Azure Digital Twins_.

    Seleccione __Guardar__ para guardar los detalles.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-3.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo agregar una nueva asignación de roles. El cuadro de diálogo muestra los campos Ámbito, Suscripción, Grupo de recursos y Rol.":::

#### <a name="configure-application-settings"></a>Configuración de la aplicación

Para hacer que la dirección URL de la instancia de Azure Digital Twins sea accesible para la función, establezca una variable de entorno. La configuración de la aplicación se expone como variables de entorno que permiten acceder a la instancia de Azure Digital Twins. Para más información sobre las variables de entorno, consulte [Administración de la aplicación de funciones](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

Para establecer una variable de entorno con la dirección URL de la instancia, busque primero el nombre de host de la instancia: 

1. Busque la instancia en [Azure Portal](https://portal.azure.com). 
1. En el menú de la izquierda, seleccione __Información general__. 
1. Copie el valor de __Nombre de host__.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/instance-host-name.png" alt-text="Captura de pantalla de Azure Portal. Está resaltado el valor Nombre de host en la página Información general de la instancia.":::

Ahora puede crear una configuración de la aplicación:

1. En la barra de búsqueda del portal, busque la aplicación de funciones y selecciónela en los resultados.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/portal-search-for-function-app.png" alt-text="Captura de pantalla de Azure Portal. Se está buscando el nombre de la aplicación de funciones en la barra de búsqueda del portal. El resultado de la búsqueda está resaltado.":::

1. En el lado izquierdo, seleccione __Configuración__. Después, en la pestaña __Configuración de la aplicación__, seleccione __+ Nueva configuración de la aplicación__.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/application-setting.png" alt-text="Captura de pantalla de Azure Portal. En la pestaña Configuración de la aplicación de funciones, el botón para crear una nueva configuración de aplicación está resaltado.":::

1. En la ventana que se abre, use el valor de nombre de host que copió para crear una configuración de la aplicación.
    * **Nombre**: `ADT_SERVICE_URL`
    * **Valor**: `https://<your-Azure-Digital-Twins-host-name>`
    
    Seleccione __Aceptar__ para crear una configuración de la aplicación.
    
    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-application-setting.png" alt-text="Captura de pantalla de Azure Portal. En la página Agregar o editar configuración de la aplicación, aparecen rellenados los campos Nombre y Valor. El botón Aceptar está resaltado.":::

1. Después de crear la configuración, debería aparecer en la pestaña __Configuración de la aplicación__. Compruebe que **ADT_SERVICE_URL** aparezca en la lista. A continuación, seleccione __Guardar__ para guardar la nueva configuración de la aplicación.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/application-setting-save-details.png" alt-text="Captura de pantalla de Azure Portal. En la pestaña Configuración de la aplicación, la nueva configuración A D T SERVICE URL y el botón Guardar también están resaltados.":::

1. Cualquier cambio en la configuración de la aplicación requiere un reinicio de la aplicación; por lo tanto, seleccione __Continuar__ para reiniciar la aplicación cuando se le solicite.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/save-application-setting.png" alt-text="Captura de pantalla de Azure Portal. Una nota indica que los cambios realizados en la configuración de la aplicación reiniciarán la aplicación.":::

---
