---
title: Carga de datos de facturación en Azure y visualización en Azure Portal
description: Carga de datos de facturación en Azure y visualización en Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: b54d77c5abb5e8043368ad1e8bce3d3865e68fd7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558770"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>Carga de datos de facturación en Azure y visualización en Azure Portal




## <a name="connectivity-modes---implications-for-billing-data"></a>Modos de conectividad: implicaciones de los datos de facturación

Hay dos modos en los que puede implementar los servicios de datos habilitados para Azure Arc:

- **Conexión indirecta**: no hay ninguna conexión directa con Azure. Los datos solo se envían a Azure a través de un proceso de exportación y carga.
- **Conexión directa**: en este modo, habrá una dependencia del servicio Kubernetes habilitado para Azure Arc a fin de proporcionar una conexión directa entre Azure y el clúster de Kubernetes en el que se implementan los servicios de datos habilitados para Azure Arc. Esto habilitará más funcionalidades de Azure y también le permitirá usar Azure Portal para administrar los servicios de datos habilitados para Azure Arc igual que administra los servicios de datos en PaaS de Azure.  

Puede obtener más información sobre la diferencia entre los [modos de conectividad](./connectivity.md).

En el modo de conexión indirecta, los datos de facturación se exportan periódicamente desde el controlador de datos de Azure Arc a un archivo seguro y, después, se cargan en Azure y se procesan.  En el próximo modo de conexión directa, los datos de facturación se enviarán automáticamente a Azure aproximadamente cada hora para ofrecer una visión casi en tiempo real de los costes de los servicios. El proceso de exportación y carga de los datos en el modo de conexión indirecta también se puede automatizar mediante scripts, o bien se puede crear un servicio que lo haga automáticamente.

## <a name="upload-billing-data-to-azure---indirectly-connected-mode"></a>Carga de datos de facturación en Azure: modo de conexión indirecta

> [!NOTE]
> La carga de datos de uso (facturación) se realiza automáticamente en el modo de conexión directa. Las instrucciones siguientes son solo para el modo de conexión indirecta. 

Para cargar los datos de facturación en Azure, primero debe ocurrir lo siguiente:

1. Cree un servicio de datos habilitado para Azure Arc si todavía no tiene uno. Por ejemplo, cree uno de los siguientes:
   - [Creación de una Instancia administrada de Azure SQL en Azure Arc](create-sql-managed-instance.md)
   - [Creación de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc](create-postgresql-hyperscale-server-group.md)
2. Espere al menos dos horas desde la creación del servicio de datos para que el proceso de recopilación de telemetría de facturación pueda recopilar algunos datos de facturación.
3. Siga los pasos descritos en [Carga del inventario de recursos, datos de uso, métricas y registros en Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md) para obtener la configuración con los requisitos previos para cargar datos de uso, facturación y registros y, a continuación, siga en [Carga de datos de uso en Azure](upload-usage-data.md) para cargar los datos de facturación. 


## <a name="view-billing-data-in-azure-portal"></a>Visualización de los datos de facturación en Azure Portal

Siga estos pasos para ver los datos de facturación en Azure Portal:

1. Abra [Azure Portal](https://portal.azure.com).
1. En el cuadro de búsqueda de la parte superior de la pantalla, escriba **Administración de costos** y haga clic en el servicio Administración de costos.
1. En **Información general de Azure Cost Management**, haga clic en la pestaña **Cost Management**.
1. Haga clic en la pestaña **Análisis de costos** de la izquierda.
1. Haga clic en el botón **Coste por recurso** de la parte superior de la vista.
1. Asegúrese de que el ámbito está establecido en la suscripción en la que se hayan creado los recursos del servicio de datos.
1. Seleccione **Coste por recurso** en el menú desplegable Vista situado junto al selector de ámbito cerca de la parte superior de la vista.
1. Asegúrese de que el filtro de fecha está establecido en **Este mes** o en algún otro intervalo de tiempo que tenga sentido en función del momento en el que hayan creado los recursos del servicio de datos.
1. Haga clic en **Agregar filtro** para agregar un filtro por **Tipo de recurso** = `Microsoft.AzureArcData/<data service type>` si solo quiere filtrar por un tipo de servicio de datos habilitado para Azure Arc.
1. Ahora verá una lista de todos los recursos que se han creado y cargado en Azure. Como el medidor de facturación es 0 USD, verá que el coste siempre es 0 USD.

## <a name="download-billing-data"></a>Descarga de los datos de facturación

Puede descargar los datos de resumen de facturación directamente desde Azure Portal.

1. En la misma vista **Análisis de costos > Ver por tipo de recurso** a la que ha llegado después de seguir las instrucciones anteriores, haga clic en el botón Descargar situado junto a la parte superior.
1. Elija el tipo de archivo de descarga, Excel o CSV, y haga clic en el botón **Descargar datos**.
1. Abra el archivo en un editor adecuado para el tipo de archivo seleccionado.

## <a name="export-billing-data"></a>Exportación de los datos de facturación

También puede exportar de forma periódica datos de facturación y uso **detallados** a un contenedor de Azure Storage mediante la creación de un trabajo de exportación de facturación. Esto resulta útil si quiere ver los detalles de la facturación, como el número de horas que se ha facturado una instancia determinada durante el período de facturación.

Siga estos pasos para configurar un trabajo de exportación de facturación:

1. Haga clic en **Exportaciones** en la parte izquierda.
1. Haga clic en **Agregar**.
1. Escriba un nombre y una frecuencia de exportación, y haga clic en Siguiente.
1. Elija entre crear una nueva cuenta de almacenamiento o bien usar una existente, y rellene el formulario para especificar la cuenta de almacenamiento, el contenedor y la ruta de acceso del directorio al que se van a exportar los archivos de datos de facturación y haga clic en Siguiente.
1. Haga clic en **Crear**.

Los archivos de exportación de datos de facturación estarán disponibles aproximadamente en cuatro horas y se exportarán según la programación que haya especificado al crear el trabajo de exportación de facturación.

Siga estos pasos para ver los archivos de datos de facturación que se exportan:

Puede validar los archivos de datos de facturación en Azure Portal. 

> [!IMPORTANT]
> Después de crear el trabajo de exportación de facturación, espere cuatro horas antes de continuar con los pasos siguientes.

1. En el cuadro de búsqueda de la parte superior del portal, escriba **Cuentas de almacenamiento** y haga clic en **Cuentas de almacenamiento**.
3. Haga clic en la cuenta de almacenamiento que haya especificado al crear el trabajo de exportación de facturación anterior.
4. Haga clic en Contenedores en la parte izquierda.
5. Haga clic en el contenedor que haya especificado al crear el trabajo de exportación de facturación anterior.
6. Haga clic en la carpeta que haya especificado al crear el trabajo de exportación de facturación anterior.
7. Explore en profundidad los archivos y carpetas generados. y haga clic en uno de los archivos .csv generados.
8. Haga clic en el botón **Descargar**, para guardar el archivo en la carpeta Descargas local.
9. Abra el archivo con un visor de archivos .csv como Excel.
10. Filtre los resultados para mostrar solo las filas con el **Tipo de recurso** = `Microsoft.AzureArcData/<data service resource type`.
11. En la columna UsageQuantity verá el número de horas que se ha usado la instancia en el período de 24 horas actual.
