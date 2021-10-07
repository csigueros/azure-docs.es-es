---
title: Procedimientos para usar API REST para planos de datos de Purview
description: En este tutorial se describe cómo usar las API REST de Azure Purview para acceder al contenido de Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/17/2021
ms.openlocfilehash: 5074e0017a5811b9418771aeef3b0883e10891c1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212824"
---
# <a name="tutorial-use-the-rest-apis"></a>Tutorial: Uso de las API REST

En este tutorial aprenderá a usar las API REST de Azure Purview. Cualquier persona puede usar las API REST para enviar datos a una instancia de Azure Purview, incluir Purview como parte de un proceso automatizado o crear su propia experiencia de usuario en Purview.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

* Para comenzar, debe tener ya una cuenta de Azure Purview. Si no tiene un catálogo, consulte el [inicio rápido para crear una cuenta de Azure Purview](create-catalog-portal.md).

## <a name="create-a-service-principal-application"></a>Creación de una entidad de servicio (aplicación)

Para que un cliente de la API REST acceda al catálogo, debe tener una entidad de servicio (aplicación) y una identidad que el catálogo reconozca y cuya configuración sea de confianza. Al realizar llamadas de API REST al catálogo, se usa la identidad de la entidad de servicio.

Muchos de los clientes que han usado entidades de servicio existentes (identificadores de aplicación) se han encontrado errores. Por lo tanto, se recomienda crear una nueva entidad de servicio para llamar a las API.

Para crear una entidad de servicio:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En el portal, busque y seleccione **Azure Active Directory**.
1. En la página **Azure Active Directory**, seleccione **Registros de aplicaciones** en el panel izquierdo.
1. Seleccione **Nuevo registro**.
1. En la página **Registrar una aplicación**:
    1. Escriba un valor en **Nombre** para la aplicación (nombre de la entidad de servicio).
    1. Seleccione **Accounts in this organizational directory only (Solo las cuentas de este directorio organizativo) ( _&lt;nombreDelInquilino&gt;_ solo: inquilino único)** .
    1. En **URI de redirección (opcional)** , seleccione **Web** y escriba un valor (no tiene por qué ser un URI válido).
    1. Seleccione **Registrar**.
1. En la página de la nueva entidad de servicio, copie los valores de **Nombre para mostrar** y **Id. de la aplicación (cliente)** y guárdelos para más adelante.

   El identificador de la aplicación es el valor de `client_id` del código de ejemplo.

Para usar la entidad de servicio (aplicación), debe obtener la contraseña. A continuación, se indica cómo puede hacerlo.

1. En Azure Portal, busque y seleccione **Azure Active Directory** y luego seleccione **Registros de aplicaciones** en el panel izquierdo.
1. Seleccione la entidad de servicio (aplicación) en la lista.
1. Seleccione **Certificados y secretos** en el menú.
1. Seleccione **Nuevo secreto de cliente**.
1. En la página **Add a client secret** (Agregar un secreto de cliente), escriba lo que corresponda en **Descripción**, seleccione una hora de expiración en **Expiración** y, después, **Agregar**.

   En la página **Client secrets** (Secretos de cliente), la cadena de la columna **Valor** del secreto nuevo es la contraseña. Guarde esta cadena.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="Captura de pantalla que muestra un secreto de cliente.":::

## <a name="set-up-authentication-using-service-principal"></a>Configuración de la autenticación mediante una entidad de servicio

Una vez creada la entidad de servicio, debe asignar los roles de plano de datos de la cuenta de Purview a la entidad de servicio creada anteriormente. Es necesario seguir los pasos siguientes para asignar el rol y establecer la confianza entre la entidad de servicio y la cuenta de Purview.

1. Vaya a [Purview Studio](https://web.purview.azure.com/resource/).
1. Seleccione Data Map (Mapa de datos) en el menú izquierdo.
1. Seleccione Collections (Colecciones).
1. Seleccione la colección raíz en el menú de colecciones. Es la colección superior de la lista y tendrá el mismo nombre que la cuenta de Purview.
1. Seleccione la pestaña Asignaciones de roles.
1. Asigne los siguientes roles a la entidad de servicio creada anteriormente para acceder a varios planos de datos en Purview.
    1. Rol "Administrador de datos" para acceder al plano de datos del catálogo.
    1. Rol "Administrador de orígenes de datos" para acceder al plano de datos de análisis. 
    1. Rol "Administrador de colecciones" para acceder al plano de datos de la cuenta.
    1. Rol "Administrador de colecciones" para acceder al plano de datos de la directiva de metadatos.

> [!Note]
> Solo el rol "Administrador de colecciones" puede asignar roles del plano de datos en Purview [Control de acceso en Azure Purview](./catalog-permissions.md).

## <a name="get-token"></a>Obtener el token
Puede enviar una solicitud POST a la siguiente dirección URL para obtener el token de acceso.

https://login.microsoftonline.com/{your-tenant-id}/oauth2/token

Los parámetros siguientes deben pasarse a la dirección URL anterior.

- **client_id**: identificador de cliente de la aplicación registrada en Azure Active Directory y se asigna a un rol de plano de datos para la cuenta de Purview.
- **client_secret**: secreto de cliente creado para la aplicación anterior.
- **grant_type**: este debería ser "client_credentials".
- **resource**: este debería ser "https://purview.azure.net".
 
Ejemplo de token de respuesta:

```json
    {
        "token_type": "Bearer",
        "expires_in": "86399",
        "ext_expires_in": "86399",
        "expires_on": "1621038348",
        "not_before": "1620951648",
        "resource": "https://purview.azure.net",
        "access_token": "<<access token>>"
    }
```

Use el token de acceso anterior para llamar a las API del plano de datos.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Administración de orígenes de datos](manage-data-sources.md)
> [API REST del plano de datos de Purview](/rest/api/purview/)
