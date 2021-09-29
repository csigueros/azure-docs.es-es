---
title: Solución de problemas de facturación de Proveedor de soluciones en la nube de Azure con tablas dinámicas de archivos de uso
description: Este artículo le ayuda a solucionar problemas de facturación del Proveedor de soluciones en la nube de Azure (CSP) con las tablas dinámicas creadas a partir de los archivos de uso de CSV.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 2532b02247a85d180d7b1cccd504838b83a8f93f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591850"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>Solución de problemas de facturación de CSP con tablas dinámicas de archivos de uso

Este artículo le ayuda a solucionar problemas de facturación del Proveedor de soluciones en la nube de Azure (CSP) con las tablas dinámicas en los archivos de conciliación (uso) del Centro de partners. Los archivos de uso de Azure contienen toda la información de uso y consumo de Azure. La información del archivo puede ayudarle a:

- Comprender cómo se usan y aplican las reservas de Azure
- Conciliar información en Azure Cost Management con la factura facturada
- Solucionar un aumento de costos
- Calcular un importe de reembolso para un acuerdo de nivel de servicio

Con la información de los archivos de uso, puede obtener una mejor comprensión de los problemas de uso y diagnosticarlos. Los archivos de uso se generan en formato delimitado por comas (CSV). Dado que los archivos de uso podrían ser archivos CSV grandes, son más fáciles de manipular y ver como tablas dinámicas en una aplicación de hoja de cálculo como Excel. En los ejemplos de este artículo se usa Excel, pero puede usar cualquier aplicación de hoja de cálculo que desee.

Solo los administradores de facturación y globales tienen acceso para descargar archivos de conciliación. Para más información, consulte [Obtenga información sobre cómo leer los elementos de línea de los archivos de conciliación del Centro de partners](/partner-center/use-the-reconciliation-files).

## <a name="get-the-data-and-format-it"></a>Obtención de los datos y aplicación de formato

Dado que los archivos de uso de Azure están en formato CSV, debe preparar los datos para usarlos en Excel. Use los pasos siguientes para dar formato a los datos como tabla.

1. Descargue el archivo de uso con las instrucciones de [Búsqueda de tu factura](/partner-center/read-your-bill#find-your-bill).
1. Abra el archivo en Excel.
1. Los datos sin formato se parecen al ejemplo siguiente.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="Ejemplo que muestra datos sin formato en Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. Seleccione el primer campo de la tabla, **PartnerID**.
1. Presione Ctrl + Mayús + Flecha abajo y, a continuación, Ctrl + Mayús + Flecha derecha para seleccionar toda la información de la tabla.
1. En el menú superior, seleccione **Insertar** > **Tabla**. En el cuadro Crear tabla, seleccione **Mi tabla tiene encabezados** y, después, seleccione **Aceptar**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Ejemplo que muestra el cuadro de diálogo Crear tabla" :::
1. En el menú superior, seleccione **Insertar** > **Tabla dinámica** y, a continuación, seleccione **Aceptar**. La acción crea una nueva hoja en el archivo y le lleva al área de la tabla dinámica en el lado derecho de la hoja.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Ejemplo que muestra el área Campos de la tabla dinámica" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

El área Campos de la tabla dinámica es un área de arrastrar y colocar. Continúe con la siguiente sección para crear la tabla dinámica.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Creación de una tabla dinámica para ver los costos de Azure por recursos

En esta sección, va a crear una tabla dinámica en la que podrá solucionar problemas generales del uso de Azure. La tabla de ejemplo puede ayudarle a investigar qué servicio consume la mayoría de los recursos. También podrá ver los recursos que incurren en el mayor costo y cómo se cobra un servicio.

1. En el área Campos de la tabla dinámica, arrastre **Nombre de servicio** y **Recurso** al área **Filas**. Coloque **Recurso** debajo de **Nombre de servicio**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Ejemplo que muestra el nombre de servicio y el recurso en Filas" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. A continuación, coloque **Total después de impuestos** en el área **Valores**. También puede usar la columna Consumed Quantity (Cantidad consumida) en su lugar para obtener información sobre las transacciones y unidades de consumo. Por ejemplo, GB y horas. O bien, transacciones en lugar de costos en monedas diferentes como USD, EUR y INR.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Ejemplo que muestra las columnas agregadas a los campos de tabla dinámica" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Ahora tiene un panel para la investigación generalizada del consumo. Puede filtrar por un servicio específico mediante las opciones de filtrado de la tabla dinámica.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Ejemplo que muestra la opción de filtro de la tabla dinámica para la etiqueta de fila" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Para filtrar un segundo nivel en una tabla dinámica, por ejemplo un recurso, seleccione un elemento de segundo nivel en la tabla.
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Ejemplo que muestra las opciones de filtro para el campo de selección" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. En el caso de los filtros adicionales, puede agregar **SubscriptionID** y **Customer Company Name** al área **Filtros** y seleccionar el ámbito deseado.

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>Creación de una tabla dinámica para ver el uso de Azure por fecha

En esta sección, va a crear una tabla dinámica en la que podrá solucionar problemas generales del uso de Azure por cantidad consumida y fecha. Resulta útil para identificar los picos de facturación por fecha y servicio. También podrá ver los recursos que incurren en el mayor costo y cómo se cobra un servicio.

El archivo de conciliación tiene dos tablas. Uno está en la parte superior (la tabla principal) y hay otra tabla en la parte inferior del documento. Esta segunda tabla tiene gran parte de la misma información, pero no incluye precios ni detalles de costos. Tiene la fecha de uso y la cantidad consumida.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="Ejemplo que muestra los archivos de conciliación con dos tablas de datos" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. Siga los mismos pasos de la sección [Obtención de los datos y aplicación de formato](#get-the-data-and-format-it) para crear una tabla de Excel con la información en la parte inferior del archivo de conciliación.
1. Cuando la tabla esté lista y tenga una hoja de tabla dinámica, siga los mismos pasos de la sección Creación de una tabla dinámica para ver los costos de Azure por recursos para preparar el panel. En lugar de usar el total después de impuestos, coloque **Consumed Quantity** (Cantidad consumida) en el área **Valores**.
1. Agregue la **fecha de uso** a la sección de columnas. La salida debe tener un aspecto similar al ejemplo siguiente.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="Ejemplo que muestra los campos finales de la tabla dinámica" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. Ahora tiene un panel que muestra el uso por fecha. Puede ampliar cada mes seleccionando el símbolo **+** .

El panel muestra la cantidad consumida en unidades como GB, horas y transferencias.

Para ver el precio por día, puede agregar el **GUID del recurso** al área **Filas**. En la tabla superior, agregue el precio por unidad (**ListPrice**) del recurso. Multiplique **ListPrice** por el valor de **Consumed quantity** (Cantidad consumida) para calcular los cargos antes de impuestos. Las cantidades deben coincidir.

Algunos recursos (servicios) tienen precios escalados por cantidad consumida. Por ejemplo, algunos recursos tienen un precio mayor para los primeros 100 GB consumidos y un precio más bajo para los GB usados después. Tenga en cuenta los precios escalados al calcular los costos de forma manual.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Creación de una tabla dinámica para ver el costo de un recurso específico

Un único recurso puede incurrir en varios cargos por los distintos servicios. Por ejemplo, una máquina virtual puede incurrir en cargos de proceso, licencias de sistema operativo, ancho de banda (transferencias de datos), uso de instancias reservadas y almacenamiento para instantáneas. Siempre que desee revisar el uso general de recursos específicos, los siguientes pasos le guiarán a través de la creación de un panel para ver el uso general con los archivos de uso.

Los archivos de conciliación no contienen detalles específicos de los recursos. Por lo tanto, se utiliza el archivo de uso agregado. Póngase en contacto con el [soporte técnico de facturación de Azure](https://go.microsoft.com/fwlink/?linkid=2083458) para que le proporcione el archivo de uso agregado para su suscripción. Los archivos agregados se generan en el nivel de suscripción. Los datos sin formato se parecen al ejemplo siguiente.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="Ejemplo que muestra el archivo de uso agregado sin formato" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

La tabla contiene las columnas siguientes.

- **UsageStart** y **UsageEnd**: fecha para cada elemento de línea (cada unidad de uso). Por ejemplo, cada día.
- **MeteredResourceID**: en Azure, se corresponde con el identificador del medidor.
- **Properties**: contiene el identificador de instancia (nombre del recurso) con otros detalles, como la ubicación.
- **Quantity** (Cantidad): cantidad consumida en el archivo de conciliación.

1. Seleccione el primer campo de la tabla, **PartnerID**.  
1. Presione Ctrl + Mayús + Flecha abajo y, a continuación, Ctrl + Mayús + Flecha derecha para seleccionar toda la información de la tabla.
1. En el menú superior, seleccione **Insertar** > **Tabla**. En el cuadro Crear tabla, seleccione **Mi tabla tiene encabezados** y, después, seleccione **Aceptar**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Ejemplo que muestra el cuadro de diálogo Crear tabla" :::
1. En el menú superior, seleccione **Insertar** > **Tabla dinámica** y, a continuación, seleccione **Aceptar**. La acción crea una nueva hoja en el archivo y le lleva al área de la tabla dinámica en el lado derecho de la hoja.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="Ejemplo que muestra el área Campos de la tabla dinámica para el archivo de conciliación" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. A continuación, agregue **MeteredResourceID** al área **Filas** y **Quantity** a **Valores**. Los resultados muestran la información de uso general. Para obtener más información, coloque **UsageEndDateTime** en el área **Columnas**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="Ejemplo que muestra información de uso general" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. Para ver un informe general, agregue **Properties** a **Filas** en **MeteredResourceID**. Muestra un panel completo para su uso.
1. Para filtrar por un recurso específico, agregue **Properties** al área **Filtros** y seleccione el uso deseado. Puede usar la búsqueda para localizar un nombre de recurso.
    Para ver el costo del recurso, busque la cantidad total consumida y multiplique el valor por el precio de la lista. El precio de venta es específico para cada GUID de recurso (MeteredResourceID). Si un recurso está consumiendo varios MeteredResourceID, tiene que anotar el valor total de cada identificador.

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Azure Cost Management para los asociados](../costs/get-started-partners.md)