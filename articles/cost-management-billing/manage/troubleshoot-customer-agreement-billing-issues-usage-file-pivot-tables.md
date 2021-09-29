---
title: Solución de problemas de facturación de Azure MCA con tablas dinámicas de archivos de uso
description: Este artículo le ayuda a solucionar problemas de facturación de Contrato de cliente de Microsoft (MCA) con las tablas dinámicas creadas a partir de los archivos de uso de CSV.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 1d4e6a407e75a70618089f20a7d19343785380ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616486"
---
# <a name="troubleshoot-mca-billing-issues-with-usage-file-pivot-tables"></a>Solución de problemas de facturación de MCA con tablas dinámicas de archivos de uso

Este artículo le ayuda a solucionar problemas de facturación de Contrato de cliente de Microsoft (MCA) con las tablas dinámicas de los archivos de uso. Los archivos de uso de Azure contienen toda la información de uso y consumo de Azure. La información del archivo puede ayudarle a:

- Comprender cómo se usan y aplican las reservas de Azure
- Conciliar información en Azure Cost Management con la factura facturada
- Solucionar un aumento de costos
- Calcular un importe de reembolso para un acuerdo de nivel de servicio

Con la información de los archivos de uso, puede obtener una mejor comprensión de los problemas de uso y diagnosticarlos. Los archivos de uso se generan en formato delimitado por comas (CSV). Dado que los archivos de uso podrían ser archivos CSV grandes, son más fáciles de manipular y ver como tablas dinámicas en una aplicación de hoja de cálculo como Excel. En los ejemplos de este artículo se usa Excel, pero puede usar cualquier aplicación de hoja de cálculo que desee.

Solo los propietarios de perfiles de facturación, colaboradores, lectores o administradores de facturas tienen acceso para descargar archivos de uso. Para más información, consulte [Descarga del uso para el Contrato de cliente de Microsoft](../understand/download-azure-daily-usage.md). 

## <a name="get-the-data-and-format-it"></a>Obtención de los datos y aplicación de formato

Dado que los archivos de uso de Azure están en formato CSV, debe preparar los datos para usarlos en Excel. Use los pasos siguientes para dar formato a los datos como una tabla.

1. Descargue el archivo de uso con las instrucciones que se indican en [Descarga del uso en Azure Portal](../understand/download-azure-daily-usage.md).
1. Abra el archivo en Excel.
1. Los datos sin formato se parecen al ejemplo siguiente.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" alt-text="Ejemplo que muestra datos sin formato" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" :::
1. Seleccione el primer campo de la tabla, **invoiceID**.
1. Presione Ctrl + Mayús + Flecha abajo y, a continuación, Ctrl + Mayús + Flecha derecha para seleccionar toda la información de la tabla.
1. En el menú superior, seleccione **Insertar** > **Tabla**. En el cuadro Crear tabla, seleccione **Mi tabla tiene encabezados** y, después, seleccione **Aceptar**.  
:::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Ejemplo que muestra el cuadro de diálogo Crear tabla" :::
1. En el menú superior, seleccione **Insertar** > **Tabla dinámica** y, a continuación, seleccione **Aceptar**. La acción crea una nueva hoja en el archivo y le lleva al área de la tabla dinámica en el lado derecho de la hoja.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Ejemplo que muestra el área Campos de la tabla dinámica" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

El área Campos de la tabla dinámica es un área de arrastrar y colocar. Continúe con la siguiente sección para crear la tabla dinámica.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Creación de una tabla dinámica para ver los costos de Azure por recursos

En esta sección, va a crear una tabla dinámica en la que podrá solucionar problemas generales del uso de Azure. La tabla de ejemplo puede ayudarle a investigar qué servicio consume la mayoría de los recursos. También podrá ver los recursos que incurren en el mayor costo y cómo se cobra un servicio.

1. En el área Campos de la tabla dinámica, arrastre **Meter Category** (Categoría de medición) y **Product** (Producto) a la sección **Filas**. Coloque **Product** (Producto) debajo de **Meter Category** (Categoría de medición).  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="[Ejemplo que muestra la categoría de medición y el producto en Filas" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. A continuación, agregue la columna **costInBillingCurrenty** a la sección **Valores**. También puede usar la columna **Quantity** (Cantidad) en su lugar para obtener información sobre las transacciones y unidades de consumo. Por ejemplo, GB y horas. O bien, transacciones en lugar de costos en monedas diferentes como USD, EUR y INR.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Ejemplo que muestra los campos agregados a la tabla dinámica" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Ahora tiene un panel para la investigación generalizada del consumo. Puede filtrar por un servicio específico mediante las opciones de filtrado de la tabla dinámica.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Ejemplo que muestra la opción de filtro de la tabla dinámica para la etiqueta de fila" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Para filtrar un segundo nivel en una tabla dinámica, por ejemplo un recurso, seleccione un elemento de segundo nivel en la tabla.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Ejemplo que muestra las opciones de filtro para el campo de selección" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Arrastre la columna **ResourceID** al área **Filas** debajo de **Product** (Producto) para ver el costo de cada servicio por recurso.
1. Agregue la columna **Date** (Fecha) al área **Columnas** para ver el consumo diario del producto.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="Ejemplo que muestra dónde colocar la fecha en el área de columnas" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. Expanda y contraiga los meses con los símbolos **+** para cada columna de mes.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Ejemplo que muestra el símbolo +" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::

La adición de las columnas **Cost** (Costo) y **Quantity** (Cantidad) en el área **Valores** es opcional. Al hacerlo, se crean dos columnas para cada sección de datos por debajo de cada mes y día cuando la columna de fecha está en la sección de columnas de la tabla dinámica.

En el caso de los filtros adicionales, puede agregar las etiquetas InvoiceSection, costCenter, SubscriptionID, ResourceGroupName o Tags a la sección de filtros y seleccionar el ámbito deseado.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Creación de una tabla dinámica para ver el costo de un recurso específico

Un único recurso puede incurrir en varios cargos por los distintos servicios. Por ejemplo, una máquina virtual puede incurrir en cargos de proceso, licencias de sistema operativo, ancho de banda (transferencias de datos), uso de instancias reservadas y almacenamiento para instantáneas. Siempre que desee revisar el uso general de recursos específicos, los siguientes pasos le guiarán a través de la creación de un panel para ver el uso general con los archivos de uso.

1. En el menú de la derecha, arrastre **ResourceID** a la sección **Filtro** del menú de tabla dinámica.
1. Seleccione el recurso para el que desea ver el costo. Escriba en el cuadro **Buscar** para buscar un nombre de recurso.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" alt-text="Ejemplo que muestra dónde buscar resourceID" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" :::
1. Agregue **meterCategory** y **Product** (Producto) al área **Filas**. Coloque **Product** (Producto) debajo de **meterCategory**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="Ejemplo que muestra dónde colocar meterCategory en los campos de la tabla dinámica" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. A continuación, agregue la columna **Extended Cost** (Costo extendido) a la sección **Valores**. También puede usar la columna Consumed Quantity (Cantidad consumida) en su lugar para obtener información sobre las transacciones y unidades de consumo. Por ejemplo, GB y horas. O bien, transacciones en lugar de costos en monedas diferentes como USD, EUR y INR. Ahora tiene un panel que muestra todos los servicios que consume el recurso.
1. Agregue la columna **Date** (Fecha) a la sección **Columnas**. Muestra el consumo diario.
1. Puede expandir y reducir mediante los iconos **+** de la columna de cada mes.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Ejemplo que muestra el símbolo +" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

[!INCLUDE [Troubleshoot usage spikes](../../../includes/cost-management-billing-troubleshoot-usage-spikes.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Inicio rápido: Explore y analice los costos con Análisis de costos](../costs/quick-acm-cost-analysis.md)