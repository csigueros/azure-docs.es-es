---
title: Configuración de Microsoft Defender para Storage
titleSuffix: Azure Storage
description: Configure Microsoft Defender para Storage con el fin de detectar anomalías en la actividad de su cuenta y recibir notificaciones cuando haya intentos de acceso a la cuenta potencialmente peligrosos.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 17cc82bd448910cc92eec42db889605c48b65fb5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297019"
---
# <a name="configure-microsoft-defender-for-storage"></a>Configuración de Microsoft Defender para Storage

Microsoft Defender para Storage proporciona una capa adicional de inteligencia de seguridad que detecta intentos poco habituales y potencialmente peligrosos de acceder a las cuentas de almacenamiento o vulnerarlas. Esta capa de protección le permite afrontar las amenazas sin necesidad de ser un experto en seguridad ni administrar sistemas de supervisión de la seguridad.

Las alertas de seguridad se desencadenan cuando se producen anomalías en una actividad. Estas alertas de seguridad se integran con [Microsoft Defender for Cloud](https://azure.microsoft.com/services/security-center/) y también se envían por correo electrónico a los administradores de las suscripciones, con detalles de la actividad sospechosa y recomendaciones sobre cómo investigar y solucionar las amenazas.

El servicio ingiere los registros de recursos de las solicitudes de lectura, escritura y eliminación en Blob Storage y en Azure Files para la detección de amenazas. Para investigar las alertas de Microsoft Defender for Cloud, puede ver la actividad de almacenamiento relacionada en los registros de Storage Analytics. Para más información, consulte **Configuración del registro** en [Supervisión de una cuenta de almacenamiento en Azure Portal](./manage-storage-analytics-logs.md#configure-logging).

## <a name="availability"></a>Disponibilidad

Microsoft Defender para Storage está disponible actualmente para Blob Storage, Azure Files y Azure Data Lake Storage Gen2. Los tipos de cuenta que admiten Microsoft Defender para Storage son: De uso general v2, de blob en bloques y de Blob Storage. Microsoft Defender para Storage está disponible en todas las nubes públicas y de la Administración pública estadounidense, pero no en otras regiones de nubes soberanas o de Azure Government.

Las cuentas con espacios de nombres jerárquicos habilitados para Data Lake Storage admiten transacciones que usan las API de Azure Blob Storage y las API de Data Lake Storage. Los recursos compartidos de Azure admiten transacciones en SMB.

Para obtener más información sobre los precios, incluida una evaluación gratuita durante 30 días, consulte la [página de precios de Microsoft Defender for Cloud](https://azure.microsoft.com/pricing/details/security-center/).

En la lista siguiente, se resume la disponibilidad de Microsoft Defender para Storage:

- Estado de la versión:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (disponibilidad general)
  - [Azure Files](../files/storage-files-introduction.md) (disponibilidad general)
  - Azure Data Lake Storage Gen2 (disponibilidad general)
- Nubes: ✔ Nubes comerciales<br>
    ✔ Azure Government<br>
    ✘ Azure China 21Vianet

## <a name="set-up-microsoft-defender-for-cloud"></a>Configuración de Microsoft Defender for Cloud

Microsoft Defender para Storage se puede configurar de distintas formas, que se explican en las secciones siguientes.

### <a name="microsoft-defender-for-cloud"></a>[Microsoft Defender for Cloud](#tab/azure-security-center)

Microsoft Defender para Storage está integrado en Microsoft Defender for Cloud. Cuando habilita las características de seguridad mejoradas de Microsoft Defender for Cloud en su suscripción, se habilita automáticamente Microsoft Defender para Storage en todas las cuentas de almacenamiento. Si desea habilitar o deshabilitar Defender para Storage en cuentas de almacenamiento individuales de una suscripción específica, siga estos pasos:

1. Inicie **Microsoft Defender for Cloud** en [Azure Portal](https://portal.azure.com).
1. En el menú principal de Defender for Cloud, seleccione **Environment settings** (Configuración del entorno).
1. Seleccione la suscripción para la que desea habilitar o deshabilitar Microsoft Defender for Cloud.
1. Seleccione **Enable all Microsoft Defender plans** (Habilitar todos los planes de Microsoft Defender) para habilitar Microsoft Defender for Cloud en la suscripción.
1. En **Select Azure Defender plan by resource type** (Seleccionar plan de Microsoft Defender por tipo de recurso), busque la fila **Storage** y seleccione **On** (Habilitado) en la columna **Plan**.
1. Guarde los cambios.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Captura de pantalla que muestra cómo habilitar Microsoft Defender para Storage.":::

Microsoft Defender está ahora habilitado en todas las cuentas de almacenamiento de esta suscripción.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie [Azure Portal](https://portal.azure.com/).
1. Vaya a la cuenta de almacenamiento. En **Configuración**, seleccione **Seguridad avanzada**.
1. Seleccione **Habilitar Microsoft Defender para Storage**.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Captura de pantalla que muestra cómo habilitar una cuenta para Microsoft Defender para Storage.":::

Microsoft Defender para Storage está ahora habilitado en esta cuenta de almacenamiento.​

### <a name="template"></a>[Plantilla](#tab/template)

Use una plantilla de Azure Resource Manager para implementar una cuenta de Azure Storage con Microsoft Defender para Storage habilitado. Para más información, consulte [Storage account with Advanced Threat Protection](https://azure.microsoft.com/resources/templates/storage-advanced-threat-protection-create/) (Cuenta de Storage con Advanced Threat Protection).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Use una directiva de Azure Policy para habilitar Microsoft Defender for Cloud en todas las cuentas de almacenamiento de un grupo de recursos o una suscripción específicos.

1. Inicie la página **Directiva: Definiciones** de Azure.
1. Busque la directiva **Deploy Microsoft Defender for Storage accounts** (Implementar Microsoft Defender para cuentas de almacenamiento).

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Aplicación de una directiva para habilitar Microsoft Defender para cuentas de almacenamiento.":::

1. Seleccione una suscripción a Azure o un grupo de recursos.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Selección de una suscripción o grupo de recursos para el ámbito de la directiva":::

1. Asigne la directiva.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Asignación de una directiva para habilitar Microsoft Defender para Storage.":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Si desea habilitar Microsoft Defender para Storage en una cuenta de almacenamiento mediante PowerShell, asegúrese primero de que esté instalado el módulo [Az.Security](https://www.powershellgallery.com/packages/Az.Security). A continuación, llame al comando [Enable-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection). No olvide reemplazar los valores entre corchetes angulares por sus propios valores:

```azurepowershell
Enable-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

Para comprobar la configuración de Microsoft Defender para Storage en una cuenta de almacenamiento mediante PowerShell, llame al comando [Get-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection). No olvide reemplazar los valores entre corchetes angulares por sus propios valores:

```azurepowershell
Get-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para habilitar Microsoft Defender para Storage en una cuenta de almacenamiento mediante la CLI de Azure, llame al comando [az security atp storage update](/cli/azure/security/atp/storage#az_security_atp_storage_update). No olvide reemplazar los valores entre corchetes angulares por sus propios valores:

```azurecli
az security atp storage update \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --is-enabled true
```

Para comprobar la configuración de Microsoft Defender para Storage en una cuenta de almacenamiento mediante la CLI de Azure, llame al comando [az security atp storage show](/cli/azure/security/atp/storage#az_security_atp_storage_show). No olvide reemplazar los valores entre corchetes angulares por sus propios valores:

```azurecli
az security atp storage show \
    --resource-group <resource-group> \
    --storage-account <storage-account>
```

---

## <a name="explore-security-anomalies"></a>Exploración de anomalías de seguridad

Cuando se producen anomalías en la actividad de almacenamiento, recibe una notificación por correo electrónico con información acerca del evento de seguridad sospechoso. Los detalles del evento incluyen:

- La naturaleza de la anomalía
- El nombre de la cuenta de almacenamiento
- La hora del evento
- El tipo de almacenamiento
- Las causas posibles
- Los pasos de investigación
- Los pasos para la corrección

El correo electrónico también incluye detalles acerca de las posibles causas y las medidas recomendadas para investigar y mitigar la potencial amenaza.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Correo electrónico de alerta sobre Microsoft Defender para Storage.":::

Las alertas de seguridad vigentes se pueden revisar y administrar desde el [icono Alertas de seguridad](../../security-center/security-center-managing-and-responding-alerts.md) de Microsoft Defender for Cloud. Seleccione una alerta para ver los detalles y acciones para investigar la amenaza actual y solucionar amenazas futuras.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Alerta sobre Microsoft Defender para Storage.":::

## <a name="security-alerts"></a>Alertas de seguridad

Las alertas las generan los intentos inusuales y potencialmente dañinos de acceso o aprovechamiento de cuentas de almacenamiento. Para obtener una lista de las alertas de Azure Storage, consulte [Alertas para Azure Storage](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Microsoft Defender para Storage](../../security-center/defender-for-storage-introduction.md)
- [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md)
- [Registros en las cuentas de Azure Storage](/rest/api/storageservices/About-Storage-Analytics-Logging)
