---
title: Importación de GraphQL API con Azure Portal | Microsoft Docs
titleSuffix: ''
description: Obtenga información sobre la compatibilidad de API Management con GraphQL, agregue una GraphQL API y las limitaciones de GraphQL.
ms.service: api-management
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: c1541e9de6d2f9346826e256fcb854b0ca7cdac3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091728"
---
# <a name="import-a-graphql-api-preview"></a>Importación de GraphQL API (versión preliminar)

GraphQL es un lenguaje de consulta estándar del sector de código abierto para las API. A diferencia de las API basadas en puntos de conexión (o estilo REST) diseñadas en torno a acciones sobre recursos, las GraphQL API admiten un conjunto más amplio de casos de uso y se centran en tipos de datos, esquemas y consultas.

API Management aborda los desafíos de seguridad, autenticación y autorización que se conlleva la publicación de GraphQL API. Con API Management para exponer las GraphQL API, puede hacer lo siguiente:
* Agregar un servicio de GraphQL como API a través de Azure Portal.  
* Proteger las GraphQL API mediante la aplicación de directivas de control de acceso existentes y una [nueva directiva](graphql-validation-policies.md) para protegerse frente a ataques específicos de GraphQL. 
* Explore el esquema y ejecute consultas de prueba en las GraphQL API en los portales de Azure y para desarrolladores. 

[!INCLUDE [preview-callout-graphql.md](./includes/preview/preview-callout-graphql.md)]

En este artículo, hará lo siguiente:
> [!div class="checklist"]
> * Obtener más información sobre las ventajas de usar las GraphQL API.
> * Agregar una GraphQL API a la instancia de API Management.
> * Probar la GraphQL API.
> * Conocer las limitaciones de GraphQL API en API Management.

## <a name="prerequisites"></a>Requisitos previos

- Tener una instancia de API Management existente. [Cree una suscripción si todavía no lo ha hecho](get-started-create-service-instance.md).
- Una GraphQL API. 

## <a name="add-a-graphql-api"></a>Agregar una GraphQL API

1. Navegue hasta su instancia de API Management.
1. En el menú de navegación lateral, en la sección **API**, seleccione **API**.
1. En **Definir una API nueva**, seleccione el icono **GraphQL**.

    :::image type="content" source="media/graphql-api/import-graphql-api.png" alt-text="Selección del icono GraphQL de la lista de API":::

1. En el cuadro de diálogo, seleccione **Completo** y rellene los campos necesarios del formulario.

    :::image type="content" source="media/graphql-api/create-from-graphql-schema.png" alt-text="Mostrar campos para crear GraphQL":::

    | Campo | Descripción |
    |----------------|-------|
    | Nombre para mostrar | Nombre con el que se mostrará la GraphQL API. |
    | Nombre | Nombre sin formato de la GraphQL API. Se rellena automáticamente a medida que escribe el nombre para mostrar. |
    | Punto de conexión de la GraphQL API | Dirección URL base con el nombre del punto de conexión de la GraphQL API. <br /> Por ejemplo: *https://example.com/your-GraphQL-name* . También puede usar el [punto de conexión común de GraphQL "Star Wars"](https://swapi-graphql.netlify.app/.netlify/functions/index) como demo. |
    | Cargue el archivo de esquema | Seleccione esta opción para examinar y cargar el archivo de esquema. |
    | Descripción | Agregue una descripción de la API. |
    | Esquema URL | Seleccione HTTP, HTTPS o Ambos. Selección predeterminada: *Ambos*. |
    | Sufijo de dirección URL de API| Agregue un sufijo de URL para identificar esta API específica en esta instancia de API Management. Tiene que ser único en esta instancia de API Management. |
    | URL base | Campo no editable que muestra la dirección URL base de la API |
    | Etiquetas | Asocie la GraphQL API a etiquetas nuevas o existentes. |
    | Productos | Asocie la GraphQL API a un producto para publicarla. |
    | Puertas de enlace | Asocie la GraphQL API a las puertas de enlace existentes. Selección de puerta de enlace predeterminada: *Administrada*. |
    | ¿Definir versión de esta API? | Seleccione esta opción para controlar la versión de la GraphQL API. |
 
1. Haga clic en **Crear**.

## <a name="test-your-graphql-api"></a>Probar la GraphQL API

1. Navegue hasta su instancia de API Management.
1. En el menú de navegación lateral, en la sección **API**, seleccione **API**.
1. En **Todas las API**, seleccione la GraphQL API.
1. Seleccione la pestaña **Prueba** para acceder a la consola Prueba. 
1. En **Encabezados**:
    1. Seleccione el encabezado en el menú desplegable **Nombre**.
    1. Escriba el valor en el campo **Valor**.
    1. Agregue más encabezados seleccionando **+ Agregar encabezado**.
    1. Elimine los encabezados mediante el **icono de la papelera**.
1. Si ha agregado un producto a la GraphQL API, aplique el ámbito del producto en **Aplicar ámbito del producto**.
1. En el **Editor de consultas**, haga una de las opciones siguientes:
    1. Seleccione al menos un campo o subcampo de la lista en el menú lateral. Los campos y subcampos que seleccione aparecerán en el editor de consultas.
    1. Comience a escribir en el editor de consultas para crear una consulta.
    
        :::image type="content" source="media/graphql-api/test-graphql-query.png" alt-text="Demostración de la adición de campos al editor de consultas":::

1. En **Variables de consulta**, agregue variables para reutilizar la misma consulta o mutación y pasar valores diferentes.
1. Haga clic en **Enviar**.
1. Vea la **Respuesta**.

    :::image type="content" source="media/graphql-api/graphql-query-response.png" alt-text="Visualización de la respuesta de la consulta de prueba":::

1. Repita los pasos anteriores para probar diferentes cargas.
1. Una vez completadas las pruebas, salga de la consola de prueba.

## <a name="limitations"></a>Limitaciones

* Solo se admite el paso a través de GraphQL. 
* Una única GraphQL API en API Management corresponde solo a un único punto de conexión de back-end de GraphQL.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Transformación y protección de una API publicada](transform-api.md)
