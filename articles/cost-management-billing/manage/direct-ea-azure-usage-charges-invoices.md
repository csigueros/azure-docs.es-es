---
title: Visualización de los detalles de resumen de uso de Azure y descarga de informes de inscripciones directas de EA
description: En este artículo se explica cómo los administradores empresariales de inscripciones directas de Enterprise Agreement (EA) pueden ver un resumen de sus datos de uso, su pago por adelantado de Azure consumido y los cargos asociados a otro uso en Azure Portal.
author: bandersmsft
ms.author: banders
ms.date: 10/12/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: sapnakeshari
ms.openlocfilehash: 005f2c16e463bd01c7e453548ddb29c50e2f937b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007159"
---
# <a name="view-your-usage-summary-details-and-download-reports-for-direct-ea-enrollments"></a>Visualización de los detalles de resumen de uso y descarga de informes de inscripciones directas de EA

En este artículo se explica cómo los administradores empresariales de inscripciones directas de Enterprise Agreement (EA) pueden ver un resumen de sus datos de uso, su pago por adelantado de Azure consumido y los cargos asociados a otro uso en Azure Portal. Los cargos se presentan en el nivel de resumen en todas las cuentas y suscripciones de la inscripción.

## <a name="prerequisites"></a>Prerrequisitos

Para revisar y comprobar los gastos en su factura, debe ser un administrador de empresa. Para más información, consulte [Roles administrativos del Contrato Enterprise de Azure en Azure](understand-ea-roles.md). Si no sabe quién es el administrador empresarial de la organización, cree una solicitud de soporte técnico en Azure Portal.

## <a name="review-usage-charges"></a>Revisión de cargos de uso

Para ver el uso detallado para cuentas específicas, descargue el informe de detalles de uso:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque **Cost Management + Billing** y selecciónelo.
1. Seleccione **Ámbitos de facturación** en el menú de navegación y luego seleccione la cuenta de facturación con la que quiere trabajar.
1. En el menú de navegación seleccione **Uso y cargos**.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/navigation-usage-charges.png" alt-text="Captura de pantalla que muestra la página Uso y cargos." lightbox="./media/direct-ea-azure-usage-charges-invoices/navigation-usage-charges.png" :::
1. Para ver detalles de años anteriores, seleccione un **Intervalo de tiempo**.

En la tabla siguiente se enumeran los términos y las descripciones que se muestran en la página Uso y cargos de Azure Portal. Los cargos que se muestran en Azure Portal están en dólares estadounidenses.

| **Término** | **Descripción** |
| --- | --- |
| Mes | Mes de uso |
| Cargos de créditos | El crédito que se aplica durante ese período específico. |
| Uso por encima del límite del servicio | Los cargos de uso de la organización superan el saldo de crédito |
| Facturación por separado | los servicios que ha usado la organización no están cubiertos por el crédito. |
| Azure Marketplace | Las compras y el uso de Azure Marketplace no están incluidos en el crédito de la organización y se facturan por separado |
| Total de cargos | Cargos de créditos + Uso por encima del límite del servicio + Facturación por separado + Azure Marketplace |

## <a name="download-usage-charges-csv-file"></a>Descarga del archivo CSV de cargos de uso

Los administradores empresariales usan la página Descarga r uso y cargos para descargar los informes siguientes como archivos CSV.

- **Detalles de uso**: en función de la selección, el archivo CSV proporciona todos los cargos (uso y compras), incluidas las compras de RI (reservadas). O bien, cargos amortizados (uso y compras), incluidas las compras reservadas.
- **Cargo de la tienda Marketplace**: el archivo CSV Cargo de tienda Marketplace contiene los cargos de Marketplace basados en el uso desglosados por día del período de facturación indicado.
- **Hoja de precios**: el archivo CSV Hoja de precios contiene la tarifa aplicable de cada medidor de la inscripción y el período de facturación determinados.
- **Saldo y resumen**: el archivo CSV Saldo y resumen contiene un resumen mensual de información sobre saldos, nuevas compras, cargos del servicio Marketplace, ajustes y cargos de uso por encima del límite.

1. Seleccione el símbolo **Descargar** junto al informe del mes.  
:::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/download-usage-charges-csv.png" alt-text="Captura de pantalla que muestra la opción Descargar." :::
1. En la página Descargar uso y cargos, seleccione **Preparar documento** en el informe que quiere descargar.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png" alt-text="Captura de pantalla que muestra la página Preparar documento." lightbox="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png" :::
1. Azure puede tardar un poco en preparar la descarga, en función del uso mensual. Cuando esté listo para la descarga, seleccione **Descargar CSV**.

Los administradores empresariales también pueden ver un resumen general de los cargos del intervalo de tiempo seleccionado en la parte inferior de la página Uso y cargos.

:::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/usage-charges-summary.png" alt-text="Captura de pantalla que muestra un resumen de cargos de uso." lightbox="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png":::

## <a name="download-or-view-your-azure-billing-invoice"></a>Descargar o visualización de los datos de la factura de Azure

También puede descargar la factura desde [Azure Portal](https://portal.azure.com) o recibirla por correo electrónico. Las facturas se envían a la persona que se configuró para recibir las facturas de la inscripción.

Solo un administrador empresarial tiene permiso para ver y obtener la factura. Para obtener más información sobre cómo obtener acceso a la información de facturación, vea [Manage access to Azure billing using roles](manage-billing-access.md) (Administrar el acceso a la facturación de Azure mediante roles).

Recibe una factura de Azure cuando se produce alguno de estos eventos durante el ciclo de facturación:

- **Uso por encima del límite del servicio**: los cargos de uso de la organización superan el saldo de crédito.
- **Cargos facturados por separado**: los servicios que ha usado la organización no están cubiertos por el crédito. Se le facturará por los siguientes servicios a pesar del saldo de crédito:
  - Canonical
  - Citrix XenApp Essentials
  - Citrix XenDesktop
  - Usuario registrado
  - Openlogic
  - Usuario registrado de Remote Access Rights XenApp Essentials
  - Ubuntu Advantage
  - Visual Studio Enterprise (mensual)
  - Visual Studio Enterprise (anual)
  - Visual Studio Professional (mensual)
  - Visual Studio Professional (anual)
- **Cargos de Marketplace**: las compras y el uso de Azure Marketplace no están incluidos en el crédito de la organización. Por lo tanto, los cargos de Marketplace se le facturan a pesar del saldo de crédito. En Azure Portal, un administrador empresarial puede habilitar y deshabilitar las compras de Marketplace.

La factura muestra los cargos de uso de Azure con los costos asociados en primer lugar, seguidos de los cargos de Marketplace. Si tiene un saldo de crédito, se aplica al uso de Azure, y la factura muestra dicho uso y el uso de Marketplace sin ningún costo en último lugar.

### <a name="download-your-azure-invoices-pdf"></a>Descarga de las facturas de Azure (.pdf)

En la mayoría de las suscripciones es posible descargar la factura en Azure Portal.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque **Cost Management + Billing** y selecciónelo.
1. Seleccione **Ámbitos de facturación** en el menú de navegación y luego seleccione la cuenta de facturación con la que quiere trabajar.
1. En el menú de navegación seleccione  **Facturas**. La página Facturas muestra todas las facturas y notas de crédito generadas en los últimos doce meses.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/invoices-page.png" alt-text="Captura de pantalla que muestra la página Facturas." lightbox="./media/direct-ea-azure-usage-charges-invoices/invoices-page.png" :::
    
1. En la página Facturas busque la fila de la factura que quiere descargar. Seleccione el símbolo de puntos suspensivos ( **…** ) situado a la derecha de la fila.
1. En el menú contextual seleccione **Descargar**.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/download-context-menu.png" alt-text="Captura de pantalla que muestra el menú contextual Descargar."  :::

Puede seleccionar un intervalo de tiempo para ver hasta los últimos tres años de detalles de la factura.

Si la factura tiene varios elementos de línea que superan las 40 páginas de un archivo PDF, se divide en varios números de documento.

En la tabla siguiente se enumeran los términos y las descripciones que se muestran en la página Facturas:

| **Término** | **Descripción** |
| --- | --- |
| Fecha del documento | Fecha en que se ha generado la factura o la nota de crédito. |
| Número de documento | Número de la factura o la nota de crédito. |
| Período de facturación | Período de facturación de la factura o la nota de crédito. |
| Número de pedido de compra | Número de pedido de compra de la factura o la nota de crédito. |
| Importe total | Importe total de la factura o la nota de crédito. |

## <a name="review-credit-charges"></a>Revisión de los cargos de crédito

La información de esta sección describe cómo se puede ver el saldo inicial, el saldo final y los ajustes de crédito del pago por adelantado de Azure (anteriormente denominado compromiso monetario).

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque **Cost Management + Billing** y selecciónelo.
1. Seleccione **Ámbitos de facturación** en el menú de navegación y luego seleccione la cuenta de facturación con la que quiere trabajar.
1. En el menú de navegación seleccione **Créditos y compromisos**.
1. La pestaña Créditos muestra un desglose de los créditos y un gráfico con el saldo a lo largo del tiempo.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/credits-tab.png" alt-text="Captura de pantalla que muestra la pestaña Créditos." lightbox="./media/direct-ea-azure-usage-charges-invoices/credits-tab.png" :::

En la tabla siguiente se enumeran los términos y las descripciones que se muestran en la pestaña Créditos.

| **Término** | **Descripción** |
| --- | --- |
| Mes | Mes del crédito |
| Crédito inicial | Crédito inicial de ese mes |
| Nuevo crédito | Nuevos créditos agregados |
| Ajustes | Ajustes realizados durante el mes |
| Crédito aplicado a cargos | Importe total de la factura o el crédito generados |
| Crédito final | Saldo final de crédito |

## <a name="review-reservation-transaction-details"></a>Revisión de los detalles de una transacción de reserva

Puede ver todas las reservas realizadas en un Enterprise Agreement en Azure Portal.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque **Cost Management + Billing** y selecciónelo.
1. Seleccione **Ámbitos de facturación** en el menú de navegación y luego seleccione la cuenta de facturación con la que quiere trabajar.
1. En el menú de navegación seleccione **Transacciones de reserva**. Los precios mostrados en la imagen siguiente son ejemplos.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/reservation-transactions.png" alt-text="Captura de pantalla que muestra la página Transacciones de reserva." lightbox="./media/direct-ea-azure-usage-charges-invoices/reservation-transactions.png" :::

En la tabla siguiente se enumeran los términos y las descripciones que se muestran en la página Transacciones de reserva.

| **Término** | **Descripción** |
| --- | --- |
| Date | Fecha en que se ha realizado la reserva |
| Nombre | Nombre de la reserva |
| Descripción | Descripción de la reserva |
| Suscripción de compras | Suscripción con la que se ha realizado la reserva |
| Cuenta de compras | Cuenta de compras con la que se ha realizado la reserva |
| Frecuencia de facturación | Frecuencia de facturación de la reserva |
| Tipo | Tipo de la transacción. Por ejemplo, Compra o Reembolso. |
| Cantidad | Cantidad de reserva que se ha adquirido |
| Importe (USD) | Costo de la reserva |

## <a name="csv-report-formatting-issues"></a>Problemas de formato de los informes CSV

Al ver informes CSV en Excel con la moneda de facturación en euros, es posible que tenga problemas de formato con las comas y los puntos.

Los costos mostrados son ejemplos.

Por ejemplo, puede que vea:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
|---|--- | ---|---|
| Horas | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Pero *debería* ver:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Horas | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

El problema de formato se debe a la configuración predeterminada de la funcionalidad de importación de Excel. Excel importa todos los campos como texto *General* y da por hecho que un número está separado con el estándar matemático. Por ejemplo: *1,000.00*.

Si la moneda usa un punto ( **.** ) como separador de miles y una coma ( **,** ) como separador decimal, se muestra incorrectamente. Por ejemplo: *1.000,00*. Los resultados de la importación pueden variar en función de la configuración regional del idioma.

Para importar el archivo CSV sin problemas de formato:

1. En Microsoft Excel, vaya a **Archivo** > **Abrir**. Aparece el Asistente para importar texto.
1. En **Tipo de los datos originales**, elija **Delimitado**. El valor predeterminado es **De ancho fijo**.
1. Seleccione **Next** (Siguiente).
1. En **Delimitadores**, active la casilla de **Coma**. Desactive **Tabulación** si está seleccionada.
1. Seleccione **Next** (Siguiente).
1. Desplácese a las columnas **Tasa de recursos** y **Coste ampliado**.
1. Seleccione la columna **Tasa de recursos**. Aparece resaltada en negro.
1. En la sección **Formato de los datos en columnas**, seleccione **Texto** en lugar de **General**. El encabezado de columna cambia de **General** a **Texto**.
1. Repita los pasos 8 y 9 en la columna **Coste ampliado** y seleccione **Finalizar**.

> [!TIP]
> Si ha establecido que los archivos CSV se abran automáticamente en Excel, debe usar la función **Abrir** en Excel en su lugar. En Excel, vaya a **Archivo** > **Abrir**.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre las tareas comunes que realiza un administrador empresarial directo en Azure Portal, vea [Administración directa de EA de Azure](direct-ea-administration.md).