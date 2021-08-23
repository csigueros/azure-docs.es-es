---
title: Modelos de facturación y precios
description: Información general sobre cómo funcionan los modelos de precios y facturación para Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 3b627abdf27907a5c0739e6dd7920932a3035ee7
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111981704"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Modelos de precios y facturación en Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ayuda a crear y ejecutar flujos de trabajo de integración automatizados en la nube. En este artículo se describe cómo funcionan los modelos de facturación y precios para el servicio Azure Logic Apps y los recursos relacionados. Para tarifas de precios específicas, consulte [Precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps). Para obtener información sobre cómo planear, administrar y supervisar los costos, consulte [Planificación y administración de costos para Azure Logic Apps](plan-manage-costs.md).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-multi-tenant"></a>Precios de consumo (multiinquilino)

Se aplica un modelo de precios de consumo de pago por uso a las aplicaciones lógicas que se ejecutan en el entorno de Azure Logic Apps multiinquilino, público y "global". Puede crear estas aplicaciones lógicas mediante varias opciones, por ejemplo:

* Tipo de recurso de aplicación lógica (consumo) en Azure Portal
* Extensión de Azure Logic Apps (consumo) en Visual Studio Code
* Extensión de Herramientas de Azure Logic Apps en Visual Studio
* Plantilla de Azure Resource Manager. (plantilla de ARM) con el tipo de recurso `Microsoft.Logic`
* CLI de Azure para Azure Logic Apps mediante los comandos `az logic`
* Azure PowerShell para Azure Logic Apps con el módulo `Az.LogicApp`
* API REST para Azure Logic Apps
* [Tareas de Automation](create-automation-tasks-azure-resources.md) en Azure Portal

La medición y facturación se basan en las ejecuciones del desencadenador y la acción en un flujo de trabajo de la aplicación lógica. Estas ejecuciones se miden y facturan, independientemente de si el flujo de trabajo se ejecuta correctamente o de si se crea una instancia del flujo de trabajo. Por ejemplo, suponga que la tarea de automatización usa un desencadenador de sondeo que realiza regularmente una llamada saliente a un punto de conexión. Esta solicitud de salida se mide y factura como una ejecución, independientemente de si el desencadenador se activa o se omite, lo que afecta a si se crea una instancia de flujo de trabajo.

| Elementos | Descripción |
|-------|-------------|
| Acciones y desencadenadores [integrados](../connectors/built-in.md) | Se ejecutan de forma nativa en el servicio Logic Apps y se miden con el [precio de **Acciones**](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>Por ejemplo, el desencadenador HTTP y el desencadenador de solicitud son desencadenadores integrados, mientras que la acción de respuesta y la acción HTTP son acciones integradas. Las operaciones de datos, las operaciones por lotes, las operaciones de variables y las [acciones de control de flujo de trabajo](../connectors/built-in.md), como bucles, condiciones, conmutadores, ramas paralelas, etc., también son acciones integradas. <p><p>**Nota:** Como bonificación mensual, el plan de consumo incluye varios miles de ejecuciones integradas de forma gratuita. |
| Desencadenadores y acciones de [conector estándar](../connectors/managed.md) <p><p>Desencadenadores y acciones de [conector personalizado](../connectors/apis-list.md#custom-apis-and-connectors) | Se miden con el [precio de conector estándar](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Desencadenadores y acciones de [conector empresarial](../connectors/managed.md) | Se miden con el [precio de conector empresarial](https://azure.microsoft.com/pricing/details/logic-apps/). Sin embargo, durante la versión preliminar del conector, los conectores empresariales se miden con el [precio de conector *estándar*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Acciones dentro de [bucles](logic-apps-control-flow-loops.md) | Cada acción que se ejecuta en un bucle se mide para cada ciclo de bucle que se ejecuta. <p><p>Por ejemplo, supongamos que tiene un bucle "para cada uno" que incluye acciones que procesan una lista. El servicio Logic Apps mide una acción de ese bucle multiplicando el número de elementos de lista por el número de acciones del bucle, y agrega la acción que inicia el bucle. Por lo tanto, el cálculo de una lista de 10 elementos es (10x1)+1, lo que da como resultado 11 ejecuciones de acción. |
| Número de reintentos | Para controlar las excepciones y errores más básicos, puede configurar una [directiva de reintentos](logic-apps-exception-handling.md#retry-policies) sobre desencadenadores y acciones si se admite. Estos reintentos junto con la solicitud original se cobran según tarifas basadas en si el desencadenador o la acción tiene un tipo integrado, estándar o empresarial. Por ejemplo, una acción que se ejecuta con 2 reintentos se cobra por 3 ejecuciones de acción. |
| [Retención de datos y uso de almacenamiento](#data-retention) | Se mide con el precio de retención de datos, que puede encontrar en la [página de precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/), en la tabla **Detalles de precios**. |
|||

Para más información, revise la siguiente documentación:

* [Visualización de las métricas de las ejecuciones y el uso de almacenamiento](plan-manage-costs.md#monitor-billing-metrics)
* [Límites de Azure Logic Apps](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Sin medir

* Desencadenadores que se omiten debido a condiciones no satisfechas
* Acciones que no se ejecutaron porque la aplicación lógica se detuvo antes de finalizar
* [Aplicaciones lógicas deshabilitadas](#disabled-apps)

### <a name="other-related-resources"></a>Otros recursos relacionados

Logic apps funciona con otros recursos relacionados, como cuentas de integración, puertas de enlace de datos locales y entornos de servicio de integración (ISE). Para obtener información sobre los precios de esos recursos, revise estas secciones más adelante en este tema:

* [Puerta de enlace de datos local](#data-gateway)
* [Modelo de precios de la cuenta de integración](#integration-accounts)
* [Modelo de precios ISE](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Sugerencias para la estimación de los costos de consumo

Para ayudarle a estimar de manera más precisa los costos de consumo, revise estas sugerencias:

* Piense en el número de mensajes o eventos posibles que podrían llegar cualquier día, en lugar de basar sus cálculos únicamente en el intervalo de sondeo.

* Cuando un evento o mensaje cumple los criterios del desencadenador, muchos desencadenadores intentan leer inmediatamente todos los otros eventos o mensajes de espera que cumplen los criterios. Este comportamiento significa que incluso cuando se selecciona un intervalo de sondeo más largo, el desencadenador se activa en función del número de eventos o mensajes de espera que pueden iniciar los flujos de trabajo. Los desencadenadores que siguen este comportamiento son Azure Service Bus y Azure Event Hubs.

  Por ejemplo, suponga que configura un desencadenador que comprueba un punto de conexión cada día. Cuando el desencadenador comprueba el punto de conexión y busca 15 eventos que satisfacen los criterios, se activa y ejecuta el flujo de trabajo correspondiente 15 veces. El servicio Logic Apps mide todas las acciones que realizan esos 15 flujos de trabajo, incluidas las solicitudes del desencadenador.

<a name="standard-pricing"></a>

## <a name="standard-pricing-single-tenant"></a>Precios estándar (un solo inquilino)

Se aplica un plan de hospedaje y un modelo de precios basado en planes de tarifa a las aplicaciones lógicas que se ejecutan en el entorno de Azure Logic Apps de inquilino único. Este precio se aplica al tipo de recurso **Aplicación lógica (estándar)** en Azure Portal o a las aplicaciones lógicas con las que trabaja mediante la extensión **Azure Logic Apps (estándar)** para Visual Studio Code. Al crear o implementar una aplicación lógica de este tipo, debe elegir un plan de hospedaje y un plan de tarifa que determine las tarifas de precios que se usarán para la medición y facturación al ejecutar los flujos de trabajo.

> [!NOTE]
> Para las nuevas aplicaciones lógicas que cree con el tipo de recurso **Logic App (Standard)** (Aplicación lógica [estándar]), debe usar el plan de hospedaje **Workflow Standard** (Flujo de trabajo estándar). El plan de Service App y App Service Environment no están disponibles para las nuevas aplicaciones lógicas.

<a name="hosting-plans"></a>

### <a name="pricing-tiers-and-billing-rates"></a>Planes de tarifas y tasas de facturación

Cada plan de tarifa de un plan de hospedaje incluye una cantidad específica de recursos de proceso, memoria y almacenamiento. Para ver las tarifas por hora por recurso y región, revise la [página de precios de Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

Para comprender mejor cómo funcionan los precios, en este ejemplo se proporcionan estimaciones de ejemplo para la *región Este de EE. UU. 2*.

* En la [página de precios Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/), seleccione la región **Este de EE. UU. 2** para ver las tarifas por hora o revise la tabla siguiente:

  | Recurso | USD por hora (Este de EE. UU. 2) |
  |----------|------------------------|
  | **CPU virtual (vCPU)** | 0,192 USD |
  | **Memoria** | 0,0137 USD por GB |
  |||

* En función de la información anterior, en esta tabla se muestra la tarifa mensual estimada para cada plan de tarifa y los recursos incluidos en ese plan de tarifa:

  | Plan de tarifa | USD mensuales (Este de EE. UU. 2) | CPU virtual (vCPU) | Memoria (GB) | Almacenamiento (GB) |
  |--------------|-------------------------|--------------------|-------------|--------------|
  | **WS1** | 175,20 USD | 1 | 3,5 | 250 |
  | **WS2** | 350,40 USD | 2 | 7 | 250 |
  | **WS3** | 700,80 USD | 4 | 14 | 250 |
  ||||||

* En función de la información anterior, en esta tabla se enumera cada recurso y la tarifa mensual estimada si elige el plan de precios **WS1**:

  | Recurso | Amount | USD mensuales (Este de EE. UU. 2) |
  |----------|--------|-------------------------|
  | **CPU virtual (vCPU)** | 1 | 140,16 USD |
  | **Memoria** | 3,5 GB | 35,04 USD |
  ||||

<a name="storage-transactions"></a>

### <a name="storage-transactions"></a>Transacciones de almacenamiento

Azure Logic Apps usa [Azure Storage](../storage/index.yml) para las operaciones de almacenamiento. Con Azure Logic Apps multiinquilino, el uso y los costos de almacenamiento se asocian a la aplicación lógica. Con Azure Logic Apps de un solo inquilino, puede usar su propia [cuenta de almacenamiento](../azure-functions/storage-considerations.md#storage-account-requirements) de Azure. Esta funcionalidad proporciona más control y flexibilidad con los datos de Logic Apps.

Cuando los flujos de trabajo *con estado* ejecutan sus operaciones, Azure Logic Apps realiza transacciones de almacenamiento en tiempo de ejecución. Por ejemplo, las colas se utilizan para la programación, mientras que las tablas y blobs se utilizan para almacenar los estados de los flujos de trabajo. Los costos de almacenamiento cambian en función del contenido del flujo de trabajo. Los distintos desencadenadores, acciones y cargas tienen como resultado diferentes operaciones y necesidades de almacenamiento. Las transacciones de almacenamiento siguen el [modelo de precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Los costos de almacenamiento se enumeran por separado en la factura de Azure.

### <a name="tips-for-estimating-storage-needs-and-costs"></a>Sugerencias para calcular los costos y las necesidades de almacenamiento

En el caso de Azure Logic Apps de un solo inquilino, puede hacerse una idea del número de operaciones de almacenamiento que un flujo de trabajo podría ejecutar y su costo con la [calculadora de almacenamiento de Logic Apps](https://logicapps.azure.com/calculator). Puede seleccionar un flujo de trabajo de ejemplo o usar una definición de flujo de trabajo existente. El primer cálculo estima el número de operaciones de almacenamiento en el flujo de trabajo. Luego, puede usar estos números para calcular los posibles costos mediante la [calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/).

Para más información, revise la siguiente documentación:

* [Estimación de las necesidades de almacenamiento y los costos de los flujos de trabajo de Azure Logic Apps de un solo inquilino](estimate-storage-costs.md).
* [Detalles de precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/)

<a name="fixed-pricing"></a>

## <a name="ise-pricing-dedicated"></a>Precios de ISE (dedicado)

Se aplica un modelo de precios fijos a las aplicaciones lógicas que se ejecutan en un [*entorno del servicio de integración* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) dedicado. Un ISE se factura con el [precio del Entorno del servicio de integración](https://azure.microsoft.com/pricing/details/logic-apps), que depende del [nivel de ISE o *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) que cree. Este precio difiere del precio de multiinquilino ya que se paga por la capacidad reservada y los recursos dedicados, tanto si se usan como si no.

| SKU de ISE | Descripción |
|---------|-------------|
| **Premium** | La unidad base tiene una [capacidad fija](logic-apps-limits-and-config.md#integration-service-environment-ise) y se [factura según una tarifa por hora para el SKU prémium](https://azure.microsoft.com/pricing/details/logic-apps). Si necesita más rendimiento, puede [agregar más unidades de escalado](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) durante o después de crear el ISE. Cada unidad de escalado se factura según una [tarifa por hora, que es aproximadamente la mitad de la tarifa de unidad base](https://azure.microsoft.com/pricing/details/logic-apps). <p><p>Para información sobre los límites y la capacidad, consulte [Límites de ISE en Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Developer** | La unidad base tiene una [capacidad fija](logic-apps-limits-and-config.md#integration-service-environment-ise) y se [factura según una tarifa por hora para el SKU de desarrollador](https://azure.microsoft.com/pricing/details/logic-apps). Sin embargo, este SKU no tiene ningún Acuerdo de Nivel de Servicio (SLA), funcionalidad de escalado vertical ni redundancia durante el reciclaje, lo que significa que puede experimentar retrasos o tiempo de inactividad. Las actualizaciones de back-end pueden interrumpir el servicio de forma intermitente. <p><p>**Importante**: Use este SKU solo para la exploración, los experimentos, el desarrollo y las pruebas, no para pruebas de rendimiento ni en producción. <p><p>Para información sobre los límites y la capacidad, consulte [Límites de ISE en Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

### <a name="included-at-no-extra-cost"></a>Incluido sin costo adicional

| Elementos | Descripción |
|-------|-------------|
| Acciones y desencadenadores [integrados](../connectors/built-in.md) | Se muestra la etiqueta **Núcleo** y se ejecuta en el mismo ISE que las aplicaciones lógicas. |
| [Conectores estándar](../connectors/managed.md) <p><p>[Conectores de empresa](../connectors/managed.md#enterprise-connectors) | - Los conectores administrados en los que se muestra la etiqueta **ISE** están diseñados especialmente para funcionar en la puerta de enlace de datos local y ejecutarse en el mismo ISE que las aplicaciones lógicas. Los precios de ISE incluyen tantas conexiones empresariales como desee. <p><p>- Los conectores en los que no se muestra la etiqueta ISE se ejecutan en el servicio Azure Logic Apps de un solo inquilino. Sin embargo, en el precio de ISE se incluyen estas ejecuciones para aplicaciones lógicas que se ejecutan en un ISE. |
| Acciones dentro de [bucles](logic-apps-control-flow-loops.md) | En el precio de ISE se incluye todas las acciones que se ejecutan en un bucle se mide para cada ciclo de bucle que se ejecuta. <p><p>Por ejemplo, supongamos que tiene un bucle "para cada uno" que incluye acciones que procesan una lista. Para obtener el número total de ejecuciones de acciones, multiplique el número de elementos de lista por el número de acciones del bucle, y agregue la acción que inicia el bucle. Por lo tanto, el cálculo de una lista de 10 elementos es (10x1)+1, lo que da como resultado 11 ejecuciones de acción. |
| Número de reintentos | Para controlar las excepciones y errores más básicos, puede configurar una [directiva de reintentos](logic-apps-exception-handling.md#retry-policies) sobre desencadenadores y acciones si se admite. Los precios de ISE incluyen reintentos junto con la solicitud original. |
| [Retención de datos y uso de almacenamiento](#data-retention) | Logic Apps en un ISE no genera costos de retención de datos ni uso de almacenamiento. |
| [Cuentas de integración](#integration-accounts) | Incluye el uso de un solo nivel de cuenta de integración, basado en el SKU de ISE, sin costo adicional. |
|||

Para información sobre los límites, consulte [Límites de ISE en Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Cuentas de integración

Una [cuenta de integración](../logic-apps/logic-apps-pricing.md#integration-accounts) es un recurso independiente que se crea y se vincula a una aplicación lógica para que pueda explorar, compilar y probar soluciones de integración B2B que usen [EDI](logic-apps-enterprise-integration-b2b.md) y funcionalidades de [procesamiento de XML](logic-apps-enterprise-integration-xml.md).

Azure Logic Apps ofrece estos niveles de cuenta de integración que [varían en el modelo de facturación](https://azure.microsoft.com/pricing/details/logic-apps/) y [precios](logic-apps-pricing.md#integration-accounts) en función de si la aplicación lógica se ejecuta en Azure Logic Apps (multiinquilino o inquilino único) o en un ISE.

| Nivel | Descripción |
|------|-------------|
| **Basic** | Para los escenarios en los que quiere utilizar solo el control de mensajes o actuar como un asociado de pequeña empresa que tiene una relación empresarial con una entidad empresarial mayor. <p><p>Compatible con el Acuerdo de Nivel de Servicio de Logic Apps. |
| **Estándar** | Para los escenarios en los que se tienen relaciones B2B más complejas y un número mayor de entidades que debe administrar. <p><p>Compatible con el Acuerdo de Nivel de Servicio de Logic Apps. |
| **Gratis** | Para escenarios de exploración, no en escenarios de producción. Este nivel tiene límites en cuanto a disponibilidad, rendimiento y uso de regiones. Por ejemplo, el nivel Gratis solo está disponible para las regiones públicas en Azure, como Oeste de EE. UU. o Sudeste Asiático, pero no para [Azure China 21Vianet](/azure/china/overview-operations) o [Azure Government](../azure-government/documentation-government-welcome.md). <p><p>**Nota**: No compatible con el Acuerdo de Nivel de Servicio de Logic Apps. |
|||

Para información sobre los límites de las cuentas de integración, consulte [Límites y configuración de Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits), como:

* [Límites en las cuentas de integración por suscripción de Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Límites en varios artefactos por cuenta de integración](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Los artefactos incluyen entidades, contratos, mapas, esquemas, conjuntos, certificados y configuraciones de lote, etc.

### <a name="multi-tenant-or-single-tenant-based-logic-apps"></a>Aplicaciones lógicas multiinquilino o basadas en un solo inquilino

Las cuentas de integración se facturan mediante un [precio fijo de cuenta de integración](https://azure.microsoft.com/pricing/details/logic-apps/) que se basa en el nivel de cuenta que se usa.

### <a name="ise-based-logic-apps"></a>Aplicaciones lógicas basadas en ISE

El ISE incluye, sin costo adicional, una sola cuenta de integración, basada en el SKU de ISE. Por un costo adicional, puede crear más cuentas de integración para usar hasta el [límite total del ISE](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Obtenga más información sobre el [modelo de precios de ISE](#fixed-pricing) anteriormente en este tema.

| SKU de ISE | Cuenta de integración incluida | Sin costo adicional |
|---------|------------------------------|-----------------|
| **Premium** | Una sola cuenta de integración [estándar](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | hasta 19 cuentas Estándar más. No se permiten cuentas Gratis ni Básicas. |
| **Developer** | Una sola cuenta de integración [Gratis](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Hasta 19 cuentas Estándar más si ya tiene una cuenta Gratuita, o bien 20 cuentas Estándar en total si no dispone de una cuenta Gratuita. No se permiten cuentas Básicas. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-usage"></a>Retención de datos y uso de almacenamiento

Azure Logic Apps usa [Azure Storage](../storage/index.yml) en las operaciones de almacenamiento. Las entradas y salidas del historial de ejecución del flujo de trabajo se almacenan y miden, en función del [límite de retención del historial de ejecución](logic-apps-limits-and-config.md#run-duration-retention-limits) de la aplicación lógica. Para supervisar el uso del almacenamiento, consulte [Visualización de métricas para ejecuciones y uso de almacenamiento](plan-manage-costs.md#monitor-billing-metrics).

| Entorno | Notas |
|-------------|-------|
| **Multiinquilino** | El uso y la retención del almacenamiento se facturan con una tarifa fija, que puede encontrar en la [página de precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps), en la tabla **Detalles de precios**. |
| **Inquilino único** | El uso y la retención del almacenamiento se facturan mediante el [modelo de precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Los costos de almacenamiento se enumeran por separado en la factura de Azure. Para obtener más información, consulte [Transacciones de almacenamiento (inquilino único)](#storage-transactions). |
| **ISE** | El uso y la retención del almacenamiento no incurren en cargos. |
|||

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Puerta de enlace de datos local

Una [puerta de enlace de datos local](../logic-apps/logic-apps-gateway-install.md) es un recurso independiente que se crea para que los flujos de trabajo de las aplicaciones lógicas puedan acceder a los datos locales mediante conectores específicos compatibles con la puerta de enlace. Las operaciones de conectores que se ejecutan a través de la puerta de enlace generan cargos, pero la propia puerta de enlace no genera cargos.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps-or-workflows"></a>Deshabilitación de aplicaciones lógicas o flujos de trabajo

Las aplicaciones lógicas (multiinquilino) o los flujos de trabajo (inquilino único) deshabilitados no generan cargos porque no pueden crear nuevas instancias mientras están deshabilitados.

## <a name="next-steps"></a>Pasos siguientes

* [Planeamiento y administración de los costos de Azure Logic Apps](plan-manage-costs.md)