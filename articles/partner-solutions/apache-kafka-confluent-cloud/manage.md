---
title: 'Administración de una instancia de Confluent Cloud: soluciones de partners de Azure'
description: En este artículo se describe la administración de una instancia de Confluent Cloud en Azure Portal. Cómo configurar el inicio de sesión único, eliminar una organización de Confluent y obtener soporte técnico.
ms.service: partner-services
ms.topic: conceptual
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: c1e53382b6f399bccac53a75595eda4e61a915a4
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112521003"
---
# <a name="manage-the-confluent-cloud-resource"></a>Administración del recurso de Confluent Cloud

En este artículo se describe cómo administrar la instancia de Apache Kafka para Confluent Cloud en Azure. Se muestra cómo configurar el inicio de sesión único (SSO) y cómo eliminar una organización de Confluent.

## <a name="single-sign-on"></a>Inicio de sesión único

Para implementar el inicio de sesión único para su organización, el administrador de inquilinos puede importar la aplicación de la galería. Este paso es opcional. Para obtener información sobre cómo importar una aplicación, consulte [Inicio rápido: Incorporación de una aplicación al inquilino de Azure Active Directory (Azure AD)](../../active-directory/manage-apps/add-application-portal.md) Cuando el administrador de inquilinos importa la aplicación, los usuarios no tienen que dar su consentimiento explícito para permitir el acceso al portal de Confluent Cloud.

Para habilitar el inicio de sesión único, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Vaya a la **Información general** de la instancia del recurso de Confluent Cloud.
1. Seleccione el vínculo para **administrar en Confluent Cloud**.

   :::image type="content" source="media/manage/sso-link.png" alt-text="Inicio de sesión único del portal de Confluent.":::

1. Si el administrador de inquilinos no ha importado la aplicación de la galería para el consentimiento del SSO, conceda los permisos y el consentimiento. Este paso solo es necesario la primera vez que se tiene acceso al vínculo para **administrar en Confluent Cloud**.

   :::image type="content" source="media/manage/permissions-requested.png" alt-text="Conceder permisos.":::

1. Elija una cuenta de Azure AD para el inicio de sesión único en el portal de Confluent Cloud.
1. Una vez proporcionado el consentimiento, se le redirigirá al portal de Confluent Cloud.

## <a name="set-up-cluster"></a>Configuración del clúster

Para obtener información acerca de cómo configurar el clúster, consulte [Creación de un clúster en Confluent Cloud: documentación de Confluent](https://docs.confluent.io/cloud/current/clusters/create-cluster.html).

## <a name="delete-confluent-organization"></a>Eliminación de organización de Confluent

Cuando ya no necesite el recurso de Confluent Cloud, elimine el recurso en Azure y Confluent Cloud.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para eliminar los recursos en Azure:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione **Todos los recursos** y **filtre por el nombre** del recurso de Confluent Cloud o el **tipo de recurso** _Organización de Confluent_. O bien, en Azure Portal, busque el nombre del recurso.
1. En la **Información general** del recurso, seleccione **Eliminar**.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Icono de eliminación de recursos.":::

1. Para confirmar la eliminación, escriba el nombre del recurso y seleccione **Eliminar**.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="Solicitud de confirmación de eliminación de recursos.":::

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Empiece por preparar el entorno para la CLI de Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Después de iniciar sesión, use el comando [az confluent organization delete](/cli/azure/confluent#az_confluent_organization_delete) para eliminar el recurso de organización por su nombre:

```azurecli
az confluent organization delete --name "myOrganization" --resource-group "myResourceGroup"
```

O bien, por identificador de recurso:

```azurecli
az confluent organization delete --id "/subscriptions/{SubID}/resourceGroups/{myResourceGroup}/providers/Microsoft.Confluent/organizations/{myOrganization}"
```

---

Para eliminar el recurso en Confluent Cloud, consulte la documentación de [Entornos de Confluent Cloud: documentación de Confluent](https://docs.confluent.io/current/cloud/using/environments.html) y [Aspectos básicos de Confluent Cloud: documentación de Confluent](https://docs.confluent.io/current/cloud/using/cloud-basics.html).

El clúster y todos los datos del clúster se eliminan de forma permanente. Si el contrato incluye una cláusula de retención de datos, Confluent mantiene los datos durante el período de tiempo especificado en los [términos del servicio de la documentación de Confluent](https://www.confluent.io/confluent-cloud-tos).

Se le facturará el uso prorrateado hasta el momento de la eliminación del clúster. Una vez que el clúster se elimina permanentemente, Confluent le envía una confirmación por correo electrónico.

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda con la solución de problemas, consulte [Solución de problemas de las soluciones de Apache Kafka para Confluent Cloud](troubleshoot.md).

Si necesita ponerse en contacto con el soporte técnico, consulte [Obtención de soporte técnico para el recurso de Confluent Cloud](get-support.md).
