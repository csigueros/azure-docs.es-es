---
title: Planeamiento de la administración de costos de App Service
description: Aprenda a planear y administrar los costos de Azure App Service mediante el análisis de costos de Azure Portal.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 06/23/2021
ms.openlocfilehash: 3f2ae25c3f2e1076cf714aa56f7ecdf867d6dd8f
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112554196"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Planeamiento y administración de costos de Azure App Service

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

En este artículo se describe cómo puede planear y administrar los costos de Azure App Service. Primero, usará la calculadora de precios de Azure para ayudar a planear los costos de App Service antes de agregar recursos al servicio para estimar los costos. Después, a medida que agregue recursos de Azure, revise los costos estimados. Después de comenzar a usar los recursos de App Service, utilice las características de [Cost Management](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para establecer presupuestos y supervisar los costos. También puede revisar los costos previstos e identificar las tendencias de gasto para identificar las áreas en las que podría querer actuar. Los costos de Azure App Service son solo una parte de los costos mensuales de la factura de Azure. Aunque en este artículo se explica cómo planear y administrar los costos de App Service, se le facturarán todos los servicios y recursos de Azure usados para su suscripción de Azure, incluidos los servicios de terceros.

## <a name="understand-the-full-billing-model-for-azure-app-service"></a>Descripción del modelo de facturación completo de Azure App Service

Azure App Service se ejecuta en la infraestructura de Azure, que genera costos cuando implementa recursos nuevos. Es importante entender que podrían generarse otros costos de infraestructura adicionales.

### <a name="how-youre-charged-for-azure-app-service"></a>Cómo se cobra Azure App Service

Cuando crea o utiliza recursos de Azure App Service, se le cobra en función de los siguientes medidores:

- Se le cobra una tarifa por hora según el plan de tarifa de su plan de App Service, prorrateada al segundo.
- El cargo se aplica a cada instancia del plan escalada horizontalmente, según la cantidad de tiempo que esté asignada la instancia de máquina virtual. 

Otros recursos de costo de App Service son (consulte [Precios de App Service](https://azure.microsoft.com/pricing/details/app-service/) para más información):

- [Dominios de App Service](manage-custom-dns-buy-domain.md): la suscripción se cobra por el registro de dominio anualmente, si la renovación automática está habilitada.
- [Certificados de App Service](configure-ssl-certificate.md#import-an-app-service-certificate): un cargo único en el momento de la compra. Si tiene que proteger varios subdominios, puede reducir el costo adquiriendo un certificado comodín en lugar de varios certificados estándar.
- [Enlaces de certificados basados en IP](configure-ssl-bindings.md#create-binding): el enlace se configura en un certificado en el nivel de la aplicación. Los costos se acumulan para cada enlace. En el nivel **Estándar** o niveles superiores, no se cobra el primer enlace basado en IP.

Al final del ciclo de facturación, se suman los cargos de cada instancia de máquina virtual. La factura muestra una sección con todos los costos de App Service. Hay un elemento de línea independiente para cada medidor.

### <a name="other-costs-that-might-accrue-with-azure-app-service"></a>Otros costos que pueden generarse con Azure App Service

En función de la característica que use en App Service, se pueden crear los siguientes recursos que acumulan costos:

- **Nivel Aislado**: se requiere una [red virtual](../virtual-network/index.yml) para un entorno de App Service y se cobra aparte.
- **Copias de seguridad**: se requiere una [cuenta de almacenamiento](../storage/index.yml) para realizar copias de seguridad y se cobra por separado.
- **Registros de diagnóstico**: puede seleccionar [Cuenta de almacenamiento](../storage/index.yml) como opción de registro, o bien integrarse con [Azure Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md). Estos servicios se cobran por separado.
- **Certificados de App Service**: los certificados que adquiera en Azure deben mantenerse en [Azure Key Vault](../key-vault/index.yml), que se cobra aparte.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Costos que pueden generarse tras eliminar un recurso

Si elimina todas las aplicaciones de un plan de App Service, el plan continúa acumulando cargos según el nivel de precios configurado y el número de instancias. Para evitar cargos no deseados, elimine el plan o redúzcalo verticalmente al nivel **Gratis**.

Aunque elimine los recursos de Azure App Service, puede que los recursos de los servicios de Azure relacionados sigan existiendo. Estos recursos siguen generando costos hasta que se eliminan. Por ejemplo:

- La red virtual que creó para un plan de App Service de nivel **Aislado**.
- Las cuentas de almacenamiento que se crearon para almacenar copias de seguridad o registros de diagnóstico.
- La instancia de Key Vault que creó para almacenar certificados de App Service.
- Los espacios de nombres de Log Analytics que creó para enviar registros de diagnóstico.
- [Reservas de instancias o de timbres](#azure-reservations) de App Service que todavía no han expirado

### <a name="using-azure-prepayment-with-azure-app-service"></a>Uso del pago por adelantado de Azure con Azure App Service

Puede pagar los cargos de Azure App Service con el crédito del pago por adelantado de Azure. Sin embargo, no puede usar el crédito del pago por adelantado de Azure para pagar los cargos de productos y servicios de terceros, como los que proceden de Azure Marketplace.

## <a name="estimate-costs"></a>cálculo de los costos

Una manera sencilla de estimar y optimizar el costo de App Service de antemano es usar la [calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/).

Para usar la calculadora de precios, haga clic en **App Service** en la pestaña **Productos**. A continuación, desplácese hacia abajo para trabajar con la calculadora. La captura de pantalla siguiente es un ejemplo y no refleja los precios actuales.

![Ejemplo que muestra el costo estimado en la calculadora de precios de Azure](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Revisión de los costos estimados en Azure Portal

Al crear una aplicación o un plan de App Service, puede ver los costos estimados.

Para crear una aplicación y ver el precio estimado:

1. En la página de creación, desplácese hacia abajo hasta **Plan de App Service** y haga clic en **Crear nuevo**.
1. Especifique un nombre y haga clic en **Aceptar**.
1. Junto a **SKU y tamaño**, haga clic en **Cambiar tamaño**.
1. Revise el precio estimado que se muestra en el resumen. La captura de pantalla siguiente es un ejemplo y no refleja los precios actuales.

    ![Revisión del costo estimado de cada plan de tarifa en el portal](media/overview-manage-costs/pricing-estimates.png)

Si la suscripción de Azure tiene un límite de gasto, Azure le impide gastar por encima del importe del crédito. A medida que crea y usa recursos de Azure, se usan los créditos. Cuando alcanza el límite de crédito, los recursos que ha implementado se deshabilitan para el resto de ese período de facturación. No se puede cambiar el límite de crédito, pero sí puede quitarlo. Para más información sobre los límites de gasto, consulte [Límite de gasto de Azure](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="optimize-costs"></a>Optimización de costos

En un nivel básico, las aplicaciones de App Service se cobran según el plan de App Service que las hospeda. Los costos asociados a la implementación de App Service dependen de algunos factores principales:

- **Plan de tarifa**: también conocido como SKU del plan de App Service. Los niveles superiores proporcionan más núcleos de CPU, memoria, almacenamiento o características, o combinaciones de ellos.
- **Recuento de instancias**: los niveles dedicados (Básico y niveles superiores) se pueden escalar horizontalmente y cada instancia escalada horizontalmente acumula costos.
- **Impuesto sobre el timbre**: en el nivel Aislado, se acumula una tarifa plana en el entorno de App Service, independientemente del número de aplicaciones o instancias de trabajo hospedadas.

Un plan de App Service puede hospedar más de una aplicación. En función de la implementación, podría ahorrar costos si hospeda más aplicaciones en un solo plan de App Service (es decir, si hospeda las aplicaciones en menos planes de App Service).

Para más información, consulte [Introducción a los planes de Azure App Service](overview-hosting-plans.md).

### <a name="non-production-workloads"></a>Cargas de trabajo que no son de producción

Para probar App Service o la solución, y acumular solo un costo bajo o mínimo, puede comenzar con los dos planes de tarifa de nivel inicial, **Gratis** y **Compartido**, que se hospedan en instancias compartidas. Para probar la aplicación en instancias dedicadas con un mejor rendimiento, puede actualizar al nivel **Básico**, que admite aplicaciones de Windows y Linux. 

> [!NOTE]
> **Precios de Desarrollo/pruebas de Azure**: para probar cargas de trabajo de preproducción que requieran niveles más altos (todos los niveles excepto **Aislado**), los suscriptores de Visual Studio también pueden aprovechar las ventajas de [Precios de Desarrollo/pruebas de Azure](https://azure.microsoft.com/pricing/dev-test/), que ofrece importantes descuentos.
>
> Los niveles **Gratis** y **Compartido**, así como los descuentos de precios del nivel Desarrollo/pruebas de Azure, no tienen un Acuerdo de Nivel de Servicio con respaldo financiero.

### <a name="production-workloads"></a>Cargas de trabajo de producción

Para las cargas de trabajo de producción se recomienda el plan de tarifa dedicado **Estándar** u otro superior. Aunque el precio aumenta en los niveles superiores, también ofrece más memoria y almacenamiento y hardware de mayor rendimiento, lo que le proporciona mayor densidad de aplicaciones por instancia de proceso. Esto se traduce en un recuento inferior de instancias para el mismo número de aplicaciones y, por tanto, un costo menor. De hecho, el nivel **Premium V3** (el nivel más alto aparte del nivel **Aislado**) es la manera más rentable de usar la aplicación a gran escala. Para agregarlos al ahorro, puede obtener descuentos en [reservas de Premium V3](#azure-reservations).

> [!NOTE]
> **Premium V3** admite contenedores de Windows y de Linux. 

Una vez que elija el plan de tarifa que desee, debe minimizar las instancias inactivas. En una implementación escalada, puede desperdiciar dinero en instancias de proceso infrautilizadas. Debe [configurar el escalado automático](../azure-monitor/autoscale/autoscale-get-started.md), disponible en el nivel **Estándar** y en niveles superiores. Mediante la creación de programaciones de escalado horizontal, así como de reglas de escalado horizontal basadas en métricas, solo paga por las instancias que realmente necesita en un momento dado.

### <a name="azure-reservations"></a>Reservas de Azure

Si planea usar un número mínimo conocido de instancias de proceso durante un año o más, aproveche las ventajas del nivel **Premium V3** y reduzca el costo de la instancia de forma significativa reservando esas instancias en incrementos de 1 o 3 años. El ahorro de costos mensuales puede ser de hasta un 55% por instancia. Son posibles dos tipos de reservas:

- **Windows (o independiente de la plataforma)** : pueden aplicarse a instancias de Windows o Linux de su suscripción.
- **Específica de Linux**: solo se aplican a las instancias de Linux de su suscripción.

Los precios de instancia reservada se aplican a las instancias correspondientes de su suscripción, hasta el número de instancias que haya reservado. Las instancias reservadas son una cuestión de facturación y no están vinculadas a instancias de proceso específicas. Aunque ejecute menos instancias de las que reserve en cualquier momento durante el período de reserva, seguirá pagando por todas las instancias reservadas. Si ejecuta más instancias de las que reserva en cualquier momento durante el período de reserva, deberá pagar el costo acumulado normal de las instancias adicionales.

El nivel **Aislado** (entorno de App Service) también admite reservas de 1 y 3 años a precios reducidos. Para más información, consulte [Cómo se aplican los descuentos de reserva a Azure App Service](../cost-management-billing/reservations/reservation-discount-app-service.md).

## <a name="monitor-costs"></a>Supervisión de costos

A medida que se usan recursos de Azure con App Service, se incurre en costos. Los costos de la unidad de uso de recursos de Azure varían en función de intervalos de tiempo (segundos, minutos, horas y días). En cuanto se inicia el uso de App Service, se generan costos, que puede ver en el [análisis de costos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Al usar el análisis de costos, puede ver los costos de App Service de diferentes intervalos de tiempo en gráficos y tablas. Algunos ejemplos son: por día, mes actual y anterior y año. También puede ver los costos comparados con los presupuestos y los costos previstos. Con el tiempo, cambiar a vistas más largas puede ayudarle a identificar las tendencias de gasto y comprobar dónde este se ha sobrepasado. Si ha creado presupuestos, también podrá ver fácilmente dónde se han excedido.
    
Para ver los costos de App Service en el análisis de costos:

1. Inicie sesión en Azure Portal.
2. Abra el ámbito en Azure Portal y seleccione **Análisis de costos** en el menú. Por ejemplo, vaya a **Suscripciones**, seleccione una suscripción de la lista y, a continuación, seleccione **Análisis de costos** en el menú. Seleccione **Ámbito** para cambiar a otro ámbito del análisis de costos.
3. De forma predeterminada, el costo de los servicios se muestra en el primer gráfico de anillos. Seleccione el área del gráfico con la etiqueta "App Service".

Los costos mensuales reales se muestran cuando se abre inicialmente el análisis de costos. Este es un ejemplo con todos los costos mensuales de uso.

![Ejemplo que muestra los costos acumulados de una suscripción](media/overview-manage-costs/all-costs.png)

Para limitar la información a los costos de un único servicio, como App Service, seleccione **Agregar filtro** y, luego, elija **Nombre del servicio**. Después, seleccione **App Service**.

Este es un ejemplo que muestra solo los costos de App Service.

![Ejemplo que muestra los costos acumulados del servicio](media/overview-manage-costs/service-specific-costs.png)

En el ejemplo anterior, hemos visto el costo actual del servicio. También se muestran los costos por regiones de Azure (ubicaciones) y los costos de App Service por grupo de recursos. A partir de aquí, puede explorar los costos por su cuenta.

## <a name="create-budgets"></a>Creación de presupuestos

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Puede crear [presupuestos](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para administrar los costos y crear [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que envíen notificaciones automáticamente a las partes interesadas sobre anomalías en los gastos y riesgos de gastos adicionales. Las alertas se basan en el gasto comparado con los umbrales de presupuesto y costo. Los presupuestos y las alertas se crean para las suscripciones y los grupos de recursos de Azure, por lo que son útiles como parte de una estrategia general de supervisión de costos. 

Los presupuestos se pueden crear con filtros para recursos o servicios específicos de Azure si quiere disponer de más granularidad en la supervisión. Los filtros ayudan a garantizar que no se crean accidentalmente recursos nuevos con un costo adicional. Para más información sobre las opciones de filtro disponibles al crear un presupuesto, consulte [Opciones de agrupación y filtrado](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportación de datos de costos

También puede [exportar los datos de costos](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a una cuenta de almacenamiento. Esto resulta útil cuando usted u otro usuario necesita hacer un análisis de datos adicional para los costos. Por ejemplo, un equipo de finanzas puede analizar los datos con Excel o Power BI. Puede exportar los costos en una programación diaria, semanal o mensual y establecer un intervalo de fechas personalizado. La exportación de los datos de costos es la forma recomendada de recuperar conjuntos de datos de costos.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre cómo funcionan los precios con Azure Storage. Consulte [Precios de App Service](https://azure.microsoft.com/pricing/details/app-service/).
- Aprenda a [optimizar su inversión en la nube con Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Obtenga más información sobre la administración de costos con los [análisis de costos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Obtenga información sobre cómo [evitar los costos inesperados](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Haga el curso de aprendizaje guiado sobre [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->