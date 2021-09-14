---
title: Exportación de plantilla en Azure Portal
description: Use Azure Portal para exportar una plantilla de Azure Resource Manager desde los recursos de la suscripción.
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: c6987f95f2ccb953977244d8ff70b2f925f35f2e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436245"
---
# <a name="use-azure-portal-to-export-a-template"></a>Uso de Azure Portal para exportar una plantilla

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

En este artículo, se muestra cómo exportar plantillas mediante el **portal**. Para conocer otras opciones, consulte:

* [Exportación de plantilla con la CLI de Azure](export-template-cli.md)
* [Exportación de plantilla con Azure PowerShell](export-template-powershell.md)
* [Exportación de API REST a partir de un grupo de recursos](/rest/api/resources/resourcegroups/exporttemplate) y [Exportación de API REST a partir del historial de implementación](/rest/api/resources/deployments/export-template).

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>Exportación de la plantilla desde un grupo de recursos

Para exportar uno o varios recursos de un grupo de recursos:

1. Seleccione el grupo de recursos que contiene los recursos que quiere exportar.

1. Seleccione uno o varios recursos activando las casillas.  Para seleccionar todo, active la casilla situada a la izquierda de **Nombre**. El elemento de menú **Exportar plantilla** solo se habilita después de seleccionar al menos un recurso.

   ![Exportar todos los recursos](./media/export-template-portal/select-all-resources.png)

    En la captura de pantalla, solo se selecciona la cuenta de almacenamiento.
1. Seleccione **Exportar plantilla**.

1. Se mostrará la plantilla exportada, que estará disponible para descargar e implementar.

   ![Mostrar la plantilla](./media/export-template-portal/show-template.png)

   **Incluir parámetros** está seleccionado de forma predeterminada.  Cuando se selecciona esta opción, se incluirán todos los parámetros de plantilla cuando se genere la plantilla. Si desea crear sus propios parámetros, desactive esta casilla para no incluirlos.

## <a name="export-template-from-a-resource"></a>Exportación de la plantilla desde un recurso

Para exportar un recurso:

1. Seleccione el grupo de recursos que contiene el recurso que quiere exportar.

1. Seleccione el recurso que quiere exportar para abrir el recurso.

1. Para ese recurso, seleccione **Exportar plantilla** en el panel de la izquierda.

   ![Exportación del recurso](./media/export-template-portal/export-single-resource.png)

1. Se mostrará la plantilla exportada, que estará disponible para descargar e implementar. La plantilla solo contiene ese único recurso. **Incluir parámetros** está seleccionado de forma predeterminada.  Cuando se selecciona esta opción, se incluirán todos los parámetros de plantilla cuando se genere la plantilla. Si desea crear sus propios parámetros, desactive esta casilla para no incluirlos.

## <a name="download-template-before-deployment"></a>Descarga de la plantilla antes de la implementación

El portal tiene la opción de descargar una plantilla antes de implementarla. Esta opción no está disponible mediante PowerShell ni la CLI de Azure.

1. Seleccione el servicio de Azure que quiera implementar.

1. Rellene los valores para el nuevo servicio.

1. Después de pasar la validación, pero antes de iniciar la implementación, seleccione **Descargar una plantilla para la automatización**.

   ![Descarga de una plantilla](./media/export-template-portal/download-before-deployment.png)

1. Se mostrará la plantilla y estará disponible para descargarla e implementarla.

## <a name="export-template-after-deployment"></a>Exportación de la plantilla después de la implementación

Puede exportar la plantilla que se ha usado para implementar los recursos existentes. La plantilla que se obtiene es exactamente la que se ha usado para la implementación.

1. Seleccione el grupo de recursos que quiera exportar.

1. Seleccione el vínculo situado bajo **Implementaciones**.

   ![Selección del historial de implementación](./media/export-template-portal/select-deployment-history.png)

1. Seleccione una de las implementaciones del historial de implementación.

   ![Selección de la implementación](./media/export-template-portal/select-details.png)

1. Seleccione **Plantilla**. Se mostrará la plantilla que se ha usado para esta implementación y estará disponible para descargarla.

   ![Seleccionar plantilla](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a exportar plantillas con la [CLI de Azure](export-template-cli.md), [Azure PowerShell](export-template-powershell.md) o la [API REST](/rest/api/resources/resourcegroups/exporttemplate).
- Para obtener información sobre la sintaxis de las plantillas de Resource Manager, consulte [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](./syntax.md).
- Para obtener información sobre cómo desarrollar plantillas, consulte los [tutoriales paso a paso](../index.yml).