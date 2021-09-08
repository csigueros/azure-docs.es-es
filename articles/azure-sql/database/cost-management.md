---
title: Planificación y administración de costos
description: Aprenda a planear y administrar los costos de Azure SQL Database mediante el análisis de costos de Azure Portal.
author: MashaMSFT
ms.author: mathoma
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.subservice: service-overview
ms.topic: how-to
ms.date: 06/30/2021
ms.openlocfilehash: 29d0bdba30bb75a085e147c72438f22121744870
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113134589"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Planeamiento y administración de los costos de Azure SQL Database

En este artículo se describe cómo puede planear y administrar los costos de Azure SQL Database. 

En primer lugar, use la calculadora de precios de Azure para agregar recursos de Azure y revise los costos estimados. Después de comenzar a usar los recursos de Azure SQL Database, utilice las características de Cost Management para establecer presupuestos y supervisar los costos. También puede revisar los costos previstos e identificar las tendencias de gasto para identificar las áreas en las que podría querer actuar. Los costos de Azure SQL Database son solo una parte de los costos mensuales de la factura de Azure. Aunque en este artículo se explica cómo planear y administrar los costos de Azure SQL Database, se le facturarán todos los servicios y recursos de Azure que use en su suscripción de Azure, incluidos los servicios de cualquier tercero.


## <a name="prerequisites"></a>Requisitos previos

El análisis de costos admite la mayoría de los tipos de cuenta de Azure, pero no todos. Para ver la lista completa de tipos de cuenta compatibles, consulte [Understand Cost Management data](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (Información sobre los datos de Cost Management). Para ver los datos de costos, se necesita al menos acceso de lectura en la cuenta de Azure. 

Para más información acerca de cómo asignar acceso a los datos de Azure Cost Management, consulte [Asignación de acceso a los datos](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).


## <a name="sql-database-initial-cost-considerations"></a>Consideraciones iniciales sobre costos de SQL Database

Al trabajar con Azure SQL Database, se deben tener en cuenta varias características de ahorro de costos:

### <a name="vcore-or-dtu-purchasing-models"></a>Modelos de compra de núcleo virtual o DTU 

Azure SQL Database admite dos modelos de compra: núcleo virtual y DTU. La forma en que se cobra varía entre los modelos de compra, por lo que es importante comprender el modelo que mejor se adapte a su carga de trabajo al planear y considerar los costos. Para obtener información sobre los modelos de compra de núcleo virtual y DTU, consulte [Elección entre los modelos de compra de núcleo virtual y DTU](purchasing-models.md).

### <a name="provisioned-or-serverless"></a>Aprovisionado o sin servidor

En el modelo de compra de núcleo virtual, Azure SQL Database también admite dos tipos de niveles de proceso: rendimiento aprovisionado y sin servidor. La forma en que se cobra por cada nivel de proceso varía, por lo que es importante comprender lo que se adapta mejor a su carga de trabajo al planear y considerar los costos. Para obtener más información, consulte [Información general del modelo de núcleo virtual: Niveles de proceso](service-tiers-sql-database-vcore.md#compute-tiers).

En el nivel de proceso aprovisionado del modelo de compra basado en núcleo virtual, puede intercambiar sus licencias existentes por tarifas con descuento. Para obtener más información, consulte [Ventaja híbrida de Azure (AHB)](../azure-hybrid-benefit.md).

### <a name="elastic-pools"></a>Grupos elásticos

En el caso de entornos con varias bases de datos que tienen demandas de uso variables e imprevisibles, los grupos elásticos pueden ofrecer ahorros en los costos en comparación con el aprovisionamiento del mismo número de bases de datos únicas. Para obtener más información, consulte [Grupos elásticos](elastic-pool-overview.md).

## <a name="estimate-azure-sql-database-costs"></a>Cálculo de los costos de Azure SQL Database

Use la [calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/) para calcular los costos de diferentes configuraciones de Azure SQL Database. Para más información, consulte [Precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/azure-sql-database/). 

La información y los precios de la siguiente imagen solo se incluyen como ejemplo: 

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Ejemplo de calculadora de precios de Azure SQL Database":::

También puede calcular la manera en que las diferentes opciones de la Directiva de retención afectan al costo. La información y los precios de la siguiente imagen solo se incluyen como ejemplo:

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="Ejemplo de calculadora de precios de Azure SQL Database para almacenamiento":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Modelo de facturación completo de Azure SQL Database

Azure SQL Database se ejecuta en una infraestructura de Azure que genera otros costos, además de los de Azure SQL Database, cuando se implementa el nuevo recurso. Es importante que comprenda que hay otras infraestructuras que pueden generar costos.  

Azure SQL Database (con la excepción del nivel sin servidor) se factura según una tarifa por hora predecible. Si la base de datos SQL está activa durante menos de una hora, se le factura cada hora el nivel de servicio, el almacenamiento aprovisionado y la E/S más altos seleccionados durante esa hora, con independencia del uso o de que la base de datos estuviese activa durante menos de una hora.

La facturación depende de la SKU del producto, la generación de hardware de la SKU y la categoría de medidor. Azure SQL Database cuenta con las siguientes SKU posibles:

- Básico (B)
- Estándar (S)
- Premium (P)
- De uso general (GP)
- Crítico para la empresa (BC)
- Y para el almacenamiento: almacenamiento con redundancia geográfica (GRS), almacenamiento con redundancia local (LRS) y almacenamiento con redundancia de zona (ZRS)
- También es posible tener una SKU en desuso procedente de ofertas de recursos en desuso.

Para más información, consulte los [niveles de servicio](service-tiers-general-purpose-business-critical.md). 

En la tabla siguiente, se muestran los medidores de facturación más comunes y sus SKU posibles para **bases de datos únicas**: 

| Medición| SKU posibles | Descripción | 
| :----|:----|:----|
| Copia de seguridad\* | GP/BC/HS | Mide el consumo del almacenamiento utilizado por las copias de seguridad, se factura por la cantidad de almacenamiento utilizado en GB al mes. | 
| Copia de seguridad (LTR) | GRS/LRS/ZRS/GF | Mide el consumo del almacenamiento utilizado por las copias de seguridad a largo plazo configuradas mediante la retención a largo plazo, se factura por la cantidad de almacenamiento utilizada. | 
| Compute  | B/S/P/GP/BC  | Mide el consumo de los recursos de proceso por hora. | 
| Proceso (réplica principal o con nombre) | HS | Mide el consumo de los recursos de proceso por hora de la réplica de HS principal. 
| Proceso (réplica de HA)             | HS | Mide el consumo de los recursos de proceso por hora de la réplica de HS secundaria. | 
| Proceso (complemento ZR)              | GP | Mide el consumo de los recursos de proceso por minuto de la réplica agregada con redundancia de zona. | 
| Proceso (sin servidor)             | GP | Mide el consumo de los recursos de proceso sin servidor por minuto.  | 
| Licencia | GP/BC/HS | Facturación de la licencia de SQL Server acumulada al mes. | 
| Almacenamiento | B/S\*/P\*/G/BC/HS | Se factura mensualmente, por la cantidad de datos almacenados por hora. |

\* En el modelo de compra de DTU, se proporciona un conjunto inicial de almacenamiento para datos y copias de seguridad sin costo adicional. El tamaño del almacenamiento depende del nivel de servicio seleccionado. Se puede adquirir almacenamiento de datos adicional en los niveles Estándar y Premium. Para más información, consulte [Precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/azure-sql-database/). 

En la tabla siguiente, se muestran los medidores de facturación más comunes y sus SKU posibles para **grupos elásticos**: 

| Medición| SKU posibles | Descripción | 
|:----|:----|:----|
| Copia de seguridad\* | GP/BC  | Mide el consumo del almacenamiento utilizado por las copias de seguridad, se factura por GB por hora mensualmente. | 
| Compute  | B/S/P/GP/BC | Mide el consumo de los recursos de proceso por hora, como núcleos virtuales y memoria o DTU. | 
| Licencia | GP/BC | Facturación de la licencia de SQL Server acumulada al mes. | 
| Almacenamiento | B/S\*/P\*/GP/HS | Se factura mensualmente, tanto por la cantidad de datos almacenados en la unidad mediante el espacio de almacenamiento por hora como por el rendimiento de megabytes por segundo (MBPS). |

\* En el modelo de compra de DTU, se proporciona un conjunto inicial de almacenamiento para datos y copias de seguridad sin costo adicional. El tamaño del almacenamiento depende del nivel de servicio seleccionado. Se puede adquirir almacenamiento de datos adicional en los niveles Estándar y Premium. Para más información, consulte [Precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/azure-sql-database/). 

### <a name="using-monetary-credit-with-azure-sql-database"></a>Uso de crédito monetario con Azure SQL Database

Puede pagar los cargos de Azure SQL Database con el crédito del pago por adelantado de Azure (antes conocido como compromiso monetario). Sin embargo, no puede usar el crédito del pago por adelantado de Azure para pagar los gastos de productos y servicios de terceros, como los que proceden de Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Revisión de los costos estimados en Azure Portal

A medida que avance por el proceso de creación de una instancia de Azure SQL Database, puede ver los costos estimados durante la configuración del nivel de proceso. 

Para acceder a esta pantalla, seleccione **Configurar base de datos** en la pestaña **Aspectos básicos** de la página **Crear base de datos SQL**. La información y los precios de la siguiente imagen solo se incluyen como ejemplo:

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Ejemplo que muestra el costo estimado en Azure Portal":::

Si la suscripción de Azure tiene un límite de gasto, Azure le impide gastar por encima del importe del crédito. A medida que crea y usa recursos de Azure, se usan los créditos. Cuando alcanza el límite de crédito, los recursos que ha implementado se deshabilitan para el resto de ese período de facturación. No se puede cambiar el límite de crédito, pero sí puede quitarlo. Para más información sobre los límites de gasto, consulte [Límite de gasto de Azure](../../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Supervisión de costos

Cuando empiece a usar Azure SQL Database, puede ver los costos estimados en el portal. Siga estos pasos para revisar la estimación de costos:

1. Inicie sesión en Azure Portal y vaya al grupo de recursos de la base de datos de Azure SQL. Para localizar el grupo de recursos, puede ir a la base de datos y seleccionar **Grupo de recursos** en la sección **Información general**.
1. En el menú, seleccione **Análisis de costos**.
1. Vea los **Costos acumulados** y establezca el gráfico de la parte inferior en **Nombre del servicio**. En este gráfico se muestra una estimación de los costos actuales de SQL Database. Para reducir los costos de toda la página a Azure SQL Database, seleccione **Agregar filtro** y, a continuación, seleccione **Azure SQL Database**. La información y los precios de la siguiente imagen solo se incluyen como ejemplo:

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Ejemplo que muestra los costos acumulados del en Azure Portal":::

A partir de aquí, puede explorar los costos por su cuenta. Para obtener más información sobre las distintas configuraciones de análisis de costos, consulte [Inicio del análisis de costos](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="create-budgets"></a>Creación de presupuestos

Puede crear [presupuestos](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para administrar los costos y crear [alertas](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que envíen notificaciones automáticamente a las partes interesadas sobre anomalías en los gastos y riesgos de gastos adicionales. Las alertas se basan en el gasto comparado con los umbrales de presupuesto y costo. Los presupuestos y las alertas se crean para las suscripciones y los grupos de recursos de Azure, por lo que son útiles como parte de una estrategia general de supervisión de costos. 

Los presupuestos se pueden crear con filtros para recursos o servicios específicos de Azure si quiere disponer de más granularidad en la supervisión. Los filtros ayudan a garantizar que no se creen accidentalmente recursos. Para más información sobre las opciones de filtro al crear un presupuesto, consulte [Opciones de agrupación y filtrado](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportación de datos de costos

También puede [exportar los datos de costos](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a una cuenta de almacenamiento. Esto resulta de utilidad cuando tiene que realizar un análisis de datos adicional sobre los costos. Por ejemplo, un equipo de finanzas puede analizar los datos con Excel o Power BI. Puede exportar los costos en una programación diaria, semanal o mensual y establecer un intervalo de fechas personalizado. La exportación de los datos de costos es la forma recomendada de recuperar conjuntos de datos de costos.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Otras maneras de administrar y reducir los costos de Azure SQL Database

Azure SQL Database también le permite escalar o reducir verticalmente los recursos para controlar los costos en función de las necesidades de la aplicación. Para obtener más información, consulte [Escalado dinámico de recursos de base de datos](scale-resources.md).

Para ahorrar dinero, confirme una reserva de recursos de proceso de uno a tres años. Para obtener más información, consulte [Ahorro de costos para los recursos con capacidad reservada](reserved-capacity-overview.md).


## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [optimizar su inversión en la nube con Azure Cost Management](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Obtenga más información sobre la administración de costos con los [análisis de costos](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Obtenga información sobre cómo [evitar los costos inesperados](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Haga el curso de aprendizaje guiado sobre [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).