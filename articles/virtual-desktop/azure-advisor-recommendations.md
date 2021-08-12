---
title: 'Tutorial de Azure Advisor para Azure Virtual Desktop: Azure'
description: Cómo resolver recomendaciones de Azure Advisor para Azure Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b8bc514f495a46e76ad882720c918824aab0aaac
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745488"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>Cómo resolver recomendaciones de Azure Advisor

En este artículo se describe cómo puede resolver las recomendaciones que aparecen en Azure Advisor para Azure Virtual Desktop.

## <a name="no-validation-environment-enabled"></a>"No se habilitó ningún entorno de validación"

>[!div class="mx-imgBorder"]
>![Captura de pantalla de la página Excelencia operativa de Azure Advisor. La recomendación "No se habilitó ningún entorno de validación" está resaltada en rojo.](media/no-validation-environment.png)

Esta recomendación aparece en Excelencia operativa. En la recomendación también se debería mostrar un mensaje de advertencia similar al siguiente:

"No tiene un entorno de validación habilitado en esta suscripción. Al crear los grupos de hosts, seleccionó **No** para el "entorno de validación" en la pestaña Propiedades. Para garantizar la continuidad empresarial a través de las implementaciones del servicio Azure Virtual Desktop, asegúrese de que tiene al menos un grupo de hosts con un entorno de validación en el que puede probar posibles problemas".

Puede descartar este mensaje de advertencia habilitando un entorno de validación en uno de los grupos host.

Para habilitar el entorno de validación, siga estos pasos:

1. Vaya a la página principal de Azure Portal y seleccione el grupo host que desee cambiar.

2. A continuación, seleccione el grupo host que desea cambiar de un entorno de producción a uno de validación.

3. En el grupo de hosts, seleccione **Propiedades** en la columna izquierda. A continuación, desplácese hacia abajo hasta que vea "Entorno de validación". Seleccione **Sí** y luego **Aplicar**.

>[!div class="mx-imgBorder"]
>![Captura de pantalla del menú Propiedades. "Entorno de validación" está resaltado en rojo y se selecciona la burbuja "Sí".](media/validation-yes.png)

Estos cambios no harán que la advertencia desparezca inmediatamente, pero debería terminar desapareciendo. Azure Advisor se actualiza dos veces al día. Hasta entonces, puede posponer o descartar la recomendación manualmente. Se recomienda dejar que la recomendación se descarte automáticamente. De este modo, Azure Advisor puede indicarle si surgen problemas a medida que cambien los valores de configuración.

## <a name="not-enough-production-non-validation-environments-enabled"></a>"No hay suficientes entornos de producción habilitados (no de validación)"

Esta recomendación aparece en Excelencia operativa.

En esta recomendación, el mensaje de advertencia aparece por una de estas razones:

- Hay demasiados grupos host en el entorno de validación.
- No tiene ningún grupo de hosts de producción.

Se recomienda que los usuarios tengan menos de la mitad de sus grupos host en un entorno de validación.

Para resolver este problema, siga estos pasos:

1. Vaya a la página principal de Azure Portal.

2. Seleccione los grupos de hosts que desea cambiar de validación a producción.

3. En el grupo host, seleccione la pestaña **Propiedades** en la columna del lado derecho de la pantalla. A continuación, desplácese hacia abajo hasta que vea "Entorno de validación". Seleccione **No** y luego **Aplicar**.

>[!div class="mx-imgBorder"]
>![Captura de pantalla del menú Propiedades. "Entorno de validación" está resaltado en rojo y se selecciona la burbuja "No".](media/validation-no.png)

Estos cambios no harán que la advertencia desparezca inmediatamente, pero debería terminar desapareciendo. Azure Advisor se actualiza dos veces al día. Hasta entonces, puede posponer o descartar la recomendación manualmente. Se recomienda dejar que la recomendación se descarte automáticamente. De este modo, Azure Advisor puede indicarle si surgen problemas a medida que cambien los valores de configuración.

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"No hay suficientes vínculos desbloqueados para implementar correctamente la máquina virtual"

Esta recomendación aparece en Excelencia operativa.

Debe desbloquear direcciones URL específicas para asegurarse de que la máquina virtual funciona correctamente. Puede ver la lista en [Lista de direcciones URL seguras](safe-url-list.md). Si las direcciones URL no están desbloqueadas, la máquina virtual no funcionará correctamente.

Para solucionar esta recomendación, asegúrese de desbloquear todas las direcciones URL de [Lista de direcciones URL seguras](safe-url-list.md). También puede usar etiquetas de servicio o etiquetas FQDN para desbloquear direcciones URL.

## <a name="next-steps"></a>Pasos siguientes

Si buscas guías más detalladas sobre cómo resolver problemas comunes, consulta [Información general, comentarios y soporte técnico para la solución de problemas de Azure Virtual Desktop](troubleshoot-set-up-overview.md).
