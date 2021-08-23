---
title: 'Información general: Acceso a redes virtuales de Azure'
description: Mas información sobre cómo acceder a las redes virtuales de Azure desde Azure Logic Apps mediante un entorno del servicio de integración (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 05/16/2021
ms.openlocfilehash: ce3526f824d34e69cf33d41ba591a8ae6a634ea5
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111985315"
---
# <a name="access-to-azure-virtual-networks-from-azure-logic-apps-using-an-integration-service-environment-ise"></a>Acceso a redes virtuales de Azure desde Azure Logic Apps mediante un entorno del servicio de integración (ISE)

En ocasiones, los flujos de trabajo de aplicación lógica necesitan acceder a recursos protegidos, como máquinas virtuales (VM) y otros sistemas o servicios que están dentro de una red virtual de Azure o conectados a ella. Para acceder directamente a estos recursos desde flujos de trabajo que normalmente se ejecutan en Azure Logic Apps multiinquilino, puede crear y ejecutar las aplicaciones lógicas en un *entorno del servicio de integración* (ISE) en su lugar. Un ISE es realmente una instancia de Azure Logic Apps que se ejecuta por separado en recursos dedicados, además del entorno global de Azure multiinquilino.

Por ejemplo, algunas redes virtuales de Azure usan puntos de conexión privados ([Azure Private Link](../private-link/private-link-overview.md)) para proporcionar acceso a los servicios de PaaS de Azure, como Azure Storage, Azure Cosmos DB o Azure SQL Database, servicios de asociados o servicios de clientes que se hospedan en Azure. Si los flujos de trabajo de aplicación lógica requieren acceso a redes virtuales que usan puntos de conexión privados, tiene estas opciones:

* Si desea desarrollar flujos de trabajo mediante el tipo de recurso **Aplicación lógica (consumo)** y los flujos de trabajo necesitan usar puntos de conexión privados, *debe* crear, implementar y ejecutar las aplicaciones lógicas en un ISE. Para obtener más información, revise [Conexión a redes virtuales de Azure desde Azure Logic Apps mediante un entorno del servicio de integración (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Si desea desarrollar flujos de trabajo mediante el tipo de recurso **Aplicación lógica (estándar)** y los flujos de trabajo necesitan usar puntos de conexión privados, no necesita un ISE. En su lugar, los flujos de trabajo pueden comunicarse de forma privada y segura con redes virtuales mediante el uso de puntos de conexión privados para el tráfico entrante y la integración de Virtual Network para el tráfico saliente. Para obtener más información, revise [Protección del tráfico entre redes virtuales y Azure Logic Apps de inquilino único mediante puntos de conexión privados](secure-single-tenant-workflow-virtual-network-private-endpoint.md).

Para obtener más información, revise las [diferencias entre Azure Logic Apps de inquilino único y los entornos del servicio de integración](logic-apps-overview.md#resource-environment-differences).

## <a name="how-an-ise-works-with-a-virtual-network"></a>Funcionamiento de un ISE con una red virtual

Cuando se crea un ISE, se selecciona la red virtual de Azure donde quiere que Azure *inserte* o implemente el ISE. Al crear aplicaciones lógicas y cuentas de integración que necesitan acceso a esta red virtual, puede seleccionar el ISE como ubicación del host de esas aplicaciones lógicas y cuentas de integración. Dentro del ISE, las aplicaciones lógicas se ejecutan en recursos dedicados separadas de otras del entorno de Azure Logic Apps multiinquilino. Los datos de un ISE permanecen en la [misma región en la que crea e implementa ese ISE](https://azure.microsoft.com/global-infrastructure/data-residency/).

![Selección del entorno de servicio de integración](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Para obtener más control sobre las claves de cifrado utilizadas por Azure Storage, puede configurar, usar y administrar su propia clave mediante [Azure Key Vault](../key-vault/general/overview.md). Esta funcionalidad también se conoce como "Bring Your Own Key" (BYOK) y la clave se denomina "clave administrada por el cliente". Para obtener más información, revise [Configure claves administradas por el cliente para cifrar los datos en reposo para los entornos del servicio de integración (ISE) en Azure Logic Apps](../logic-apps/customer-managed-keys-integration-service-environment.md).

En esta información general se proporciona más información sobre [por qué querría usar un ISE](#benefits), las [diferencias entre el servicio Logic Apps dedicado y multiinquilino](#difference) y cómo se puede acceder directamente a los recursos que están dentro de la red virtual de Azure o conectados a ella.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>¿Por qué usar un ISE?

La ejecución de aplicaciones lógicas en una instancia dedicada e individual ayuda a reducir el impacto que podrían tener otros inquilinos de Azure en el rendimiento de la aplicación, también conocido como el [efecto "vecinos ruidosos"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Un ISE también proporciona estas ventajas:

* Acceso directo a los recursos incluidos en la red virtual o conectados a ella

  Las aplicaciones lógicas que se crean y ejecutan en un ISE pueden usar [conectores específicamente diseñados que se ejecutan en el ISE](../connectors/managed.md#ise-connectors). Si existe un conector ISE para un origen de datos o un sistema local, se puede conectar directamente sin tener que usar la [puerta de enlace de datos local](../logic-apps/logic-apps-gateway-connection.md). Para obtener más información, vea [Diferencias entre dedicado y multiinquilino](#difference) y [Acceso a sistemas locales](#on-premises) más adelante en este tema.

* Acceso continuado a los recursos que están fuera de la red virtual o no conectados a ella

  Las aplicaciones lógicas que se crean y ejecutan en un ISE pueden seguir usando conectores que se ejecutan en el servicio Logic Apps multiinquilino cuando no hay un conector específico de ISE disponible. Para obtener más información, vea [Diferencias entre dedicado y multiinquilino](#difference).

* Direcciones IP estáticas propias, que son independientes de las direcciones IP estáticas que comparten las aplicaciones lógicas en el servicio multiinquilino. Puede configurar una dirección IP de salida única, pública, estática y predecible para comunicarse con los sistemas de destino. De ese modo, no es necesario configurar aperturas adicionales del firewall en los sistemas de destino para cada ISE.

* Se aumentan los límites de duración de ejecución, retención de almacenamiento, rendimiento, solicitud HTTP y tiempos de espera de respuesta, tamaños de mensaje y solicitudes de conector personalizado. Para más información, consulte el artículo de [límites y configuración para Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Diferencias entre dedicado y multiinquilino

Cuando se crean y ejecutan aplicaciones lógicas en un ISE, se proporcionan las mismas experiencias de usuario y funcionalidades similares que el servicio multiinquilino de Logic Apps. Puede usar los mismos desencadenadores, acciones y conectores administrados integrados que están disponibles en el servicio multiinquilino de Logic Apps. Algunos conectores administrados ofrecen versiones de ISE adicionales. La diferencia entre los conectores de ISE y los que no son de ISE radica en la ubicación en la que se ejecutan y las etiquetas que tienen en el Diseñador de aplicación lógica cuando se trabaja en un ISE.

![Conectores con y sin etiquetas en un ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Los desencadenadores y acciones integrados, como HTTP, muestran la etiqueta **CORE** y se ejecutan en el mismo ISE que la aplicación lógica.

* Los conectores administrados en los que se muestra la etiqueta **ISE** están diseñados específicamente para los ISE y *siempre se ejecutan en el mismo ISE que la aplicación lógica*. Por ejemplo, estos son algunos [conectores que ofrecen versiones de ISE](../connectors/managed.md#ise-connectors):<p>

  * Azure Blob Storage, File Storage y Table Storage
  * Azure Service Bus, Azure Queues y Azure Event Hubs
  * Azure Automation, Azure Key Vault, Azure Event Grid y Azure Monitor Logs
  * FTP, SFTP-SSH, Sistema de archivos y SMTP
  * SAP, IBM MQ, IBM DB2 e IBM 3270
  * SQL Server, Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 y EDIFACT

  Con algunas excepciones, si hay un conector ISE disponible para un origen de datos o un sistema local, se puede conectar directamente sin usar la [puerta de enlace de datos local](../logic-apps/logic-apps-gateway-connection.md). Para obtener más información, vea [Acceso a sistemas locales](#on-premises) más adelante en este tema.

* Los conectores administrados en los que no se muestra la etiqueta **ISE** continúan funcionando para las aplicaciones lógicas dentro de un ISE. Estos conectores *siempre se ejecutan en el servicio Logic Apps multiinquilino*, no en el ISE.

* Los conectores personalizados que cree *fuera de un ISE*, con independencia de que requieran la [puerta de enlace de datos local](../logic-apps/logic-apps-gateway-connection.md), seguirán funcionando para las aplicaciones lógicas dentro de un ISE. Pero los conectores personalizados que cree *dentro de un ISE* no funcionarán con la puerta de enlace de datos local. Para obtener más información, vea [Acceso a sistemas locales](#on-premises).

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Acceso a los sistemas locales

Las aplicaciones lógicas que se ejecutan dentro de un ISE pueden acceder directamente a los sistemas y orígenes de datos locales que se encuentran dentro de una red virtual de Azure o están conectados a ella mediante estos elementos:<p>

* Desencadenador o acción HTTP, que muestra la etiqueta **CORE**

* Conector **ISE**, si está disponible, para un origen de datos o un sistema local

  Si hay un conector ISE disponible, puede acceder directamente al sistema o al origen de datos sin la [puerta de enlace de datos local](../logic-apps/logic-apps-gateway-connection.md). Pero si tiene que acceder a SQL Server desde un ISE y usar la autenticación de Windows, debe usar la versión del conector que no es para ISE y la puerta de enlace de datos local. La versión para ISE del conector no admite la autenticación de Windows. Para obtener más información, vea [Conectores ISE](../connectors/managed.md#ise-connectors) y [Conexión desde un entorno del servicio de integración](../connectors/managed.md#integration-account-connectors).

* Un conector personalizado.

  * Los conectores personalizados que cree *fuera de un ISE*, con independencia de que requieran la [puerta de enlace de datos local](../logic-apps/logic-apps-gateway-connection.md), seguirán funcionando para las aplicaciones lógicas dentro de un ISE.

  * Los conectores personalizados que cree *dentro de un ISE* no funcionan con la puerta de enlace de datos local. Pero estos conectores pueden acceder directamente a orígenes de datos y sistemas locales que están dentro de la red virtual en la que se hospeda el ISE o conectados a ella. Por tanto, las aplicaciones lógicas que se encuentran dentro de un ISE normalmente no necesitan la puerta de enlace de datos al acceder a esos recursos.

Para acceder a los sistemas y orígenes de datos locales que no tienen conectores ISE, que están fuera de la red virtual o que no están conectados a ella, seguirá teniendo que usar la puerta de enlace de datos local. Las aplicaciones lógicas dentro un ISE pueden seguir usando conectores sin la etiqueta **CORE** o **ISE**. Esos conectores solo se ejecutan en el servicio Logic Apps multiinquilino, en lugar de hacerlo en el ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>SKU de ISE

Al crear el ISE, puede seleccionar la SKU de desarrollador o SKU Premium. La opción de la SKU solo está disponible durante la creación del ISE y no se puede cambiar más adelante. Las diferencias entre estas SKU son las siguientes:

* **Developer**

  Proporciona un ISE de menor costo que puede usar para exploración, experimentos, desarrollo y pruebas, pero no para producción ni para pruebas de rendimiento. La SKU de desarrollador incluye desencadenadores y acciones integrados, conectores estándar, conectores empresariales y una sola cuenta de integración de [nivel Gratis](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) por un [precio mensual fijo](https://azure.microsoft.com/pricing/details/logic-apps). 

  > [!IMPORTANT]
  > Esta SKU no tiene ningún Acuerdo de Nivel de Servicio (SLA), funcionalidad de escalado vertical o redundancia durante el reciclaje, lo que significa que puede experimentar retrasos o tiempos de inactividad. Las actualizaciones de back-end pueden interrumpir el servicio de forma intermitente.

  Para información sobre los límites y la capacidad, consulte [Límites de ISE en Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Para saber cómo funciona la facturación para los ISE, consulte el [modelo de precios de Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

* **Premium**

  Proporciona un ISE que puede usar para producción y para pruebas de rendimiento. La SKU Premium incluye soporte de SLA, desencadenadores y acciones integrados, conectores estándar, conectores empresariales, una sola cuenta de integración de [nivel Estándar](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), funcionalidad de escalado vertical y redundancia durante el reciclaje para un [precio fijo al mes](https://azure.microsoft.com/pricing/details/logic-apps).

  Para información sobre los límites y la capacidad, consulte [Límites de ISE en Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Para saber cómo funciona la facturación para los ISE, consulte el [modelo de precios de Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Acceso al punto de conexión del ISE

Al crear el ISE, puede optar por usar puntos de conexión de acceso internos o externos. La selección determina si los desencadenadores de solicitudes o de webhooks de las aplicaciones lógicas del ISE pueden recibir llamadas desde fuera de la red virtual o no. Estos puntos de conexión también afectan a la manera en que puede acceder a las entradas y salidas en el historial de ejecución de las aplicaciones lógicas.

> [!IMPORTANT]
> Puede seleccionar el punto de conexión de acceso solo durante la creación del ISE y no puede cambiar esta opción más adelante.

* **Internas**: Los puntos de conexión privados permiten las llamadas a aplicaciones lógicas del ISE, donde puede ver las entradas y salidas del historial de ejecuciones de las aplicaciones lógicas, así como acceder a dichas entradas y salidas, *solo desde dentro de la red virtual*.

  > [!IMPORTANT]
  > Si tiene que usar estos desencadenadores basados en webhook y el servicio está fuera de la red virtual y las redes virtuales emparejadas, use puntos de conexión externos, *no* puntos de conexión internos, al crear el ISE:
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP (versión multiinquilino)
  > 
  > Además, asegúrese de tener conectividad de red entre los puntos de conexión privados y el equipo desde el que quiere acceder al historial de ejecución. De lo contrario, cuando intente ver el historial de ejecución de la aplicación lógica, recibirá un error que indica "Error inesperado. No se pudo capturar".
  >
  > ![Error de acción de Azure Storage debido a la imposibilidad de enviar tráfico a través del firewall](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Por ejemplo, el equipo cliente puede existir dentro de la red virtual del ISE o en una red virtual que esté conectada a la red virtual del ISE, mediante emparejamiento o una red privada virtual. 

* **Externas**: Los puntos de conexión públicos permiten las llamadas a aplicaciones lógicas del ISE, donde puede ver las entradas y salidas del historial de ejecuciones de las aplicaciones lógicas, así como acceder a dichas entradas y salidas, *desde fuera de la red virtual*. Si usa grupos de seguridad de red (NSG), asegúrese de que están configurados con reglas de entrada para permitir el acceso a las entradas y salidas del historial de ejecución. Para más información, consulte [Habilitar el acceso para el ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

Para determinar si el ISE usa un punto de conexión de acceso interno o externo, en el menú del ISE, en **Configuración**, seleccione **Propiedades** y busque la propiedad **Punto de conexión de acceso**:

![Búsqueda del punto de conexión de acceso del ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modelo de precios

Las aplicaciones lógicas, los desencadenadores y las acciones integrados, y los conectores que se ejecutan en el ISE usan un plan de tarifa fijo diferente al plan de tarifa basado en el consumo. Para obtener más información, consulte [Modelo de precios de Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para ver las tarifas de precios, consulte los [precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Cuentas de integración con ISE

Puede usar cuentas de integración con aplicaciones lógicas dentro de un entorno de servicio de integración (ISE). Sin embargo, esas cuentas de integración deben usar el *mismo ISE* que las aplicaciones lógicas vinculadas. Las aplicaciones lógicas de una instancia de ISE solo pueden hacer referencia a aquellas cuentas de integración que se encuentran en la misma instancia de ISE. Cuando se crea una cuenta de integración, puede seleccionar la instancia de ISE como la ubicación de la cuenta de integración. Para saber cómo funcionan los precios y la facturación de las cuentas de integración con un ISE, consulte [Modelo de precios de Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para ver las tarifas de precios, consulte los [precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). Para información sobre los límites, consulte los [límites de la cuenta de integración](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="next-steps"></a>Pasos siguientes

* [Conexión a redes virtuales de Azure desde Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).
* Consulte más información sobre [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).
* Obtenga información sobre la [integración de redes virtuales para los servicios de Azure](../virtual-network/virtual-network-for-azure-services.md).
