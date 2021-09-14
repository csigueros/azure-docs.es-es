---
title: Incorporación de esquemas para validar XML
description: Incorpore esquemas para validar documentos XML en Azure Logic Apps con Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/25/2021
ms.openlocfilehash: dc55e70e9ceaa9546890b2ed7dd5df0d705b1a92
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099996"
---
# <a name="add-schemas-to-validate-xml-in-azure-logic-apps"></a>Incorporación de esquemas para validar XML en Azure Logic Apps

Para comprobar que los documentos usan XML válidos y cuentan con los datos esperados en el formato predefinido para escenarios de integración empresarial en Azure Logic Apps, la aplicación lógica puede usar esquemas. Un esquema también puede validar los mensajes que las aplicaciones lógicas intercambia en escenarios negocio a negocio (B2B).

Si no conoce las aplicaciones lógicas, consulte la siguiente documentación:

* [Qué es Azure Logic Apps: tipo de recurso y entornos de host](logic-apps-overview.md#resource-type-and-host-environment-differences)

* [Creación de un flujo de trabajo de integración con Azure Logic Apps (estándar) de inquilino único](create-single-tenant-workflows-azure-portal.md)

* [Creación de flujos de trabajo aplicación lógica de un solo inquilino](create-single-tenant-workflows-azure-portal.md)

* [Modelos de medición, facturación y precios de uso de Azure Logic Apps](logic-apps-pricing.md)

## <a name="limits"></a>Límites

* En el caso de recursos de aplicación lógica **estándar**, no existen límites con respecto al tamaño de archivo de esquema.

* En el caso de los recursos de aplicación lógica de **consumo**, existen límites para las cuentas de integración y los artefactos, como los esquemas. Para obtener más información, consulte [Información de límites y configuración para Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

  Normalmente, cuando se usa una cuenta de integración con el flujo de trabajo y se quiere validar código XML, se agrega o se carga el esquema en esa cuenta. Si hace referencia a un esquema que no está en la cuenta de integración, o lo importa, es posible que reciba el siguiente error al usar el elemento `xsd:redefine`:

  `An error occurred while processing the XML schemas: ''SchemaLocation' must successfully resolve if <redefine> contains any child other than <annotation>.'.`

  Para resolver este error, debe usar el elemento `xsd:import` o `xsd:include`, en lugar de `xsd:redefine`, o bien usar un URI.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene suscripción, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Si usa el tipo de recurso **Logic App (estándar)** , no necesita una cuenta de integración, sino que puede agregar esquemas directamente al recurso de aplicación lógica en Azure Portal o Visual Studio Code. Posteriormente puede usar dichas asignaciones en varios flujos de trabajo dentro del *mismo recurso de aplicación lógica*.

* Si usa el tipo de recurso **Logic App (consumo)** , debe tener un [recurso de cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) donde poder almacenar sus esquemas y otros artefactos que va a usar en las soluciones de integración empresarial y negocio a negocio (B2B). Este recurso tiene que satisfacer los siguientes requisitos:

  * Estar asociado a la misma suscripción de Azure que el recurso de aplicación lógica.

  * Existir en la misma ubicación o región de Azure que el recurso de aplicación lógica donde piensa usar la acción de validación de XML.

  * Estar [vinculado](logic-apps-enterprise-integration-create-integration-account.md#link-account) al recurso de aplicación lógica donde quiere usar los esquemas.

    Para crear y agregar esquemas con el fin de usarlos en flujos de trabajo de aplicación lógica de consumo, no necesita todavía un recurso de aplicación lógica. Sin embargo, cuando tenga todo listo para usar dichos esquemas en los flujos de trabajo, el recurso de aplicación lógica requiere una cuenta de integración vinculada que almacene los esquemas.

* Si el esquema es de [2 MB o menos](#smaller-schema), puede agregarlo a la cuenta de integración *directamente* desde Azure Portal. Sin embargo, si el esquema tiene un tamaño superior a 2 MB, pero inferior al [límite de tamaño para esquemas](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), puede cargarlo en una cuenta de almacenamiento de Azure. Para agregar ese esquema a la cuenta de integración, puede vincular a la cuenta de almacenamiento desde la cuenta de integración. Para esta tarea, necesita estos elementos:

    | Elemento | Descripción |
    |------|-------------|
    | [Cuenta de Almacenamiento de Azure](../storage/common/storage-account-overview.md) | En esta cuenta, cree un contenedor de blobs de Azure para el esquema. Aprenda a [crear una cuenta de almacenamiento](../storage/common/storage-account-create.md). |
    | Contenedor de blobs | En este contenedor, puede cargar el esquema. También necesitará el URI de contenido de este contenedor más adelante, cuando agregue el esquema a su cuenta de integración. Aprenda a [crear un contenedor de blobs](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Explorador de Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Esta herramienta ayuda a administrar más fácilmente las cuentas de almacenamiento y los contenedores de blobs. Para usar el Explorador de Storage, elija un paso: <p>- En Azure Portal, seleccione su cuenta de almacenamiento. En el menú de la cuenta de almacenamiento, seleccione **Explorador de Storage**. <p>- Para la versión de escritorio, [descargue e instale el Explorador de Azure Storage](https://www.storageexplorer.com/). Luego, para conectarlo con su cuenta de almacenamiento, siga los pasos que aparecen en [Introducción al Explorador de Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Para más información, vea esta [Inicio rápido: Cree un blob en el almacenamiento de objetos con el Explorador de Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).  |
    |||

  Si quiere agregar asignaciones más grandes para recursos de aplicación lógica de consumo, también puede usar [esquemas de la API de REST de Azure Logic Apps](/rest/api/logic/schemas/create-or-update). Sin embargo, la API de REST Azure Logic Apps no está disponible actualmente para recursos de aplicación lógica estándar.

<a name="add-schemas"></a>

## <a name="add-schemas"></a>Incorporación de esquemas

### <a name="consumption-resource"></a>[Recurso de consumo](#tab/consumption-1)

1. En [Azure Portal](https://portal.azure.com), inicie sesión con las credenciales de su cuenta de Azure.

1. En el cuadro de búsqueda principal de Azure, escriba `integration accounts` y seleccione **Cuentas de integración**.

1. Seleccione la cuenta integración en la que quiera agregar el esquema.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Esquemas**.

1. En la barra de herramientas **Esquemas** del panel, seleccione **Agregar**.

Según el tamaño de archivo del esquema (.xsd), siga los pasos para cargar un esquema que tengas [hasta 2 MB](#smaller-schema) o [más de 2 MB pero menos de 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Incorporación de esquemas de hasta 2 MB

1. En **Agregar esquema**, escriba un nombre para el esquema. Mantenga seleccionado **Archivo pequeño**. Junto a la casilla **Esquema**, seleccione el icono de carpeta. Busque y seleccione el esquema que va a cargar.

1. Cuando tenga todo listo, seleccione **Aceptar**.

   Una vez que el esquema termine de cargarse, aparecerá en la lista de **esquemas**.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Incorporación de esquemas de más de 2 MB

Para agregar esquemas de mayor tamaño, puede cargar el esquema en un contenedor de blobs de Azure de la cuenta de almacenamiento de Azure. Los pasos que debe seguir para agregar esquemas varían en función de si el contenedor de blobs tiene acceso de lectura público. En primer lugar, revise si el contenedor de blobs tiene o no acceso de lectura público con estos pasos: [Establecimiento del nivel de acceso público para un contenedor de blobs](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Comprobación del nivel de acceso de un contenedor

1. Abra el Explorador de Azure Storage. En la ventana del Explorador, expanda la suscripción de Azure si todavía no está expandida.

1. Expanda **Cuentas de almacenamiento** > {*su-cuenta-de-almacenamiento*} > **Contenedores de blobs**. Seleccione el contenedor de blobs.

1. En el menú contextual del contenedor de blobs, seleccione **Establecer nivel de acceso público**.

   * Si el contenedor de blobs tiene como mínimo acceso público, seleccione **Cancelar** y siga los pasos que aparecen más adelante en esta página en [Carga en contenedores con acceso público](#public-access).

     ![Acceso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Si el contenedor de blobs no tiene acceso público, seleccione **Cancelar** y siga los pasos que aparecen más adelante en esta página, en [Carga en contenedores sin acceso público](#public-access).

     ![Sin acceso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Carga en contenedores con acceso público

1. Cargue el esquema en la cuenta de almacenamiento. En la ventana de la derecha, seleccione **Cargar**.

1. Cuando termine de cargar, seleccione el esquema cargado. En la barra de herramientas, seleccione **Copiar dirección URL** para copiar la dirección URL del esquema.

1. Vuelva a Azure Portal donde está abierto el panel **Agregar esquema**. Escriba un nombre para el esquema. Seleccione **Archivo grande (más de 2 MB)** .

   Ahora aparece la casilla **URI de contenido** en lugar de la casilla **Esquema**.

1. En la casilla **URI de contenido**, pegue la dirección URL del esquema. Termine de agregar el esquema.

Una vez que el esquema termine de cargarse, aparecerá en la lista de **esquemas**. En la página **Información general** de su cuenta de integración, en **Artefactos**, también aparece el esquema cargado.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Carga en contenedores sin acceso público

1. Cargue el esquema en la cuenta de almacenamiento. En la ventana de la derecha, seleccione **Cargar**.

1. Cuando termine de cargar, genere una firma de acceso compartido (SAS) para el esquema. En el menú contextual del esquema, seleccione **Obtener firma de acceso compartido**.

1. En el panel **Firma de acceso compartido**, seleccione **Generate container-level shared access signature URI** > **Create** (Generar URI de firma de acceso compartido de nivel de contenedor > Crear). Una vez que se genere la dirección URL de SAS, junto a la casilla **Dirección URL**, seleccione **Copiar**.

1. Vuelva a Azure Portal donde está abierto el panel **Agregar esquema**. Seleccione **Archivo grande**.

   Ahora aparece la casilla **URI de contenido** en lugar de la casilla **Esquema**.

1. En el cuadro **URI de contenido**, pegue el URI de SAS que generó previamente. Termine de agregar el esquema.

Una vez que el esquema termine de cargarse, aparecerá en la lista de **esquemas**. En la página **Información general** de su cuenta de integración, en **Artefactos**, también aparece el esquema cargado.

### <a name="standard-resource"></a>[Recurso estándar](#tab/standard-1)

#### <a name="azure-portal"></a>Azure portal

1. En el menú del recursos de aplicación lógica, en **Configuración**, seleccione **Esquemas**.

1. En la barra de herramientas **Esquemas** del panel, seleccione **Agregar**.

1. En **Agregar esquema**, escriba un nombre único para el esquema.

1. Junto a la casilla **Esquema**, seleccione el icono de carpeta. Seleccione el esquema que desee cargar.

1. Cuando tenga todo listo, seleccione **Aceptar**.

   Una vez que el archivo de esquema termine de cargarse, aparecerá en la lista de **esquemas**. En la página **Información general** de su cuenta de integración, en **Artefactos**, también aparece el esquema cargado.

#### <a name="visual-studio-code"></a>Visual Studio Code

1. En la estructura del proyecto de aplicación lógica, abra la carpeta **Artefactos** y, a continuación, la carpeta **Esquemas**.

1. En la carpeta **Esquemas**, agregue su esquema.

---

## <a name="edit-schemas"></a>Editar esquemas

Para actualizar un esquema existente, debe cargar un nuevo archivo de esquema que tiene todos los cambios que desea. Sin embargo, primero puede descargar el esquema existente para editarla.

### <a name="consumption-resource"></a>[Recurso de consumo](#tab/consumption-2)

1. En [Azure Portal](https://portal.azure.com), abra su cuenta de integración si todavía no está abierta.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Esquemas**.

1. Una vez que se abra el panel **Esquemas**, seleccione su esquema. Para descargar y editar primero el esquema, en la barra de herramientas **Esquemas** del panel, seleccione **Descargar** y guarde el esquema.

1. Cuando tenga todo listo para cargar el esquema actualizado, en la página **Esquemas**, seleccione el esquema que quiera actualizar. En la barra de herramientas **Esquemas** del panel, seleccione **Actualizar**.

1. Busque y seleccione el esquema actualizado que quiere cargar.

   Una vez que el esquema termine de cargarse, aparecerá en la lista de **esquemas**.

### <a name="standard-resource"></a>[Recurso estándar](#tab/standard-2)

1. En [Azure Portal](https://portal.azure.com), abra su recurso de aplicación lógica si todavía no está abierto.

1. En el menú de su recurso de aplicación lógica, en **Configuración**, seleccione **Esquemas**.

1. Una vez que se abra el panel **Esquemas**, seleccione su esquema. Para descargar y editar primero el esquema, en la barra de herramientas **Esquemas** del panel, seleccione **Descargar** y guarde el esquema.

1. En la barra de herramientas **Esquemas** del panel, seleccione **Agregar**.

1. En **Agregar esquema**, escriba un nombre único para el esquema.

1. Junto a la casilla **Esquema**, seleccione el icono de carpeta. Seleccione el esquema que desee cargar.

1. Cuando tenga todo listo, seleccione **Aceptar**.

   Una vez que el esquema termine de cargarse, aparecerá en la lista de **esquemas**.

---

## <a name="delete-schemas"></a>Eliminar esquemas

### <a name="consumption-resource"></a>[Recurso de consumo](#tab/consumption-3)

1. En [Azure Portal](https://portal.azure.com), abra su cuenta de integración si todavía no está abierta.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Esquemas**.

1. Una vez que se abra el panel **Esquemas**, seleccione su esquema y, a continuación, **Eliminar**.

1. Para confirmar que quiere eliminar el esquema, seleccione **Sí**.

### <a name="standard-resource"></a>[Recurso estándar](#tab/standard-3)

1. En [Azure Portal](https://portal.azure.com), abra su recurso de aplicación lógica si todavía no está abierto.

1. En el menú de su recurso de aplicación lógica, en **Configuración**, seleccione **Esquemas**.

1. Una vez que se abra el panel **Esquemas**, seleccione su esquema y, a continuación, **Eliminar**.

1. Para confirmar que quiere eliminar el esquema, seleccione **Sí**.

---

## <a name="next-steps"></a>Pasos siguientes

* [Validación de XML para flujos de trabajo en Azure Logic Apps](logic-apps-enterprise-integration-xml-validation.md)
* [Transformación de XML para flujos de trabajo en Azure Logic Apps](logic-apps-enterprise-integration-transform.md)