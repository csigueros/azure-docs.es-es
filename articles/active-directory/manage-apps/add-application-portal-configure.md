---
title: 'Inicio rápido: Configuración de las propiedades de aplicaciones empresariales'
titleSuffix: Azure AD
description: Configuración de las propiedades de una aplicación empresarial en Azure Active Directory.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: f6325dfceaa0ee6cedc60e5555b177e1f7a25d6c
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058287"
---
# <a name="quickstart-configure-enterprise-application-properties-in-azure-active-directory"></a>Inicio rápido: Configuración de las propiedades de aplicaciones empresariales en Azure Active Directory

En este inicio rápido, usará el Centro de administración de Azure Active Directory para configurar las propiedades de una aplicación empresarial que haya agregado anteriormente al inquilino de Azure Active Directory (Azure AD).

Puede configurar los siguientes atributos comunes de una aplicación empresarial:

- **¿Está habilitado para que los usuarios inicien sesión?** - Determina si los usuarios asignados a la aplicación pueden iniciar sesión.
- **¿Se requiere la asignación de usuarios?** - Determina si los usuarios que no están asignados a la aplicación pueden iniciar sesión.
- **¿Es visible para los usuarios?** - Determina si los usuarios asignados a una aplicación pueden verla en Aplicaciones y en el iniciador de aplicaciones de Microsoft 365. (Vea el menú de gofres en la esquina superior izquierda de un sitio web de Microsoft 365).
- **Logo** (Logotipo) - Determina el logotipo que representa la aplicación.
- **Notes** (Notas) - Proporciona un lugar para agregar notas que se aplican a la aplicación.

Para probar los pasos de este inicio rápido, se recomienda usar un entorno que no sea de producción.

## <a name="prerequisites"></a>Requisitos previos

Para configurar las propiedades de una aplicación empresarial, necesita:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.
- Finalización de los pasos de [Inicio rápido: Adición de una aplicación empresarial](add-application-portal.md).

## <a name="configure-application-properties"></a>Configurar las propiedades de la aplicación

Las propiedades de la aplicación controlan cómo se representa la aplicación y cómo se accede a ella.

Para editar las propiedades de la aplicación:

1. Vaya al [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) e inicie sesión con uno de los roles enumerados en los requisitos previos.
1. En el menú de la izquierda, seleccione **Aplicaciones empresariales**. Se abre el panel **Todas las aplicaciones**, en el que se ve una lista de las aplicaciones que hay en su inquilino de Azure AD. Busque y seleccione la aplicación que desea utilizar. Por ejemplo, **Azure AD SAML Toolkit 1**.
1. En la sección **Administrar**, seleccione **Propiedades** para abrir el panel **Propiedades** para editarlo.
1. Seleccione **Sí** o **No** para decidir si la aplicación está habilitada para que los usuarios puedan iniciar sesión.
1. Seleccione **Sí** o **No** para decidir si solo las cuentas de usuario que se han asignado a la aplicación pueden iniciar sesión.
1. Seleccione **Sí** o **No** para decidir si los usuarios asignados a una aplicación empresarial pueden verla en los portales de Aplicaciones y de Microsoft 365. 

    :::image type="content" source="media/add-application-portal-configure/configure-properties.png" alt-text="Configure las propiedades de una aplicación empresarial.":::

## <a name="use-a-custom-logo"></a>Uso de un logotipo personalizado

El logotipo de la aplicación se ve en los portales de Aplicaciones y de Microsoft 365, y cuando los administradores ven esta aplicación en la galería de aplicaciones empresariales. Los logotipos personalizados deben tener exactamente 215x215 píxeles de tamaño y estar en formato PNG. Se recomienda utilizar un fondo de color sólido sin transparencia en el logotipo de la aplicación para que los usuarios la puedan ver mejor.

Para usar un logotipo personalizado:

1. Cree un logotipo de 215 por 215 píxeles y guárdelo en formato .png.
1. Seleccione el icono en **Seleccionar un archivo** para descargar el logotipo.
1. Cuando haya terminado, seleccione **Guardar**.

La miniatura del logotipo no se actualiza de inmediato. Puede cerrar y volver a abrir el panel **Propiedades** para ver la miniatura actualizada.

## <a name="add-notes"></a>Agregar notas

Puede usar la propiedad **Notes** (Notas) para agregar información que sea adecuada para la administración de la aplicación en Azure AD. La propiedad **Notes** (Notas) es un campo de texto libre con un tamaño máximo de 1024 caracteres.

Para escribir notas para la aplicación:

1. Escriba las notas que desea conservar con la aplicación.
1. Seleccione **Guardar**.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si tiene previsto completar el siguiente inicio rápido, mantenga la aplicación empresarial que ha creado. De lo contrario, puede considerar la posibilidad de eliminarla para limpiar el inquilino. Para más información, consulte [Inicio rápido: Eliminación de una aplicación del inquilino](delete-application-portal.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo buscar y ver las aplicaciones en el inquilino de Azure AD.
> [!div class="nextstepaction"]
> [Ver las aplicaciones](view-applications-portal.md)
