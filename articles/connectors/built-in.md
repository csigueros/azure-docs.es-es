---
title: Acciones y desencadenadores integrados
description: Use desencadenadores y acciones integrados para crear flujos de trabajo automatizados que integren aplicaciones, datos, servicios y sistemas, y así poder controlar flujos de trabajo y para administrar datos mediante Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 83800e088599bca0023d734bba52b6ed3207f0a3
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515345"
---
# <a name="built-in-triggers-and-actions-in-azure-logic-apps"></a>Desencadenadores y acciones integrados en Azure Logic Apps

Los [desencadenadores y las acciones integrados](apis-list.md) proporcionan [maneras de controlar la programación y la estructura del flujo de trabajo](#control-workflow), [ejecutar su propio código](#run-code-from-workflows), [administrar o manipular datos](#manage-or-manipulate-data) y completar otras tareas en los flujos de trabajo. A diferencia de los [conectores administrados](managed.md), muchas operaciones integradas no están vinculadas a un servicio, sistema o protocolo específicos. Por ejemplo, puede iniciar casi cualquier flujo de trabajo según una programación mediante el desencadenador Periodicidad. O bien, puede hacer que el flujo de trabajo espere hasta que se llame mediante el desencadenador Solicitud. Todas las operaciones integradas se ejecutan de forma nativa en Azure Logic Apps, y la mayoría no requieren la creación de una conexión para usarlas.

Para un número menor de servicios, sistemas y protocolos, Azure Logic Apps proporciona operaciones integradas, como Azure App Service, Azure Functions o Azure API Management, y de llamada a otros flujos de trabajo de aplicaciones lógicas de Azure Logic Apps. El número y el intervalo disponibles varían en función de si se crea un recurso de aplicación lógica basado en el plan de consumo que se ejecute en Azure Logic Apps multiinquilino o un recurso de aplicación lógica basado en planes estándar que se ejecute en Azure Logic Apps de un solo inquilino. Para más información, revise [Entorno de servicio de integración (ISE): inquilino único o multiinquilino](../logic-apps/single-tenant-overview-compare.md). En cambio, en la mayoría de los casos, la versión integrada proporciona un mejor rendimiento, funcionalidades y precios, entre otras cosas.

Por ejemplo, si crea una aplicación lógica de un solo inquilino, tanto las operaciones integradas como las [operaciones de conector administrado](managed.md) están disponibles para algunos servicios, en concreto, Azure Service Bus, Azure Event Hubs, SQL Server, DB2 y MQ. En algunos casos, una operación integrada solo está disponible en un entorno, por ejemplo, el archivo plano solo está disponible actualmente en entornos multiinquilino, mientras que el archivo host de IBM solo está disponible en entornos de un solo inquilino. En cambio, en la mayoría de los casos, la versión integrada proporciona un mejor rendimiento, funcionalidades y precios, entre otras cosas.

En la siguiente lista solo se describen algunas de las tareas que puede realizar con [desencadenadores y acciones integrados](#general-built-in-triggers-and-actions):

- Ejecute flujos de trabajo cuando use programaciones personalizadas y avanzadas. Para más información sobre la programación, revise la [sección del comportamiento de periodicidad de la información general del conector para Azure Logic Apps](apis-list.md#recurrence-behavior).

- Organice y controle la estructura del flujo de trabajo, por ejemplo, mediante bucles y condiciones.

- También puede trabajar con variables, fechas, operaciones de datos, transformaciones de contenido y operaciones por lotes.

- Comuníquese con otros puntos de conexión mediante acciones y desencadenadores HTTP.

- Recibir y responder solicitudes.

- Llame a sus propias funciones (Azure Functions), como las aplicaciones web (Azure App Services), las API (Azure API Management) u otros flujos de trabajo de Azure Logic Apps que pueden recibir solicitudes, y así sucesivamente.

## <a name="general-built-in-triggers-and-actions"></a>Acciones y desencadenadores generales integrados

Azure Logic Apps proporciona los siguientes desencadenadores y acciones integrados:

:::row:::
    :::column:::
        [![Icono de programación][schedule-icon]][schedule-doc]
        \
        \
        [**Programación**][schedule-doc]
        \
        \
        [**Periodicidad:**][schedule-recurrence-doc] permite desencadenar un flujo de trabajo en función de la periodicidad especificada.
        \
        \
        [**Ventana deslizante:**][schedule-sliding-window-doc] permite desencadenar un flujo de trabajo que necesita controlar los datos en fragmentos continuos.
        \
        \
        [**Retraso:**][schedule-delay-doc] permite pausar el flujo de trabajo durante el tiempo especificado.
        \
        \
        [**Retrasar hasta**][schedule-delay-until-doc]: permite pausar el flujo de trabajo hasta la fecha y hora especificadas.
    :::column-end:::
    :::column:::
        [![Icono de acción y desencadenador de HTTP][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        Llame a un punto de conexión HTTP o HTTPS mediante el desencadenador o la acción HTTP.
        \
        \
        También puede usar estos otros desencadenadores y acciones HTTP integrados:
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP + Webhook][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![icono de desencadenador de solicitud][http-request-icon]][http-request-doc]
        \
        \
        [**Solicitud**][http-request-doc]
        \
        \
        [**Cuando se recibe una solicitud HTTP:**][http-request-doc] permite esperar una solicitud de otro flujo de trabajo, aplicación o servicio. Este desencadenador permite llamar al flujo de trabajo sin tener que revisarlo o sondearlo en un horario.
        \
        \
        [**Respuesta:**][http-request-doc] permite responder a una solicitud que recibe el desencadenador denominado **Cuando se recibe una solicitud HTTP** en el mismo flujo de trabajo.
    :::column-end:::
    :::column:::
        [![Icono de lotes][batch-icon]][batch-doc]
        \
        \
        [**Batch**][batch-doc]
        \
        \
        [**Mensajes por lotes:**][batch-doc] permite desencadenar un flujo de trabajo que procese los mensajes en lotes.
        \
        \
        [**Enviar mensajes al lote:**][batch-doc] permite llamar a un flujo de trabajo existente que se inicia actualmente con un **desencadenador de mensajes de Batch**.
    :::column-end:::
:::row-end:::

## <a name="service-based-built-in-trigger-and-actions"></a>Desencadenador y acciones integrados basados en el servicio

Azure Logic Apps proporciona las siguientes acciones integradas para los servicios a continuación:

:::row:::
    :::column:::
        [![Icono de Azure API Management][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Azure API Management**][azure-api-management-doc]
        \
        \
        Llame a sus propios desencadenadores y acciones en las API que defina, administre y publique mediante [Azure API Management](../api-management/api-management-key-concepts.md). <p><p>**Nota:** Esto no se admite cuando se usa el [nivel de consumo para API Management](../api-management/api-management-features.md).
    :::column-end:::
    :::column:::
        [![Icono de Azure App Services][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**Azure App Services**][azure-app-services-doc]
        \
        \
        Llame a las aplicaciones que haya creado y hospédelas en [Azure App Service](../app-service/overview.md); por ejemplo, API Apps y Web Apps.
        \
        \
        Cuando Swagger se incluye, los desencadenadores y las acciones que hayan definido las aplicaciones aparecen como cualquier otro desencadenador y acción de primera clase en Azure Logic Apps.
    :::column-end:::
    :::column:::
        [![Icono de Azure Logic Apps][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        Llame a otros flujos de trabajo que comiencen con el desencadenador se solicitud denominado **Cuando se recibe una solicitud HTTP**.
    :::column-end:::
    :::column:::
        [![Icono de SQL Server][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc] <br>(*Solo inquilino único*)     \
        \
        Conéctese a SQL Server en el entorno local o a una base de datos de Azure SQL Database en la nube para poder administrar los registros, ejecutar procedimientos almacenados o realizar consultas. <p>**Nota**: Azure Logic Apps de un solo inquilino proporciona operaciones de conectores SQL integrados y administrados, mientras que Azure Logic Apps multiinquilino proporciona solo operaciones de conectores administrados. <p>Para más información, consulte [Comparación de las opciones de un solo inquilino, multiinquilino y entorno del servicio de integración para Azure Logic Apps](../logic-apps/single-tenant-overview-compare.md).
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>Ejecución de código desde flujos de trabajo

Azure Logic Apps proporciona las acciones integradas siguientes para ejecutar código propio en el flujo de trabajo:

:::row:::
    :::column:::
        [![Icono de Azure Functions][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure Functions**][azure-functions-doc]
        \
        \
        Llame a las [funciones hospedadas en Azure](../azure-functions/functions-overview.md) para ejecutar sus propios *fragmentos de código* (C# o Node.js) en el flujo de trabajo.
    :::column-end:::
    :::column:::
        [![Icono de acción Código en línea][inline-code-icon]][inline-code-doc]
        \
        \
        [**Código en línea**][inline-code-doc]
        \
        \
        [**Ejecutar código JavaScript**][inline-code-doc]: permite agregar y ejecutar sus propios *fragmentos de código* de JavaScript insertados en el flujo de trabajo.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>Controlar el flujo de trabajo

Azure Logic Apps proporciona las acciones integradas siguientes para estructurar y controlar las acciones en el flujo de trabajo:

:::row:::
    :::column:::
        [![Icono de acción Condición][condition-icon]][condition-doc]
        \
        \
        [**Condition**][condition-doc]
        \
        \
        Evalúa una condición y ejecuta acciones diferentes según si la condición es true o false.
    :::column-end:::
    :::column:::
        [![Icono de acción Para cada][for-each-icon]][for-each-doc]
        \
        \
        [**For Each**][for-each-doc]
        \
        \
        Realiza las mismas acciones en todos los elementos de una matriz.
    :::column-end:::
    :::column:::
        [![Icono de acción Ámbito][scope-icon]][scope-doc]
        \
        \
        [**Nombre**][scope-doc]
        \
        \
        Agrupa las acciones en *ámbitos*, que obtienen su propio estado después de que las acciones del ámbito terminen de ejecutarse.
    :::column-end:::
    :::column:::
        [![Icono de acción Cambiar][switch-icon]][switch-doc]
        \
        \
        [**Switch**][switch-doc]
        \
        \
        Agrupa las acciones en *casos* a los que se asignan valores únicos, excepto el caso predeterminado. Ejecuta solo el caso cuyo valor asignado coincida con el resultado de una expresión, objeto o token. Si no hay coincidencias, ejecuta el caso predeterminado.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icono de acción Terminar][terminate-icon]][terminate-doc]
        \
        \
        [**Terminate**][terminate-doc]
        \
        \
        Detiene el flujo de trabajo que la aplicación lógica está ejecutando activamente.
    :::column-end:::
    :::column:::
        [![Icono de acción Hasta][until-icon]][until-doc]
        \
        \
        [**Until**][until-doc]
        \
        \
        Repite las acciones hasta que la condición especificada sea true o cambie algún estado.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>Administrar o manipular datos

Azure Logic Apps proporciona las siguientes acciones integradas para trabajar con salidas de datos y sus formatos:

:::row:::
    :::column:::
        [![Icono de Data Operations][data-operations-icon]][data-operations-doc]
        \
        \
        [**Data Operations**][data-operations-doc]
        \
        \
        Realice operaciones con datos.
        \
        \
        **Redactar**: crea una única salida a partir de varias entradas con varios tipos.
        \
        \
        **Crear tabla CSV**: crea una tabla con valores separados por comas (CSV) a partir de una matriz con objetos JSON.
        \
        \
        **Crear tabla HTML**: crea una tabla HTML a partir de una matriz con objetos JSON.
        \
        \
        **Filtrar matriz**: crea una matriz a partir de elementos de otra matriz que cumplen los criterios.
        \
        \
        **Combinar**: crea una cadena a partir de todos los elementos de una matriz y los separa con el delimitador especificado.
        \
        \
        **Analizar JSON**: crea tokens sencillos a partir de propiedades y sus valores en contenido JSON para que pueda usar esas propiedades en el flujo de trabajo.
        \
        \
        **Select**: crea una matriz con objetos JSON mediante la transformación de elementos o valores de otra matriz, y la asignación de esos elementos a propiedades especificadas.
    :::column-end:::
    :::column:::
        ![Icono de acción Fecha y hora][date-time-icon]
        \
        \
        **Date Time**
        \
        \
        Realice operaciones con marcas de tiempo.
        \
        \
        **Agregar a la hora**: agrega el número especificado de unidades a una marca de tiempo.
        \
        \
        **Convertir la zona horaria**: Convierte una marca de tiempo de la zona horaria de origen a la zona horaria de destino.
        \
        \
        **Hora actual**: Devuelve la marca de tiempo actual como una cadena.
        \
        \
        **Obtener la hora futura**: Devuelve la marca de tiempo actual más las unidades de tiempo especificadas.
        \
        \
        **Obtener la hora pasada**: Devuelve la marca de tiempo actual menos las unidades de tiempo especificadas.
        \
        \
        **Restar de la hora**: Resta un número de unidades de tiempo de una marca de tiempo.
    :::column-end:::
    :::column:::
        [![Icono de acción Variables][variables-icon]][variables-doc]
        \
        \
        [**Variables**][variables-doc]
        \
        \
        Realiza operaciones con variables.
        \
        \
        **Anexar a la variable de matriz**: inserta un valor como el último elemento en una matriz almacenada por una variable.
        \
        \
        **Anexar a la variable de cadena**: inserta un valor como el último carácter en una cadena almacenada por una variable.
        \
        \
        **Reducir variable**: reduce una variable por un valor constante.
        \
        \
        **Incremento de variable**: incrementa una variable por un valor constante.
        \
        \
        **Inicializar la variable**: crea una variable y declara su tipo de datos y el valor inicial.
        \
        \
        **Establecer variable**: asigna otro valor a una variable existente.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-built-in-actions"></a>Acciones integradas de la cuenta de integración

Azure Logic Apps proporciona las siguientes acciones integradas, que requieren una cuenta de integración cuando se usa Azure Logic Apps multiinquilino basado en planes de consumo o que no requieren una cuenta de integración cuando se usa Azure Logic Apps de un solo inquilino basado en el plan estándar:

> [!NOTE]
> Para poder la acción de la cuenta de integración en una instancia de Azure Logic Apps multiinquilino basada en planes de consumo, debe [vincular el recurso de la aplicación lógica a una cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Sin embargo, en las instancias de Azure Logic Apps basadas en planes estándar de un solo inquilino, algunas operaciones de la cuenta de integración no requieren vincular el recurso de la aplicación lógica a una cuenta de integración, como las operaciones Liquid y XML. Para usar estas acciones, debe tener asignaciones de Liquid, asignaciones XML o esquemas XML que pueda cargar por medio de las acciones respectivas en Azure Portal o agregar a la carpeta **Artefactos** del proyecto de Visual Studio Code mediante las carpetas **Asignaciones** y **Esquemas** respectivas.

:::row:::
    :::column:::
        [![Icono de descodificación de archivo plano][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**Flat file decoding**<br>(*solo multiinquilino*)][flat-file-decode-doc]
        \
        \
        Codifique los archivos XML antes de enviar el contenido a un socio comercial.
    :::column-end:::
    :::column:::
        [![Icono de codificación de archivo plano][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**Flat file encoding**<br>(*solo multiinquilino*)][flat-file-encode-doc]
        \
        \
        Descodifique los archivos XML al recibir el contenido de un socio comercial.
    :::column-end:::
    :::column:::
        [![Icono de cuenta de integración][integration-account-icon]][integration-account-doc]
        \
        \
        [**Integration Account Artifact Lookup**<br>(*solo multiinquilino*)][integration-account-doc]
        \
        \
        Obtenga metadatos personalizados para artefactos, como socios comerciales, contratos, esquemas, etc., en su cuenta de integración.
    :::column-end:::
    :::column:::
        [![Icono de operaciones Liquid][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Operaciones Liquid**][json-liquid-transform-doc]
        \
        \
        Convierta los siguientes formatos mediante plantillas de Liquid: <p><p>- De JSON a JSON <br>- De JSON a texto <br>- De XML a JSON <br>- De XML a texto
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icono de Transformar XML][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**Transform XML**][xml-transform-doc]
        \
        \
        Convierta el formato XML de origen a otro formato XML.
    :::column-end:::
    :::column:::
        [![Icono de validación de XML][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**XML validation**][xml-validate-doc]
        \
        \
        Valide los documentos XML con el esquema especificado.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Creación de API personalizadas que se pueden llamar desde Azure Logic Apps](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-in icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[sql-server-icon]: ./media/apis-list/sql.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Built-in integration account connector icons -->
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[xml-transform-icon]: ./media/apis-list/xml-transform.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Creación de una instancia del servicio Azure API Management para administrar y publicar sus API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Integrar aplicaciones lógicas con App Service API Apps"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Creación y administración de blobs en Azure Blob Storage con Azure Logic Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integración de aplicaciones lógicas con Azure Functions"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Procesamiento de mensajes en grupos o lotes"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Evaluar una condición y ejecutar acciones diferentes según si la condición es true o false"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Realizar operaciones de datos como el filtrado de matrices o la creación de tablas CSV y HTML"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Realizar las mismas acciones en todos los elementos de una matriz"
[http-doc]: ./connectors-native-http.md "Llamar a puntos de conexión HTTP o HTTPS desde las aplicaciones lógicas"
[http-request-doc]: ./connectors-native-reqres.md "Recibir solicitudes HTTP en las aplicaciones lógicas"
[http-response-doc]: ./connectors-native-reqres.md "Responder a solicitudes HTTP desde las aplicaciones lógicas"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Llamar a puntos de conexión REST desde las aplicaciones lógicas"
[http-webhook-doc]: ./connectors-native-webhook.md "Esperar eventos específicos de los puntos de conexión HTTP o HTTPS"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Agregar y ejecutar fragmentos de código JavaScript de las aplicaciones lógicas"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrar aplicaciones lógicas con flujos de trabajo anidados"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Seleccionar y filtrar matrices con la acción Consulta"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Ejecutar aplicaciones lógicas en función de una programación"
[schedule-delay-doc]: ./connectors-native-delay.md "Retrasar la ejecución de la siguiente acción"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Retrasar la ejecución de la siguiente acción"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Ejecutar aplicaciones lógicas según una programación periódica"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Ejecutar aplicaciones lógicas que necesitan manipular datos en fragmentos contiguos"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizar las acciones en grupos, que obtienen su propio estado después de que las acciones del grupo terminen de ejecutarse"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Conectar con Azure SQL Database o SQL Server. Crear, actualizar, obtener y eliminar entradas en una tabla de base de datos SQL"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organizar las acciones en los casos, a los que se asignan valores únicos. Ejecutar solo el caso cuyo valor asignado coincida con el resultado de una expresión, objeto o token. Si no hay coincidencias, ejecutar el caso predeterminado"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Detener o cancelar un flujo de trabajo que se está ejecutando activamente para la aplicación lógica"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Repetir las acciones hasta que la condición especificada sea true o cambie algún estado"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Realizar operaciones con variables, por ejemplo, inicializar, establecer, incrementar, reducir y anexar a la variable de cadena o matriz"

<!--Built-in integration account doc links-->
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Más información sobre archivos planos para integración empresarial"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Más información sobre archivos planos para integración empresarial"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Administrar metadatos de artefactos para cuentas de integración"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transformar JSON mediante plantillas de Liquid"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Transformar mensajes XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Validar mensajes XML"
