---
title: Visualización de las transacciones de compra y reembolso de Reserva de Azure
description: Aprenda a ver las transacciones de compra y reembolso de Reserva de Azure.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 06/14/2021
ms.author: banders
ms.openlocfilehash: 402f2b9b8d4e7528e6578f9735e0360b4a049878
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112114526"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>Visualización de las transacciones de compra y reembolso de las reservas

Hay varias maneras de ver las transacciones de compra y reembolso de reserva. Se pueden usar Azure Portal, Power BI y las API REST.

## <a name="view-reservation-purchases-in-the-azure-portal"></a>Visualización de las compras de reservas en Azure Portal

Los lectores de facturación de Contrato Enterprise y Contrato de cliente de Microsoft pueden ver las compras acumuladas de reservas en Análisis de costos.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Vaya a **Administración de costos + facturación**.
1. Seleccione Análisis de costos en el menú de la izquierda.
1. Aplique un filtro para  **Modelo de precios**  y, a continuación, seleccione **Reserva**.
1. Para ver las compras de reservas, aplique un filtro para  **Tipo de cargo**  y, a continuación, seleccione **Compra**.
1. Establezca  **Granularidad**  en  **Mensual**.
1. Establezca el tipo de gráfico en  **Columna (apilada)** .

:::image type="content" source="./media/view-purchase-refunds/reservation-purchase-cost-analysis.png" alt-text="Captura de pantalla que muestra las compras de reservas en Análisis de costos." lightbox="./media/view-purchase-refunds/reservation-purchase-cost-analysis.png" :::

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Visualización de transacciones de reserva en Azure Portal

Los administradores de facturación de una inscripción Enterprise o de un Contrato de cliente de Microsoft pueden ver las transacciones de reserva en Cost Management y facturación.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque **Administración de costos + facturación**.
1. Seleccione **Transacciones de reserva**.
1. Para filtrar los resultados, seleccione **Intervalo de tiempo**, **Tipo** o **Descripción**.
1. Seleccione **Aplicar**.

[![Captura de pantalla que muestra las transacciones de reservas en Azure Portal.](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Visualización de transacciones de reserva en Power BI

Un administrador de una inscripción Enterprise puede ver las transacciones de reserva con la aplicación Cost Management para Power BI.

1. Obtenga la [aplicación Azure Cost Management para Power BI](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
1. Vaya al informe de compras de RI.

[![Ejemplo que muestra transacciones de reservas.](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

Para más información, consulte [Azure Cost Management para Power BI para Contratos Enterprise](../costs/analyze-cost-data-azure-cost-management-power-bi-template-app.md).

## <a name="use-apis-to-get-reservation-transactions"></a>Uso de las API para obtener transacciones de reserva

Los usuarios de Contrato Enterprise (EA) y Contrato de cliente de Microsoft pueden obtener datos de transacciones de reserva mediante [Transacciones de reserva: List API](/rest/api/consumption/reservationtransactions/list).

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo administrar una reserva, consulte [Administración de Azure Reservations](manage-reserved-vm-instance.md).
- Para obtener más información acerca de Azure Reservations, consulte los siguientes artículos:
  - [¿Qué es Azure Reservations?](save-compute-costs-reservations.md)
  - [Administración de reservas en Azure](manage-reserved-vm-instance.md)