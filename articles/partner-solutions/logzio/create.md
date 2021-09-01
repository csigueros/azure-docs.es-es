---
title: 'Creación de un recurso de Logz.io: soluciones de asociados de Azure'
description: Artículo de inicio rápido que describe cómo crear un recurso de Logz.io en Azure.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 07/28/2021
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 323508356678da00f75918fcd59244fd5c860bcc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780264"
---
# <a name="quickstart-create-a-logzio-resource-in-azure-portal"></a>Inicio rápido: Creación de un recurso de Logz.io en Azure Portal

En este artículo se describe cómo habilitar la integración en Azure del software como servicio (SaaS) Logz.io. Logz.io se puede usar para supervisar el estado y el rendimiento de un entorno de Azure.

## <a name="prerequisites"></a>Requisitos previos

- **Propietario de la suscripción**: para configurar Logz.io, es preciso tener asignado el [rol de Propietario](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) en la suscripción de Azure. Antes de comenzar esta integración, [compruebe el acceso](../../role-based-access-control/check-access.md).
- **Registrar el proveedor de recursos**: si `Microsoft.Insights` no se ha registrado en la suscripción, regístrelo. Para más información, consulte [Registro del proveedor de recursos](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

## <a name="find-offer"></a>Búsqueda de la oferta

Use Azure Portal para buscar Logz.io en Azure Marketplace.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Si ha visitado el **Marketplace** en una sesión reciente, seleccione el icono entre las opciones disponibles. En caso contrario, busque _Marketplace_.

    :::image type="content" source="./media/create/marketplace.png" alt-text="Azure Marketplace.":::

1. En Azure Marketplace, busque _Logz.io_.
1. Seleccione **Logz.io - Observability based on ELK & Prometheus**.
1. Seleccione el botón **Configurar y suscribirse**. Se abre la ventana **Create new Logz.io account** (Crear cuenta de Logz.io).

    :::image type="content" source="./media/create/logzio-app.png" alt-text="Aplicación de observabilidad Logz.io.":::

## <a name="create-new-logzio-account"></a>Creación de una cuenta de Logz.io

En la pestaña **Aspectos básicos** de la pantalla **Create a Logz.io account** (Crear una cuenta de Logz.io), especifique los valores siguientes:

| Propiedad | Descripción |
| ---- | ---- |
| **Suscripción** | En el menú desplegable, seleccione la suscripción de Azure a la que tenga acceso como propietario. |
| **Grupos de recursos** | Especifique si desea crear un grupo de recursos o utilizar uno existente. Un [grupo de recursos](../../azure-resource-manager/management/overview.md#resource-groups) es un contenedor que almacena los recursos relacionados con una solución de Azure. |
| **Nombre de cuenta de Logz** | Especifique el nombre de la cuenta de Logz.io que desea crear. |
| **Ubicación** | Seleccione **Oeste de EE. UU. 2** u **Oeste de Europa**. En la versión preliminar, Logz.io solo admite estas regiones de Azure. |
| **Plan de precios** | Selecciónelo en la lista de planes de Logz.io disponibles. |
| **Período de facturación** | El valor predeterminado es **Monthly** (Mensual). |
| **Precio** | Se especifica en función del plan de Logz.io seleccionado. |

:::image type="content" source="./media/create/basics.png" alt-text="Crear una cuenta de Logz.io.":::

Después de especificar los valores, seleccione el botón **Siguiente: Registros y métricas**.

## <a name="configure-logs"></a>Configuración de registros

En la pestaña **Logs and Metrics** (Registros y métricas), especifique qué recursos de Azure enviarán registros y métricas a Logz.io.

Hay dos tipos de registros que se pueden enviar desde Azure a Logz.io:

- **Registros de nivel de suscripción**: proporcionan información sobre las operaciones de todos y cada uno de los recursos de Azure que hay en la suscripción desde fuera (en el plano de administración) y de las actualizaciones de los eventos de **Service Health**. Use el **registro de actividad** para determinar la información de qué, quién y cuándo en las operaciones de escritura (PUT, POST, DELETE) realizadas en los recursos de la suscripción. Hay un único **registro de actividad** para cada suscripción de Azure.
- **Registros de recursos de Azure**: proporcionan información sobre las operaciones que se han ejecutado en un recurso de Azure (el plano de datos). Por ejemplo, obtener un secreto de un almacén de claves o realizar una solicitud a una base de datos. El contenido de estos registros de recurso varía según el servicio de Azure y el tipo de recurso.

Para enviar los registros de nivel de suscripción a Logz.io, se debe activar la casilla **Send subscription level logs** (Enviar registros de nivel de suscripción). Si esta casilla no está seleccionada, no se envía ninguno de los registros de nivel de suscripción a Logz.io.

Para enviar los registros de recursos de Azure a Logz.io, es preciso seleccionar la casilla **Send Azure resource logs for all defined resources** (Enviar registros de recursos de Azure para todos los recursos definidos). Los tipos de registros de recursos de Azure se enumeran en [Categorías admitidas en los registros de recursos de Azure](../../azure-monitor/essentials/resource-logs-categories.md). Para filtrar el conjunto específico de recursos de Azure que envían registros a Logz.io, se pueden usar las etiquetas de recursos de Azure.

Estas son las reglas de etiquetas para el envío de registros:

- De forma predeterminada, se recopilan registros para todos los recursos.
- Los recursos de Azure con etiquetas _Include_ envían registros a Logz.io.
- Los recursos de Azure con etiquetas _Exclude_ **no** envían registros a Logz.io.
- Si hay un conflicto entre las reglas de inclusión y las de exclusión, tiene prioridad estas últimas.

:::image type="content" source="./media/create/logs.png" alt-text="Configurar registros y métricas.":::

Después de configurar los registros y las métricas, seleccione el botón **Siguiente: Etiquetas**.

## <a name="add-custom-tags"></a>Adición de etiquetas personalizadas

Para especificar etiquetas personalizadas para el nuevo recurso de Logz.io, agregue pares clave-valor.

Cada par clave-valor incluye un **nombre** y un **valor**:

| Propiedad | Descripción |
| ---- | ---- |
| **Nombre** | Nombre de la etiqueta correspondiente al recurso de Azure Logz.io. |
| **Valor** | Valor de la etiqueta correspondiente al recurso de Azure Logz.io. |

:::image type="content" source="./media/create/tags.png" alt-text="Agregar etiquetas personalizadas.":::

Tras agregar las etiquetas, seleccione el botón **Siguiente: Inicio de sesión único**.

## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El inicio de sesión único (SSO) es una característica opcional:

- Para no participar en el inicio de sesión único, omita este paso.
- Para participar en el inicio de sesión único, consulte [Configuración del inicio de sesión único de Logz.io](setup-sso.md).

Una vez que se configura AAD, en la pestaña **Inicio de sesión único**, seleccione su aplicación de inicio de sesión único de Logz.io.

:::image type="content" source="./media/create/sso.png" alt-text="Configurar el inicio de sesión único.":::

Seleccione el botón **Siguiente: Revisar y crear** para ir al paso final para la creación del recurso.

## <a name="create-resource"></a>Creación del recurso

En la página **Revisar y crear** se ejecutan todas las validaciones. Puede revisar todas las selecciones realizadas en las pestañas **Basics** (Aspectos básicos), **Logs and metrics** (Registros y métricas), **Tags** (Etiquetas) e **Single sign-on** (Inicio de sesión único). También puede revisar los términos y condiciones de Logz.io y Azure Marketplace.

Revise toda la información para comprobar que es correcta. Para comenzar la implementación, seleccione el botón **Create** (Crear).

:::image type="content" source="./media/create/create-resource.png" alt-text="Revisar y crear una cuenta.":::

Si la implementación se ha realizado correctamente, al seleccionar el botón **Ir al recurso** verá el recurso de Logz.io implementado.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [administrar](manage.md) la integración de Logz.io.
- Para solucionar los problemas de la integración, consulte la [solución de problemas](troubleshoot.md).
