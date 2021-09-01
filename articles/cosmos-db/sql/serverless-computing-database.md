---
title: Informática de base de datos sin servidor con Azure Cosmos DB y Azure Functions
description: Obtenga información sobre cómo Azure Cosmos DB y Azure Functions se pueden usar en conjunto para crear aplicaciones informáticas sin servidor basadas en eventos.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 657ff6a82115cc5f3dbfa5c10fbab9e080d95cdc
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114294"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Informática de base de datos sin servidor con Azure Cosmos DB y Azure Functions
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

La informática sin servidor se trata de la capacidad de centrarse en partes de lógica individuales repetibles y sin estado. Estas partes no requieren administración de la infraestructura y solo usan recursos durante los segundos o milisegundos durante los cuales se ejecutan. La base del movimiento de la informática sin servidor son las funciones, las que están disponibles en el ecosistema de Azure mediante [Azure Functions](https://azure.microsoft.com/services/functions). Para información acerca de otros entornos de ejecución sin servidor en Azure, consulte la página [Informática sin servidor de Azure](https://azure.microsoft.com/solutions/serverless/). 

Con la integración nativa entre [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) y Azure Functions, puede crear desencadenadores de base de datos, enlaces de entrada y enlaces de salida directamente desde la cuenta de Azure Cosmos DB. Con Azure Functions y Azure Cosmos DB, puede crear e implementar aplicaciones sin servidor basadas en eventos con acceso de baja latencia a los datos enriquecidos para una base de usuarios global.

## <a name="overview"></a>Información general

Azure Cosmos DB y Azure Functions permite integrar las aplicaciones sin servidor y bases de datos de las maneras siguientes:

* Cree un **desencadenador de Azure Functions para Cosmos DB** basado en eventos. Este desencadenador se basa en secuencias de [fuente de cambios](../change-feed.md) para supervisar los cambios en el contenedor de Azure Cosmos. Cuando se hace algún cambio en un contenedor, la secuencia de fuente de cambios se envía al desencadenador, que invoca la instancia de Azure Function.
* Como alternativa, enlace una instancia de Azure Functions a un contenedor de Azure Cosmos mediante un **enlace de entrada**. Los enlaces de entrada leen los datos de un contenedor cuando se ejecuta una función.
* Enlace una función a un contenedor de Azure Cosmos mediante un **enlace de salida**. Los enlaces de salida escriben datos en un contenedor cuando se completa una función.

> [!NOTE]
> Actualmente, el desencadenador de Azure Functions, los enlaces de entrada y los enlaces de salida para Cosmos DB solo son compatibles para usarlos con la API de SQL. En todas las demás API de Azure Cosmos DB, debe acceder a la base de datos desde la función utilizando el cliente estático de la API.


En el diagrama siguiente se muestran cada una de estas tres integraciones: 

:::image type="content" source="./media/serverless-computing-database/cosmos-db-azure-functions-integration.png" alt-text="Integración de Azure Cosmos DB y Azure Functions" border="false":::

El desencadenador de Azure Functions, el enlace de entrada y el enlace de salida para Azure Cosmos DB se pueden usar en las combinaciones siguientes:

* Un desencadenador de Azure Functions para Cosmos DB se puede usar con un enlace de salida a otro contenedor de Azure Cosmos. Una vez que una función realiza una acción en un elemento de la fuente de cambios, puede escribirlo en otro contenedor (si lo escribe en el mismo contenedor de origen, se crearía efectivamente un bucle recursivo). O bien puede usar un desencadenador de Azure Functions para Cosmos DB para migrar de manera eficaz todos los elementos modificados de un contenedor a otro, mediante un enlace de salida.
* Los enlaces de entrada y los enlaces de salida de Azure Cosmos DB se pueden usar en la misma instancia de Azure Function. Esto funciona bien cuando desea encontrar ciertos datos con el enlace de entrada, modificarlos en la instancia de Azure Function y, luego, guardarlos en el mismo contenedor o en uno distinto, después de la modificación.
* Un enlace de entrada a un contenedor de Azure Cosmos se puede usar en la misma función que un desencadenador de Azure Functions para Cosmos DB y también se puede usar con un enlace de salida o sin él. Podría usar esta combinación para aplicar información actualizada sobre el cambio de moneda (extraído con un enlace de entrada a un contenedor de intercambio) a la fuente de cambios de los pedidos nuevos en el servicio del carro de la compra. El total actualizado del carro de la compra, con la conversión de moneda actual aplicada, se puede escribir en un tercer contenedor con un enlace de salida.

## <a name="use-cases"></a>Casos de uso

Los casos de uso siguientes muestran algunas formas en las que puede aprovechar al máximo los datos de Azure Cosmos DB, mediante la conexión los datos a instancias de Azure Functions basadas en eventos.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Caso de uso de IoT: desencadenador de Azure Functions y enlace de salida para Cosmos DB

En las implementaciones de IoT, puede invocar una función cuando la luz de comprobación del motor se muestra en un automóvil conectado.

**Implementación**: uso de un desencadenador de Azure Functions y un enlace de salida para Cosmos DB

1. Un **desencadenador de Azure Functions para Cosmos DB** se usa para desencadenar eventos relacionados con alertas de automóviles, como la luz de comprobación del motor que aparece en un automóvil conectado.
2. Cuando aparece la luz de comprobación del motor, los datos del sensor se envían a Azure Cosmos DB.
3. Azure Cosmos DB crea o actualiza los documentos de datos de sensor nuevos y, luego, esos cambios se transmiten al desencadenador de Azure Functions para Cosmos DB.
4. El desencadenador se invoca en cada cambio de datos de la recopilación de datos del sensor, del mismo modo que todos los datos se transmiten a través de la fuente de cambios.
5. Una condición de umbral se usa en la función para enviar los datos del sensor al departamento de garantías.
6. Si la temperatura también supera cierto valor, también se envía una alerta al propietario.
7. El **enlace de salida** de la función actualiza el registro del automóvil en otro contenedor de Azure Cosmos para almacenar información sobre el evento de comprobación del motor.

La imagen siguiente muestra el código escrito en Azure Portal para este desencadenador.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-trigger-portal.png" alt-text="Creación de un desencadenador de Azure Functions para Cosmos DB en Azure Portal":::

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Caso de uso financiero: desencadenador de temporizador y enlace de entrada

En implementaciones financieras, puede invocar una función cuando el saldo de una cuenta bancaria cae por debajo de cierto importe.

**Implementación**: desencadenador de temporizador con un enlace de entrada de Azure Cosmos DB

1. Con un [desencadenador de temporizador](../../azure-functions/functions-bindings-timer.md), puede recuperar la información del saldo de la cuenta bancaria almacenada en un contenedor de Azure Cosmos a intervalos de tiempo con un **enlace de entrada**.
2. Si el saldo está por debajo del umbral de saldo mínimo que establece el usuario, debe hacer seguimiento con una acción desde Azure Function.
3. El enlace de salida puede ser una [integración de SendGrid](../../azure-functions/functions-bindings-sendgrid.md) que envía un correo electrónico desde una cuenta de servicio a las direcciones de correo electrónico identificadas para cada una de las cuentas con saldo bajo.

En las imágenes siguientes se muestra el código de Azure Portal para este escenario.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png" alt-text="Archivo Index.js para un desencadenador de temporizador en un escenario financiero":::

:::image type="content" source="./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png" alt-text="Archivo Run.csx para un desencadenador de temporizador en un escenario financiero":::

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Caso de uso de juegos: desencadenador de Azure Functions y enlace de salida para Cosmos DB 

En el ámbito de los juegos, cuando se crea un usuario nuevo, puede buscar otros usuarios que tal vez lo conozcan con [Azure Cosmos DB Gremlin API](../graph-introduction.md). Luego, puede escribir los resultados en una base de datos SQL o de Azure Cosmos DB  para poder recuperarlos fácilmente.

**Implementación**: uso de un desencadenador de Azure Functions y un enlace de salida para Cosmos DB

1. Con una [base de datos de gráficos](../graph-introduction.md) de Azure para almacenar a todos los usuarios, puede crear una función nueva con un desencadenador de Azure Functions para Cosmos DB. 
2. Cada vez que se inserta un usuario nuevo, se invoca la función y, luego, el resultado se almacena con un **enlace de salida**.
3. La función consulta la base de datos de gráficos para buscar todos los usuarios que están relacionados directamente con el usuario nuevo y devuelve ese conjunto de datos a la función.
4. Estos datos se almacenan en una base de datos de Azure Cosmos DB que cualquier aplicación de front-en puede recuperar fácilmente y que muestra al usuario nuevo sus amigos conectados.

### <a name="retail-use-case---multiple-functions"></a>Caso de uso de venta minorista: varias funciones

En implementaciones de venta minorista, cuando un usuario agrega un elemento a su cesta, ahora se tiene la flexibilidad de crear e invocar funciones para componentes opcionales de la canalización comercial.

**Implementación**: varios desencadenadores de Azure Functions para Cosmos DB que escuchan un contenedor

1. Puede crear varias instancias de Azure Functions si agrega desencadenadores de Azure Functions para Cosmos DB a cada una de ellas, todas las cuales escuchan la misma fuente de cambios de los datos del carro de la compra. Tenga en cuenta que cuando varias funciones escuchan en la misma fuente de cambios, es necesario una colección de concesiones para cada función. Para más información sobre las colecciones de concesiones, consulte [Biblioteca del procesador de fuente de cambios](change-feed-processor.md).
2. Cada vez que un elemento nuevo se agrega al carro de la compra de los usuarios, la fuente de cambios invoca cada función de manera independiente desde el contenedor de carros de la compra.
   * Una función puede usar el contenido de la cesta actual para cambiar la visualización de otros artículos que puedan interesarle al usuario.
   * Otra función podría actualizar los totales de inventario.
   * Otra función podría enviar la información del cliente para determinados productos al departamento de marketing, el que envía a los clientes un correo electrónico masivo promocional. 

     Todos los departamentos pueden crear un desencadenador de Azure Functions para Cosmos DB si escuchan la fuente de cambios, con la seguridad de no retrasar los eventos de procesamiento de pedidos críticos en el proceso.

En todos estos casos de uso, como la función desacopló la aplicación misma, no es necesario iniciar instancias de aplicación nuevas todo el tiempo. En lugar de eso, Azure Functions inicia funciones individuales para completar los procesos discretos según sea necesario.

## <a name="tooling"></a>Tooling

La integración nativa entre Azure Cosmos DB y Azure Functions está disponible en Azure Portal y en Visual Studio 2019.

* En el portal de Azure Functions, puede crear un desencadenador. Para instrucciones de inicio rápido, consulte [Creación de un desencadenador de Azure Functions para Cosmos DB en Azure Portal](../../azure-functions/functions-create-cosmos-db-triggered-function.md).
* En el portal de Azure Cosmos DB, puede agregar un desencadenador de Azure Functions para Cosmos DB a una aplicación de Azure Function existente en el mismo grupo de recursos.
* En Visual Studio 2019, puede crear el desencadenador mediante las [Herramientas de Azure Functions](../../azure-functions/functions-develop-vs.md):

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>¿Por qué elegir la integración de Azure Functions para la informática sin servidor?

Azure Functions ofrece la capacidad de crear unidades de trabajo escalables, o partes concisas de lógica que se pueden ejecutar a petición, sin tener que aprovisionar o administrar infraestructura. Con Azure Functions, no es necesario crear una aplicación completa para responder a los cambios en la base de datos de Azure Cosmos, porque puede crear funciones pequeñas reutilizables para tareas específicas. Además, también puede usar los datos de Azure Cosmos DB como entrada o salida a una instancia de Azure Functions en respuesta a eventos tales como solicitudes HTTP o un desencadenador de temporizador.

Azure Cosmos DB es la base de datos recomendada para la arquitectura de informática sin servidor por los motivos siguientes:

* **Acceso instantáneo a todos los datos**: tiene acceso pormenorizado a cada valor almacenado porque Azure Cosmos DB [indexa automáticamente](../index-policy.md) todos los datos de manera predeterminada y permite que esos índices estén disponibles de inmediato. Esto significa que puede consultar, actualizar y agregar elementos nuevos constantemente a la base de datos y tiene acceso instantánea vía Azure Functions.

* **Sin esquema**. Azure Cosmos DB no tiene esquemas, por lo que es capaz de forma exclusiva de controlar cualquier salida de datos de una instancia de Azure Function. Este enfoque de "controlar todo" permite que el proceso de crear una variedad de instancias de Functions que tengan salida a Azure Cosmos DB sea sencillo.

* **Rendimiento escalable**. En Azure Cosmos DB, el rendimiento se puede escalar y reducir verticalmente de manera instantánea. Si tiene cientos o miles de instancias de Functions que consultan y escriben en el mismo contenedor, puede escalar verticalmente las [RU/s](../request-units.md) para controlar la carga. Todas las funciones pueden trabajar en paralelo con las RU/s asignadas y se garantiza que los datos serán [coherentes](../consistency-levels.md).

* **Replicación global**. Puede replicar los datos de Azure Cosmos DB [en todo el mundo](../distribute-data-globally.md) para reducir la latencia y localizar geográficamente los datos más cercanos al lugar en que se encuentran los usuarios. Del mismo modo que ocurre con todas las consultas de Azure Cosmos DB, los datos de los desencadenadores basados en eventos son datos de lectura de la instancia de Azure Cosmos DB más cercana al usuario.

Si busca la integración con Azure Functions para almacenar datos y no necesita una indexación profunda o si necesita almacenar los archivos adjuntos y los archivos multimedia, una mejor opción podría ser el [desencadenador de Azure Blob Storage](../../azure-functions/functions-bindings-storage-blob.md).

Ventajas de Azure Functions: 

* **Basado en eventos**. Azure Functions está basado en eventos y puede escuchar una fuente de cambios de Azure Cosmos DB. Esto significa que no es necesario crear una lógica de escucha, sino que solo debe estar atento a los cambios que escucha. 

* **Sin límites**. Las instancias de Functions se ejecutan en paralelo y el servicio se inicia las veces que sea necesario. Los parámetros los establece usted.

* **Ideal para las tareas rápidas**. El servicio inicia instancias nuevas de funciones cada vez que se activa un evento y los cierra tan pronto se completa la función. Solo se paga por el tiempo durante el cual se ejecutan las funciones.

Si no está seguro si Flow, Logic Apps, Azure Functions o WebJobs es la mejor opción para su implementación, consulte [Elección entre Flow, Logic Apps, Functions y WebJobs](../../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Pasos siguientes

Ahora conectaremos realmente Azure Cosmos DB y Azure Functions: 

* [Creación de un desencadenador de Azure Functions para Cosmos DB en Azure Portal](../../azure-functions/functions-create-cosmos-db-triggered-function.md)
* [Creación de un desencadenador HTTP de Azure Functions con un enlace de entrada de Azure Cosmos DB](../../azure-functions/functions-bindings-cosmosdb.md?tabs=csharp)
* [Enlaces y desencadenadores de Azure Cosmos DB](../../azure-functions/functions-bindings-cosmosdb-v2.md)
