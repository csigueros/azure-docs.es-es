---
title: Visualización y descarga de los datos de uso y los cargos de Azure
description: Obtenga información acerca de cómo descargar o ver los cargos y el uso diario de Azure, y vea los recursos adicionales disponibles.
keywords: billing usage, usage charges, usage download, view usage, azure invoice, azure usage
author: bandersmsft
ms.author: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 7b5a9f195d2ba8b682dd4458358cb9d85b7f16d0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644531"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Visualización y descarga de los datos de uso y los cargos de Azure

Puede descargar un desglose diario del uso y los cargos de Azure en Azure Portal. También puede obtener los datos de uso mediante la CLI de Azure. Solo determinados roles tienen permiso para obtener la información de uso de Azure, como el administrador de la cuenta o el administrador de Enterprise. Para obtener más información sobre cómo obtener acceso a la información de facturación, vea [Manage access to Azure billing using roles](../manage/manage-billing-access.md) (Administrar el acceso a la facturación de Azure mediante roles).

Si tiene un contrato de cliente de Microsoft (MCA), debe ser el propietario del perfil de facturación, colaborador, lector o administrador de facturación para ver los datos de uso y los cargos de Azure.  Si tiene un contrato Microsoft Partner Agreement (MPA), solo el administrador global o el agente de administrador de la organización asociada pueden ver y descargar el uso y los cargos de Azure.

Según el tipo de suscripción que utilice, las opciones para descargar el uso y los cargos varían.

## <a name="download-usage-from-the-azure-portal-csv"></a>Descarga de uso en Azure Portal (.csv)

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque *Administración de costos + facturación*.  
    ![Captura de pantalla que muestra la búsqueda en Azure Portal de Cost Management + Billing.](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Dependiendo de su acceso, es posible que tenga que seleccionar una cuenta de facturación o un perfil de facturación.
1. En el menú de la izquierda, seleccione **Facturas** en **Facturación**.
1. En la cuadrícula de la factura, busque la fila del período de facturación correspondiente al uso que quiere descargar.
1. Seleccione el **icono de descargas** o los puntos suspensivos (`...`) de la derecha.  
  ![Captura de pantalla que muestra la página de facturas de Cost Management + Billing con la opción de descarga.](./media/download-azure-daily-usage/download-usage-others.png)  
1. Se abre el panel de descarga a la derecha. Seleccione **Descargar** en la sección **Detalles de uso**.  

## <a name="download-usage-for-ea-customers"></a>Descarga del uso para los clientes de EA

Para ver y descargar los datos de uso como cliente de EA, debe ser administrador de Enterprise, o bien propietario de la cuenta o administrador de departamento con la directiva para ver los cargos habilitada.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque *Administración de costos + facturación*.  
    ![Captura de pantalla que muestra la búsqueda en Azure Portal.](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Si tiene acceso a varias cuentas de facturación, seleccione el ámbito de facturación de su cuenta de Contrato Enterprise.
1. Seleccione **Uso + cargos**.
1. Seleccione **Descargar** para el mes que quiere descargar.  
    ![Captura de pantalla que muestra la página de facturas de Cost Management + Billing para clientes de EA.](./media/download-azure-daily-usage/download-usage-ea.png)

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Descarga del uso para el contrato de cliente de Microsoft

Para ver y descargar datos de uso de un perfil de facturación, debe ser el propietario del perfil de facturación, colaborador, lector o administrador de facturación.

### <a name="download-usage-for-billed-charges"></a>Descarga del uso para los cargos facturados

1. Busque **Administración de costos + facturación**.
2. Seleccione un perfil de facturación.
3. Seleccione **Facturas**.
4. En la cuadrícula de la factura, busque la fila de la factura correspondiente a los datos de uso que quiere descargar.
5. Haga clic en el botón de puntos suspensivos (`...`) al final de la fila.
6. En el menú contextual de descarga, seleccione **Uso y cargos de Azure**.

### <a name="download-usage-for-open-charges"></a>Descarga del uso para los cargos abiertos

También puede descargar el uso del mes hasta la fecha para el período de facturación actual; esto es, los cargos que aún no se han facturado.

1. Busque **Administración de costos + facturación**.
2. Seleccione un perfil de facturación.
3. En la hoja **Información general**, haga clic en **Uso y cargos de Azure**.

### <a name="download-usage-for-pending-charges"></a>Descarga del uso para cargos pendientes

Si tiene un contrato de cliente de Microsoft, puede descargar el uso mensual hasta la fecha para el período de facturación actual. Estos cargos de utilización que todavía no se han facturado.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Busque *Administración de costos + facturación*.
3. Seleccione un perfil de facturación. En función de su acceso, es posible que tenga que seleccionar primero una cuenta de facturación.
4. En el área **Información general**, busque los vínculos de descarga debajo de los cargos recientes.
5. Seleccione **Descargar uso y precios**.

## <a name="get-usage-data-with-azure-cli"></a>Obtención de los datos de uso con la CLI de Azure

Empiece por preparar el entorno para la CLI de Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Después de iniciar sesión, use el comando [az costmanagement query](/cli/azure/costmanagement#az_costmanagement_query) para consultar la información de uso mensual hasta la fecha de su suscripción:

```azurecli
az costmanagement query --timeframe MonthToDate --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

También puede restringir la consulta mediante el parámetro **--dataset-filter** u otros parámetros:

```azurecli
az costmanagement query --timeframe MonthToDate --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" \
   --dataset-filter "{\"and\":[{\"or\":[{\"dimension\":{\"name\":\"ResourceLocation\",\"operator\":\"In\",\"values\":[\"East US\",\"West Europe\"]}},{\"tag\":{\"name\":\"Environment\",\"operator\":\"In\",\"values\":[\"UAT\",\"Prod\"]}}]},{\"dimension\":{\"name\":\"ResourceGroup\",\"operator\":\"In\",\"values\":[\"API\"]}}]}"
```

El parámetro **--dataset-filter** toma una cadena JSON o `@json-file`.

También tiene la opción de usar los comandos [az costmanagement export](/cli/azure/costmanagement/export) para exportar datos de uso a una cuenta de almacenamiento de Azure. Puede descargar los datos aquí.

1. Cree un grupo de recursos o use uno existente. Para crear un grupo de recursos, ejecute el comando [az group create](/cli/azure/group#az_group_create):

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Cree una cuenta de almacenamiento para recibir las exportaciones o use una existente. Para crear una cuenta, use el comando [az storage account create](/cli/azure/storage/account#az_storage_account_create):

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Ejecute el comando [az costmanagement export create](/cli/azure/costmanagement/export#az_costmanagement_export_create) para crear la exportación:

   ```azurecli
   az costmanagement export create --name DemoExport --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --storage-directory demodirectory
   ```

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de los cargos de factura y utilización, consulte:

- [Descripción de los términos de uso detallados de Microsoft Azure](understand-usage.md)
- [Comprender la factura de Microsoft Azure](review-individual-bill.md)
- [Visualización y descarga de la factura de Microsoft Azure](download-azure-invoice.md)
- [Visualización y descarga de los precios de Azure de la organización](../manage/ea-pricing.md)

Si tiene un contrato de cliente de Microsoft, vea:

- [Descripción de los términos de la utilización detallada de Azure del contrato de cliente de Microsoft](mca-understand-your-usage.md)
- [Descripción de los cargos de la factura del contrato de cliente de Microsoft](review-customer-agreement-bill.md)
- [Visualización y descarga de la factura de Microsoft Azure](download-azure-invoice.md)
- [Visualización y descarga de los documentos fiscales para el contrato de cliente de Microsoft](mca-download-tax-document.md)
- [Visualización y descarga de los precios de Azure de la organización](../manage/ea-pricing.md)
