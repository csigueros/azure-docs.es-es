---
ms.openlocfilehash: fc175bdad79bbb01bd0686dcdbf314d8d6ee47fb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "114200953"
---

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Recurso activo de Communication Services.](../../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Obtención de un número de teléfono

Para empezar a aprovisionar números, vaya al recurso de Communication Services en [Azure Portal](https://portal.azure.com).

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Captura de pantalla que muestra la página principal de un recurso de Communication Services.":::

### <a name="search-for-available-phone-numbers"></a>Búsqueda de los números de teléfono disponibles

Vaya a la hoja **Phone Numbers** (Números de teléfono) en el menú de recursos.

:::image type="content" source="../../media/manage-phone-azure-portal-phone-page.png" alt-text="Captura de pantalla que muestra la página de teléfonos de un recurso de Communication Services.":::

Presione el botón **Obtener** para iniciar el asistente. El asistente de la hoja **Phone numbers** (Números de teléfono) le guiará por una serie de preguntas que le ayudarán a elegir el número de teléfono que mejor se adapte a su escenario. 

En primer lugar, deberá elegir el valor de **País o región** donde desea aprovisionar el número de teléfono. Después de seleccionar el país o la región, deberá seleccionar el valor de **Use case** (Caso de uso) que mejor se adapte a sus necesidades. 

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers.png" alt-text="Captura de pantalla que muestra la vista para obtener números de teléfono.":::

### <a name="select-your-phone-number-features"></a>Selección de las características de los números de teléfono

La configuración del número de teléfono se divide en dos pasos: 

1. Selección del tipo del [tipo de número](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services).
2. Selección de las [funcionalidades del número](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services).

Puede seleccionar entre dos tipos de números de teléfono: **Geographic** (Geográfico) y **Número gratuito**. Después de seleccionar un tipo de número, puede elegir las características.

En nuestro ejemplo hemos seleccionado un tipo **Número gratuito** con las características **Llamadas salientes** y **SMS entrantes y salientes**.

:::image type="content" source="../../media/manage-phone-azure-portal-select-plans.png" alt-text="Captura de pantalla que muestra la vista para seleccionar características.":::

Desde aquí, haga clic en el botón **Next: Numbers** (Siguiente: números) en la parte inferior de la página para personalizar los números de teléfono que le gustaría aprovisionar.

### <a name="customizing-phone-numbers"></a>Personalización de los números de teléfono

En la página **Numbers** (Números), personalizará los números de teléfono que le gustaría aprovisionar.

:::image type="content" source="../../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Captura de pantalla que muestra la página para la selección de números.":::

> [!NOTE]
> En este inicio rápido se muestra el flujo de personalización del tipo **Número gratuito**. La experiencia puede ser ligeramente diferente si ha elegido el tipo de número **Geographic** (Geográfico), pero el resultado final será el mismo.

Elija el valor de **Código de área** de la lista de códigos de área disponibles y escriba la cantidad que desea aprovisionar y, a continuación, haga clic en **Buscar** para buscar números que cumplan los requisitos seleccionados. Los números de teléfono que satisfagan sus necesidades se mostrarán junto con el costo mensual.

:::image type="content" source="../../media/manage-phone-azure-portal-found-numbers.png" alt-text="Captura de pantalla que muestra la página para la selección de números con números reservados.":::

> [!NOTE]
> La disponibilidad depende del tipo de número, la ubicación y las características que haya seleccionado.
> Los números se reservan durante un breve período de tiempo antes de que expire la transacción. Si la transacción expira, tendrá que volver a seleccionar los números.

Para ver el resumen de la compra y realizar el pedido, haga clic en el botón **Next: Summary** (Siguiente: resumen) situado en la parte inferior de la página.

### <a name="purchase-phone-numbers"></a>Compra de números de teléfono

En la página de resumen se revisará el tipo de número, las características, los números de teléfono y el costo mensual total para aprovisionar los números de teléfono.

> [!NOTE]
> Los precios que se muestran son los **cargos periódicos mensuales** que cubren el costo por conceder el número de teléfono seleccionado. **Los costos de pago por uso**, que se incurren al realizar o recibir llamadas, no se incluyen en esta vista. Los costos de lista están [disponibles aquí](../../../concepts/pricing.md). Estos costos dependen del tipo de número y de la ubicación del número a llamar. Por ejemplo, precio por minuto para una llamada de un número regional de Seattle a un número regional de Nueva York y una llamada del mismo número a un número de teléfono móvil del Reino Unido puede ser diferente.

Finalmente, haga clic en **Place order** (Realizar pedido) situado en la parte inferior de la página para confirmar.

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Captura de pantalla que muestra la página de resumen con el tipo de número, las características, los números de teléfono y el costo mensual total.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Busque los números de teléfono en Azure Portal

En [Azure Portal](https://portal.azure.com), vaya al recurso de Azure Communication Services:

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Captura de pantalla que muestra la página principal de un recurso de Communication Services.":::

Seleccione la hoja Números de teléfono en el menú para administrar los números de teléfono.

:::image type="content" source="../../media/manage-phone-azure-portal-phones.png" alt-text="Captura de pantalla que muestra la página de números de teléfono de un recurso de Communication Services.":::

> [!NOTE]
> Los números aprovisionados pueden tardar unos minutos en mostrarse en esta página.


### <a name="update-phone-number-capabilities"></a>Actualización de las funcionalidades del número de teléfono

En la página **Números** puede seleccionar un número de teléfono para configurarlo.

:::image type="content" source="../../media/manage-phone-azure-portal-capability-update.png" alt-text="Captura de pantalla que muestra la página de actualización de características.":::

Seleccione las características de las opciones disponibles y haga clic en **Confirmar** para aplicar la selección.

### <a name="release-phone-number"></a>Descarte de un número de teléfono

En la página **Números** puede descartar números de teléfono.

:::image type="content" source="../../media/manage-phone-azure-portal-release-number.png" alt-text="Captura de pantalla que muestra la página para descartar números de teléfono.":::

Seleccione el número de teléfono que desea descartar y haga clic en el botón **Release** (Descartar).