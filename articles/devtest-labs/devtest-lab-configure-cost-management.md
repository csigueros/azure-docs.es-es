---
title: Visualización de la tendencia de costos mensuales estimados de laboratorio
description: En este artículo se proporciona información sobre cómo realizar un seguimiento del costo del laboratorio (gráfico de tendencias de costo estimadas mensuales) en Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: f0e99ae3da38ef723b211075c9f32dc1bbdfda70
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286476"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Seguimiento de los costos asociados con un laboratorio en Azure DevTest Labs
En este artículo se proporciona información sobre cómo seguir el costo del laboratorio. Muestra cómo ver la tendencia de costo estimado durante el mes actual para el laboratorio. El artículo también muestra cómo ver el costo por recurso del mes hasta la fecha en el laboratorio.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Visualización de la tendencia de costos mensuales estimados de laboratorio 
En esta sección, se muestra el uso del gráfico **Monthly Estimated Cost Trend** (Tendencia de costos estimados mensuales) para ver el costo estimado hasta la fecha del mes de calendario actual, así como el costo a fin de mes previsto para el mes del calendario actual. También se muestra cómo administrar mejor los costos de laboratorio estableciendo objetivos y umbrales de gastos que, al alcanzarlos, desencadenan que DevTest Labs le informe de los resultados.

Para ver el gráfico de tendencias de costos mensuales estimados, siga estos pasos: 

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** y, luego, **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el suyo.  
4. Seleccione **Configuración y directivas** en el menú de la izquierda.  
4. Seleccione **Tendencia de costos** en la sección **Seguimiento de costos**, en el menú de la izquierda. La siguiente captura de pantalla muestra un ejemplo de gráfico de costos. 
   
    ![Captura de pantalla que muestra un gráfico de costos.](./media/devtest-lab-configure-cost-management/graph.png)

    El valor **Costo estimado** es el costo estimado hasta la fecha del mes de calendario actual. El valor de **Projected cost** (Costo previsto) es el costo estimado para el mes de calendario actual completo, calculado con el costo de laboratorio para los cinco días anteriores.

    Los importes de los costos se redondean al siguiente número entero. Por ejemplo: 

   * 5,01 se redondea a 6. 
   * 5,50 se redondea a 6.
   * 5,99 se redondea a 6.

     Como indica anteriormente el gráfico, los costos que se ven de forma predeterminada en el gráfico son costos *estimados* con tarifas de oferta de [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/). También puede establecer sus propios objetivos de gastos que se muestran en los gráficos mediante la [administración de los objetivos de costos para un laboratorio](#managing-cost-targets-for-your-lab).

     Los siguientes costos *no* se incluyen en el cálculo de costos:

   * Actualmente no se admiten las suscripciones CSP y DreamSpark. Azure DevTest Labs usa las API de facturación de Azure para calcular el costo del laboratorio, que no es compatible con las suscripciones CSP o Dreamspark.
   * Las tarifas de su oferta. Actualmente, no puede usar las tarifas de oferta (que aparecen en su suscripción) negociadas con Microsoft o con asociados de Microsoft. Solo se usan tarifas de pago por uso.
   * Los impuestos
   * Los descuentos
   * La divisa de facturación. Actualmente, el costo de laboratorio solo se muestra en divisa USD.

### <a name="managing-cost-targets-for-your-lab"></a>Administración de objetivos de costos para un laboratorio
DevTest Labs le ayuda a administrar los costos del laboratorio estableciendo un objetivo de gastos que puede ver en el gráfico Tendencia de costos estimados mensuales. DevTest Labs puede enviarle una notificación cuando los gastos alcancen el umbral de destino especificado. 

1. En la página **Tendencia de costos**, seleccione **Administrar objetivo**.

    ![Captura de pantalla en la que se muestra el botón Administrar objetivo.](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. En el panel **Administrar objetivo**, especifique el objetivo y los umbrales de gastos. También puede establecer si se informa de cada umbral seleccionado en el gráfico de tendencias de costos o mediante una notificación de webhook.

    ![Captura de pantalla que muestra el panel Administrar objetivo.](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Seleccione un período de tiempo durante el que desea que se realice el seguimiento de los objetivos de costos.
      - **Mensual**: se realiza un seguimiento de los objetivos de costos mensual.
      - **Fijo**: se realiza un seguimiento de los objetivos de costos para el intervalo de fechas que especifique en las fechas de inicio y de finalización. Por lo general, estos valores pueden corresponder a cuánto tiempo está programada la ejecución del proyecto.
   - Especifique un **Costo objetivo**. Por ejemplo, puede tratarse de cuánto planea gastar en este laboratorio en el período de tiempo definido.
   - Seleccione habilitar o deshabilitar cualquier umbral del que desee que le informen, en incrementos del 25 %, hasta un 125 % del **Costo objetivo** que ha especificado.
      - **Notificar**: cuando los resultados alcanzan este umbral, se le notifica mediante una dirección URL de webhook que haya especificado.
      - **Trazar en gráfico**: cuando los resultados alcanzan este umbral, el gráfico de resultados se traza en un gráfico de tendencias de costos que puede ver.
   - Si opta por **Notificar** cuando se alcanza el umbral, debe especificar una dirección URL de webhook. En el área de integraciones de costos, seleccione **Haga clic aquí para agregar una integración**. Escriba una **dirección URL de webhook** en el panel Configurar notificación y seleccione **Aceptar**.

       ![Captura de pantalla que muestra el panel Configurar notificación.](./media/devtest-lab-configure-cost-management/configure-notification-new.png)

     - Si especifica **Notificar**, debe definir una dirección URL de webhook.
     - Del mismo modo, si define una dirección URL de webhook, debe establecer **Notificación** en **Activado** en el panel Umbral de costo.
     - Cree el webhook antes de escribirlo aquí.  

       Para obtener más información sobre los webhooks, consulte [Creación de un webhook o una función de API de Azure](../azure-functions/functions-bindings-http-webhook.md). 

## <a name="view-cost-by-resource"></a>Visualización del costo por recurso 
La característica de tendencia de costo mensual en los laboratorios permite ver cuánto ha empleado en el mes en curso. La característica también muestra la proyección de los gastos hasta el final del mes, en función de los gastos en los últimos siete días. Para ayudarle a entender por qué el gasto en el laboratorio está cumpliendo los umbrales, puede usar la característica de **costo por recurso** que muestra el costo del mes hasta la fecha **por recurso** en una tabla.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** y, luego, **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que quiera.  
4. Seleccione **Configuración y directivas** en el menú de la izquierda.
5. Seleccione **Costo por recurso** en la sección **Seguimiento de costos**, en el menú de la izquierda. Verá los costos asociados con cada recurso asociado a un laboratorio. 

    ![Captura de pantalla que muestra Costo por recurso.](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Esta característica le ayuda a identificar fácilmente los recursos que cuestan más para que pueda tomar acciones destinadas a reducir el gasto de laboratorio. Por ejemplo, el costo de una máquina virtual se basa en el tamaño de la máquina virtual. Cuanto mayor sea el tamaño de la máquina virtual, más cuesta. Puede encontrar el tamaño de una máquina virtual y el propietario, y hablar con el propietario sobre por qué necesitan el tamaño de la máquina virtual y si pueden reducirlo.

La [directiva de apagado automático](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) le ayuda a reducir el costo de apagar las máquinas virtuales de laboratorio en un momento determinado del día. Sin embargo, un usuario de laboratorio puede rechazar la directiva de apagado, lo que aumenta el costo de la ejecución de la máquina virtual. Seleccione una máquina virtual en la tabla para ver si se ha dado de baja de la directiva de apagado automático. Hable con el propietario de la máquina virtual para averiguar por qué ha optado por no participar y ver si puede volver a participar.
 
## <a name="next-steps"></a>Pasos siguientes
Vea algunas acciones que puede probar a continuación:

* [Definición de directivas de laboratorio](devtest-lab-set-lab-policy.md) Obtenga información sobre cómo establecer las distintas directivas que se emplean para controlar el uso del laboratorio y sus máquinas virtuales. 
* [Creación de una imagen personalizada](devtest-lab-create-template.md) Cuando se crea una máquina virtual, se especifica una base. La base puede ser una imagen personalizada o una imagen de Marketplace. Este artículo describe cómo crear una imagen personalizada desde un archivo VHD.
* [Configuración de imágenes de Marketplace](devtest-lab-configure-marketplace-images.md) DevTest Labs admite la creación de máquinas virtuales basadas en imágenes de Azure Marketplace. En este artículo se muestra cómo especificar las imágenes de Azure Marketplace que se pueden usar en el momento de crear máquinas virtuales en un laboratorio.
* [Creación de una máquina virtual de un laboratorio](devtest-lab-add-vm.md). En este artículo se muestra cómo crear una máquina virtual a partir de una imagen base personalizada o de Marketplace y cómo trabajar con artefactos en la máquina virtual.
