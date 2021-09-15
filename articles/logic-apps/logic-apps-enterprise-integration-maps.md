---
title: Adición de asignaciones XSLT para transformaciones XML
description: Cree y agregue asignaciones XSLT para transformar XML en Azure Logic Apps con Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: c597a7d44b620ee33acec028812aa2d1651283ff
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123100452"
---
# <a name="add-xslt-maps-for-xml-transformation-in-azure-logic-apps"></a>Adición de asignaciones XSLT para la transformación XML en Azure Logic Apps

Para transferir datos XML entre formatos en escenarios de integración empresarial de Azure Logic Apps, el recurso de aplicación lógica puede usar asignaciones o, más específicamente, asignaciones de Lenguaje de transformación basado en hojas de estilo (XSLT). Una asignación es un documento XML que describe cómo convertir datos de un documento XML en otro formato.

Por ejemplo, imagine que recibe periódicamente pedidos o facturas B2B de un cliente que usa el formato de fecha añoMesDía (AAAAMMDD), mientras que su organización emplea el formato de fecha mesDíaAño (MMDDAAAA). Puede definir y usar una asignación que transforme el formato de fecha AAAMMDD en MMDDAAA antes de almacenar los detalles de los pedidos o las facturas en la base de datos de actividades de clientes.

Con una asignación, puede definir una transformación sencilla, como copiar un nombre y una dirección de un documento a otro. O bien puede crear transformaciones más complejas mediante las operaciones de asignación integradas.

> [!NOTE]
> Azure Logic Apps asigna memoria finita para procesar transformaciones XML. Si crea aplicaciones lógicas basadas en el tipo de recurso **Aplicación lógica (consumo)** y las transformaciones de asignación o carga tienen un consumo elevado de memoria, podrían producirse errores en estas transformaciones, lo que provocaría errores de memoria insuficiente. Para evitar este escenario, tenga en cuenta estas opciones:
>
> * Edite las asignaciones o las cargas para reducir el consumo de memoria.
>
> * Cree las aplicaciones lógicas con el tipo de recurso **Aplicación lógica (estándar)** .
>
>   Estos flujos de trabajo se ejecutan en Azure Logic Apps de inquilino único, que ofrece opciones dedicadas y flexibles para recursos de proceso y memoria. 
>   Sin embargo, el tipo de recurso de aplicación lógica estándar actualmente no admite la referencia a ensamblados externos desde asignaciones. Además, actualmente solo se admite el Lenguaje de transformación basado en hojas de estilo (XSLT) 1.0.

Si no está familiarizado con las aplicaciones lógicas, vea la siguiente documentación:

* [¿Qué es Azure Logic Apps?: tipo de recurso y entornos de host](logic-apps-overview.md#resource-type-and-host-environment-differences)

* [Creación de un flujo de trabajo de integración con Azure Logic Apps (estándar) de inquilino único](create-single-tenant-workflows-azure-portal.md)

* [Creación de flujos de trabajo de un solo inquilino](create-single-tenant-workflows-azure-portal.md)

* [Modelos de medición de uso, facturación y precios para Azure Logic Apps](logic-apps-pricing.md)

## <a name="limits"></a>Límites

* En el caso de recursos de aplicación lógica **estándar**, no existen límites con respecto al tamaño de los archivos de asignación.

* En el caso de los recursos de aplicación lógica de **consumo**, existen límites para las cuentas de integración y los artefactos, como las asignaciones. Para más información, consulte el artículo [Información de límites y configuración de Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene suscripción, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Si usa el tipo de recurso **Logic App (Standard)** (Aplicación lógica [estándar]), no necesita una cuenta de integración, sino que puede agregar asignaciones directamente al recurso de aplicación lógica en Azure Portal o Visual Studio Code. Actualmente solo se admite XSLT 1.0. Luego puede usar estas asignaciones en varios flujos de trabajo dentro del *mismo recurso de aplicación lógica*.

* Si usa el tipo de recurso **Logic App (Consumption)** (Aplicación lógica [consumo]), debe tener un [recurso de cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) donde poder almacenar las asignaciones y otros artefactos que va a usar en las soluciones de integración empresarial y de negocio a negocio (B2B). Este recurso tiene que satisfacer los siguientes requisitos:

  * Estar asociado a la misma suscripción de Azure que el recurso de aplicación lógica.

  * Existir en la misma ubicación o región de Azure que el recurso de aplicación lógica donde piensa usar la acción **Transformar XML**.

  * Estar [vinculado](logic-apps-enterprise-integration-create-integration-account.md#link-account) al recurso de aplicación lógica donde quiere usar las asignaciones.

    Para crear y agregar asignaciones para usarlas en flujos de trabajo de aplicación lógica de consumo, aún no es necesario un recurso de aplicación lógica. Sin embargo, cuando esté listo para usar esas asignaciones en los flujos de trabajo, el recurso de aplicación lógica necesitará una cuenta de integración vinculada que almacene esas asignaciones.

* Aunque **Logic App (Consumption)** (Aplicación lógica [consumo]) admite la referencia a ensamblados externos desde las asignaciones, **Logic App (Standard)** (Aplicación lógica [estándar]) no admite esta funcionalidad. La referencia a un ensamblado permite llamadas directas desde asignaciones XSLT a código .NET personalizado.

  * Necesita un ensamblado de 64 bits. El servicio de transformación ejecuta un proceso de 64 bits, por lo que no se admiten ensamblados de 32 bits. Si tiene el código fuente de un ensamblado de 32 bits, vuelva a compilarlo en un ensamblado de 64 bits. Si no tiene el código fuente, pero ha recibido el binario por parte de un proveedor de terceros, obtenga la versión de 64 bits de ese proveedor. Por ejemplo, algunos proveedores proporcionan ensamblados en paquetes que incluyen las versiones de 32 y 64 bits. Si puede elegir, use la versión de 64 bits.

  * Debe cargar *tanto el ensamblado como la asignación* en un orden específico en la cuenta de integración. Asegúrese de [*cargar primero el ensamblado*](#add-assembly) y luego la asignación que hace referencia a este.

  * Si el ensamblado es de *2 MB o menos*, puede agregar este y la asignación a la cuenta de integración [directamente](#smaller-map) desde Azure Portal. Sin embargo, si el ensamblado o la asignación es mayor que 2 MB, pero no mayor que el [límite de tamaño para ensamblados o asignaciones](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), puede usar un contenedor de blobs de Azure donde puede cargar el ensamblado y la ubicación de ese contenedor. De ese modo, puede proporcionar esa ubicación más adelante cuando agregue el ensamblado a la cuenta de integración. Para esta tarea, necesita estos elementos:

    | Elemento | Descripción |
    |------|-------------|
    | [Cuenta de Almacenamiento de Azure](../storage/common/storage-account-overview.md) | En esta cuenta, cree un contenedor de blobs de Azure para el ensamblado. Aprenda a [crear una cuenta de almacenamiento](../storage/common/storage-account-create.md). |
    | Contenedor de blobs | En este contenedor, puede cargar el ensamblado. También necesitará la ubicación del URI de contenido de este contenedor cuando agregue el ensamblado a la cuenta de integración. Aprenda a [crear un contenedor de blobs](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Explorador de Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Esta herramienta ayuda a administrar más fácilmente las cuentas de almacenamiento y los contenedores de blobs. Para usar el Explorador de Storage, [descargue e instale el Explorador de Azure Storage](https://www.storageexplorer.com/). Luego, para conectarlo con su cuenta de almacenamiento, siga los pasos que aparecen en [Introducción al Explorador de Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Para más información, vea esta [Inicio rápido: Cree un blob en el almacenamiento de objetos con el Explorador de Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>O bien, en Azure Portal, seleccione la cuenta de almacenamiento. En el menú de la cuenta de almacenamiento, seleccione **Explorador de Storage**. |
    |||

  * Si quiere agregar asignaciones más grandes para recursos de aplicación lógica de consumo, también puede usar las [asignaciones de API REST de Azure Logic Apps](/rest/api/logic/maps/createorupdate). Sin embargo, en el caso de los recursos de aplicación lógica estándar, la API REST de Azure Logic Apps no está disponible actualmente.

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies-consumption-resource-only"></a>Adición de ensamblados a los que se hace referencia (solo recursos de consumo)

1. En [Azure Portal](https://portal.azure.com), inicie sesión con las credenciales de su cuenta de Azure.

1. En el cuadro de búsqueda principal de Azure, escriba `integration accounts` y seleccione **Cuentas de integración**.

1. Seleccione la cuenta integración en la que quiere agregar el ensamblado.

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

     ![Acceso público](media/logic-apps-enterprise-integration-maps/azure-blob-container-public-access.png)

   * Si el contenedor de blobs no tiene acceso público, seleccione **Cancelar** y siga los pasos que aparecen más adelante en esta página: [Carga en contenedores sin acceso público](#no-public-access-assemblies).

     ![Sin acceso público](media/logic-apps-enterprise-integration-maps/azure-blob-container-no-public-access.png)

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

<a name="create-maps"></a>

## <a name="create-maps"></a>Creación de asignaciones

Para crear un documento de Lenguaje de transformación basado en hojas de estilo (XSLT) que se pueda usar como asignación, puede utilizar Visual Studio 2015 o 2019 para crear un proyecto de integración mediante el [SDK de integración empresarial](https://aka.ms/vsmapsandschemas). En este proyecto, puede crear un archivo de asignación de integración que le permite asignar de manera visual elementos entre dos archivos de esquema XML. Una vez que cree este proyecto, obtendrá un documento XSLT. Para conocer los límites de las cantidades de asignaciones en cuentas de integración, revise [Límites y configuración de Azure Logic Apps](logic-apps-limits-and-config.md#artifact-number-limits).

La asignación debe tener los siguientes atributos y una sección `CDATA` que contenga la llamada al código de ensamblado:

* `name` es el nombre de ensamblado personalizado.

* `namespace` es el espacio de nombres en el ensamblado que incluye el código personalizado.

En este ejemplo se muestra una asignación que hace referencia a un ensamblado denominado `circumference` y llama al método `XslUtilitiesLib` desde el ensamblado.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
<msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
</msxsl:script>
<xsl:template match="data">
<circles>
    <xsl:for-each select="circle">
        <circle>
            <xsl:copy-of select="node()"/>
                <circumference>
                    <xsl:value-of select="user:circumference(radius)"/>
                </circumference>
        </circle>
    </xsl:for-each>
</circles>
</xsl:template>
</xsl:stylesheet>
```

## <a name="tools-and-capabilities-for-maps"></a>Herramientas y funcionalidades para asignaciones

* Al crear una asignación mediante Visual Studio y el [SDK de integración empresarial](https://aka.ms/vsmapsandschemas), se trabaja con una representación gráfica de la asignación, que muestra todas las relaciones y vínculos que se crean.

* Puede realizar una copia de datos directa entre los esquemas. El [SDK de integración empresarial](https://aka.ms/vsmapsandschemas) para Visual Studio incluye un asignador que hace que esta tarea sea tan sencilla como dibujar una línea que conecta los elementos del esquema XML de origen con sus homólogos en el esquema XML de destino.

* Hay disponibles operaciones o funciones para varias asignaciones, incluidas las funciones de cadena o de fecha y hora, entre otras.  

* Para agregar un mensaje XML de ejemplo, puede usar la funcionalidad de prueba de asignación. Con un solo clic, puede probar la asignación que creó y revisar la salida generada.

## <a name="add-maps"></a>Incorporación de asignaciones

### <a name="consumption-resource"></a>[Recurso de consumo](#tab/consumption-1)

Después de cargar cualquier ensamblado al que hace referencia la asignación, ahora puede cargar la asignación.

1. En Azure Portal, si su cuenta de integración todavía no está abierta, en el cuadro de búsqueda principal de Azure, escriba `integration accounts` y seleccione **Cuentas de integración**.

1. Seleccione la cuenta integración en la que quiere agregar la asignación.

1. En el menú de la cuenta de integración, seleccione **Información general**. En **Configuración**, seleccione **Asignaciones**.

1. En la barra de herramientas del panel **Asignaciones**, seleccione **Agregar**.

1. Continúe para agregar una asignación de [hasta 2 MB](#smaller-map) o de [más de 2 MB](#larger-map).

<a name="smaller-map"></a>

#### <a name="add-maps-up-to-2-mb"></a>Incorporación de asignaciones de hasta 2 MB

1. En **Agregar asignación**, escriba un nombre único para la asignación.

1. En **Tipo de asignación**, seleccione el tipo, por ejemplo: **Liquid**, **XSLT**, **XSLT 2.0** o **XSLT 3.0**.

1. Junto a la casilla **Asignación**, seleccione el icono de carpeta. Seleccione la asignación que quiere cargar.

   Si deja vacía la propiedad **Name**, el nombre de archivo de la asignación aparecerá automáticamente en esa propiedad en cuanto seleccione el archivo de asignación.

1. Cuando esté listo, seleccione **Aceptar**.

   Cuando el archivo de asignación termine de cargarse, la asignación aparecerá en la lista de **asignaciones**. En la página **Información general** de la cuenta de integración, en **Artefactos**, también aparece la asignación cargada.

<a name="larger-map"></a>

#### <a name="add-maps-more-than-2-mb"></a>Incorporación de asignaciones de más de 2 MB

Actualmente, para agregar asignaciones más grandes, use las [asignaciones de la API REST de Azure Logic Apps](/rest/api/logic/maps/createorupdate).

### <a name="standard-resource"></a>[Recurso estándar](#tab/standard-1)

#### <a name="azure-portal"></a>Azure portal

1. En el menú del recurso de aplicación lógica, en **Configuración**, seleccione **Asignaciones**.

1. En la barra de herramientas del panel **Asignaciones**, seleccione **Agregar**.

1. En **Agregar asignación**, escriba un nombre único para la asignación e incluya el nombre de la extensión `.xslt`.

1. Junto a la casilla **Asignación**, seleccione el icono de carpeta. Seleccione la asignación que quiere cargar.

1. Cuando esté listo, seleccione **Aceptar**.

   Cuando el archivo de asignación termine de cargarse, la asignación aparecerá en la lista de **asignaciones**. En la página **Información general** de la cuenta de integración, en **Artefactos**, también aparece la asignación cargada.

#### <a name="visual-studio-code"></a>Visual Studio Code

1. En la estructura del proyecto de aplicación lógica, abra la carpeta **Artefactos** y, luego, la carpeta **Asignaciones**.

1. En la carpeta **Asignaciones**, agregue su asignación.

---

## <a name="edit-maps"></a>Edición de asignaciones

Para actualizar una asignación existente, debe cargar un nuevo archivo de asignación que tiene todos los cambios que desea. Sin embargo, primero puede descargar la asignación existente para editarla.

### <a name="consumption-resource"></a>[Recurso de consumo](#tab/consumption-2)

1. En [Azure Portal](https://portal.azure.com), abra la cuenta de integración, si todavía no está abierta.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Asignaciones**.

1. Una vez que se abra el panel **Asignaciones**, seleccione su asignación. Para descargar y editar primero la asignación, en la barra de herramientas del panel **Asignaciones**, seleccione **Descargar** y guarde la asignación.

1. Cuando esté listo para cargar la asignación actualizada, en el panel **Asignaciones**, seleccione la asignación que quiere actualizar. En la barra de herramientas del panel **Asignaciones**, seleccione **Actualizar**.

1. Busque y seleccione la asignación actualizada que quiere cargar.

   Cuando el archivo de asignación termine de cargarse, la asignación actualizada aparecerá en la lista de **asignaciones**.

### <a name="standard-resource"></a>[Recurso estándar](#tab/standard-2)

1. En [Azure Portal](https://portal.azure.com), abra su recurso de aplicación lógica si todavía no está abierto.

1. En el menú del recurso de aplicación lógica, en **Configuración**, seleccione **Asignaciones**.

1. Una vez que se abra el panel **Asignaciones**, seleccione su asignación. Para descargar y editar primero la asignación, en la barra de herramientas del panel **Asignaciones**, seleccione **Descargar** y guarde la asignación.

1. En la barra de herramientas del panel **Asignaciones**, seleccione **Agregar**.

1. En **Agregar asignación**, escriba un nombre único para la asignación e incluya el nombre de la extensión `.xslt`.

1. Junto a la casilla **Asignación**, seleccione el icono de carpeta. Seleccione la asignación que quiere cargar.

1. Cuando esté listo, seleccione **Aceptar**.

   Cuando el archivo de asignación termine de cargarse, la asignación actualizada aparecerá en la lista de **asignaciones**.

---

## <a name="delete-maps"></a>Eliminación de asignaciones

### <a name="consumption-resource"></a>[Recurso de consumo](#tab/consumption-3)

1. En [Azure Portal](https://portal.azure.com), abra la cuenta de integración, si todavía no está abierta.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Asignaciones**.

1. Una vez que se abra el panel **Asignaciones**, elija su asignación y seleccione **Eliminar**.

1. Para confirmar que quiere eliminar la asignación, seleccione **Sí**.

### <a name="standard-resource"></a>[Recurso estándar](#tab/standard-3)

1. En [Azure Portal](https://portal.azure.com), abra su recurso de aplicación lógica si todavía no está abierto.

1. En el menú del recurso de aplicación lógica, en **Configuración**, seleccione **Asignaciones**.

1. Una vez que se abra el panel **Asignaciones**, elija su asignación y seleccione **Eliminar**.

1. Para confirmar que quiere eliminar la asignación, seleccione **Sí**.

---

## <a name="next-steps"></a>Pasos siguientes

* [Transformación XML para flujos de trabajo en Azure Logic Apps](logic-apps-enterprise-integration-transform.md)
* [Validación de XML para flujos de trabajo en Azure Logic Apps](logic-apps-enterprise-integration-xml-validation.md)
