---
title: Transformación de XML con asignaciones de XSLT
description: Incorporación de asignaciones XSLT para transformar XML en Azure Logic Apps con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/04/2021
ms.openlocfilehash: 86e7c07ba3ade77ec3913178a8dc24bb135c0a54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733773"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Transformación de XML con asignaciones en Azure Logic Apps con Enterprise Integration Pack

Para transferir datos XML entre formatos en escenarios de integración empresarial en Azure Logic Apps, la aplicación lógica puede usar asignaciones o, más específicamente, asignaciones de Lenguaje de transformación basado en hojas de estilo (XSLT). Una asignación es un documento XML que describe cómo convertir datos de un documento XML en otro formato.

Por ejemplo, suponga que recibe periódicamente pedidos o facturas B2B de un cliente que usa el formato de fecha AAAMMDD, pero su organización usa el formato de fecha MMDDAAA. Puede definir y usar una asignación para transformar el formato de fecha AAAMMDD en MMDDAAA antes de almacenar los detalles de los pedidos o las facturas en la base de datos de actividades de clientes.

> [!NOTE]
> El servicio Azure Logic Apps asigna memoria finita para procesar transformaciones XML. Si crea aplicaciones lógicas basadas en el tipo de recurso **Aplicación lógica (consumo)** y las transformaciones de asignación o carga tienen un consumo elevado de memoria, podrían producirse errores en estas transformaciones, lo que provocaría errores de memoria insuficiente. Para evitar este escenario, tenga en cuenta estas opciones:
>
> * Edite las asignaciones o las cargas para reducir el consumo de memoria.
>
> * Cree las aplicaciones lógicas con el tipo de recurso **Aplicación lógica (estándar)** .
>
>   Estos flujos de trabajo se ejecutan en Azure Logic Apps de inquilino único, que ofrece opciones dedicadas y flexibles para recursos de proceso y memoria. Para más información, revise la siguiente documentación:
>
>   * [¿Qué es Azure Logic Apps?: tipo de recurso y entornos de host](logic-apps-overview.md#resource-type-and-host-environment-differences)
>   * [Creación de un flujo de trabajo de integración con Azure Logic Apps (estándar) de inquilino único](create-single-tenant-workflows-azure-portal.md)
>   * [Comparación de las opciones de un solo inquilino, multiinquilino y entorno del servicio de integración para Azure Logic Apps](single-tenant-overview-compare.md)
>   * [Modelos de medición de uso, facturación y precios para Azure Logic Apps](logic-apps-pricing.md)

Para conocer los límites relacionados con las cuentas de integración y artefactos como asignaciones, revise [Información de límites y configuración para Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si aún no tiene una, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/).

* Una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) donde almacena las asignaciones y otros artefactos para soluciones negocio a negocio (B2B) y de integración empresarial.

* Si el mapa hace referencia a un ensamblado externo, necesita un ensamblado de 64 bits. El servicio de transformación ejecuta un proceso de 64 bits, por lo que no se admiten ensamblados de 32 bits. Si tiene el código fuente de un ensamblado de 32 bits, vuelva a compilarlo en un ensamblado de 64 bits. Si no tiene el código fuente, pero ha recibido el binario por parte de un proveedor de terceros, obtenga la versión de 64 bits de ese proveedor. Por ejemplo, algunos proveedores proporcionan ensamblados en paquetes que incluyen las versiones de 32 y 64 bits. Si puede elegir, use la versión de 64 bits.

* Si la asignación hace referencia a un ensamblado externo, debe cargar *tanto el ensamblado como la asignación* en la cuenta de integración. Asegúrese de [*cargar primero el ensamblado*](#add-assembly) y luego la asignación que hace referencia a este.

  Si el ensamblado es de 2 MB o menos, puede agregarlo a la cuenta de integración *directamente* desde Azure Portal. Pero si el ensamblado o la asignación tiene más de 2 MB, pero menos del [límite de tamaño de ensamblados o asignaciones](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), existen estas opciones:

  * En el caso de los ensamblados, necesita un contenedor de blobs de Azure donde pueda cargar el ensamblado y la ubicación de ese contenedor. De ese modo, puede proporcionar esa ubicación más adelante cuando agregue el ensamblado a la cuenta de integración. Para esta tarea, necesita estos elementos:

    | Elemento | Descripción |
    |------|-------------|
    | [Cuenta de Almacenamiento de Azure](../storage/common/storage-account-overview.md) | En esta cuenta, cree un contenedor de blobs de Azure para el ensamblado. Aprenda a [crear una cuenta de almacenamiento](../storage/common/storage-account-create.md). |
    | Contenedor de blobs | En este contenedor, puede cargar el ensamblado. También necesitará la ubicación de este contenedor cuando agregue el ensamblado a la cuenta de integración. Aprenda a [crear un contenedor de blobs](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Explorador de Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Esta herramienta ayuda a administrar más fácilmente las cuentas de almacenamiento y los contenedores de blobs. Para usar el Explorador de Storage, [descargue e instale el Explorador de Azure Storage](https://www.storageexplorer.com/). Luego, para conectarlo con su cuenta de almacenamiento, siga los pasos que aparecen en [Introducción al Explorador de Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Para más información, vea esta [Inicio rápido: Cree un blob en el almacenamiento de objetos con el Explorador de Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>O bien, en Azure Portal, seleccione la cuenta de almacenamiento. En el menú de la cuenta de almacenamiento, seleccione **Explorador de Storage**. |
    |||

  * En el caso de las asignaciones, actualmente puede agregar asignaciones más grandes si usa las [asignaciones de la API REST de Azure Logic Apps](/rest/api/logic/maps/createorupdate).

No necesita una aplicación lógica al crear y agregar asignaciones. Sin embargo, al usar una asignación, la aplicación lógica se debe vincular a una cuenta de integración donde se almacena esa asignación. Aprenda a [vincular aplicaciones lógicas a cuentas de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Si aún no tiene una aplicación lógica, obtenga información sobre [cómo crear aplicaciones lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Incorporación de ensamblados de referencia

1. En [Azure Portal](https://portal.azure.com), inicie sesión con las credenciales de su cuenta de Azure.

1. En el cuadro de búsqueda principal de Azure, escriba `integration accounts` y seleccione **Cuentas de integración**.

1. Seleccione la cuenta de integración en la que quiere agregar el ensamblado, por ejemplo:

1. En el menú de la cuenta de integración, seleccione **Información general**. En **Configuración**, seleccione **Ensamblados**.

1. En la barra de herramientas del panel **Ensamblados**, seleccione **Agregar**.

Según el tamaño del archivo de ensamblado, siga los pasos para cargar un ensamblado de [hasta 2 MB](#smaller-assembly) o de [más de 2 MB, pero solo hasta 8 MB](#larger-assembly). Para conocer los límites de las cantidades de ensamblados en cuentas de integración, revise [Límites y configuración de Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Si cambia el ensamblado, también debe actualizar la asignación tenga o no cambios.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Incorporación de ensamblados de hasta 2 MB

1. En **Agregar ensamblado**, escriba un nombre para el ensamblado. Mantenga seleccionado **Archivo pequeño**. Junto a la casilla **Ensamblado**, seleccione el icono de carpeta. Busque y seleccione el ensamblado que está cargando.

   Una vez que haya seleccionado el ensamblado, la propiedad **Nombre del ensamblado** mostrará automáticamente el nombre de archivo del ensamblado.

1. Cuando esté listo, seleccione **Aceptar**.

   Una vez que termina de cargarse el archivo de ensamblado, el ensamblado aparece en la lista de **ensamblados**. En el panel **Información general** de la cuenta de integración, en **Artefactos**, también aparece el ensamblado cargado.

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Incorporación de ensamblados de más de 2 MB

Para agregar ensamblados de mayor tamaño, puede cargar el ensamblado en un contenedor de blobs de Azure de la cuenta de almacenamiento de Azure. Los pasos que debe seguir para agregar ensamblados dependen de si el contenedor de blobs tiene acceso de lectura público. En primer lugar, revise si el contenedor de blobs tiene o no acceso de lectura público con estos pasos: [Establecimiento del nivel de acceso público para un contenedor de blobs](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Comprobación del nivel de acceso de un contenedor

1. Abra el Explorador de Azure Storage. En la ventana del Explorador, expanda la suscripción de Azure si todavía no está expandida.

1. Expanda **Cuentas de almacenamiento** > {*su-cuenta-de-almacenamiento*} > **Contenedores de blobs**. Seleccione el contenedor de blobs.

1. En el menú contextual del contenedor de blobs, seleccione **Establecer nivel de acceso público**.

   * Si el contenedor de blobs tiene como mínimo acceso público, seleccione **Cancelar** y siga los pasos que aparecen más adelante en esta página: [Carga en contenedores con acceso público](#public-access-assemblies).

     ![Acceso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Si el contenedor de blobs no tiene acceso público, seleccione **Cancelar** y siga los pasos que aparecen más adelante en esta página: [Carga en contenedores sin acceso público](#no-public-access-assemblies).

     ![Sin acceso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Carga en contenedores con acceso público

1. Cargue el ensamblado en la cuenta de almacenamiento. En la ventana del lado derecho, seleccione **Cargar**.

1. Cuando termine de cargar, seleccione el ensamblado cargado. En la barra de herramientas, seleccione **Copiar dirección URL** para copiar la dirección URL del ensamblado.

1. Vuelva a Azure Portal donde está abierto el panel **Agregar ensamblado**. Escriba un nombre para el esquema. Seleccione **Large file (larger than 2 MB)** (Archivo de gran tamaño [más de 2 MB]).

   Ahora aparece la casilla **URI de contenido** en lugar de la casilla **Ensamblado**.

1. En la casilla **URI de contenido**, pegue la dirección URL del ensamblado. Termine de agregar el ensamblado.

   Una vez que termina de cargarse el ensamblado, el esquema aparece en la lista de **ensamblados**. En el panel **Información general** de la cuenta de integración, en **Artefactos**, también aparece el ensamblado cargado.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Carga en contenedores sin acceso público

1. Cargue el ensamblado en la cuenta de almacenamiento. En la ventana del lado derecho, seleccione **Cargar**.

1. Cuando termine de cargar, genere una firma de acceso compartido (SAS) para el ensamblado. En el menú contextual del ensamblado, seleccione **Obtener firma de acceso compartido**.

1. En el panel **Firma de acceso compartido**, seleccione **Generate container-level shared access signature URI** > **Create** (Generar URI de firma de acceso compartido de nivel de contenedor > Crear). Una vez que se genere la dirección URL de SAS, junto a la casilla **Dirección URL**, seleccione **Copiar**.

1. Vuelva a Azure Portal donde está abierto el panel **Agregar ensamblado**. Escriba un nombre para el esquema. Seleccione **Large file (larger than 2 MB)** (Archivo de gran tamaño [más de 2 MB]).

   Ahora aparece la casilla **URI de contenido** en lugar de la casilla **Ensamblado**.

1. En el cuadro **URI de contenido**, pegue el URI de SAS que generó previamente. Termine de agregar el ensamblado.

Una vez que termina de cargarse el ensamblado, el ensamblado aparece en la lista de **esquemas**. En la página **Información general** de la cuenta de integración, en **Artefactos**, también aparece el ensamblado cargado.

## <a name="create-maps"></a>Creación de asignaciones

Para crear un documento de Lenguaje de transformación basado en hojas de estilo (XSLT) que se pueda usar como asignación, puede usar Visual Studio 2015 o 2019 para crear un proyecto de integración mediante [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). En este proyecto, puede crear un archivo de asignación de integración que le permite asignar de manera visual elementos entre dos archivos de esquema XML. Una vez que cree este proyecto, obtendrá un documento XSLT. Para conocer los límites de las cantidades de asignaciones en cuentas de integración, revise [Límites y configuración de Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

## <a name="add-maps"></a>Incorporación de asignaciones

Después de cargar cualquier ensamblado al que hace referencia la asignación, ahora puede cargar la asignación.

1. Si todavía no inicia sesión en [Azure Portal](https://portal.azure.com), hágalo con las credenciales de su cuenta de Azure.

1. Si su cuenta de integración todavía no está abierta, en el cuadro de búsqueda principal de Azure, escriba `integration accounts` y seleccione **Cuentas de integración**.

1. Seleccione la cuenta integración en la que quiere agregar la asignación.

1. En el menú de la cuenta de integración, seleccione **Información general**. En **Configuración**, seleccione **Asignaciones**.

1. En la barra de herramientas del panel **Asignaciones**, seleccione **Agregar**.

1. Continúe para agregar una asignación de [hasta 2 MB](#smaller-map) o de [más de 2 MB](#larger-map).

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Incorporación de asignaciones de hasta 2 MB

1. En **Agregar asignación**, escriba un nombre único para la asignación.

1. En **Tipo de asignación**, seleccione el tipo, por ejemplo: **Liquid**, **XSLT**, **XSLT 2.0** o **XSLT 3.0**.

1. Junto a la casilla **Asignación**, seleccione el icono de carpeta. Busque y seleccione la asignación que está cargando, por ejemplo:

   Si deja vacía la propiedad **Name**, el nombre de archivo de la asignación aparecerá automáticamente en esa propiedad en cuanto seleccione el archivo de asignación.

1. Cuando esté listo, seleccione **Aceptar**.

   Cuando el archivo de asignación termine de cargarse, la asignación aparecerá en la lista de **asignaciones**.

   En la página **Información general** de la cuenta de integración, en **Artefactos**, también aparece la asignación cargada.

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Incorporación de asignaciones de más de 2 MB

Actualmente, para agregar asignaciones más grandes, use las [asignaciones de la API REST de Azure Logic Apps](/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-side window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-side window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Edición de asignaciones

Para actualizar una asignación existente, debe cargar un nuevo archivo de asignación que tiene todos los cambios que desea. Sin embargo, primero puede descargar la asignación existente para editarla.

1. En [Azure Portal](https://portal.azure.com), abra la cuenta de integración, si todavía no está abierta.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Asignaciones**.

1. Una vez que se abra el panel **Asignaciones**, seleccione su asignación. Para descargar y editar primero la asignación, en la barra de herramientas del panel **Asignaciones**, seleccione **Descargar** y guarde la asignación.

1. Cuando esté listo para cargar la asignación actualizada, en el panel **Asignaciones**, seleccione la asignación que quiere actualizar. En la barra de herramientas del panel **Asignaciones**, seleccione **Actualizar**.

1. Busque y seleccione la asignación actualizada que quiere cargar.

   Cuando el archivo de asignación termine de cargarse, la asignación actualizada aparecerá en la lista de **asignaciones**.

## <a name="delete-maps"></a>Eliminación de asignaciones

1. En [Azure Portal](https://portal.azure.com), busque y abra la cuenta de integración, si todavía no está abierto.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Asignaciones**.

1. Una vez que se abra el panel **Asignaciones**, elija su asignación y seleccione **Eliminar**.

1. Para confirmar que quiere eliminar la asignación, seleccione **Sí**.

## <a name="next-steps"></a>Pasos siguientes

* [Más información acerca de Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Más información sobre los esquemas](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Más información sobre las transformaciones](../logic-apps/logic-apps-enterprise-integration-transform.md)
