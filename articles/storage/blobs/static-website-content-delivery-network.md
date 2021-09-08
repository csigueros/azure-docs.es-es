---
title: 'Integración de un sitio web estático con Azure CDN: Azure Storage'
description: Aprenda a almacenar en caché el contenido de sitios web estáticos de una cuenta de Azure Storage mediante Azure Content Delivery Network (CDN).
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: 1c17fceb267018901975f942f7cfc804f450ead9
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867647"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integración de un sitio web estático con Azure CDN

Puede habilitar [Azure Content Delivery Network (CDN)](../../cdn/cdn-overview.md) para almacenar en caché el contenido de un [sitio web estático](storage-blob-static-website.md) que esté hospedado en una cuenta de Azure Storage. Puede usar Azure CDN para configurar el punto de conexión del dominio personalizado de su sitio web estático, aprovisionar certificados TLS/SSL personalizados y configurar reglas de reescritura personalizadas. La configuración de Azure CDN da como resultado cargos adicionales, pero proporciona latencias bajas y coherentes a su sitio web desde cualquier parte del mundo. Azure CDN también proporciona el cifrado TLS con su propio certificado. 

Para más información sobre los precios de Azure CDN, consulte [Precios de Azure CDN](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Habilitación de Azure CDN para un sitio web estático

Puede habilitar Azure CDN para su sitio web estático directamente desde su cuenta de almacenamiento. Si quiere especificar valores de configuración avanzados para el punto de conexión de CDN, como la [optimización de la descarga de archivos grandes](../../cdn/cdn-optimization-overview.md#large-file-download), puede usar la [extensión Azure CDN](../../cdn/cdn-create-new-endpoint.md) para crear un perfil y un punto de conexión de CDN.

1. Busque la cuenta de almacenamiento en Azure Portal y muestre la información general de la cuenta.

1. En el menú **Seguridad y redes**, seleccione **Azure CDN** para abrir la página **Azure CDN**:

    ![Creación de un punto de conexión de CDN](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. En la sección **Perfil de CDN**, especifique si desea crear un nuevo perfil de CDN o usar uno existente. Un perfil de CDN es una colección de puntos de conexión de CDN que comparten el mismo plan de tarifa y proveedor. A continuación, escriba un nombre para la red CDN que sea único dentro de su suscripción.

1. Especifique un plan de tarifa para el punto de conexión de CDN. Para más información sobre los precios, consulte [Precios de Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/). Para obtener información sobre las características disponibles en cada nivel de servicio, consulte [Comparación de las características de los productos de Azure CDN](../../cdn/cdn-features.md).

1. En el campo **Nombre del punto de conexión de CDN**, especifique el nombre del punto de conexión de CDN. El punto de conexión de red CDN debe ser único en Azure y proporcionar la primera parte de la dirección URL del punto de conexión. El formulario valida que el nombre del punto de conexión es único.

1. Escriba el punto de conexión del sitio web estático en el campo **Nombre de host de origen**. 

   Para buscar el punto de conexión del sitio web estático, vaya al valor **Sitio web estático** de la cuenta de almacenamiento.  Copie el punto de conexión principal y péguelo en la configuración de CDN.

   > [!IMPORTANT]
   > Asegúrese de quitar el identificador de protocolo (*por ejemplo*, HTTPS) y la barra diagonal final de la dirección URL. Por ejemplo, si el punto de conexión del sitio web estático es `https://mystorageaccount.z5.web.core.windows.net/`, debe especificar `mystorageaccount.z5.web.core.windows.net` en el campo **Nombre de host de origen**.

   La siguiente imagen muestra una configuración de punto de conexión de ejemplo:

   ![Captura de pantalla que muestra la configuración de un punto de conexión de CDN de ejemplo](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Seleccione **Crear** y espere a que la red CDN lo aprovisione. Después de que se crea el punto de conexión, aparece en la lista de puntos de conexión. (Si hay algún error en el formulario, aparece un signo de admiración junto al campo).

1. Para comprobar que el punto de conexión de CDN está configurado correctamente, haga clic en él para ir a su configuración. Desde la información general de la red CDN de la cuenta de almacenamiento, busque el nombre de host del punto de conexión y vaya al punto de conexión, como se muestra en la siguiente imagen. El formato de punto de conexión de CDN será similar a `https://staticwebsitesamples.azureedge.net`.

    ![Captura de pantalla que muestra la información general del punto de conexión de CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. Una vez que ha aprovisionado el punto de conexión de CDN, si navega hasta dicho punto de conexión, se muestra el contenido del archivo index.html que ha cargado previamente en su sitio web estático.

1. Para revisar la configuración del origen del punto de conexión de CDN, vaya a **Origen** en la sección **Configuración** del punto de conexión de CDN. Verá que el valor del campo **Tipo de origen** es *Origen personalizado* y que el campo **Nombre de host de origen** muestra el punto de conexión del sitio web estático.

    ![Captura de pantalla muestra la configuración del origen del punto de conexión de CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Eliminación de contenido de Azure CDN

Si ya no desea almacenar en caché un objeto en Azure CDN, puede realizar uno de los siguientes pasos:

* Convierta el contenedor en privado en lugar de público. Para más información, consulte [Administración del acceso de lectura anónimo a contenedores y blobs](./anonymous-read-access-configure.md).
* Deshabilite o elimine el punto de conexión de CDN mediante Azure Portal.
* Modifique su servicio hospedado para no seguir respondiendo a las solicitudes del objeto.

Un objeto que ya está almacenado en caché en Azure CDN permanece en caché hasta que cumple el período de vida del objeto o hasta que se [purgue](../../cdn/cdn-purge-endpoint.md) el punto de conexión. Al cumplir el período de vida, Azure CDN determina si el punto de conexión de CDN sigue siendo válido y si el objeto sigue siendo accesible de forma anónima. En caso negativo, el objeto dejará de estar almacenado en caché.

## <a name="next-steps"></a>Pasos siguientes

(Opcional) Adición de un dominio personalizado a un punto de conexión de Azure CDN. Consulte [Tutorial: Incorporación de un dominio personalizado a un punto de conexión de Azure CDN](../../cdn/cdn-map-content-to-custom-domain.md).
