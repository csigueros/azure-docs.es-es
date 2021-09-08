---
title: Medición, facturación y precios de uso
description: Aprenda cómo funcionan los modelos de medición, facturación y precios de uso en Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 08/09/2021
ms.openlocfilehash: 63c7a2c79ca5f0d241ddc3727d006bb2befb8163
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860814"
---
# <a name="usage-metering-billing-and-pricing-models-for-azure-logic-apps"></a>Modelos medición, facturación y precios de uso de Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ayuda a crear y ejecutar flujos de trabajo de integración automatizados en la nube. En este artículo se describe cómo funcionan los modelos de medición, facturación y precios de Azure Logic Apps y los recursos relacionados. Si necesita información sobre precios específicos, planeamiento de costos o diferentes entornos de hospedaje, por ejemplo, revise el siguiente contenido:

* [Precios de Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps)
* [Planeamiento y administración de los costos de Azure Logic Apps](plan-manage-costs.md)
* [Entorno de servicio de integración de un solo inquilino o de tipo multiinquilino](single-tenant-overview-compare.md)

<a name="consumption-pricing"></a>

## <a name="consumption-multi-tenant"></a>Consumo (multiinquilino)

En las instancias de Azure Logic Apps multiinquilino, una aplicación lógica y su flujo de trabajo siguen los precios y la facturación del plan de [**consumo**](https://azure.microsoft.com/pricing/details/logic-apps). Estas aplicaciones lógicas se crean de varias maneras, por ejemplo, al elegir el tipo de recurso **Logic App (Consumption)** (Aplicación lógica [consumo]), cuando se usa la extensión **Azure Logic Apps (Consumption)** (Azure Logic Apps [consumo]) en Visual Studio Code o cuando se crean [tareas de automatización](create-automation-tasks-azure-resources.md).

En la tabla siguiente se resume cómo el modelo de consumo controla la medición y la facturación de los siguientes componentes cuando se usa con una aplicación lógica y un flujo de trabajo en Azure Logic Apps multiinquilino:

| Componente | Medición y facturación |
| ----------|----------------------|
| Operaciones de desencadenador y acción | El modelo de consumo incluye un *número inicial* de operaciones integradas gratuitas, por suscripción de Azure, que puede ejecutar un flujo de trabajo. Por encima de este número, la medición se aplica a *cada ejecución* y la facturación sigue los precios de [*Acciones* del plan de consumo](https://azure.microsoft.com/pricing/details/logic-apps). En el caso de otros tipos de operaciones, como los conectores administrados, la facturación sigue los precios del conector [*estándar* o *empresarial* del plan de consumo](https://azure.microsoft.com/pricing/details/logic-apps). Para más información, consulte [Operaciones de desencadenador y acción en el modelo de consumo](#consumption-operations). |
| Operaciones de almacenamiento | La medición se aplica *solo al consumo de almacenamiento relacionado con la retención de datos*, como el almacenamiento de entradas y salidas del historial de ejecución del flujo de trabajo. La facturación sigue los [precios de retención de datos del plan de consumo](https://azure.microsoft.com/pricing/details/logic-apps/). Para más información, consulte [Operaciones de almacenamiento](#storage-operations). |
| Cuentas de integración | La medición se aplica en función del tipo de cuenta de integración que cree y use con la aplicación lógica. La facturación sigue los precios de la [*cuenta de integración*](https://azure.microsoft.com/pricing/details/logic-apps/), a menos que la aplicación lógica se implemente y hospede en un [entorno de servicio de integración (ISE)](#ise-pricing). Para más información, consulte [Cuentas de integración](#integration-accounts). |
|||

<a name="consumption-operations"></a>

### <a name="trigger-and-action-operations-in-the-consumption-model"></a>Operaciones de desencadenador y acción en el modelo de consumo

Excepto por el número inicial de ejecuciones de operaciones integradas gratuitas, por suscripción de Azure, que puede ejecutar un flujo de trabajo, el modelo de consumo mide y factura una operación en función de *cada ejecución*, con independencia de si el flujo de trabajo global se ejecuta correctamente, finaliza o incluso se crea una instancia de él. Normalmente, una operación realiza una única ejecución [a menos que tenga habilitados los reintentos](#other-operation-behavior). A su vez, una ejecución normalmente realiza una sola llamada [a menos que la operación admita y permita la fragmentación o paginación para obtener grandes cantidades de datos](logic-apps-handle-large-messages.md). Si está habilitada la fragmentación o paginación, es posible que una ejecución de la operación tenga que realizar varias llamadas. El modelo de consumo mide y factura una operación *por ejecución, no por llamada*.

Por ejemplo, suponga que un flujo de trabajo comienza con un desencadenador de sondeo que obtiene registros mediante la realización regular de llamadas salientes a un punto de conexión. La llamada saliente se mide y se factura como una sola ejecución, independientemente de si el desencadenador se activa o se omite. El estado del desencadenador controla si la instancia de flujo de trabajo se crea y ejecuta. Ahora, supongamos que la operación también admite, y tiene habilitada, la fragmentación o la paginación. Si la operación tiene que realizar 10 llamadas para terminar de obtener todos los datos, la operación todavía se mide y se factura como una *sola ejecución*, a pesar de realizar varias llamadas.

En la tabla siguiente se resume cómo el modelo de consumo controla la medición y la facturación de los siguientes tipos de operación cuando se usa con una aplicación lógica y un flujo de trabajo en Azure Logic Apps multiinquilino:

| Tipo de operación | Descripción | Medición y facturación |
|----------------|-------------|----------------------|
| [*Integrada*](../connectors/built-in.md) | Estas operaciones se ejecutan de forma directa y nativa con el entorno de ejecución de Azure Logic Apps. En el diseñador, puede encontrar estas operaciones en la etiqueta **Integrado**. <p>Por ejemplo, el desencadenador HTTP y el desencadenador de solicitud son desencadenadores integrados. La acción HTTP y la acción de respuesta son acciones integradas. Otras operaciones integradas incluyen acciones de control de flujo de trabajo, como bucles y condiciones, operaciones de datos, operaciones por lotes, etc. | El modelo de consumo incluye un *número inicial de operaciones integradas gratuitas*, por suscripción de Azure, que puede ejecutar un flujo de trabajo. Por encima de este número, las ejecuciones de operaciones integradas siguen los precios de [*Acciones*](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Nota**: Algunas operaciones de conectores administrados *también* están disponibles como operaciones integradas, que se incluyen en las operaciones gratuitas iniciales. Por encima de las operaciones inicialmente gratuitas, la facturación sigue los precios de [*Acciones*](https://azure.microsoft.com/pricing/details/logic-apps/), no los precios de los conectores [*estándar* o *empresarial*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [*Conector administrado*](../connectors/managed.md) | Estas operaciones se ejecutan de forma independiente en Azure. En el diseñador, puede encontrar estas operaciones bajo la etiqueta **Estándar** o **Empresarial**. | Estas ejecuciones de operación siguen los precios del conector [*estándar* o *empresarial*](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Nota**: Las ejecuciones de operación del conector empresarial en versión preliminar siguen los precios del conector [estándar *del plan de consumo*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [*Conector personalizado*](../connectors/apis-list.md#custom-apis-and-connectors) | Estas operaciones se ejecutan de forma independiente en Azure. En el diseñador, puede encontrar estas operaciones en la etiqueta **Personalizado**. Para ver los límites de número de conectores, rendimiento y tiempo de espera, revise [Límites de los conectores personalizados en Azure Logic Apps](logic-apps-limits-and-config.md#custom-connector-limits). | Estas ejecuciones de operación siguen los precios del conector [*estándar*](https://azure.microsoft.com/pricing/details/logic-apps/). |
||||

Para más información sobre cómo funciona el modelo de consumo con operaciones que se ejecutan dentro de otras operaciones, como bucles, procesamiento de varios elementos (por ejemplo, matrices) y directivas de reintento, consulte [Comportamiento de otras operaciones](#other-operation-behavior).

<a name="consumption-cost-estimation-tips"></a>

### <a name="cost-estimation-tips-for-the-consumption-model"></a>Sugerencias de estimación de costos para el modelo de consumo

Para ayudarle a estimar de manera más precisa los costos de consumo, revise estas sugerencias:

* Piense en el número de mensajes o eventos posibles que podrían llegar cualquier día, en lugar de basar sus cálculos únicamente en el intervalo de sondeo.

* Cuando un evento o mensaje cumple los criterios del desencadenador, muchos desencadenadores intentan leer inmediatamente todos los otros eventos o mensajes de espera que cumplen los criterios. Este comportamiento significa que incluso cuando se selecciona un intervalo de sondeo más largo, el desencadenador se activa en función del número de eventos o mensajes de espera que pueden iniciar los flujos de trabajo. Los desencadenadores que siguen este comportamiento son Azure Service Bus y Azure Event Hubs.

  Por ejemplo, suponga que configura un desencadenador que comprueba un punto de conexión cada día. Cuando el desencadenador comprueba el punto de conexión y busca 15 eventos que satisfacen los criterios, se activa y ejecuta el flujo de trabajo correspondiente 15 veces. El servicio Logic Apps mide todas las acciones que realizan esos 15 flujos de trabajo, incluidas las solicitudes del desencadenador.

<a name="standard-pricing"></a>

## <a name="standard-single-tenant"></a>Estándar (inquilino único)

En Azure Logic Apps de inquilino único, una aplicación lógica y su flujo de trabajo siguen los precios y la facturación del plan [**estándar**](https://azure.microsoft.com/pricing/details/logic-apps/). Estas aplicaciones lógicas se crean de varias maneras, por ejemplo, cuando se elige el tipo de recurso **Logic App (Standard)** (Aplicación lógica [estándar]) o se usa la extensión **Azure Logic Apps (Standard)** (Azure Logic Apps [estándar]) en Visual Studio Code. Este modelo de precios requiere que las aplicaciones lógicas usen un plan de hospedaje y un plan de tarifa, lo que difiere del plan de consumo, ya que se le factura por la capacidad reservada y los recursos dedicados, tanto si los usa como si no.

> [!IMPORTANT]
> Al crear o implementar nuevas aplicaciones lógicas basadas en el tipo de recurso **Logic App (Standard)** (Aplicación lógica [estándar]), puede usar el plan de hospedaje Workflow Standard (Flujo de trabajo estándar) en todas las regiones de Azure, o bien puede usar el plan de hospedaje App Service, pero solo cuando seleccione la región **App Service Environment v3** en la pestaña **Aspectos básicos**.
>
> Aunque el tipo de recurso **Logic App (Standard)** (Aplicación lógica [estándar]) en versión preliminar le permite usar el plan de App Service, el plan Premium de Functions, App Service Environment v1 y App Service Environment v2, estas opciones ya no están disponibles ni se admiten para la versión pública de este tipo de recurso de Azure Logic Apps.

En la tabla siguiente se resume cómo el modelo estándar controla la medición y la facturación de los siguientes componentes cuando se usa con una aplicación lógica y un flujo de trabajo en Azure Logic Apps de inquilino único:

| Componente | Medición y facturación |
|-----------|----------------------|
| CPU virtual (vCPU) y memoria | El modelo estándar *requiere* que la aplicación lógica use el plan de hospedaje **Workflow Standard** (Flujo de trabajo estándar) y un plan de tarifa, que determina los niveles de recursos y las tarifas de precios que se aplican a la capacidad de proceso y memoria. Para más información, consulte [Planes de tarifa en el modelo estándar](#standard-pricing-tiers). |
| Operaciones de desencadenador y acción | El modelo estándar incluye un *número ilimitado* de operaciones integradas gratuitas que el flujo de trabajo puede ejecutar. <p>Si el flujo de trabajo usa operaciones de conectores administrados, la medición de esas operaciones se aplica a *cada llamada*, mientras que la facturación sigue los mismos [precios del conector *estándar* o *empresarial* que el plan de consumo](https://azure.microsoft.com/pricing/details/logic-apps). Para más información, consulte [Operaciones de desencadenador y acción en el modelo de estándar](#standard-operations). |
| Operaciones de almacenamiento | La medición se aplica a las operaciones de almacenamiento ejecutadas por Azure Logic Apps. Por ejemplo, las operaciones de almacenamiento se ejecutan cuando el servicio guarda las entradas y salidas del historial de ejecución del flujo de trabajo. La facturación sigue el [plan de tarifa](#standard-pricing-tiers) elegido. Para más información, consulte [Operaciones de almacenamiento](#storage-operations). |
| Cuentas de integración | Si crea una cuenta de integración para que la use la aplicación lógica, la medición se basa en el tipo de cuenta de integración que cree. La facturación sigue los precios de la [*cuenta de integración*](https://azure.microsoft.com/pricing/details/logic-apps/). Para más información, consulte [Cuentas de integración](#integration-accounts). |
|||

<a name="standard-pricing-tiers"></a>

### <a name="pricing-tiers-in-the-standard-model"></a>Planes de tarifa del modelo estándar

l plan de tarifa que elija para la medición y la facturación de la aplicación lógica incluye cantidades específicas de proceso en recursos de CPU virtual (vCPU) y memoria. Actualmente, solo el plan de hospedaje **Workflow Standard** (Flujo de trabajo estándar) está disponible para el tipo de recurso **Logic App (Standard)** (Aplicación lógica [estándar]) y ofrece los siguientes planes:

| Plan de tarifa | CPU virtual (vCPU) | Memoria (GB) |
|--------------|--------------------|-------------|
| **WS1** | 1 | 3,5 |
| **WS2** | 2 | 7 |
| **WS3** | 4 | 14 |
||||

> [!IMPORTANT]
>
> El ejemplo siguiente es solo ilustrativo y proporciona estimaciones de ejemplo para mostrar cómo funciona en general un plan de tarifa. 
> Para ver los precios específicos de vCPU y memoria en función de las regiones concretas en las que Azure Logic Apps está disponible, revise el [plan estándar para una región seleccionada en la página de precios de Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).
>
> Supongamos que, en una región de ejemplo, los siguientes recursos tienen estas tarifas por hora:
>
> | Resource | Tarifa por hora (región de ejemplo) |
> |----------|-----------------------------|
> | **vCPU** | 0,192 USD por vCPU |
> | **Memoria** | 0,0137 USD por GB |
> |||
>
> El cálculo siguiente proporciona una estimación de la tarifa mensual:
>
> <*monthly-rate*> = 730 horas (al mes) * [(<*number-vCPU*> * <*hourly-rate-vCPU*>) + (<*number-GB-memory*> * <*hourly-rate-GB-memory*>)]
>
> En función de la información anterior, en esta tabla se muestran las tarifas mensuales estimadas para cada plan de tarifa y los recursos incluidos en él:
>
> | Plan de tarifa | CPU virtual (vCPU) | Memoria (GB) | Tarifa mensual (región de ejemplo) |
> |--------------|--------------------|-------------|------------------------------|
> | **WS1** | 1 | 3,5 | 175,16 USD |
> | **WS2** | 2 | 7 | 350,33 UDS |
> | **WS3** | 4 | 14 | 700,65 USD |
> |||||

<a name="standard-operations"></a>

### <a name="trigger-and-action-operations-in-the-standard-model"></a>Operaciones de desencadenador y acción en el modelo estándar

Excepto por las operaciones integradas gratuitas ilimitadas que puede ejecutar un flujo de trabajo, el modelo estándar mide y factura una operación en función de *cada llamada*, con independencia de si el flujo de trabajo global se ejecuta correctamente, finaliza o incluso se crea una instancia de él. Normalmente, una operación realiza una única ejecución [a menos que tenga habilitados los reintentos](#other-operation-behavior). A su vez, una ejecución normalmente realiza una sola llamada [a menos que la operación admita y permita la fragmentación o paginación para obtener grandes cantidades de datos](logic-apps-handle-large-messages.md). Si está habilitada la fragmentación o paginación, es posible que una ejecución de la operación tenga que realizar varias llamadas. El modelo estándar mide y factura una operación *por llamada, no por ejecución*.

Por ejemplo, suponga que un flujo de trabajo comienza con un desencadenador de sondeo que obtiene registros mediante la realización regular de llamadas salientes a un punto de conexión. La llamada saliente se mide y se factura, independientemente de si el desencadenador se activa o se omite. El estado del desencadenador controla si la instancia de flujo de trabajo se crea y ejecuta. Ahora, supongamos que la operación también admite, y tiene habilitada, la fragmentación o la paginación. Si la operación tiene que realizar 10 llamadas para terminar de obtener todos los datos, la operación se mide y se factura *por llamada*.

En la tabla siguiente se resume cómo el modelo estándar controla la medición y la facturación de los tipos de operación cuando se usa con una aplicación lógica y un flujo de trabajo en Azure Logic Apps de inquilino único:

| Tipo de operación | Descripción | Medición y facturación |
|----------------|-------------|----------------------|
| [*Integrada*](../connectors/built-in.md) | Estas operaciones se ejecutan de forma directa y nativa con el entorno de ejecución de Azure Logic Apps. En el diseñador, puede encontrar estas operaciones en la etiqueta **Integrado**. <p>Por ejemplo, el desencadenador HTTP y el desencadenador de solicitud son desencadenadores integrados. La acción HTTP y la acción de respuesta son acciones integradas. Otras operaciones integradas incluyen acciones de control de flujo de trabajo, como bucles y condiciones, operaciones de datos, operaciones por lotes, etc. | El modelo estándar incluye operaciones integradas gratuitas ilimitadas. <p><p>**Nota**: Algunas operaciones de conectores administrados *también* están disponibles como operaciones integradas. Aunque las operaciones integradas son gratuitas, el modelo estándar todavía mide y factura las operaciones de conectores administrados con los [mismos precios del conector *estándar* o *empresarial* que el modelo de consumo](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [*Conector administrado*](../connectors/managed.md) | Estas operaciones se ejecutan de forma independiente en Azure. En el diseñador, puede encontrar estas operaciones en la etiqueta **Azure** combinada. | El modelo estándar mide y factura las operaciones de conectores administrados con los [mismos precios de conector *estándar* o *empresarial* que el modelo de consumo](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Nota**: Las operaciones de conector empresarial en versión preliminar siguen los precios del conector [estándar *del plan de consumo*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [*Conector personalizado*](../connectors/apis-list.md#custom-apis-and-connectors) | Actualmente, solo puede crear y usar [operaciones de conector integradas personalizadas](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) en flujos de trabajo de aplicaciones lógicas basadas en un único inquilino. | El modelo estándar incluye operaciones integradas gratuitas ilimitadas. Para ver los límites de rendimiento y tiempo de espera, consulte [Límites de los conectores personalizados en Azure Logic Apps](logic-apps-limits-and-config.md#custom-connector-limits). |
||||

Para más información sobre cómo funciona el modelo estándar con operaciones que se ejecutan dentro de otras operaciones, como bucles, procesamiento de varios elementos (por ejemplo, matrices) y directivas de reintento, consulte [Comportamiento de otras operaciones](#other-operation-behavior).

<a name="ise-pricing"></a>

## <a name="integration-service-environment-ise"></a>Entorno del servicio de integración (ISE)

Al crear una aplicación lógica mediante el tipo de recurso **Logic App (Consumption)** (Aplicación lógica [consumo]), si la implementa en un [*entorno de servicio de integración* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) dedicado, la aplicación lógica y su flujo de trabajo siguen los precios y la facturación del [plan del entorno de servicio de integración](https://azure.microsoft.com/pricing/details/logic-apps). Este modelo de precio depende del [nivel de ISE o de la *SKU*](connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) y se diferencia del plan de consumo en que se le factura por la capacidad reservada y los recursos dedicados, tanto si los usa como si no.

En la tabla siguiente se resume cómo el modelo ISE controla la medición y la facturación de la capacidad y otros recursos dedicados en función de su nivel de ISE o SKU:

| SKU de ISE | Medición y facturación |
|---------|----------------------|
| **Premium** | La unidad base tiene una [capacidad fija](logic-apps-limits-and-config.md#integration-service-environment-ise) y se [factura según una tarifa por hora para el SKU prémium](https://azure.microsoft.com/pricing/details/logic-apps). Si necesita más rendimiento, puede [agregar más unidades de escalado](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) durante o después de crear el ISE. Cada unidad de escalado se factura según una [tarifa por hora, que es aproximadamente la mitad de la tarifa de unidad base](https://azure.microsoft.com/pricing/details/logic-apps). <p><p>Para información sobre los límites y la capacidad, consulte [Límites de ISE en Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Developer** | La unidad base tiene una [capacidad fija](logic-apps-limits-and-config.md#integration-service-environment-ise) y se [factura según una tarifa por hora para el SKU de desarrollador](https://azure.microsoft.com/pricing/details/logic-apps). Sin embargo, este SKU no tiene ningún Acuerdo de Nivel de Servicio (SLA), funcionalidad de escalado vertical ni redundancia durante el reciclaje, lo que significa que puede experimentar retrasos o tiempo de inactividad. Las actualizaciones de back-end pueden interrumpir el servicio de forma intermitente. <p><p>**Importante**: Use este SKU solo para la exploración, los experimentos, el desarrollo y las pruebas, no para pruebas de rendimiento ni en producción. <p><p>Para información sobre los límites y la capacidad, consulte [Límites de ISE en Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

En la tabla siguiente se resume cómo el modelo ISE controla los siguientes componentes cuando se usa con una aplicación lógica y un flujo de trabajo en un ISE:

| Componente | Descripción |
|-----------|-------------|
| Operaciones de desencadenador y acción | El modelo ISE incluye operaciones de conector administrado y personalizado que puede ejecutar el flujo de trabajo, pero están sujetas a los [límites del ISE de Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise) y a los [límites del conector personalizado de Azure Logic Apps](logic-apps-limits-and-config.md#custom-connector-limits). Para más información, consulte [Operaciones de desencadenador y acción en el modelo ISE](#integration-service-environment-operations). |
| Operaciones de almacenamiento | El modelo ISE incluye el consumo de almacenamiento gratuito, como la retención de datos. Para más información, consulte [Operaciones de almacenamiento](#storage-operations). |
| Cuentas de integración | El modelo ISE incluye un único plan de cuenta de integración gratuito, en función de la SKU de ISE seleccionada. Por un [costo adicional](https://azure.microsoft.com/pricing/details/logic-apps/), puede crear más cuentas de integración para su ISE hasta el [límite total del ISE](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Para más información, consulte [Cuentas de integración](#integration-accounts). |
|||

<a name="integration-service-environment-operations"></a>

### <a name="trigger-and-action-operations-in-the-ise-model"></a>Operaciones de desencadenador y acción en el modelo ISE

En la tabla siguiente se resume cómo el modelo ISE controla los siguientes tipos de operación cuando se usa con una aplicación lógica y un flujo de trabajo en un ISE:

| Tipo de operación | Descripción | Medición y facturación |
|----------------|-------------|----------------------|
| [*Integrada*](../connectors/built-in.md) | Estas operaciones se ejecutan de forma directa y nativa con el entorno de ejecución de Azure Logic Apps y en el mismo ISE que el flujo de trabajo de la aplicación lógica. En el diseñador, puede encontrar estas operaciones en la etiqueta **Integrado**, pero cada operación también muestra la etiqueta **PRINCIPAL**. <p>Por ejemplo, el desencadenador HTTP y el desencadenador de solicitud son desencadenadores integrados. La acción HTTP y la acción de respuesta son acciones integradas. Otras operaciones integradas incluyen acciones de control de flujo de trabajo, como bucles y condiciones, operaciones de datos, operaciones por lotes, etc. | El modelo ISE incluye estas operaciones *de forma gratuita*, pero están sujetas a los [límites del ISE de Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| [*Conector administrado*](../connectors/managed.md) | Tanto en el modelo *estándar* como *empresarial*, las operaciones de conectores administrados se ejecutan en el ISE o en Azure multiinquilino, según si la operación o el conector se muestra en la etiqueta **ISE**. <p><p>Etiqueta - **ISE**: estas operaciones se ejecutan en el mismo ISE que la aplicación lógica y funcionan sin necesidad de la [puerta de enlace de datos local](#data-gateway). <p><p>-Sin etiqueta **ISE**: estas operaciones se ejecutan en Azure multiinquilino. | El modelo ISE incluye tanto operaciones con etiqueta **ISE** como sin etiqueta **ISE** *de forma gratuita*, pero están sujetas a los [límites de ISE de Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| [*Conector personalizado*](../connectors/apis-list.md#custom-apis-and-connectors) | En el diseñador, puede encontrar estas operaciones en la etiqueta **Personalizado**. | El modelo ISE incluye estas operaciones *de forma gratuita*, pero están sujetas a los [límites del conector personalizado de Azure Logic Apps](logic-apps-limits-and-config.md#custom-connector-limits). |
||||

Para más información sobre cómo funciona el modelo ISE con operaciones que se ejecutan dentro de otras operaciones, como bucles, procesamiento de varios elementos (por ejemplo, matrices) y directivas de reintento, consulte [Comportamiento de otras operaciones](#other-operation-behavior).

<a name="other-operation-behavior"></a>

## <a name="other-operation-behavior"></a>Comportamiento de otras operaciones

En la tabla siguiente se resume cómo los modelos de consumo, estándar e ISE controlan las operaciones que se ejecutan dentro de otras operaciones, como bucles, procesamiento de varios elementos (por ejemplo, matrices) y directivas de reintento:

| Operación | Descripción | Consumo | Estándar | ISE |
|-----------|-------------|-------------|----------|-----|
| [Acciones de bucle](logic-apps-control-flow-loops.md) | Una acción de bucle, como el bucle **For each** o **Until**, puede incluir otras acciones que se ejecutan durante cada ciclo del bucle. | Excepto por el número inicial de operaciones integradas incluidas, la acción de bucle y cada acción en el bucle se miden cada vez que se ejecuta el ciclo del bucle. Si una acción procesa elementos de una colección, como una lista o una matriz, el número de elementos también se usa en el cálculo de medición. <p><p>Por ejemplo, supongamos que tiene un bucle **For each** con acciones que procesan una lista. El servicio multiplica el número de elementos de la lista por el número de acciones del bucle, y agrega la acción que inicia el bucle. Por lo tanto, el cálculo de una lista de 10 elementos es (10x1)+1, lo que da como resultado 11 ejecuciones de acción. <p><p>Los precios dependen de si los tipos de operación son integradas, estándar o empresariales. | Excepto por las operaciones integradas incluidas, es igual que el modelo de consumo. | No se mide ni se factura. |
| [Directivas de reintentos](logic-apps-exception-handling.md#retry-policies) | En operaciones admitidas, puede implementar el control básico de excepciones y errores mediante la configuración de una [directiva de reintentos](logic-apps-exception-handling.md#retry-policies). | Excepto por el número inicial de operaciones integradas, se mide la ejecución original más cada ejecución reintentada. Por ejemplo, una acción que se ejecuta con 5 reintentos se mide y se factura como 6 ejecuciones. <p><p>Los precios dependen de si los tipos de operación son integradas, estándar o empresariales. | Excepto por las operaciones integradas incluidas, es igual que el modelo de consumo. | No se mide ni se factura. |
||||||

<a name="storage-operations"></a>

## <a name="storage-operations"></a>Operaciones de almacenamiento

Azure Logic Apps usa [Azure Storage](../storage/index.yml) para las transacciones de almacenamiento necesarias, como el uso de colas para programar operaciones de desencadenador o el uso de tablas y blobs para almacenar estados de flujo de trabajo. En función de las operaciones del flujo de trabajo, los costos de almacenamiento varían porque diferentes desencadenadores, acciones y cargas tienen como resultado diferentes operaciones y necesidades de almacenamiento. El servicio también guarda y almacena entradas y salidas del historial de ejecución del flujo de trabajo, en función del [límite de retención del historial de ejecución](logic-apps-limits-and-config.md#run-duration-retention-limits) del recurso de aplicación lógica. Este límite de retención se puede administrar en el nivel de recurso de aplicación lógica, pero no en el nivel de flujo de trabajo.

En la tabla siguiente se resume cómo los modelos de consumo, estándar e ISE controlan la medición y la facturación de las operaciones de almacenamiento:

| Modelo | Descripción | Medición y facturación |
|-------|-------------|----------------------|
| Consumo (multiinquilino) | Los recursos y el uso de almacenamiento están asociados al recurso de aplicación lógica. | La medición y la facturación *solo se aplican al consumo de almacenamiento relacionado con la retención de datos* y siguen los [precios de retención de datos del plan de consumo](https://azure.microsoft.com/pricing/details/logic-apps). |
| Estándar (inquilino único) | Puede usar su propia [cuenta de almacenamiento](../azure-functions/storage-considerations.md#storage-account-requirements) de Azure, lo que le proporciona más control y flexibilidad sobre los datos del flujo de trabajo. |  La medición y la facturación siguen el [modelo de precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Los costos de almacenamiento se enumeran por separado en la factura de Azure. <p><p>**Sugerencia**: Para comprender mejor el número de operaciones de almacenamiento que podría ejecutar un flujo de trabajo y su costo, pruebe la [calculadora de almacenamiento de Logic Apps](https://logicapps.azure.com/calculator). Seleccione un flujo de trabajo de ejemplo o use una definición de flujo de trabajo existente. El primer cálculo estima el número de operaciones de almacenamiento en el flujo de trabajo. Luego, puede usar estos números para calcular los posibles costos mediante la [calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/). Para más información, revise [Estimación de las necesidades de almacenamiento y los costos de los flujos de trabajo de Azure Logic Apps de inquilino único](estimate-storage-costs.md). |
| Entorno del servicio de integración (ISE) | Los recursos y el uso de almacenamiento están asociados al recurso de aplicación lógica. | No se mide ni se factura. |
||||

Para más información, revise la siguiente documentación:

* [Visualización de las métricas de las ejecuciones y el uso de almacenamiento](plan-manage-costs.md#monitor-billing-metrics)
* [Límites de Azure Logic Apps](logic-apps-limits-and-config.md)

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Puerta de enlace de datos local

Una [puerta de enlace de datos local](../logic-apps/logic-apps-gateway-install.md) es un recurso de Azure independiente que se crea para que los flujos de trabajo de las aplicaciones lógicas puedan acceder a los datos locales mediante conectores específicos compatibles con la puerta de enlace. El recurso de puerta de enlace en sí no genera cargos, pero sí lo hacen las operaciones que se ejecutan a través de la puerta de enlace, según el modelo de precios y facturación que usa la aplicación lógica.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Cuentas de integración

Una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) es un recurso de Azure independiente que se crea como contenedor para definir y almacenar artefactos de empresa a empresa (B2B), como socios comerciales, acuerdos, esquemas, mapas, etc. Después de crear esta cuenta y definir estos artefactos, vincule la cuenta a la aplicación lógica para que pueda usar estos artefactos y varias operaciones B2B en los flujos de trabajo para explorar, compilar y probar soluciones de integración que usan funcionalidades de [intercambio electrónico de datos](logic-apps-enterprise-integration-b2b.md) y [procesamiento de XML](logic-apps-enterprise-integration-xml.md).

En la tabla siguiente se resume cómo los modelos de consumo, estándar e ISE controlan la medición y la facturación de las cuentas de integración:

| Modelo | Medición y facturación |
|-------|----------------------|
| Consumo (multiinquilino) | Para la medición y la facturación se usan los [precios de la cuenta de integración](https://azure.microsoft.com/pricing/details/logic-apps/), en función del nivel de cuenta que se use. |
| Estándar (inquilino único) | Para la medición y la facturación se usan los [precios de la cuenta de integración](https://azure.microsoft.com/pricing/details/logic-apps/), en función del nivel de cuenta que se use. |
| ISE | Este modelo incluye una sola cuenta de integración, basada en la SKU del ISE. Por un [costo adicional](https://azure.microsoft.com/pricing/details/logic-apps/), puede crear más cuentas de integración para su ISE hasta el [límite total del ISE](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). |
|||

Para más información, revise la siguiente documentación:

* [Creación y administración de cuentas de integración](logic-apps-enterprise-integration-create-integration-account.md)
* [Límites de la cuenta de integración en Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="other-items-not-metered-or-billed"></a>Otros elementos que no se miden ni se facturan

En todos los modelos de precios, los siguientes elementos no se miden ni se facturan:

* Acciones que no se ejecutaron porque el flujo de trabajo se detuvo antes de finalizar.
* Aplicaciones lógicas o flujos de trabajo deshabilitados, ya que, al estar inactivos, no pueden crear más instancias.

## <a name="next-steps"></a>Pasos siguientes

* [Planeamiento y administración de los costos de Azure Logic Apps](plan-manage-costs.md)
